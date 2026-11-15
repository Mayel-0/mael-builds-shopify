# Modifications apportées à Horizon

Le thème part de **Horizon 4.3.0** (commit `1d2c1f7`). Le travail du projet vit
surtout dans des fichiers à part (`blocks/mb-*`, `snippets/mb-*`, `sections/mb-*`,
`src/scss/`). Ce document liste les **seuls** fichiers d'Horizon qui ont été
modifiés, pour pouvoir les reporter sans erreur lors d'une mise à jour du thème.

## Conventions

- Chaque modification du code d'Horizon est précédée d'un commentaire **`mael :`**
  (`{%- # mael : … -%}`, `# mael : …` dans un tag `{% liquid %}`, `/* mael : … */` en CSS).
  Pour toutes les retrouver : `grep -rn "mael" blocks snippets sections layout`.
- Les réglages ajoutés ont des libellés traduits sous `mael.*` dans
  `locales/*.schema.json`.
- Les styles ajoutés sont dans `src/scss/horizon/`, un fichier par fichier Horizon,
  avec un sélecteur assez spécifique pour passer devant les styles d'origine.

## Fichiers dont la logique a changé

| Fichier | Modification | Pourquoi |
|---|---|---|
| `layout/theme.liquid` | Charge `mb-polices` et `mael-builds.css` | Polices de la charte et styles compilés depuis `src/scss` |
| `blocks/_header-logo.liquid` | Réglage « Texte du logo » | Texte de remplacement quand aucune image de logo n'est choisie |
| `blocks/menu.liquid` | Réglages direction, bordure, padding, titre masquable, lien actif (`link.current` + `aria-current`) | Menu de catégories en pastilles sur la page collection |
| `blocks/filters.liquid` | Titre « Filtres » et nombre de produits masquables, séparateur de prix réglable | Correspondre à la maquette de la page collection |
| `snippets/price-filter.liquid` | Paramètre `separator_text`, titre en gras, suppression du « prix le plus élevé » | Filtre de prix plus sobre |
| `snippets/list-filter.liquid` | Classes `alter__graisse` et `filtre__liste` | Titres en gras, espacement des options |
| `blocks/price.liquid` | Texte avant / après le prix, couleur de ces textes ; prix du projet transmis par un parent | « À partir de 12,00 € » ; prix dans le hero en mode projet |
| `blocks/add-to-cart.liquid` | Texte du bouton réglable, sans icône, ligne de réassurance traduite | Fiche produit conforme à la maquette |
| `snippets/variant-main-picker.liquid` | Valeur choisie dans la légende, détail et prix de chaque format, label de livraison | Choix « Fichier STL / Kit complet » de la fiche produit |
| `blocks/image.liquid` | Image de secours : produit transmis, galerie du projet transmis, projet de la page | Le bloc Image fonctionne sans image choisie dans un contexte produit ou projet |
| `blocks/_product-card-gallery.liquid` | Réglage « Badge » affiché avec les badges Promo / Épuisé | Badge de difficulté sur les cartes produit |
| `blocks/collection-card.liquid` | `content_for 'blocks', closest.collection: collection` | Les blocs enfants reçoivent la collection (nombre de produits) |
| `blocks/_featured-product.liquid` | `mb-titre` à la place de `product-title` | Un seul bloc titre dans tout le thème |

## Fichiers dont seul le schéma a changé

Uniquement les blocs autorisés et les presets : le bloc `product-title` a été remplacé
par `mb-titre`, le badge de difficulté est relié dans les presets des cartes, et le
bloc `mb-nombre-produits` est autorisé dans les cartes de collection.

`blocks/_collection-card.liquid`, `blocks/_product-card.liquid`,
`blocks/_product-card-group.liquid`, `blocks/product-card.liquid`,
`blocks/product-recommendations.liquid`, `sections/featured-product.liquid`,
`sections/featured-product-information.liquid`, `sections/product-list.liquid`,
`sections/product-recommendations.liquid`.

## Fichier supprimé

| Fichier | Remplacé par |
|---|---|
| `blocks/product-title.liquid` | `blocks/mb-titre.liquid`, un titre générique (texte, lien, label, badge réglables ou reliés à une source dynamique) |

## Configuration

- `config/settings_schema.json` : nom, version, auteur et liens du thème.
- `config/settings_data.json`, `sections/*-group.json`, `templates/*.json` : contenu
  et réglages enregistrés depuis l'éditeur.
