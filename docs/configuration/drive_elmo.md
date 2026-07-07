# Paramétrisation du drive ELMO

Pour la paramétrisation du drive ELMO, il faut préalablement installer le logiciel de paramétrisation EASII. La communication avec le drive se fait via une liaison sérielle (RS232) : une passerelle USB ⇔ RS232 est incluse sur le PCB custom de la maquette. Lors de la première connexion USB, un pilote devrait s'installer.

La possibilité la plus simple pour paramétrer un drive ELMO vierge consiste à télécharger le fichier de configuration `pendule-furuta.gprm`, disponible dans le dossier `elmo driver/` du dépôt [pendule-furuta-installation](https://github.com/heig-vd-iai/pendule-furuta-installation). La marche à suivre est la suivante :

1. Connecter le drive ELMO à l'aide d'un câble USB-C branché sur la carte HAT du pendule de Furuta.
2. Ouvrir le logiciel EASII et se connecter en **RS232** sur le port COM correspondant (baudrate `115200`, parité `none`).
3. Dans la barre d'outils *Parameters*, cliquer sur **Textual** (*Download Textual Parameters file to drive*).
4. Sélectionner le fichier `elmo driver/pendule-furuta.gprm` et valider.
5. Charger le programme de lecture de la vitesse sur le drive : menu **Drive programming**, puis sélectionner le fichier [`furuta.ehl`](https://github.com/heig-vd-iai/pendule-furuta-installation/blob/main/elmo%20driver/furuta.ehl) et valider.

!!! note
    Résumé des settings à compléter.

Ci-dessous un *résumé* des settings inclus dans le fichier de paramétrisation :

- nombre de pôles de pairs = 7
- courant max
- vitesse max
- mode torque [UM = ?]
- configuration encodeur US-Digital  ....
- configuration Posic ....
- configuration de la commutation `Binary Search` ....
- protection bus DC  ...
- etc, etc,  ...
  
Le régulateur de courant peut être déterminé en mode "auto-tuning". Les paramètres du régulateur de courant trouvé expérimentalement sont à peu près ....

Le programme `furuta.ehl` chargé à l'étape 5 copie en permanence la vitesse du pendule `FV[2]` sur `UI[1]`, qui est mappé en PDO. Il est marqué `#@AUTOEXEC` : il démarre donc automatiquement à la mise sous tension du drive, une fois chargé.
