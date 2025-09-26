# Conception

## Mécanique

La mécanique est fortement inspirée par [Ben Katz](https://build-its-inprogress.blogspot.com/search/label/Pendulum) qui a développé et produit une série de pendules de Furuta pour un laboratoire d'automatique. L'élément principal de son design est un moteur BLDC à rotor extérieur de l'entreprise T-Motor avec un trou au milieu. Un collecteur est fourni avec, qui se place exactement dans ce trou, et qui permet de transmettre les signaux du capteur angulaire du pendule depuis la partie rotative en haut vers la partie fixe en bas. Ceci permet d'éviter des câbles qui limiteraient une rotation de plusieurs tours, ce qui est un atout majeur de notre maquette Furuta.

Pour avoir un minimum de frottement et de jeu mécanique, des roulements céramiques ont été utilisés pour le pendule.

## Capteurs

Avec ses 2 degrés de liberté, deux capteurs incrémentaux ont été utilisés, un capteur Posic pour mesurer la position angulaire du moteur, et un capteur US Digital pour mesurer la position angulaire du pendule.

## Électronique

L'électronique est composée d'un sandwich de 3 PCB, sans aucun câblage entre les PCB. En bas, une RPI5, au milieu un PCB développé dans la maison. Ce PCB du milieu comporte un convertisseur DC/DC pour pouvoir alimenter la maquette avec un seul bloc d'alimentation de 24V. Le PCB au milieu comporte aussi une interface USB-UART (RS232) pour pouvoir paramétrer le drive. Le PC en haut consiste d'un drive de la maison ELMO qui effectue uniquement la régulation de courant (couple) du moteur. 