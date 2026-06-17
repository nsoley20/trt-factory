# Refonte « Néo-Swiss TRT » — Plan d'implémentation

> **Pour les workers agentiques :** SOUS-SKILL REQUIS — utiliser
> superpowers:subagent-driven-development (recommandé) ou
> superpowers:executing-plans pour exécuter ce plan tâche par tâche. Les étapes
> utilisent la syntaxe checkbox (`- [ ]`).

**Goal :** Transformer `trt-factory.html` (site sombre/cinéma actuel) en un
portfolio de style suisse moderne (International Typographic Style réinterprété
2026), dans les deux thèmes clair/sombre, sans casser le contenu ni la logique JS.

**Architecture :** Fichier unique `trt-factory.html` (HTML + CSS inline + JS
inline). On procède en 2 temps : (1) une **fondation** mécanique et globale
(polices, tokens, suppression grain/curseur/arrondis, utilitaires Swiss), puis
(2) un **raffinage section par section** (9 sections), chacun validé visuellement
dans le navigateur (clair + sombre) avant commit.

**Tech Stack :** HTML5, CSS3 (custom properties / variables de thème), JS vanilla,
Google Fonts (Archivo + Space Mono). Pas de framework, pas de runner de test →
vérification = ouverture navigateur + contrôle des deux thèmes + JS intact.

**Note de discipline :** projet statique sans tests automatisés. La discipline
TDD est remplacée par des **checkpoints de vérification visuelle** explicites
après chaque tâche, avec commits fréquents.

---

## Structure du fichier

- `trt-factory.html` — seul fichier modifié.
  - `<head>` (l. ~8-11) : lien Google Fonts.
  - `<style>` (l. ~11-1500) : tokens de thème, reset, composants, sections.
  - `<body>` (l. ~1516-fin) : markup des 9 sections + overlays.
  - `<script>` (fin de fichier) : logique JS — **ne pas casser**.

Pas de découpage de fichier (single-file assumé par le projet et le déploiement
Vercel via `vercel.json`).

---

## Task 1 : Fondation — polices, tokens, nettoyage global

**Files:**
- Modify: `trt-factory.html` (head fonts, `:root` tokens, reset, grain/curseur)

- [ ] **Step 1 : Remplacer le lien Google Fonts**

Dans `<head>` (l. 10), remplacer la ligne `<link href="...Bebas+Neue...Syncopate...Barlow...">` par :

```html
<link href="https://fonts.googleapis.com/css2?family=Archivo:wght@400;500;600;700;800;900&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
```

- [ ] **Step 2 : Migrer la typo par remplacements globaux**

Avec Edit `replace_all` sur ces chaînes exactes (elles apparaissent ~91 fois) :

- `font-family:'Barlow',sans-serif;` → `font-family:'Archivo',sans-serif;`
- `font-family:'Bebas Neue',sans-serif;` → `font-family:'Archivo',sans-serif;`
- `font-family:'Syncopate',sans-serif;` → `font-family:'Space Mono',monospace;`
- `font-family:Syncopate,sans-serif;` → `font-family:'Space Mono',monospace;` (l. 2637, sans guillemets)

- [ ] **Step 3 : Supprimer le grain SVG**

Supprimer le bloc `body::after { ... feTurbulence ... }` (l. ~135-139).

- [ ] **Step 4 : Supprimer le curseur custom**

- Supprimer le bloc `@media(pointer:fine){ body{cursor:none;} #cur{...} #cur-r{...} .cur-lg ... }` (l. ~109-129) et `@media(pointer:coarse){ #cur,#cur-r{display:none;} }` (l. ~130).
- Supprimer les éléments `#cur` / `#cur-r` dans le markup s'ils existent.
- Dans le JS, neutraliser le code qui déplace `#cur`/`#cur-r` (listener mousemove) pour éviter les erreurs `null`.

- [ ] **Step 5 : Supprimer tous les coins arrondis (style plat anguleux)**

Pour les 12 occurrences de `border-radius`, mettre la valeur à `0` (sauf le `#theme-toggle` rond l. ~83 : décider — soit le passer en carré `border-radius:0`, soit le garder rond en exception ; recommandation : carré pour cohérence Swiss).

- [ ] **Step 6 : Ajuster les tokens de thème pour le canon Swiss**

Dans `:root,[data-theme="dark"]` et `[data-theme="light"]` (l. ~15-54), conserver l'or `--gold:#C9A84C`. Vérifier les contrastes : light = fond blanc pur `--bg-main:#ffffff`, texte `--white:#111118` ; dark = `--bg-main:#060608`, texte `--white:#f0ede8`. Ajouter au besoin des tokens utilitaires :

```css
--rule: 1px;                 /* épaisseur des filets */
--grid-max: 1440px;          /* largeur conteneur */
--col-gap: 24px;             /* gouttière */
```

- [ ] **Step 7 : Ajouter les utilitaires Swiss de base**

Ajouter dans `<style>` un bloc de classes réutilisables :

```css
/* GRILLE SWISS */
.swiss-container{ max-width:var(--grid-max); margin:0 auto; padding:0 var(--pad-x); }
.swiss-grid{ display:grid; grid-template-columns:repeat(12,1fr); gap:var(--col-gap); }
.rule{ border:0; border-top:var(--rule) solid var(--border-grey); width:100%; }
.rule-gold{ border-top:var(--rule) solid var(--gold); }
/* LABEL MONO (métadonnées, numéros) */
.mono-label{ font-family:'Space Mono',monospace; font-size:11px; letter-spacing:.12em; text-transform:uppercase; color:var(--text-dim); }
/* NUMÉRO DE SECTION */
.sec-num{ font-family:'Space Mono',monospace; font-size:12px; letter-spacing:.1em; color:var(--gold); }
```

