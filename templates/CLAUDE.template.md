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
- Tests : `<commande>` ; mesure de l'équipe : skill `mesure-equipe` du plugin `team-for-every-project` (par défaut)
  ou `<commande propre au projet>`

## Agents : équipe et organisation
Orchestrateur, architecte, qa-reviewer, documentaliste et coach-equipe viennent du plugin `team-for-every-project`,
les agents métier du plugin `team-for-business-game-generation` ; installés en plugin, leurs noms sont préfixés
(`team-for-every-project:orchestrateur`). La session principale peut tourner en orchestrateur (clé `agent` de
`.claude/settings.json` du projet, ou `claude --agent team-for-every-project:orchestrateur`).

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

Modèles : la colonne donne le palier par défaut. Une surcharge mesurée (`/team-for-every-project:choisir-modeles`)
s'écrit ici : `Modèles : <agent> <alias> <effort>, ...` ; elle prime sur le fichier de l'agent.

Carte de propriété lisible par machine : `.claude/ownership.json` (modèle : `templates/ownership.example.json`), à
tenir cohérente avec ce tableau.

## Règles d'organisation
- Un dossier = un propriétaire ; deux agents ne modifient jamais le même fichier en même temps (parallèle seulement
  sur des fichiers disjoints). Chacun écrit les tests de son propre code.
- Brief en cinq rubriques, choix du modèle, preuve, pause et reprise : skill `regles-equipe` (team-for-every-project).
- Compte rendu : fait · fichiers · vérifié (commande -> résultat réel) · reste et risques. Jamais « terminé » sans
  résultat de commande ; une vérification non lancée ou en échec est écrite telle quelle.
- Une demande sur les règles, les chiffres, les sources ou le débrief passe d'abord par `game-designer`.
- Correctif de bug : reproduire d'abord, corriger la cause par le plus petit changement prouvé, citer la reproduction
  avant (échec) et après (succès).
- Fin de chantier : `qa-reviewer` (tests complets, points de relecture ci-dessous), puis `documentaliste` si un
  comportement, un contrat ou une commande a changé.

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
- Bus d'événements (s'il existe) : commandes, événements et traitements asynchrones, jamais le temps réel du client.
- Aucun secret dans le dépôt ; pas de commit ni de push sans demande explicite de l'utilisateur.
- Coût : fichier de plus de 300 lignes = Grep puis Read avec plage ; ne jamais relire un fichier non modifié ;
  compte rendu de 15 lignes au plus.

## Points de relecture business game (qa-reviewer)
- Règle de jeu ou chiffre métier hors du moteur (API, client) ; hasard ou horloge dans le moteur.
- Appel IA hors du catalogue de tâches, budget de tokens non borné, message de joueur capable de changer le rôle d'un
  PNJ (injection).
- Route ou canal temps réel sans contrôle de rôle côté serveur.
- Fait ou chiffre pédagogique sans source, ou présenté comme sourcé sans l'être : Bloquant, renvoyé à `game-designer`.
- Chantier touchant le jeu, l'API, les PNJ ou le client : parcours de bout en bout sur la pile réelle (générer un jeu,
  lobby, actions, dialogue, tours, débrief), avec des comptes de test jetables, supprimés ensuite.

## Documentation
À lire en premier : <cahier des attentes>, <point de reprise>. Ensuite : ADR, architecture, game design, IA.
