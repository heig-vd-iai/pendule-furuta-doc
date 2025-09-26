# Introduction

Le pendule de Furuta est un pendule inversé rotatif, inventé par Katsuhisa Furuta en 1992 au "Tokyo Institute of Technology". Il s'agit d'un système instable en mode inversé (Up), fortement nonlinéaire et sous-actionné (deux degrés de liberté, mais un seul actionneur). De nombreux laboratoires automatiques disposent de pendules de Furuta, pour l'enseignement et pour la recherche.
A la HEIG-VD, lors d'un  [travail de Bachelor]("Philippe Krstev, "Développement d’un pendule de Furuta", TB HEIG-VD, 2024."), un prototype d'un pendule de Furuta a été réalisé en 2024, dans le but de l'affiner par la suite, de le dupliquer et de l'utiliser finalement comme nouvelle maquette de laboratoire.

## Avantages du pendule de Furuta

- Contrairement au pendule inverse classique qui nécessite un rail et qui est encombrant et cher, le pendule de Furuta est très compact et relativement bon marché.
- Avec ses deux positions d'équilibre "Up" et "Down", le pendule de Furuta offre de nombreuses possibilités pour créer des manipulations de laboratoire de complexité différente. 

Ci-dessous une liste de possibilités pour des futurs laboratoires, TB, TM et démonstrateurs pour des salons d'exposition :

- labo `Regul`, 4ème semestre : uniquement régler le moteur en position par un régulateur PID; ne pas prendre compte le pendule en mode "Down" qui perturbe un peu.
- labo `RegNum`, 5ème semestre : ajouter au PID  un *preshaping filter* pour filtrer la consigne de position du moteur, afin de ne pas exciter la fréquence de résonance du pendule. Le pendule devrait s'arrêter de bouger sans oscillations résiduelles après une consigne de mouvement de A à B.
- labo `AAV`, cours à choix au semestre 6 : retour d'état par placement de pôles ou par LQR pour les modes "Up" et "Down". Le modèle simplifié dans l'espace d'état est d'ordre 4, donc 4 paramètres dans le retour d'état.
- futurs `TB` et `TM`, tester différents stratégies pour le `Swing Up`, évtl. tester l'utilisation de l'intelligence artificielle (IA) comme solution alternative.
- `démonstrateur` pour salons d'exposition. Disposant dans le futur de ~14 maquettes de pendules de Furuta, on pourrait envisager une mise en scène des maquettes qui font des mouvements coordonnés de ballet. Cela nécessiterait une communication entre les maquettes.
  
## Objectifs et contraintes

L'objectif était de développer et produire une série de pendules de Furuta destinée surtout à l'enseignement. Pour cela, on s'est mis les contraintes suivantes :

- Pas besoin de PC fixes, les étudiants devraient pouvoir utiliser leurs propres PC portable.
- Pas besoin d'installer des logiciels ou des pilotes sur les PC portables des étudiants.
- Pour les étudiants, possibilité de pouvoir changer facilement le code de la régulation, sans cacher la régulation derrière une interface graphique.



