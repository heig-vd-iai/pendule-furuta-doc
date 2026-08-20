# Démarrage rapide

## Connexion
- Pour se connecter à un des pendule de Furuta il faut être connecté à la bulle de l'iAi via le VPN [Global Protect](https://vpn.heig-vd.ch) sur le portails `all-access.heig-vd.ch`.
- Ensuite le pendule est disponible en ssh à l'adresse `pendule@pendule-furuta-N.iai-heig-vd.in` ou N est le numéro du pendule de 01 à 14.

Pour se connecter depuis vscode :

1. ouvrire un fenêtre distante. 

    ![](/assets/images/remote_window.png)

2. choisir "Remote-SSH: Connect to Host..." 

    ![](/assets/images/remote_host.png)

3. entrer l'adresse du pendule (ex: `pendule@pendule-furuta-N.iai-heig-vd.ch`) 

    ![](/assets/images/remote_ssh.png)
    
4. pour la première connexion, sélectionner Linux, accepter la clé et entrer le mot de passe.

!!! Info
    L'host peux aussi être ajouté de manière permanente en sélectionnant `add new SSH host...` à l'étape 3. Il sera alors possible de se connecter directement depuis la liste des hôtes.

## Workspace
- Le dossier de travail est situé dans le répertoire `/home/pendule/workspace`.
- Il contient un exemple pour contrôler le pendule de Furuta via l'interface python.