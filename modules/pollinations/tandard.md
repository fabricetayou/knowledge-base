---
source: modules/notions/tandard.md
mis-à-jour: 2026-11-05
---

# Standard des besoins — couverture Pollinations et budget

Ce fichier suit l'organisation du cahier des charges `modules/notions/tandard.md` (exigences EF-1 à EF-5) : pour chaque exigence, la couverture Pollinations (modèles / endpoint / MCP concernés), puis un budget à trois niveaux reposant sur la qualité du travail et/ou le montant que l'utilisateur est prêt à payer.

## 1. Ce qui a été vérifié (le 2026-11-05)

- **Plateforme** : pollinations.ai est une plateforme générative open-source basée à Berlin (500+ projets communautaires) ; texte, image, vidéo, audio, musique, 3D ; « Build agents and apps ». Source : pollinations.ai (accueil) et github.com/pollinations/pollinations (README).
- **Agents Pollinations** : « Choose a system prompt and base model, optionally give it Pollinations tools, then call it through the same OpenAI-compatible API » ; configuration d'agent avec champs requis `systemPrompt` et `baseModel`, tableau optionnel `mcpServers`. Sources : README du dépôt ; référence de configuration d'agent (context7).
- **Monnaie Pollen** : chaque appel ajoute/débite du Pollen ; **1 Pollen = 1 $** ; rechargement à la demande ; crédits gratuits disponibles. Source : pollinations.ai (accueil) ; corroboré par tooljunction.io (mars 2026).
- **Paliers (tiers)** : **Spore / Seed / Flower / Nectar** avec recharges horaires ; recharges quotidiennes pour seed/flower/nectar, hebdomadaire pour spore ; codes 402/429 quand le solde du palier est épuisé. Sources : issues GitHub #11503 (01/06/2026) et #8994 (09/03/2026). Les montants exacts de recharge par palier n'ont pas été vérifiés (guide #5913 non consulté en détail) → **estimations**.
- **Serveur MCP officiel Pollinations-MCP** : `https://gen.pollinations.ai/mcp/pollinations` (Streamable HTTP), authentification `Authorization: Bearer <clé>`, appels débités du portefeuille Pollen de la clé. Outils vérifiés : `generateText` (/v1/chat/completions), `generateImage` (/v1/images/generations), `generateVideo`, `generateAudio`, `transcribeAudio`, `generate3D`, `createEmbeddings`, `listModels` (modèles, capacités, voix et **tarifs en direct**), `getModelStatus`, `getBalance`, `getUsage`, `getEarnings`, `listQuests`, `listKeys`, `createKey`, `revokeKey`, `listSites`. Source : packages/mcp/README.md du dépôt officiel (oct. 2026).
- **Endpoints de génération** : `https://gen.pollinations.ai/v1/chat/completions` (compatible OpenAI), `/text/{prompt}`, `/image/{prompt}?{params}` ; modèles image : `flux` (défaut), `turbo`, `gptimage`, `kontext`, `seedream`, `nanobanana`, `nanobanana-pro` ; dimensions 16–2048 px (défaut 1024) ; vision supportée en chat ; modèles texte : openai, claude, gemini, mistral, deepseek, grok, qwen, gpt-4, o1, o3 et 20+ autres. Sources : documentation API relayée par une fiche technique (llmbase.ai) ; tooljunction.io.
- **Offre gratuite** : génération d'images Flux **illimitée et gratuite** ; niveau gratuit en Pollen ≈ 1,5 Pollen/semaine (source tierce aicredits.dev, févr. 2026) ; accès sans clé ni inscription possible pour une partie des opérations (niveau anonyme). Sources : aicredits.dev, tooljunction.io — **à re-vérifier périodiquement**.
- **Clés API** : création sur enter.pollinations.ai ; clés secrètes `sk_` recommandées (côté serveur, sans limite de débit). Source : enter.pollinations.ai ; fiche technique tierce.
- **Computer-MCP** : deux produits vérifiés — **Agent360 Computer MCP** (computermcp.dev / Agent360dk/computerMCP : macOS, ~32 outils, MIT, `npx -y @agent360/computer-mcp`) et **Perplexity Computer MCP** (service cloud d'ordinateur via MCP, docs.perplexity.ai). Existe aussi un serveur MCP communautaire MCPollinations (pinkpixel-dev, successeur Nectar-MCP).

Tout montant mensuel ou par projet ci-dessous est une **estimation d'ordre de grandeur** : les tarifs exacts par modèle sont consultables en direct via l'outil `listModels` (vérifié ci-dessus).

## 2. Couverture exigence par exigence

### EF-1 — Base de connaissances modulaire

Hors périmètre Pollinations : le dépôt GitHub privé, le catalogue `modules/_index` et la chaîne `studies → notions → pollinations` vivent sur GitHub (hébergement et versionnage). Pollinations n'est pas mobilisé pour cette exigence ; il intervient à partir de EF-2.

### EF-2 — Agent applicatif

- Un agent applicatif sur Pollinations se configure avec `systemPrompt` + `baseModel` (+ `mcpServers`) — vérifié.
- La qualification du besoin (titre, taille, style, public, contraintes) et la proposition de montants sont de la génération de texte : `generateText` (MCP) ou endpoint `/v1/chat/completions` — vérifié.
- Les montants proposés proviennent des niveaux de qualité définis dans chaque module de `modules/<plateforme>/`, exprimés en Pollen (1 Pollen = 1 $) pour rester alignés sur les frais réels — vérifié pour la monnaie, grille à définir par module (à préciser).

### EF-3 — Agent de ronde

- La ronde s'exécute hors Pollinations (tâche planifiée de l'assistant expert) : Pollinations ne l'héberge pas aujourd'hui.
- Pollinations fournit en revanche la matière première des fichiers de ce dossier : potentialités et frais réels, consultables en direct via `listModels` (tarifs live) et `getModelStatus` (fiabilité/latence) — vérifié.
- Si la ronde devait être portée sur Pollinations, Pollinations-MCP et Computer-MCP seraient mobilisables via `mcpServers`.

