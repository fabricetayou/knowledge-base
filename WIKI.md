# WIKI — knowledge-base

**Document de référence du projet.** Il rassemble tout ce qu'il faut pour comprendre le système ainsi que l'ensemble des instructions données par fabricetayou via le chat Z.ai. Il sert de source de vérité en cas de reprise dans un nouveau fil de discussion : en cas de contradiction ailleurs, c'est ce fichier qui fait foi — et il doit être mis à jour à chaque évolution du projet.

Dernière mise à jour : 2026-11-05

---

## 1. Le projet en une phrase

Un dépôt GitHub privé, `fabricetayou/knowledge-base`, sert de base de connaissances modulaire à des agents applicatifs : ils y cherchent un module correspondant au besoin d'un utilisateur ; s'ils n'en trouvent pas, ils consignent le besoin dans `modules/studies/`, et un assistant expert planifié (la « ronde ») transforme ces études en cahiers des charges (`modules/notions/`) puis en fichiers d'implémentation et de budget par plateforme (`modules/pollinations/`).

## 2. Vue d'ensemble

```
Utilisateur ⇄ Agent applicatif (ex. Fabel sur Pollinations)
                  │  lit   modules/_index (catalogue) + modules/<plateforme>/
                  │  écrit modules/studies/ (besoins non couverts)
                  ▼
           Dépôt GitHub privé (branche main)
                  ▲
                  │  sync / étude / push (tâche planifiée, chaque heure)
Assistant Z.ai « de ronde » : studies → notions → pollinations
```

## 3. Structure du dépôt

| Chemin | Rôle |
|---|---|
| `README.md` | Page d'accueil du dépôt ; renvoie au présent WIKI |
| `WIKI.md` | Ce document : compréhension du projet + toutes les instructions |
| `agents/` | Fiches des agents : description publique + prompt système (ex. Fabel) |
| `modules/_index` (.md/.yaml/.yml/.json) | Catalogue des modules disponibles — point d'entrée unique |
| `modules/studies/` | Besoins non couverts, rédigés par les agents applicatifs |
| `modules/notions/` | Cahiers des charges instructifs produits par la ronde |
| `modules/pollinations/` | Fichiers d'implémentation Pollinations + budgets par niveau de qualité |
| `modules/<plateforme>/` | Équivalent pour toute autre plateforme d'agent |

## 4. La chaîne de connaissances

### 4.1 studies — rédigés par l'agent applicatif

- Provoqués par un besoin réel d'utilisateur auquel l'agent n'a pas su répondre par module. L'agent n'a pas l'expertise de la connaissance modulée : il recueille tout ce qu'il peut de l'utilisateur (subtilement) et rédige le study scrupuleusement.
- Frontmatter : la **source** de l'étude (plateforme — ex. Pollinations —, l'application qui tourne), la **version**, la date **as-of**.
- Complément inter-sessions : un autre utilisateur (autre session) peut compléter un study → l'agent applicatif ajuste le fichier et **incrémente la version** et la date as-of.
- Format :

  ```
  ---
  source: pollinations / <application qui tourne>
  version: 1
  as-of: 2026-11-05
  ---
  <le besoin tel qu'exprimé, le contexte, les contraintes, le livrable attendu, ce qui reste à préciser>
  ```

### 4.2 notions — produites par la ronde

- La ronde a l'expertise et les ressources pour bien étudier le study ; elle l'analyse scrupuleusement.
- Un study peut s'étendre en **plusieurs notions** (une par domaine).
- Fichiers organisés, structurés, instructifs : ils ressemblent à des **cahiers des charges**.
- Frontmatter : `source` (study source), `version-source`, `as-of-source`, `mis-à-jour`.

### 4.3 pollinations — produits par la ronde

- La ronde connaît bien Pollinations : potentialités et frais autour des fonctionnalités.
- Le fichier suit **l'organisation de la notion** (même découpage, mêmes exigences).
- Budget reposant sur la qualité du travail et/ou le montant que l'utilisateur est prêt à payer, avec estimations **meilleure qualité / qualité moyenne / basse qualité**.
- **Pollinations-MCP et Computer-MCP seront utilisés dans toutes les applications sur Pollinations.**
- Frontmatter : `source` (notion source), `mis-à-jour`.

### 4.4 Ré-étude

