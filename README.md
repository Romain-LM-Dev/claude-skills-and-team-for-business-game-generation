<div align="center">

🇫🇷 Français | 🇬🇧 [English](README.en.md)

</div>

# claude-skills-and-team-for-business-game-generation

Une équipe d'agents Claude Code pour les projets de génération de business games : un formateur choisit des
paramètres, un moteur déterministe assemble et calcule la simulation, des PNJ IA disent les faits calculés. L'équipe est
issue du projet G-mix, généralisée pour être réutilisée dans tout projet du même type, quelle que soit la stack.
Principe : peu d'agents, des descriptions courtes, le modèle adapté à la tâche (haiku pour le mécanique, sonnet pour
le code, opus pour la conception et l'exactitude métier), des briefs complets.

L'équipe générique (orchestrateur, architecte, développeur, qa-reviewer, documentaliste, coach-equipe, skills
`regles-equipe` et `mesure-equipe`) vient du dépôt frère
[claude-skills-and-team-for-every-project](https://github.com/Romain-LM-Dev/claude-skills-and-team-for-every-project),
installé automatiquement comme dépendance. Ce dépôt n'apporte que les agents et skills propres au business game ; les
règles de relecture business game sont dans le modèle de CLAUDE.md, lu par tous les agents.

## Contenu

Agents du noyau (business game) :

| Agent | Rôle | Modèle |
|---|---|---|
| `game-designer` | justesse métier, chiffres, sources, objectifs pédagogiques, PNJ, débrief | opus |
| `moteur-simulation` | moteur déterministe, génération depuis une spec, logique et repli des PNJ | sonnet |
| `ia-runtime` | tâches IA, prompts des PNJ, budgets, évaluations des dialogues | sonnet |
| `rag-knowledge` | ingestion, recherche, corpus, sources et droits | sonnet |

Agents optionnels, selon la stack (Godot, Unity, FastAPI, Kafka, etc. ne sont que des exemples) :

| Agent | Rôle | Modèle |
|---|---|---|
| `client-dev` | client 3D ou front web, HUD, panneaux par rôle | sonnet |
| `backend-api` | API, rôles, base, migrations | sonnet |
| `pipeline-evenements` | bus d'événements et workers asynchrones | sonnet |
| `assets` | catalogue d'assets, licences, couverture | sonnet |
| `devops` | conteneurs, scripts, environnement | haiku |

Skills :
- `moteur-business-game`, méthode du moteur (fonctions pures, spec validée, pilote automatique, calibrage, PNJ) ;
- `business-game-designer`, conception de business games et de simulations de formation (préchargée par game-designer).

Modèles à copier dans un projet, dans `templates/` : `CLAUDE.template.md` (tableau des agents, règles d'organisation,
brief, compte rendu, règles et points de relecture business game) et `ownership.example.json` (carte de propriété).

Pour l'exploration en lecture seule, utiliser l'agent intégré `Explore`.

## Installation dans Claude Code

Plugin, une fois le dépôt publié, dans une session :

```
/plugin marketplace add Romain-LM-Dev/claude-skills-and-team-for-every-project
/plugin marketplace add Romain-LM-Dev/claude-skills-and-team-for-business-game-generation
/plugin install team-for-business-game-generation@skills-and-team-business-game
```

La dépendance `team-for-every-project@skills-and-team` s'installe avec lui, ou réutilise la copie déjà installée.
Sans la première marketplace, le plugin ne se charge pas (« Dependency ... is not installed ») : l'ajouter suffit, la
dépendance s'installe alors d'elle-même.

Essai local sans installer : `claude --plugin-dir <ce-dépôt> --plugin-dir <dépôt-every-project>`. Les agents
s'appellent `team-for-business-game-generation:game-designer`, `team-for-every-project:orchestrateur`, etc. Les
skills préchargées par un agent ne s'appliquent que quand l'agent est lancé en sous-agent par l'orchestrateur.

Copie manuelle :
1. copier les agents voulus de `agents/` de ce dépôt et du dépôt every-project dans `.claude/agents/` du projet (ou
   `~/.claude/agents/`) ;
2. copier les dossiers de `skills/` des deux dépôts dans `.claude/skills/` ;
3. copier `templates/CLAUDE.template.md` en `CLAUDE.md` à la racine du projet, le remplir, et supprimer les lignes des
   agents non installés ;
4. copier `templates/ownership.example.json` en `.claude/ownership.json` et adapter les motifs de fichiers.

Les agents ne contiennent ni chemin, ni port, ni commande : la stack et les dossiers sont définis dans le `CLAUDE.md`
du projet, auquel ils renvoient.

## Activer l'orchestrateur

Il n'est pas forcé : il coûte plus qu'il n'économise sur une petite tâche. Pour un projet à plusieurs domaines :

- une session : `claude --agent team-for-every-project:orchestrateur` ;
- en permanence pour un projet : dans `.claude/settings.json` du projet,
  `{ "agent": "team-for-every-project:orchestrateur" }`.

## Principes business game

- **Connaissances vraies et sourcées** : chaque notion et chaque chiffre est un fait sourcé, un choix de calibrage ou
  une valeur illustrative, et le dit ; sans source, « à vérifier ».
- **Moteur déterministe** : la simulation est calculée par des fonctions pures à partir d'une spec validée, avec un
  hasard dérivé de la graine ; une partie en pilote automatique doit la terminer.
- **PNJ IA factuels** : ils ne disent que les faits calculés par le moteur, avec une mémoire bornée et un repli
  déterministe.
- **Tâche IA, jamais un modèle** : le code appelle une tâche d'un catalogue ; le modèle choisit, dit ou classe, le
  moteur calcule.
- **Débrief pédagogique** appuyé sur des événements réellement survenus ; un jeu non testé reste un prototype.
- **Rôles** joueur, formateur (ses lobbies) et admin, vérifiés côté serveur.

## Utiliser la skill dans claude.ai

Les agents ne tournent pas dans le chat claude.ai ; seules les skills s'y importent, par ZIP.

```powershell
.\scripts\package-skills.ps1
```

Le script écrit un ZIP par skill dans `dist/` (ignoré par git), après avoir vérifié `name` (64 caractères au plus,
identique au dossier) et `description` (200 au plus). Puis, dans claude.ai : Customize, Skills, Upload.

## Validation

```
claude plugin validate .
claude plugin validate . --strict
```

La CI GitHub (`.github/workflows/validate.yml`) lance à chaque push la validation stricte et le packaging des skills
(échec si une skill est ignorée).

Coût mesuré le 2026-11-10 (`claude plugin details`) : environ 1 080 tokens permanents par session, plus environ 990
pour `team-for-every-project`.

## Licence

Licence source-available non commerciale : voir `LICENSE.md`. Usage commercial sur autorisation écrite du titulaire.