- [ ] **Step 8 : Supprimer glows / ombres dorées**

Rechercher les `box-shadow` et `text-shadow` à teinte dorée (`rgba(201,168,76,...)`) et les supprimer (Swiss = plat). Garder uniquement d'éventuelles ombres neutres fonctionnelles si nécessaires.

- [ ] **Step 9 : Checkpoint visuel + commit**

Ouvrir `trt-factory.html` dans le navigateur. Vérifier : polices Archivo/Space Mono chargées, plus de grain, plus de curseur custom, angles droits, pas d'erreur JS en console (toggle thème, onglets fonctionnent). Tester clair ET sombre.

```bash
git add trt-factory.html
git commit -m "Néo-Swiss: fondation — typo Archivo/Space Mono, suppression grain/curseur/arrondis"
```

---

## Tasks 2-10 : Raffinage section par section

Pour **chaque** section ci-dessous, appliquer le même traitement Swiss puis le
même checkpoint. Travailler une section à la fois.

**Traitement Swiss standard (à appliquer à chaque section) :**
1. Envelopper le contenu dans `.swiss-container` ; aligner sur `.swiss-grid` (12 col).
2. Ajouter un en-tête de section : `<span class="sec-num">0N</span>` + label `.mono-label` + titre Archivo très grand (font-weight 800/900, `letter-spacing:-.02em`, `line-height:.95`).
3. Alignement strict à gauche, ragged right ; augmenter le vide (padding vertical généreux).
4. Remplacer fonds dégradés / glows par aplats ; séparateurs via `.rule` / `.rule-gold`.
5. Or réservé aux filets, numéros, hover, et au plus un bloc accent plein.
6. Vérifier responsive (mobile : grille retombe en 1 colonne, tailles réduites).

**Checkpoint standard (fin de chaque tâche) :** ouvrir dans le navigateur,
vérifier clair + sombre, vérifier que la logique JS de la section marche, puis :
`git add trt-factory.html && git commit -m "Néo-Swiss: section <nom>"`.

- [ ] **Task 2 — Hero** (`#hero`, l. ~1566 ; ticker l. ~1606). Titre massif Archivo cala à gauche, métadonnées en mono (`Design. Cinéma. Digital.`, localisation Libreville). Ticker conservé mais restylé sobre.
- [ ] **Task 3 — About / ADN** (`#about`, l. ~1628).
- [ ] **Task 4 — Team** (`#team`, l. ~1673). Grille modulaire des membres, filets, labels mono pour les rôles.
- [ ] **Task 5 — Process** (`#process`, l. ~1719). Étapes numérotées en mono.
- [ ] **Task 6 — Stats** (`#stats`, l. ~1757). Gros chiffres Archivo, libellés mono.
- [ ] **Task 7 — Services** (`#services`, l. ~1765 ; panes design/cinema/digital + calculateur de devis). **Préserver** les `data-svc-*` et la logique JS du calculateur. Lignes de services en grille stricte, prix en mono, filets entre lignes. Onglets restylés.
- [ ] **Task 8 — Portfolio** (`#portfolio`, l. ~1968 ; panes graphiste/videaste/dev + lightbox). **Préserver** la lightbox JS (`#ptf-lb`) et les onglets. Grille modulaire stricte des projets.
- [ ] **Task 9 — Difference** (`#difference`, l. ~2224). Possibilité d'un bloc accent plein (or ou noir) ici.
- [ ] **Task 10 — Contact** (`#contact` + `#contact-form`, l. ~2249). Champs à filets fins, labels mono. **Préserver** la logique du formulaire et le `<select>` de service.

---

## Tasks finales

- [ ] **Task 11 — Navigation & chrome global** : sidebar `#sb` (numéro `01 / 08`),
  barre de progression `#prog`, header mobile `#mob-hd` / `#burger` / `#mob-menu`,
  bouton `#theme-toggle`, skip-link. Restyler en Swiss minimal (mono, filets,
  angles droits). Vérifier nav desktop + mobile dans les deux thèmes.

- [ ] **Task 12 — Passe de cohérence finale** : relire tout le rendu, harmoniser
  l'échelle typographique (display/h1/h2/h3/body/label), vérifier l'usage parcimonieux
  de l'or, valider responsive global (desktop + mobile), vérifier la console
  (zéro erreur JS). Commit final.

```bash
git add trt-factory.html
git commit -m "Néo-Swiss: passe de cohérence finale"
```

---

## Vérification end-to-end

1. Ouvrir `trt-factory.html` dans le navigateur (desktop).
2. Basculer le thème via `#theme-toggle` → vérifier clair ET sombre sur chaque section.
3. Tester chaque interaction JS : onglets services + calculateur de devis, onglets
   portfolio + lightbox, formulaire de contact + select, navigation sidebar / menu
   mobile, scroll progress.
4. Réduire la fenêtre (mobile) → vérifier que la grille retombe proprement.
5. Console navigateur : aucune erreur (notamment plus de référence à `#cur`/`#cur-r`).
6. Confirmer visuellement : zéro grain, zéro glow doré, zéro coin arrondi, curseur
   système standard, typo Archivo + Space Mono partout.
```
