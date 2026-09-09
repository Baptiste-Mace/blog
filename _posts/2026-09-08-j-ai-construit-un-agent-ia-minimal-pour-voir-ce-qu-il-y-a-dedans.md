---
layout: post
title: "J'ai construit un agent IA minimal pour voir ce qu'il y a dedans"
date: 2026-09-08
author: Baptiste Macé
categories: [IA, architecture, technologie]
tags: [agents, orchestration, LLM, MCP, EPD, sobriété]
---

# **J'ai construit un agent IA minimal pour voir ce qu'il y a dedans**

J'ai déjà écrit que les agents IA ne sont pas une révolution du *runtime*. Le mot est
partout, chaque framework promet un nouveau paradigme, et je reste sceptique sur le
niveau où on place la nouveauté.

Plutôt que d'en débattre encore, j'ai voulu vérifier par la pratique. J'ai écrit un
agent orchestrateur minimal : quelques centaines de lignes, la bibliothèque standard
et rien d'autre, capable de tourner avec un modèle local. Le but n'était pas la
performance. C'était de pouvoir tout lire, ligne à ligne, et voir ce qui se passe
réellement.

## **Ce qu'il reste quand on enlève le vernis**

Une fois le décor retiré, l'agent tient dans cette boucle :

```
plan = planifier(objectif)
pour chaque tâche du plan :
    si budget de jetons dépassé : arrêter
    outil    = choisir_outil(tâche, contexte)
    résultat = exécuter(tâche, contexte, outil)
    contexte = enrichir(contexte, résultat)
```

Est-ce un agent ? Oui. Est-ce autre chose qu'un workflow un peu souple, dont certaines
décisions sont déléguées à un modèle ? Pas vraiment. La planification est un
aller-retour avec le LLM. L'exécution en est un autre. Entre les deux, il n'y a pas
d'intention cachée, juste un contexte que l'on transporte et que l'on complète.

## **Le contexte fait le travail, pas le modèle**

La partie qui m'a le plus intéressé n'est pas le modèle, c'est le contexte. L'agent
tient un carnet : le plan, les résultats déjà obtenus, les fichiers produits. Chaque
tâche relit ce carnet et y ajoute sa contribution.

C'est exactement la logique que je défends avec l'*Empirical Product Development*, celle
de mon talk *SDD vs EPD* à Agile en Seine : on ne donne pas au LLM un objectif géant à
résoudre d'un coup, on lui confie des tâches atomiques, et la réalisation de la tâche
*n* révèle les contraintes réelles de la tâche *n+1*. Le raisonnement n'est pas *dans*
le modèle. Il est dans le découpage, dans l'ordre des étapes, et dans les données qu'on
lui remet à chaque tour.

Quand l'agent se trompe, ce n'est presque jamais parce que le modèle est faible. C'est
parce que le contexte était incomplet. J'ai fait l'erreur moi-même : au début, l'étape
qui choisissait l'outil ne voyait pas le résultat des étapes précédentes, et l'agent
copiait un texte fantôme au lieu du vrai. Le correctif n'était pas un meilleur modèle,
c'était un meilleur passage de contexte.

## **La condition d'arrêt n'est pas un détail**

Mon agent s'arrête sur deux conditions : le plan est terminé, ou le budget de jetons
est atteint. Chaque appel au modèle incrémente ce compteur.

On peut voir ça comme une limite technique. Je préfère le voir comme une décision de
conception. Un agent qui boucle sans budget explicite, c'est un coût qui grossit sans
qu'on le regarde, et une facture, matérielle et financière, qu'on découvre après. Fixer
un budget de jetons, c'est se donner les moyens de mesurer, donc de décider. Le même
réflexe que le FinOps, appliqué à l'IA.

## **Local par défaut, remplaçable partout**

Le modèle par défaut tourne en local, via Ollama. Pas par posture : dépendre d'un cloud
propriétaire simplement pour comprendre le fonctionnement d'un agent n'aurait pas de
sens. J'ai aussi écrit un petit serveur d'outils au format MCP, qui donne à l'agent des
capacités concrètes sur ma machine : notifier, lire un texte à voix haute, manipuler le
presse-papier. L'agent choisit lui-même l'outil, avant chaque tâche et entre chaque
étape.

L'important n'est pas la liste des outils, c'est que tout soit interchangeable :
le fournisseur, le modèle, les outils. On garde la main, on peut revenir en arrière.

## **Deux prolongements, dans le même esprit**

J'ai ajouté deux capacités depuis, en gardant la règle : le minimum pour comprendre, pas
pour impressionner.

La première : une étape peut appeler un sous-agent. Avant chaque étape, l'agent se
demande si elle est vraiment atomique ou si elle cache un objectif à part entière. Dans
le second cas, il relance *la même boucle* sur cette étape, avec son propre budget de
jetons et une profondeur bornée pour éviter la récursion sans fin. Le sous-agent rend un
résultat unique au parent, et les fichiers qu'il produit remontent dans le contexte
commun. Rien de spectaculaire : c'est la boucle appelée depuis l'intérieur d'une étape.
L'intérêt n'est pas de créer une armée d'agents, c'est de découper encore, en gardant
chaque budget lisible.

La seconde : le *méta-prompt* se compose au moment de l'appel. Chaque prompt vit toujours
dans son fichier, mais avant une exécution l'agent peut y ajouter une spécialisation
ciblée pour l'étape du moment : un rôle, des points de vigilance, un format attendu. Si
rien n'est utile, on garde le prompt de base. Toujours la même idée : le travail se fait
dans le contexte, alors autant le soigner jusque dans l'instruction qu'on donne au
modèle.

Ces deux ajouts ne changent pas la nature de l'agent. Ils la confirment : tout reste une
question de découpage et de contexte.

## **Pourquoi se donner cette peine**

Je ne prétends pas livrer la bonne façon d'écrire un agent. Ce projet est volontairement
pauvre en fonctionnalités, et c'est ce qui le rend lisible. Ce que j'en retiens tient en
une phrase : on comprend mieux ce qu'on a démonté soi-même. Et un agent dont on comprend
la boucle, on sait aussi quand ne pas s'en servir.

## **Pour aller voir**

- Le code : github.com/Baptiste-Mace/orchestrator-agent-demo
- Mon talk *SDD vs EPD* à Agile en Seine : https://www.agileenseine.com/programme/ia-agilite-le-spec-driven-development-ou-le-retour-du-cycle-en-v-et-comment-sen-sortir/
- Mon article sur le runtime des agents : *Les agents IA ne sont pas une révolution du runtime*
- Mon article sur la méthode : *Empirical Product Development*
- Ollama (modèles locaux) : ollama.com
- Le protocole MCP : modelcontextprotocol.io
