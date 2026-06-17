# Refonte visuelle « Néo-Swiss TRT » — Design

**Date :** 2026-06-17
**Fichier cible :** `trt-factory.html` (single-file, ~139 KB)

## Contexte

Le portfolio TRT Factory utilise aujourd'hui une esthétique sombre/cinéma :
fond noir, accent or, grain SVG, glows dorés, curseur custom, coins arrondis,
polices Bebas Neue + Syncopate + Barlow. Le client veut un virage radical vers
un **style suisse / International Typographic Style à la Josef Müller-Brockmann,
mais réinterprété pour 2026** (« des temps modernes ») : grille, ordre, typo
souveraine, vide, surfaces plates et pures.

L'objectif : un site dont le choc visuel vient de la **rigueur** (grille,
hiérarchie, espace) et non des effets décoratifs.

## Décisions validées

- **Thèmes** : on garde le toggle clair/sombre actuel, les **deux** versions sont
  refaites en style suisse.
- **Accent** : on **conserve l'or `#C9A84C`** (identité de marque), mais utilisé
  avec parcimonie — accent rare = accent fort.
- **Typo** : famille unique **Archivo** (grotesque néo-suisse, gamme de graisses
  complète) + **Space Mono** pour les métadonnées (labels, numéros, prix). On
  supprime Bebas Neue, Syncopate, Barlow.
- **Curseur custom** : **retiré** (curseur système standard).
- **Approche** : refonte du système de design (tokens, typo, grille, composants
  de base) puis application **section par section** pour validation visuelle
  incrémentale.
- **Conservé** : tout le contenu (textes, équipe, services + prix, projets) et
  toute la logique JS (toggle thème, onglets services/portfolio, calculateur de
  devis, lightbox, formulaire, navigation sidebar, scroll progress).

## Système de design

### Typographie
- **Archivo** : titres en très grandes tailles, tracking négatif (~-0.02em à
  -0.04em), interlignage serré (~0.95–1.0). Graisses : 800/900 pour le display,
  500/600 pour les sous-titres, 400 pour le corps.
- **Space Mono** : uppercase, letter-spacing positif (~0.1em), pour labels de
  section, numéros (01–08), prix, coordonnées, métadonnées — c'est l'élément qui
  donne le côté « technique / temps modernes ».
- Échelle typographique modulaire claire et limitée (display / h1 / h2 / h3 /
  body / label-mono).

### Grille & mise en page
- Grille **12 colonnes** avec gouttières fixes, conteneur à largeur max.
- **Filets fins** (« rules », 1px) pour structurer sections, lignes de services,
  séparateurs — signature Swiss.
- **Numérotation des sections** en mono (ex. `01 — HERO`, `02 — ADN`), exploitant
  le compteur `sb-pg` existant.
- Alignement **strict à gauche**, ragged right.
- Composition **asymétrique équilibrée** ; usage généreux du vide.
- Au moins un **bloc accent plein** (or ou noir) à la Brockmann.

### Couleur
- **Clair** : fond blanc (`#ffffff` / off-white `#f4f2ef`), texte noir (`#111`),
  or `#C9A84C` en accent rare.
- **Sombre** : fond noir (`#060608`), texte blanc cassé (`#f0ede8`), or en accent.
- Or réservé : filets, numéros, états hover, le bloc accent.

### À supprimer (rupture nette)
- Grain SVG (`body::after`), glows et ombres dorées.
- Tous les **coins arrondis** → angles droits.
- Le **curseur custom** (`#cur`, `#cur-r`, `cursor:none`).
- Animations clinquantes → transitions **sobres et fonctionnelles** uniquement
  (fade / translate discrets, durées courtes).

### Composants (re-stylés en Swiss)
- Boutons : rectangulaires, contour fin, hover en inversion (invert).
- Tableau de services : lignes strictes alignées sur la grille, prix en mono.
- Portfolio : grille modulaire stricte.
- Formulaire : champs à filets fins, labels mono.
- Sidebar / nav : minimale, typo mono, filets.

## Sections concernées (ordre de traitement)

Système global (tokens, fonts, reset, grille, composants de base) → puis :
1. Hero — 2. About/ADN — 3. Team — 4. Process — 5. Stats — 6. Services —
7. Portfolio — 8. Difference — 9. Contact.

Chaque section validée visuellement avant de passer à la suivante.

## Vérification

- Ouvrir `trt-factory.html` dans le navigateur après chaque section refaite.
- Vérifier les **deux thèmes** (clair + sombre via le toggle).
- Vérifier que la logique JS fonctionne toujours : onglets services/portfolio,
  calculateur de devis, lightbox, formulaire, navigation, scroll progress.
- Vérifier le rendu **responsive** (desktop + mobile).
- Confirmer la disparition du grain, des glows, des arrondis et du curseur custom.
