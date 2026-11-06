---
name: moteur-business-game
description: Méthode pour coder le moteur d'un business game généré par spec. Fonctions pures et déterministes, spec validée, pilote automatique, calibrage, PNJ qui disent des faits calculés, repli sans IA.
---

# Moteur de business game : méthode

Règles tirées de la pratique de l'équipe de développement dont est issu ce dépôt. À appliquer quand on code ou relit le
moteur de simulation, la génération d'un jeu ou les PNJ.

## 1. Moteur pur et déterministe
- Fonctions pures : état (JSON) en entrée, nouvel état en sortie (copie profonde).
- Hasard seulement par un générateur dérivé de la graine de la spec et du numéro de tour.
- Ni entrée-sortie, ni horloge, ni `hash()` de chaîne (variable d'un processus à l'autre en Python).
- Toute règle et tout chiffre vivent dans le moteur, pas dans l'API ni dans le client.

## 2. Spec et actions
- La spec est validée par un modèle de validation (références croisées) avant tout usage.
- Toute action déclarée dans la spec a un handler dans le moteur et un test chiffré. Une action refusée lève une
  erreur de règle avec un message affichable.

## 3. Garde-fou du pilote automatique
- Une spec n'est valide que si une partie en pilote automatique la termine : ne contourne jamais ce contrôle.
- Génération : même graine, même jeu ; chaque secteur et chaque difficulté sont testés.

## 4. Calibrage
À chaque changement de chiffre ou de règle : noter les valeurs avant et après, rejouer le pilote automatique sur
chaque difficulté. Attendu : le pilote atteint les objectifs au niveau facile, peine au niveau difficile ; un joueur
passif échoue. Un objectif calé sur la partie de référence ne descend pas sous la valeur de départ.

## 5. Chiffres et sources
Règles métier, chiffres et sources viennent du concepteur pédagogique. Sans sa validation, signaler au lieu de
trancher. Toute valeur est un fait sourcé, un choix de calibrage ou une valeur illustrative, jamais présentée
autrement.

## 6. PNJ
- Les faits essentiels sont calculés par le moteur (briefings courts) ; le modèle IA ne fait que les dire, en une ou
  deux phrases, avec le caractère du PNJ et une mémoire bornée des échanges.
- Le code appelle une tâche IA du catalogue, jamais un modèle ; aucun appel IA hors du module des PNJ.
- Repli : modèle indisponible, trop lent ou sortie vide, le PNJ répond quand même de façon déterministe (préfixe de
  caractère et fait le plus important, deux phrases au plus), réponse marquée `fallback`. Seules les erreurs du
  modèle sont attrapées, jamais les bugs ; un test par cause.

## 7. Piège connu
Une valeur YAML contenant « : » doit être entre guillemets.