La ronde compare les métadonnées (version, as-of) et le contenu de chaque fichier avec son état précédent : nouveau fichier, version/as-of modifiées, ou contenu modifié → re-étude.

## 5. Les agents applicatifs

Comportement attendu (toute plateforme) :

1. Cherche le module adéquat via le catalogue `modules/_index`, puis consulte les modules de `modules/<plateforme>/`.
2. **Module trouvé** : réunit les informations (titre, taille — physique ou en nombre de caractères —, style, public…) et propose un montant, ou plusieurs au choix.
3. **Module introuvable** : informe l'utilisateur qu'il apprend encore (« Je vais créer ce livre pour vous. Sachez cependant que je suis toujours en train d'évoluer à force d'apprendre. »), répond de son mieux avec le module standard, et crée le fichier study dans le dépôt avec toutes les explications récupérées subtilement.

Cas d'usage de référence : « J'ai besoin d'un livre avec illustrations. »

## 6. Fabel — agent Pollinations

- **Id** : `fabel` — **Full name** : Fabel.
- Fichiers : `agents/description-fabel.md` (description publique) et `agents/prompt-systeme-fabel.md` (prompt système, v1.1).
- Lit le catalogue `modules/_index` et consulte ses modules dans `modules/pollinations/`.
- Écrit ses studies via l'API GitHub (PUT `/contents/modules/studies/<slug>.md`), uniquement dans `modules/studies/`.
- **Contrainte Pollinations** : les commandes doivent être littérales et les secrets ne peuvent pas être masqués → le jeton GitHub de Fabel est intégré en clair dans son prompt système (consigne de fabricetayou, 2026-11-05) ; l'ancienne variable d'environnement `GITHUB_RO_TOKEN` est abandonnée.
- Répond dans la langue de l'utilisateur ; studies rédigés en français.

## 7. L'agent de ronde (assistant Z.ai, planifié)

### 7.1 Tâche planifiée

- **ID cron** : 504061 — nom : « Ronde : studies → notions → pollinations (fabricetayou/knowledge-base) ».
- **Planning** : `0 0 * * * ?` — **chaque heure à :00, heure de Douala (Africa/Douala)**.
- Exécutions isolées (MaxConcurrency 1, timeout 7200 s).

### 7.2 Infrastructure locale (persistante, hors dépôt)

| Chemin | Rôle |
|---|---|
| `/home/z/my-project/scripts/studies_watch.py` | Script de ronde : `sync`, `pending`, `push "<message>"`, `mark-study <chemin>`, `mark-notion <chemin>`, `checkpush` |
| `/home/z/my-project/github-watch/repo` | Copie locale du dépôt (branche main) |
| `/home/z/my-project/github-watch/state.json` | État des fichiers déjà traités (sha256 + version + as-of par étape) |
| `/home/z/my-project/github-watch/.github_token` | **Jeton de ronde** (voir §8) |
| `/home/z/my-project/github-watch/.fabel_token` | Copie de secours du jeton Fabel |

### 7.3 Procédure de chaque ronde

1. `python3 /home/z/my-project/scripts/studies_watch.py sync` — fetch + reset --hard origin/main.
2. `... pending` — JSON : `studies.en_attente` et `notions.en_attente` (fichiers nouveaux, ou dont version / as-of / contenu a changé).
3. Les deux listes vides → réponse unique : « Rien à signaler — studies et notions inchangés. »
4. **Phase 1** : chaque study en attente est lu intégralement (skills de lecture pour PDF/DOCX et fichiers non-texte) et étendu en notions — cahiers des charges en français, frontmatter `source`/`version-source`/`as-of-source`/`mis-à-jour`, « à préciser » pour les manques, aucune invention.
5. **Phase 2** : re-`pending` → chaque notion en attente est étudiée → fichier pollinations suivant l'organisation de la notion ; recherche web sur les frais réels Pollinations si besoin (estimations signalées comme telles) ; budget à 3 niveaux ; Pollinations-MCP et Computer-MCP mentionnés.
6. `... push "feat(knowledge): notions + pollinations — <fichiers>"` — n'ajoute que `modules/notions/` et `modules/pollinations/`.
7. Si le push renvoie `"ok": true` : `mark-study` / `mark-notion` pour chaque fichier traité. Sinon : rien n'est marqué (tout sera refait au prochain passage).
8. Compte-rendu bref en français.

