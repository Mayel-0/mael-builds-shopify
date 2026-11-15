# Mael Builds · thème Shopify

Boutique de démonstration de fichiers STL et de kits d'objets imprimés en 3D,
construite sur **Horizon**, le thème officiel de Shopify.

L'objectif : une boutique **entièrement gérable par le marchand**. Un nouveau projet
se crée depuis l'admin, les pages se composent dans l'éditeur de thème, et les
réglages restent bornés pour que le design ne casse pas.

> Le marchand gère tout. Le code reste propre.

**Stack** : Shopify (Online Store 2.0, theme blocks), Liquid, métaobjets, SCSS (BEM),
Shopify CLI, Theme Check, Search & Discovery, Digital Products.

---

## Sommaire

- [Le projet](#le-projet)
- [Architecture](#architecture)
- [Modèle de données](#modèle-de-données)
- [Blocs et snippets](#blocs-et-snippets)
- [Styles](#styles)
- [Conventions](#conventions)
- [Démarrer](#démarrer)
- [Basé sur Horizon](#basé-sur-horizon)

---

## Le projet

**Côté client** : choisir, comprendre, acheter.

- Chaque projet existe en **fichier STL** (téléchargement immédiat) et en **kit complet**.
- La fiche produit détaille la fiche technique, le matériel nécessaire et les étapes de montage.
- Chaque projet a sa page **making-of**, qui renvoie vers le produit.

**Côté marchand** : tout gérer sans une ligne de code.

- Un projet = un **métaobjet** : pièces, matériaux, composants et étapes se saisissent dans l'admin.
- Les pages sont composées dans l'**éditeur de thème** avec des blocs réutilisables.
- Les **filtres** (format, niveau, prix) sont configurés dans Search & Discovery.

| Page | Rôle |
|---|---|
| Accueil | Donne envie et oriente vers une catégorie (hero, garanties, catégories, produits phares, making-of à la une) |
| Collection | Navigation par catégorie (collections automatiques) et filtres |
| Fiche produit | Choix du format, ajout au panier, fiche technique, matériel, étapes |
| Page projet | Le making-of, avec un lien pour acheter |

---

## Architecture

```
.
├── blocks/      # blocs de thème ; les blocs du projet sont préfixés mb-
├── sections/    # sections ; mb-hero
├── snippets/    # fragments réutilisables ; préfixés mb-
├── locales/     # traductions fr et en uniquement : textes du site et libellés de l'éditeur
├── src/scss/    # sources SCSS, compilées dans assets/mael-builds.css
└── docs/        # documentation technique
```

### Des blocs génériques

Un bloc **ne devine pas** sur quelle page il se trouve. Ses valeurs viennent de ses
réglages, écrits à la main ou reliés à une **source dynamique** dans l'éditeur.
Le même bloc `mb-titre` sert ainsi de titre de fiche produit, de titre de carte
et de titre de hero.

### Le parent transmet, l'enfant lit

Les données descendent de parent en enfant avec `closest` :

```liquid
{%- # Le parent fournit un produit à tous ses blocs enfants -%}
{% content_for 'blocks', closest.product: block.settings.produit %}
```

```liquid
{%- # L'enfant lit le produit le plus proche, sans savoir d'où il vient -%}
{{ closest.product.title }}
```

- `mb-groupe-produit` fournit un produit à ses enfants (titre, prix, image…).
- `mb-hero` fournit un **produit ou un projet** à ses deux zones.
- Les cartes de collection fournissent leur collection (`mb-nombre-produits`).

Les blocs liés à un projet le cherchent toujours dans le même ordre :
le projet du produit transmis, puis le projet transmis directement
(`closest.metaobject.projet`), puis le projet de la page en cours.

### Horizon étendu, modifications tracées

Le code du projet vit dans ses propres fichiers. Quand un fichier d'Horizon doit
être modifié, chaque modification est marquée d'un commentaire `mael :` et listée
dans [docs/modifications-horizon.md](docs/modifications-horizon.md), pour pouvoir
la reporter lors d'une mise à jour d'Horizon.

---

## Modèle de données

```
Produit ──(custom.projet)──▶ Projet
                              ├── titre, accroche, galerie, difficulté
                              ├── produit ──▶ Produit
                              ├── pièces ────────▶ Pièce ──▶ Matériau
                              │                    (nom, quantité,      (nom, couleur,
                              │                     temps, couche)       température)
                              ├── composants ────▶ Ligne de composant ──▶ Composant
                              │                    (quantité)             (nom, image, lien)
                              └── étapes ────────▶ Étape (titre, contenu, image)
```

| Métachamp | Sur | Rôle |
|---|---|---|
| `custom.projet` | Produit | Le projet (métaobjet) lié au produit |
| `custom.difficulte` | Produit | Niveau affiché en badge sur les cartes |
| `custom.livraison` | Variante | Message de livraison sous le sélecteur de format |
| `custom.detail_format` | Variante | Détail de chaque format (« Téléchargement immédiat · usage personnel ») |

Chaque projet a aussi sa propre page, générée par Shopify à partir du métaobjet
(`templates/metaobject/projet.json`).

---

## Blocs et snippets

### Blocs

| Bloc | Rôle |
|---|---|
| `mb-titre` | Titre générique : texte, lien, label, type de produit, badge, niveau H1 à H4 |
| `mb-lien` | Lien en carte, avec label et petit lien ; mène au projet ou au produit par défaut |
| `mb-groupe-produit` | Groupe qui transmet un produit à ses blocs enfants |
| `mb-description` | Description d'un produit, ou résumé chiffré d'un projet |
| `mb-accordeon` | Accordéon qui accueille d'autres blocs (fiche technique, matériel) |
| `mb-liste-pieces` | Pièces imprimées et temps d'impression total |
| `mb-liste-composants` | Composants électroniques, avec lien d'achat |
| `mb-etapes` | Étapes de montage repliables, numérotées |
| `mb-galerie-projet` | Galerie du projet, en mosaïque ou en grille |
| `mb-garanties` / `mb-garantie` | Bandeau de réassurance |
| `mb-nombre-produits` | Nombre de produits d'une collection (« 8 articles ») |

### Section

| Section | Rôle |
|---|---|
| `mb-hero` | Hero en deux zones fixes (contenu, visuel), alimenté par un produit ou un projet |

### Snippets

| Snippet | Rôle |
|---|---|
| `mb-details` | Accordéon `<details>` générique |
| `mb-resume-projet` | Résumé chiffré d'un projet (temps, matériaux, pièces, composants, niveau) |
| `mb-piece` | Une pièce imprimée et son badge de matériau |
| `mb-composant-ligne` | Une ligne de composant |
| `mb-etape` | Une étape de montage |
| `mb-duree` | Durée en heures décimales, formatée « 8,3 h » ou « 8h18 » |
| `mb-badge-difficulte` | Badge de niveau |
| `mb-label-livraison` | Message de livraison d'une variante |
| `mb-polices` | Déclaration des polices de la charte |

---

## Styles

- **SCSS compilé** par `sass` dans `assets/mael-builds.css`, chargé par `layout/theme.liquid`.
- **Un fichier par composant**, rangé comme le Liquid : `src/scss/blocks/`,
  `snippets/`, `sections/`, et `horizon/` pour les surcharges d'Horizon.
- **BEM** : `.mb-lien`, `.mb-lien__label`, `.mb-lien--sombre`.
- **Design tokens** dans [`src/scss/abstracts/_variables.scss`](src/scss/abstracts/_variables.scss) :
  palette de la charte, espacements, rayons, graisses. Aucune valeur de design en dur.
- **Mixins** de breakpoints (`a-partir-de(tablette)`, `avant(tablette)`), en mobile d'abord.
- Un **réglage qui change une propriété CSS** passe par une variable CSS ; un réglage
  qui en change plusieurs passe par une classe.

```
src/scss/
├── abstracts/   # variables et mixins (ne génèrent pas de CSS)
├── base/        # :root, typographie
├── blocks/      # un fichier par bloc mb-
├── snippets/    # un fichier par snippet mb-
├── sections/    # un fichier par section mb-
├── horizon/     # surcharges ciblées des fichiers d'Horizon
└── main.scss    # point d'entrée
```

---

## Conventions

- **Nommage** : tout ce qui est propre au projet est préfixé `mb-` et nommé d'après son rôle.
- **Traductions** : aucun texte en dur. Textes du site dans `locales/*.json`, libellés
  de l'éditeur dans `locales/*.schema.json`, sous la clé `mael.*`. Le thème est livré
  en **français** et en **anglais** (langue par défaut) : les 53 autres langues
  d'Horizon ont été retirées, la boutique n'en utilisant aucune.
- **Documentation** : chaque snippet commence par un `{% doc %}` (paramètres et exemple).
- **Qualité** : Theme Check sans erreur ni avertissement sur le code du projet.
- **Commits** : [Conventional Commits](https://www.conventionalcommits.org/fr/)
  (`feat`, `fix`, `refactor`, `docs`…), un commit par sujet.

---

## Démarrer

**Prérequis** : Node.js, [Shopify CLI](https://shopify.dev/docs/storefronts/themes/tools/cli),
un accès à la boutique de développement.

```bash
npm install
```

Lancer le serveur de développement (compilation SCSS en continu et aperçu synchronisé
avec l'éditeur de thème) :

```bash
npm run dev
```

Compiler le CSS une fois, minifié :

```bash
npm run build
```

Vérifier le thème :

```bash
npm run check
```

---

## Basé sur Horizon

Ce thème est dérivé de [Horizon](https://github.com/Shopify/horizon) 4.3.0, le thème
de référence de Shopify. Horizon est distribué sous la licence de Shopify
([LICENSE.md](LICENSE.md)), qui s'applique à ce dépôt. Les modifications apportées
à Horizon sont listées dans [docs/modifications-horizon.md](docs/modifications-horizon.md).

---

Réalisé par **Maël Llado**, projet de démonstration (prix fictifs).
