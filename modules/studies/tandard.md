---
source: zai-chat / instructions projet (fabricetayou)
version: 1
as-of: 2026-11-05
---

# Standard des besoins à mettre en œuvre

Ce fichier est le **standard** : l'ensemble des besoins et règles à mettre en œuvre dans le système. Il sert de référence à toute évolution ; tout écart constaté est une lacune à combler.

## 1. Base de connaissances modulaire

- Dépôt GitHub privé : `fabricetayou/knowledge-base`, branche `main`.
- Catalogue : `modules/_index` (essayer `.md`, `.yaml`, `.yml`, `.json`) — point d'entrée unique des modules disponibles.
- Chaîne de connaissances : `modules/studies/` → `modules/notions/` → `modules/pollinations/`.
- Multi-plateformes : un agent tournant sur une plateforme P consulte `modules/<plateforme>/` (ex. Pollinations → `modules/pollinations/`).
- Agents : description publique et prompt système dans `agents/`.
- Documentation de référence : `WIKI.md` à la racine, référencé par `README.md`.

## 2. Agent applicatif (en session utilisateur)

1. Il a un catalogue de `modules` pour trouver rapidement les modules adéquats : il recherche ceux qui conviennent au besoin de l'utilisateur.
2. **Module trouvé** : il réunit les informations nécessaires — titre, taille (physique ou en nombre de caractères), style, public, contraintes — puis propose un montant, ou plusieurs au choix, selon les niveaux de qualité du module.
3. **Module introuvable** : il informe l'utilisateur qu'il apprend encore et fait de son mieux pour répondre au besoin avec le module standard ; en même temps, il rédige scrupuleusement un fichier study dans `modules/studies/` avec toutes les explications de l'utilisateur récupérées subtilement.
4. Frontmatter du study : la source de la nouvelle étude à faire (la plateforme — ex. Pollinations —, l'application qui tourne), la version, la date (as-of).
5. Complément inter-sessions : lorsque de nouvelles informations d'un autre utilisateur complètent un study existant, l'agent applicatif ajuste le fichier, incrémente la version et met à jour la date as-of.

## 3. Agent de ronde (assistant expert planifié)

1. Il prend les fichiers de `modules/studies/` et les étudie pour créer des fichiers dans `modules/notions/`.
2. Il prend les fichiers de `modules/notions/`, les étudie et crée des fichiers dans `modules/pollinations/`.
3. Il compare les métadonnées (version, as-of) et le contenu pour refaire une étude ou une analyse quand ils ont changé.
4. **Notions** : l'agent de ronde a l'expertise et les ressources ; il analyse le study scrupuleusement et peut l'étendre en plusieurs notions. Fichiers organisés, structurés, instructifs, ressemblant à des cahiers des charges ; frontmatter avec le study source.
5. **Pollinations** : l'agent de ronde connaît les potentialités et les frais autour des fonctionnalités de Pollinations. Le fichier suit l'organisation de la notion pour proposer un budget reposant sur la qualité du travail et/ou le montant que l'utilisateur est prêt à payer, avec des estimations pour meilleure qualité, qualité moyenne ou basse. Pollinations-MCP et Computer-MCP seront utilisés dans toutes les applications sur Pollinations.

## 4. Agent Fabel (plateforme Pollinations)

- Id : `fabel` — Full name : Fabel.
- Description publique et prompt système dans `agents/`.
- Lit le catalogue (`modules/_index`) et consulte ses modules dans `modules/pollinations/`.
- Accès au dépôt par l'API GitHub ; les commandes sont littérales (jeton intégré), car Pollinations ne permet pas de masquer les secrets ; écriture restreinte à `modules/studies/`.

## 5. Cas d'usage de référence

Utilisateur : « J'ai besoin d'un livre avec illustrations. »

- Module trouvé : quel titre, quelle taille (physique ou en nombre de caractères) ; les informations réunies, l'agent propose un montant (ou plusieurs au choix).
- Module introuvable : « Je vais créer ce livre pour vous. Sachez cependant que je suis toujours en train d'évoluer à force d'apprendre. » — et l'agent crée le fichier study dans le dépôt avec toutes les explications récupérées subtilement.
