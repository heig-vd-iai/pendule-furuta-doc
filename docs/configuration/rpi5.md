# Mise en service du Raspberry Pi 5

Le dépôt [pendule-furuta-installation](https://github.com/heig-vd-iai/pendule-furuta-installation) automatise le déploiement complet d'une maquette : flash de l'image système, configuration réseau et système via Ansible, compilation de la stack CANopen, déploiement de l'application `pendule-furuta-core` et configuration du drive moteur ELMO.

## Prérequis

- [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
- [uv](https://docs.astral.sh/uv/) (gestion de l'environnement Python et des dépendances Ansible)
- Une clé de déploiement SSH (`id_ed25519_repo` / `id_ed25519_repo.pub`) donnant accès en lecture au dépôt privé `pendule-furuta-core`, à placer dans `ansible/roles/bootstrap/files/`
- Le logiciel EASII pour la configuration du drive ELMO (voir [Paramétrisation du drive ELMO](drive_elmo.md))

!!! danger
    Ne jamais committer la clé de déploiement SSH dans le dépôt.

## 1. Flash de l'image Raspberry Pi OS

Le script `flash_rpi.bat` flashe une carte SD avec l'image Raspberry Pi OS Lite (via `rpi-imager` en ligne de commande). Il doit être lancé depuis un terminal Windows **en administrateur** :

```cmd
flash_rpi.bat <disk_num>
```

Appelé sans argument, le script affiche la liste des disques disponibles pour identifier `<disk_num>`.

La configuration réseau et utilisateur est injectée au flash via les fichiers `cloud-init/user-data` et `cloud-init/network-config`.

## 2. Connexion initiale

La première connexion au Raspberry Pi se fait via le port Ethernet, sur l'adresse IP statique `192.168.137.100`. L'interface Ethernet du PC hôte doit être configurée avec une IP dans la même plage, par exemple `192.168.137.1`.

Le partage de connexion internet doit être activé sur une autre interface du PC hôte (par exemple le Wi-Fi), afin que le Raspberry Pi ait accès à internet le temps de l'installation :

1. Ouvrir les propriétés de la connexion internet (Wi-Fi) dans le panneau de configuration Windows.
2. Onglet **Partage** → cocher *Autoriser les autres utilisateurs du réseau à se connecter via la connexion Internet de cet ordinateur*.
3. Sélectionner l'interface Ethernet cible dans la liste déroulante.

## 3. Configuration avec Ansible

### Phase 1 — Bootstrap

```bash
uv run ansible-playbook -i ansible/inventory/hosts.yml ansible/site.yml --limit pendule-furuta-01 --tags bootstrap
```

Cette phase :

- configure le système (hostname, utilisateur `pendule`) ;
- compile et installe Lely CANopen depuis les sources ;
- clone (via la clé de déploiement) et compile `pendule-furuta-core` ;
- active le lien CAN (contrôleur MCP2515 sur SPI, bitrate 500 kbit/s) ;
- installe les services systemd de l'application (voir [Application](architecture.md)).

En fin d'exécution, le playbook affiche l'adresse MAC de l'interface `wlan0`, à noter pour la phase suivante (enregistrement auprès du réseau Wi-Fi).

### Phase 2 — Wi-Fi

Avant de lancer cette phase, éditer `ansible/host_vars/pendule-furuta-01.yml` pour renseigner `wifi_ssid` et `wifi_psk` :

```bash
uv run ansible-playbook -i ansible/inventory/hosts.yml ansible/site.yml --limit pendule-furuta-01 --tags wifi
```

## 4. Configuration du drive ELMO

Le drive doit ensuite être paramétré avec le fichier `elmo driver/pendule-furuta.gprm` du dépôt : voir la page [Paramétrisation du drive ELMO](drive_elmo.md).
