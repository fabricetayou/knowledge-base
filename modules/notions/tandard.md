---
source: modules/studies/tandard.md
version-source: 1
as-of-source: 2026-11-05
mis-à-jour: 2026-11-05
---

# Standard des besoins à mettre en œuvre — cahier des charges

## Objet et contexte

Ce cahier des charges est issu du study `modules/studies/tandard.md` (v1, as-of 2026-11-05), qui constitue le **standard de référence** du projet : l'ensemble des besoins et règles à mettre en œuvre pour le système de base de connaissances modulaire hébergé dans le dépôt GitHub privé `fabricetayou/knowledge-base` (branche `main`). Le système articule trois acteurs : une base de connaissances modulaire, des agents applicatifs en session utilisateur, et un agent de ronde planifié qui fait avancer la chaîne de connaissances. Toute évolution du système doit rester cohérente avec ce standard ; tout écart constaté est une lacune à combler.

## Exigences fonctionnelles

### EF-1 — Base de connaissances modulaire

- **EF-1.1** Un dépôt GitHub privé unique (`fabricetayou/knowledge-base`, branche `main`) héberge toute la connaissance.
- **EF-1.2** Un catalogue `modules/_index` (extensions acceptées dans l'ordre : `.md`, `.yaml`, `.yml`, `.json`) est le point d'entrée unique répertoriant les modules disponibles.
- **EF-1.3** La chaîne de connaissances est matérialisée par trois dossiers : `modules/studies/` (besoins bruts) → `modules/notions/` (cahiers des charges) → `modules/pollinations/` (implémentation et budgets Pollinations).
- **EF-1.4** Le système est multi-plateformes : un agent tournant sur une plateforme P consulte `modules/<plateforme>/` (ex. Pollinations → `modules/pollinations/`).
- **EF-1.5** Les agents ont une fiche dans `agents/` : description publique et prompt système.
- **EF-1.6** La documentation de référence vit dans `WIKI.md` à la racine, référencé par `README.md`.

### EF-2 — Agent applicatif (en session utilisateur)

- **EF-2.1** L'agent dispose d'un catalogue de `modules` pour trouver rapidement les modules adéquats : il recherche ceux qui conviennent au besoin de l'utilisateur.
- **EF-2.2** Module trouvé : il réunit les informations nécessaires — titre, taille (physique ou en nombre de caractères), style, public, contraintes — puis propose un montant, ou plusieurs au choix, selon les niveaux de qualité du module.
- **EF-2.3** Module introuvable : il informe l'utilisateur qu'il apprend encore, fait de son mieux pour répondre au besoin avec le module standard, et rédige simultanément, scrupuleusement, un fichier study dans `modules/studies/` avec toutes les explications de l'utilisateur récupérées subtilement.
- **EF-2.4** Chaque study porte un frontmatter : la source de la nouvelle étude à faire (la plateforme — ex. Pollinations — et l'application qui tourne), la version, la date as-of.
- **EF-2.5** Complément inter-sessions : lorsque de nouvelles informations d'un autre utilisateur complètent un study existant, l'agent ajuste le fichier, incrémente la version et met à jour la date as-of.

### EF-3 — Agent de ronde (assistant expert planifié)

- **EF-3.1** Il prend les fichiers de `modules/studies/` et les étudie pour créer des fichiers dans `modules/notions/`.
- **EF-3.2** Il prend les fichiers de `modules/notions/`, les étudie et crée des fichiers dans `modules/pollinations/`.
- **EF-3.3** Il compare les métadonnées (version, as-of) et le contenu pour refaire une étude ou une analyse quand ils ont changé.
- **EF-3.4** Les notions sont produites avec expertise et ressources : analyse scrupuleuse du study, extension possible en plusieurs notions, fichiers organisés, structurés, instructifs, ressemblant à des cahiers des charges, avec frontmatter référençant le study source.
- **EF-3.5** Les fichiers pollinations traduisent les potentialités et les frais autour des fonctionnalités de Pollinations : ils suivent l'organisation de la notion et proposent un budget reposant sur la qualité du travail et/ou le montant que l'utilisateur est prêt à payer, avec des estimations pour meilleure qualité, qualité moyenne et basse qualité. **Pollinations-MCP et Computer-MCP seront utilisés dans toutes les applications sur Pollinations.**

### EF-4 — Agent Fabel (plateforme Pollinations)

- **EF-4.1** Identité : id `fabel`, Full name Fabel, hébergé sur Pollinations.
- **EF-4.2** Sa description publique et son prompt système sont versionnés dans `agents/`.
- **EF-4.3** Il lit le catalogue (`modules/_index`) et consulte ses modules dans `modules/pollinations/` ; l'accès au dépôt passe par l'API GitHub avec des commandes littérales (jeton intégré, Pollinations ne permettant pas de masquer les secrets).
- **EF-4.4** Son écriture dans le dépôt est restreinte à `modules/studies/`.

### EF-5 — Cas d'usage de référence (livre avec illustrations)

- **EF-5.1** Utilisateur : « J'ai besoin d'un livre avec illustrations. » Module trouvé : l'agent demande quel titre, quelle taille (physique ou en nombre de caractères) ; les informations réunies, il propose un montant (ou plusieurs au choix).
- **EF-5.2** Module introuvable : « Je vais créer ce livre pour vous. Sachez cependant que je suis toujours en train d'évoluer à force d'apprendre. » — et l'agent crée le fichier study dans le dépôt avec toutes les explications récupérées subtilement.

## Contraintes

- **C-1 Langues** : les artifacts du dépôt sont en français ; un agent applicatif comme Fabel répond dans la langue de l'utilisateur ; le prompt système de Fabel est en anglais.
- **C-2 Nommage** : kebab-case ; même nom de base tout au long de la chaîne (study → notion → pollinations) ; suffixe distinctif en cas de collision ; sous-dossiers aplatis.
- **C-3 Intégrité** : aucune invention de faits ni de prix ; les estimations sont explicitement signalées ; les manques sont notés « à préciser ».
- **C-4 Secrets** : le jeton de ronde (assistant planifié) ne figure jamais dans le dépôt ; le jeton Fabel est intégré au prompt système (contrainte de la plateforme Pollinations) ; les deux jetons ne doivent jamais être confondus.
- **C-5 Périmètres d'écriture** : la ronde ne modifie que `modules/notions/` et `modules/pollinations/` ; Fabel ne modifie que `modules/studies/`.

## Critères de réussite

- **CR-1** Tout study nouveau ou modifié (version/as-of/contenu) produit une ou plusieurs notions à la prochaine exécution de la ronde (horaires).
- **CR-2** Toute notion nouvelle ou modifiée produit son fichier pollinations dans la même exécution de la ronde.
- **CR-3** La re-étude est automatiquement déclenchée par l'incrément version/as-of d'un study complété en inter-sessions.
- **CR-4** Fabel trouve ses modules via `modules/_index` et consigne ses studies dans `modules/studies/` par commandes littérales.
- **CR-5** Chaque exigence du standard dispose, dans `modules/pollinations/`, d'une couverture Pollinations et d'un budget à trois niveaux.

## Informations restant à préciser

- Le contenu initial du catalogue `modules/_index` (à créer ; le dépôt ne contient pas encore de module).
- La grille de montants que les modules devront proposer aux utilisateurs (à définir module par module, en s'appuyant sur les frais réels de Pollinations).
- Les noms exacts des « applications qui tournent » côté utilisateur, à reporter dans le frontmatter `source` des studies.
- Le ou les Computer-MCP retenus (plusieurs produits existent ; le choix précis reste à arrêter côté Pollinations).