Règles : ne modifier dans le dépôt **que** `modules/notions/` et `modules/pollinations/` ; ne jamais afficher le jeton de ronde.

## 8. Jetons GitHub — attention, ne pas les confondre

Deux jetons coexistent ; ils partagent le même préfixe `github_pat_` et couvrent le même dépôt privé :

| | Jeton de ronde (assistant Z.ai) | Jeton Fabel |
|---|---|---|
| **Où il vit** | `/home/z/my-project/github-watch/.github_token` (permissions 600) — **jamais dans le dépôt** | En clair dans `agents/prompt-systeme-fabel.md` (contrainte Pollinations) + copie locale `.fabel_token` |
| **Sert à** | git fetch/push de la ronde (clone local) | appels API GitHub de Fabel (lecture du catalogue, écriture des studies) |
| **Interdits** | l'afficher, le citer, le committer, le mettre dans le prompt de Fabel | l'utiliser pour autre chose que les commandes du prompt Fabel |

- Portée réelle constatée le 2026-11-05 : **les deux jetons ont un accès admin** sur le dépôt (pull, push, maintain, triage, admin). Recommandation : les resserrer à « Contents: Read and write ».
- Règle d'or : jamais le jeton de ronde dans le prompt de Fabel, jamais le jeton Fabel dans l'infrastructure de la ronde.

## 9. Le standard

`modules/studies/tandard.md` (v1, 2026-11-05) : le **standard des besoins à mettre en œuvre** — il consolide l'ensemble des règles ci-dessus sous forme de study. La prochaine exécution de la ronde l'étudiera et produira les notions puis les fichiers pollinations correspondants.

## 10. Conventions

- **Langues** : dépôt et artifacts en français ; Fabel répond dans la langue de l'utilisateur ; son prompt système est en anglais.
- **Nommage** : kebab-case ; même nom de base tout au long de la chaîne (study → notion → pollinations) ; suffixe distinctif en cas de collision ; sous-dossiers aplatis (`modules/studies/a/b.md` → `modules/notions/b.md`).
- **Commits** : `study: <slug> v<n>` (study) ; `feat(knowledge): notions + pollinations — …` (ronde) ; `feat(agents): …` (agents) ; `docs: …`.
- **Catalogue** : `modules/_index` — essayer dans l'ordre `.md`, `.yaml`, `.yml`, `.json`.

## 11. Journal des instructions (chat Z.ai)

- **2026-11-05 — fil 1** : création d'une tâche planifiée surveillant `modules/studies/` (résultats dans `modules/features/`) ; clarifications : horaire, dépôt privé `fabricetayou/knowledge-base`, jeton fourni, push des résultats, fichiers nouveaux/modifiés seulement, fiche ~300 mots, silencieux sinon → tâche 503858 (supprimée depuis).
- **2026-11-05 — fil 2** : refonte de l'architecture en chaîne **studies → notions → pollinations** (formats et frontmatters détaillés, compléments inter-sessions avec incrément version/as-of, budgets à 3 niveaux, Pollinations-MCP/Computer-MCP partout) ; ancienne tâche supprimée, ronde actuelle créée (**504061**).
- **2026-11-05 — fil 3** : agent **Fabel** (Pollinations, id `fabel`) : description publique + prompt système livrés puis poussés dans `agents/` ; jeton d'abord prévu « lecture seule via variable d'environnement », corrigé en accès écriture.
- **2026-11-05 — fil 4 (courant)** : création de `modules/studies/tandard.md` (standard des besoins) ; prompt v1.1 : commandes **littérales**, jeton intégré (masquage impossible d'après la doc Pollinations), prompt plus détaillé ; création du présent `WIKI.md`, référencé par le `README.md` ; push de l'ensemble. Rappel : ne pas confondre les deux jetons.

## 12. Reprise dans un nouveau thread (procédure)

1. Lire ce WIKI en entier.
2. Vérifier l'infrastructure (§7.2) : si `repo` manque, le re-cloner avec le jeton de `.github_token` (credential helper) ; si `studies_watch.py` manque, le reconstituer d'après §7.3.
3. Vérifier la tâche cron 504061 (list/get) ; si elle a disparu, la recréer selon §7.1 et §7.3.
4. Respecter la distinction des jetons (§8).
5. Répondre en français ; en cas de doute, demander à fabricetayou.
