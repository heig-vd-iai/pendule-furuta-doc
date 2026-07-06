# Application

## Services
3 services tournent sur le Raspberry Pi 5 pour faire fonctionner la pendule de Furuta :

- **Application core (furuta-core.service)** : 
    service principal de l'application, écrit en C++. Il gère la communication CANopen avec le drive ELMO, exécute la régulation en temps réel et communique avec les autres services via une mémoire partagée.
- **Application display (furuta-display.service)** : 
    service responsable de l'affichage sur l'écran OLED du pendule de Furuta et de la gestion des quatre boutons. Il permet de changer de mode et d'afficher l'état du pendule sans PC.
- **Jupyter notebook server (furuta-jupyter.service)** : 
    service qui héberge les notebooks Jupyter pour l'interaction utilisateur et la visualisation des données. **Pas encore disponible.**

Les unités systemd `furuta-core.service` et `furuta-display.service` sont fournies dans le dossier `service/` du dépôt [pendule-furuta-core](https://github.com/heig-vd-iai/pendule-furuta-core).

![diagramme d'architecture](/assets/diagrams/architecture.svg)

!!! tip
    Les services peuvent être gérés avec `systemctl` :
    
    ```bash
    sudo systemctl start furuta-core.service
    sudo systemctl stop furuta-display.service
    sudo systemctl status furuta-jupyter.service
    ```

## Application core (`furuta_core`)

L'application core se trouve dans le dépôt [pendule-furuta-core](https://github.com/heig-vd-iai/pendule-furuta-core). Elle est écrite en C++ et s'appuie sur la bibliothèque [lely-core](https://opensource.lely.com/canopen/) pour jouer le rôle de master CANopen sur le bus `can0`.

- **Master CANopen** : node 1, configuré par `master_config.yaml` (le fichier `master.dcf` est généré avec `dcfgen -r master_config.yaml`).
- **Drive ELMO** : node 127, décrit par le fichier EDS `elmo.eds`.
- **Cadence** : le master émet un message SYNC toutes les 2 ms (`sync_period: 2000` µs). Le drive publie ses mesures par TPDO synchrones et le core calcule le couple à chaque SYNC, soit à 500 Hz.

Les objets échangés par PDO sont :

| Sens | Objet | Contenu |
| --- | --- | --- |
| drive → core | `0x6064` | position du bras (codeur moteur) |
| drive → core | `0x606C` | vitesse du bras |
| drive → core | `0x20A0` | position du pendule |
| drive → core | `0x2F00:01` | vitesse du pendule |
| core → drive | `0x6071` | consigne de couple (Target Torque) |

Le drive est configuré en mode *Profile Torque* au démarrage. En mode `IDLE`, l'étage de puissance est coupé (moteur libre) ; il n'est activé (*Operation Enabled*) qu'à l'entrée dans un mode actif.

Pour garantir un comportement proche du temps réel, le service systemd lance l'application avec une priorité FIFO élevée sur un cœur CPU dédié :

```bash
sudo chrt -f 90 taskset -c 3 ./furuta_core
```

## Communication interface ↔ core

Le core crée un segment de mémoire partagée POSIX nommé `/furuta_ctl_shm`. Toute la communication avec les autres processus (interface Python, display) passe par ce segment, avec un protocole *seq/ack* :

1. le client écrit ses commandes (mode, demande d'acquisition, taille d'acquisition) puis incrémente le champ `seq` ;
2. à chaque SYNC, le core lit les nouvelles commandes et acquitte en recopiant `seq` dans `ack` ;
3. le core publie en continu l'état mesuré (positions, vitesses) et le mode courant, en lecture seule pour les clients.

La structure `Communication::SharedData` (`src/communication.hpp`) définit la disposition mémoire :

| Offset | Champ | Type | Description |
| --- | --- | --- | --- |
| 0 | `seq` | uint32 | numéro de séquence de commande (client) |
| 4 | `ack` | uint32 | acquittement (core) |
| 8 | `mode` | uint32 | mode demandé / mode courant |
| 12 | `startAcquisition` | uint32 | demande de démarrage d'acquisition |
| 16 | `acquisitionSize` | uint32 | nombre d'échantillons à acquérir |
| 20 | `position` | float | angle du bras (rad) |
| 24 | `pendulumPosition` | float | angle du pendule (rad), 0 = en bas |
| 28 | `velocity` | float | vitesse du bras (rad/s) |
| 32 | `pendulumVelocity` | float | vitesse du pendule (rad/s) |

!!! warning
    Le mode publié par le core peut différer du mode commandé : le core change de mode de lui-même, par exemple `SWING_UP` → `REGULATION_UP` une fois le pendule capturé en haut, ou passage en `IDLE` en cas de chute.

## Interface Python (`pendule-furuta-interface`)

La bibliothèque Python `pendule-furuta-interface` (dossier `pendule-furuta-interface/` du dépôt core) abstrait la communication par mémoire partagée. C'est l'API utilisée dans les notebooks Jupyter et par le display.

```python
from pendule_furuta_interface import FurutaPendulum, Mode

pendulum = FurutaPendulum()

pendulum.set_mode(Mode.SWING_UP)     # change le mode du core
pendulum.start_acquisition(1000)     # enregistre 1000 échantillons

print(pendulum.position)             # angle du bras (rad)
print(pendulum.pendulum_position)    # angle du pendule (rad), 0 = en bas
print(pendulum.velocity)             # vitesse du bras (rad/s)
print(pendulum.pendulum_velocity)    # vitesse du pendule (rad/s)
print(pendulum.current_mode)         # mode courant du core
```

Les modes disponibles (`Mode`) sont : `IDLE`, `REGULATION_DOWN`, `REGULATION_UP`, `IDENTIFICATION`, `SWING_UP` (voir la page [Régulation](../régulation/regulation.md)).

Les méthodes `set_mode` et `start_acquisition` retournent `True` si le core a acquitté la commande dans le délai imparti (1 s par défaut).

## Acquisition de données

Sur demande de l'interface (`start_acquisition(n)`), le core enregistre `n` échantillons — un par SYNC, soit toutes les 2 ms — dans un fichier HDF5 horodaté (`acquisition_YYYYMMDD_HHMMSS.h5`). Le fichier est écrit localement puis déplacé dans `/home/pendule/workspace/data/`, où il est accessible depuis les notebooks Jupyter.

Chaque enregistrement contient :

- les horodatages (µs) de la dernière mise à jour de chaque mesure,
- un compteur de séquence et le node CANopen,
- `position`, `pendulumPosition`, `velocity`, `pendulumVelocity`,
- le couple commandé `torque`.

En mode `IDENTIFICATION`, la demande d'acquisition déclenche en plus le signal d'excitation chargé depuis `/home/pendule/workspace/excitation.h5` (dataset `signal`, attribut `fs`) ; l'acquisition dure alors exactement la longueur du signal et le fichier est nommé `identification_YYYYMMDD_HHMMSS.h5`.