### EF-4 — Agent Fabel (Pollinations)

- Fabel est un agent Pollinations natif : `systemPrompt` (v1.1, commandes littérales) + `baseModel` + `mcpServers`.
- **Pollinations-MCP** (officiel) : génération texte/image des livrables, plus `listModels` (tarifs à jour pour les devis) et `getBalance`/`getUsage` (suivi du budget Pollen) — outils vérifiés.
- **Computer-MCP** : exécution des commandes GitHub littérales (lecture du catalogue, écriture des studies) quand aucun outil shell natif n'est disponible ; deux candidats vérifiés (Agent360, macOS local ; Perplexity, cloud) — **choix final à préciser**.
- Écriture restreinte à `modules/studies/` (contrainte du standard).

### EF-5 — Cas d'usage « livre avec illustrations »

- Rédaction du livre : `generateText` avec modèles claude / gemini / openai (longs documents, vision pour les relectures d'illustrations) — modèles vérifiés.
- Illustrations (couverture, scènes) : `generateImage` avec `flux` (gratuit, illimité) jusqu'à `nanobanana-pro` / `seedream` / `gptimage` en haute qualité ; dimensions jusqu'à 2048 px — vérifié.
- Option livre parlant : `generateAudio` (outil MCP vérifié).
- Montants proposés à l'utilisateur : directement en Pollen (1 Pollen = 1 $) ou convertis en devise par le module — vérifié pour la monnaie.

## 3. MCP dans toutes les applications

Conformément au standard : **Pollinations-MCP** (officiel : génération + suivi Pollen + tarifs live) et **Computer-MCP** (contrôle d'ordinateur/terminal pour les commandes littérales) seront intégrés dans **toutes** les applications de l'utilisateur sur Pollinations, via le tableau `mcpServers` de la configuration d'agent.

## 4. Budget à trois niveaux

Base vérifiée : 1 Pollen = 1 $ ; image Flux gratuite et illimitée ; paliers Spore/Seed/Flower/Nectar avec recharges périodiques ; clés `sk_` sans limite de débit. Toutes les valeurs en $ ci-dessous sont des **estimations mensuelles d'ordre de grandeur**, à caler sur la consommation réelle mesurée via `getBalance`/`getUsage`.

### Niveau basse qualité — ≈ 0 $ (gratuit)

- **Ce que ça couvre** : images Flux illimitées gratuites (1024×1024 par défaut) ; texte sur le niveau anonyme/gratuit avec recharges faibles (≈ 1,5 Pollen/semaine — source tierce) ; modèles rapides (`turbo`, texte standard) ; production en une passe, sans itération ni relecture.
- **Limites** : soumis aux limites de débit publiques ; qualité texte et image moindre ; pas de clé secrète ; risque 402/429 sur les modèles premium.
- **Pour qui** : tests, prototypes, validation de concept.

### Niveau qualité moyenne — ≈ 5 à 10 $/mois (estimation)

- **Ce que ça couvre** : clé API + palier Seed/Flower (recharges quotidiennes vérifiées) ; texte `openai` / `gemini` / `mistral` ; images Flux avec dimensions personnalisées ; 1 à 2 itérations par livrable ; suivi du budget via `getBalance`/`getUsage`.
- **Pour qui** : productions régulières de bonne facture (ex. livre illustré standard), budget maîtrisé.

### Niveau meilleure qualité — ≈ 20 à 50 $/mois (estimation)

- **Ce que ça couvre** : clé secrète `sk_` sans limite de débit (recommandation vérifiée) ; modèles premium côté texte (claude, gemini, o3) et image (`nanobanana-pro`, `seedream`, `gptimage`, jusqu'à 2048 px) ; plusieurs itérations et relectures (vision) ; option audio (`generateAudio`) ; suivi Pollen systématique et ajustement des devis via `listModels` (tarifs live).
- **Pour qui** : livrables aboutis destinés à la diffusion, exigence de qualité maximale.

## 5. Synthèse budgétaire

| Niveau | Texte | Image | Itérations | Coût estimé/mois | Accès |
|---|---|---|---|---|---|
| Basse qualité | standard / turbo | flux 1024 px | 0 | ≈ 0 $ | anonyme ou clé gratuite |
| Qualité moyenne | openai / gemini / mistral | flux dimensionné | 1–2 | ≈ 5–10 $ | clé + palier Seed/Flower |
| Meilleure qualité | claude / gemini / o3 | nanobanana-pro / seedream / gptimage | 3+ | ≈ 20–50 $ | clé secrète `sk_` |

## 6. Informations restant à préciser

- Tarifs exacts par modèle : à relever en direct via `listModels` avant chaque production (le pricing est une donnée vivante).
- Montants des recharges Spore/Seed/Flower/Nectar : guide des paliers (issue #5913) non consulté en détail — **estimations ci-dessus à confirmer**.
- Choix du Computer-MCP retenu (Agent360 macOS local vs Perplexity cloud) : à trancher selon les applications visées.
- Volumes réels par type de livrable (nombre d'illustrations par livre, longueur en caractères) : à définir par les modules lors de la création du catalogue `modules/_index`.
