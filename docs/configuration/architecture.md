# Application

3 services tournent sur le Raspberry Pi 5 pour faire fonctionner la pendule de Furuta :

- **Application core (furuta-core.service)** : 
    service principal de l'application, il gère la logique de contrôle de la pendule et communique avec les autres services.
- **Application display (furuta-display.service)** : 
    service responsable de l'affichage sur l'écran du pendule de Furuta.
- **Jupyter notebook server (furuta-jupyter.service)** : 
    service qui héberge les notebooks Jupyter pour l'interaction utilisateur et la visualisation des données.

![diagramme d'architecture](/assets/diagrams/architecture.svg)

!!! tip
    Les services peuvent être gérés avec `systemctl` :
    
    ```bash
    sudo systemctl start furuta-core.service
    sudo systemctl stop furuta-display.service
    sudo systemctl status furuta-jupyter.service
    ```