<!-- Modèle à copier à la racine du projet sous le nom CLAUDE.md. Remplacer chaque <...>, supprimer les lignes des agents non utilisés. -->
# <Nom du projet>

Générateur de business games : un formateur choisit des paramètres (secteur, modules, PNJ, difficulté, durée, aléas) ;
le projet assemble sans modèle IA une simulation, jouée par des joueurs avec des PNJ IA qui disent les faits calculés
par le moteur. Rôles : joueur, formateur (crée et gère ses lobbies), admin (accès total).
Exigence n° 1 : les connaissances transmises doivent être vraies et sourcées.

Domaine prioritaire : <domaine>. Décisions : <liens vers les ADR>. Points ouverts : <fichier>.

## Stack et dossiers
| Brique | Techno | Où |
|---|---|---|
| Client | <ex. Godot, Unity, front web> | <dossier> |
| API | <ex. FastAPI, Node> | <dossier> |
| Bus d'événements (optionnel) | <ex. Kafka, RabbitMQ> | <dossier> |
| Base et RAG | <ex. PostgreSQL + pgvector> | <dossier> |
| IA | <ex. Ollama local, API hébergée> | catalogue de tâches : <fichier> |

Matériel et limites : <mémoire GPU, RAM, budget>.

## Commandes
- Lancer : `<commande>` ; arrêter : `<commande>`
- Tests : `<commande>` ; mesure de l'équipe : skill `mesure-equipe` du plugin (par défaut) ou `<commande propre au projet>`

## Agents (.claude/agents) : équipe et organisation
La session principale peut tourner en `orchestrateur` (clé `agent` de `.claude/settings.json` du projet, ou
`claude --agent orchestrateur`). Si le plugin est installé, les noms sont préfixés
(`team-for-business-game-generation:orchestrateur`).

| Agent | Modèle | Possède | À lui pour |
|---|---|---|---|
| orchestrateur | session | rien : il délègue | découper, déléguer, contrôler, rendre compte |
| architecte | opus | ADR, contrats (spec, événements) | choix transverse, ADR, plan d'exécution |
| game-designer | opus | spec de simulation, domaines, packs | justesse métier, chiffres, sources, PNJ, débrief |
| moteur-simulation | sonnet | moteur et génération, leurs tests | règles du jeu, génération, mémoire des PNJ |
| ia-runtime | sonnet | catalogue de tâches IA, prompts, évaluations | tâches IA, prompts des PNJ, budgets, mesure des dialogues |
| rag-knowledge | sonnet | pipeline RAG, corpus | ingestion, recherche, sources et droits |
| backend-api (optionnel) | sonnet | API, base, migrations | endpoints, rôles, base |
| client-dev (optionnel) | sonnet | client | monde 3D ou front, HUD, menus |
| pipeline-evenements (optionnel) | sonnet | bus, workers | topics, événements, workers |
| assets (optionnel) | sonnet | catalogue d'assets | import, licences, couverture |
| devops (optionnel) | haiku | conteneurs, scripts | lancement, environnement |
| qa-reviewer | sonnet | tests manquants | relecture de diff, tests, vérifications |
| documentaliste | sonnet | docs hors ADR, README, CLAUDE.md, `.claude/` | docs à jour, nouveaux agents |
| coach-equipe | opus | rétros | mesurer et améliorer l'équipe (propositions appliquées après accord) |

Carte de propriété lisible par machine : `.claude/ownership.json` (modèle : `templates/ownership.example.json`), à
tenir cohérente avec ce tableau.

## Règles d'organisation
- Un dossier = un propriétaire ; deux agents ne modifient jamais le même fichier en même temps (parallèle seulement
  sur des fichiers disjoints). Chacun écrit les tests de son propre code.
- Brief de délégation (un agent ne voit pas la conversation) : objectif, contexte (docs, fichiers et plages de
  lignes, décisions), périmètre, critère de fin, compte rendu attendu.
- Compte rendu : fait · fichiers · vérifié (commande -> résultat réel) · reste et risques. Jamais « terminé » sans
  résultat de commande ; une vérification non lancée ou en échec est écrite telle quelle.
- Fin de chantier : `qa-reviewer` (tests complets), puis `documentaliste` si un comportement, un contrat ou une
  commande a changé.
- Mécanique -> haiku, code -> sonnet, conception et exactitude métier -> opus.
- Tâche sans compétence correspondante : ajouter la compétence à l'agent concerné, ou créer l'agent si le domaine
  n'est pas couvert (brief à `documentaliste`) ; jamais `general-purpose`.

## Règles transverses business game
- Connaissances vraies et sourcées : chaque notion et chaque chiffre est un fait sourcé, un choix de calibrage ou une
  valeur illustrative, et le dit ; sans source, « à vérifier ». Aucune source inventée.
- Simulation calculée par un moteur déterministe (fonctions pures, hasard dérivé de la graine) à partir d'une spec
  validée ; une partie en pilote automatique doit la terminer. Aucune règle de jeu dans l'API ni dans le client.
- PNJ IA : ils ne disent que les faits calculés par le moteur (briefings), en une ou deux phrases, avec une mémoire
  bornée ; repli déterministe si le modèle ne répond pas.
- IA : le code appelle une tâche du catalogue, jamais un modèle ; une sortie générée est un brouillon que le
  formateur relit avant publication.
- Débrief pédagogique appuyé sur des événements réellement survenus ; un jeu non testé est un prototype.
- Rôles joueur, formateur, admin vérifiés côté serveur.
- Aucun secret dans le dépôt ; pas de commit ni de push sans demande explicite de l'utilisateur.
- Coût : fichier de plus de 300 lignes = Grep puis Read avec plage ; ne jamais relire un fichier non modifié ;
  compte rendu de 15 lignes au plus.

## Documentation
À lire en premier : <cahier des attentes>, <point de reprise>. Ensuite : ADR, architecture, game design, IA.
