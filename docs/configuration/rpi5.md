
# Configuration du Raspberry Pi 5

## Raspberry Pi OS
Afin d'optimiser les performances du Raspberry Pi 5, une image minimale de Raspberry Pi OS préconfigurée est construite avec l'outil [Pi-Gen](https://github.com/RPi-Distro/pi-gen). L'image peut être téléchargée ou compilée depuis le dépôt GitHub [pendule-furuta-installation](). **Dépôt pas encore disponible.**

## Guide d'installation du Raspberry Pi
1. Récupérer l'adresse MAC du Raspberry Pi 5 en branchant le RPI5 à un écran sans la carte microSD. L'adresse MAC est affichée au démarrage.
2. Enregistrer l'adresse MAC auprès du service informatique qui attribuera un mot de passe WiFi unique.
3. Télécharger l'image Raspberry Pi OS préconfigurée depuis le dépôt GitHub [pendule-furuta-installation]() **Dépôt pas encore disponible.** ou compiler l'image avec Pi-Gen.
4. Flasher l'image sur une carte microSD avec [Raspberry Pi Imager](https://www.raspberrypi.com/software/).
5. Modifier le fichier `pendule-furuta-installation/cloud-init/user-data` pour y insérer le mot de passe WiFi attribué.
6. Ajouter le contenu du dossier `pendule-furuta-installation/cloud-init` dans la partition `boot` de la carte microSD.
7. Insérer la carte microSD dans le Raspberry Pi 5 et démarrer.

!!! WARNING
    L'étape 5 doit impérativement être réalisée avant le premier démarrage du Raspberry Pi 5 sur la carte microSD nouvellement flashée. Sinon, le Raspberry Pi 5 n'exécutera pas la configuration cloud-init. Si le Raspberry Pi 5 a déjà été démarré une fois avec cette carte microSD, il faut nettoyer l'état cloud-init avec la commande `sudo cloud-init clean --logs` et redémarrer le Raspberry Pi 5 pour appliquer la configuration.