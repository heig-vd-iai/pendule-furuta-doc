# Documentation Pendule de Furuta

Ce référentiel contient la documentation pour la maquette de laboratoire "Pendule de Furuta" utilisée dans les cours de régulation à la HEIG-VD.

La documentation est générée à l'aide de [MkDocs](https://www.mkdocs.org/) et hébergée sur GitHub Pages.

## Développement

Initialisez le dépôt avec :

```bash
git clone https://github.com/heig-vd-iai/pendule-furuta-doc
cd pendule-furuta-doc
pipx install poetry
poetry install
```

Pour lancer le serveur de développement, utilisez :

```bash
poetry run mkdocs serve
```

## Déploiement

La documentation est automatiquement déployée sur GitHub Pages à chaque push sur la branche `main`.

