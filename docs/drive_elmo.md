# Paramétrisation du drive ELMO
!!! note
    A Compléter

Pour la paramétrisation du drive ELMO, il faut préalablement installer le logiciel de paramétrisation EASII. La communication avec le drive se fait via une liaison sérielle (RS232). Pour cela, une puce `????` est inclue sur le PCB custom de la maquette qui fait passerelle USB <=> RS232. Lors de la première connection USB, un pilote devrait s'installer.

Configuration du baudrate ......

La possibilité la plus simple pour paramétrer un drive ELMO vierge consiste à downloader le fichier de configuration `xxxxxx.yyy`. Pour ce faire, la marche à suivre est la suivante :

- bla1
- bla2
- bla3

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

Un petit code embarqué sur le drive copie en permanence la vitesse du pendule FV[2] sur UI[1] qui est mappé en PDO.  .....  #@AUTOEXEC  ....
