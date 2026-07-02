---
layout: post
title: "Les agents IA ne sont pas une révolution du runtime"
date: 2026-07-02
author: Baptiste Macé
categories: [IA, architecture, technologie]
tags: [agents, workflows, LLM, orchestration, runtime]
---

# **Les agents IA ne sont pas une révolution du runtime**

Depuis quelques mois, le mot **agent** est partout. Chaque outil, chaque framework, chaque démo promet un nouveau paradigme logiciel qui va enterrer les workflows.

En tant qu'ingénieur, je suis de moins en moins convaincu.

Non pas que les agents n'apportent rien, ils ouvrent de vrais cas d'usage. Mais parce que, du point de vue de l'architecture, la différence est presque toujours présentée au mauvais niveau. Et cette confusion nous fait perdre du temps sur un faux débat.

## **Le faux débat : « suivre des règles » vs « réfléchir »**

La comparaison classique ressemble à ça :

- Un **workflow** est une suite d'étapes définies à l'avance. Un email arrive, si le sujet contient « facture » alors on enregistre le PDF et on notifie. Le chemin est connu d'avance.
- Un **agent** reçoit un objectif plutôt qu'une liste d'étapes. « Traite cette demande client. » Il lit, interprète, choisit ses outils, se corrige, puis répond.

L'analogie habituelle : le workflow est une recette, l'agent est un chef cuisinier.

C'est pédagogique, mais c'est une lecture orientée *produit*, pas *architecture*. Elle décrit ce que l'utilisateur perçoit, pas ce que la machine exécute. Et quand on descend au niveau du **runtime**, l'histoire devient beaucoup plus banale.

## **Ce qu'un moteur de workflow sait déjà faire**

On réduit souvent le workflow à un `if / then / else` figé. C'est faux depuis longtemps. Un orchestrateur moderne, Temporal, n8n, Camunda, ou simplement du code bien structuré, sait déjà :

- exécuter des boucles ;
- gérer des conditions complexes ;
- lancer des tâches en parallèle ;
- attendre des événements ;
- faire des retries, des compensations, des timeouts ;
- maintenir un état persistant ;
- itérer jusqu'à atteindre un critère.

L'itération et l'adaptation ne sont donc pas des propriétés exclusives des agents. Prenons un exemple que tout le monde qualifierait spontanément d'« agentique » :

```
Tant que les tests échouent :
    générer une correction
    exécuter les tests
    analyser le résultat
```

Est-ce un workflow ? Oui.
Est-ce une boucle d'amélioration qui observe un résultat et recommence ? Oui aussi.

Ces boucles ne transforment pas magiquement le système en agent. Elles montrent juste que « boucler jusqu'à un objectif » fait partie du vocabulaire des workflows depuis des années.

## **Alors qu'apporte réellement un runtime d'agent ?**

On lit souvent que la différence tiendrait à ceci : *« la logique de décision passe du développeur au modèle »*.

C'est faux.

Le développeur ne cède pas sa logique de décision. Il continue de définir le cadre : les objectifs, les outils disponibles, les garde-fous, les critères d'arrêt. Ce qui change n'est pas *qui décide*, mais **la nature de ce sur quoi on décide.**

Ce que le LLM apporte au runtime, c'est la **capacité à opérer sur des entrées et des états que personne n'a normalisés à l'avance.**

Concrètement, un runtime d'agent permet de :

- **prendre en compte des états non déterministes**, là où un workflow classique exige une transition définie pour chaque cas prévu ;
- **choisir l'étape suivante à partir de données non normées**, un message brut, ambigu, incomplet, sans schéma, plutôt qu'à partir d'un champ typé et validé ;
- **produire une réponse plus complète et « mouvante »**, adaptée au contexte de l'utilisateur, reformulée à la volée, plutôt qu'un template figé.

Dans un workflow, tout ce qui n'a pas été anticipé casse la boucle ou déclenche une nouvelle règle à écrire. Dans un agent, le modèle absorbe cette variabilité : il interprète l'imprévu et façonne une sortie qui n'était pas gabarisée d'avance.

Ce n'est donc pas la décision qu'on déplace. C'est le **type de données et d'états** que le runtime devient capable de digérer.

## **Mais cette souplesse a un coût, et il est élevé**

Chaque décision déléguée au modèle, c'est des tokens, de la latence, de l'imprévisibilité et une auditabilité plus difficile.

Et surtout : beaucoup de cas que l'on cherche à traiter « par agent » sont en réalité **normalisables.**

Si l'entrée peut être un formulaire plutôt qu'un prompt, un champ typé plutôt qu'une phrase libre, alors le workflow gagne sur toute la ligne : plus rapide, moins cher, plus prévisible. Faire raisonner un LLM sur une donnée qu'on aurait pu structurer, c'est payer le prix de l'ambiguïté pour un problème qui n'en avait pas.

Et ce coût n'est pas qu'une ligne sur une facture cloud. Un appel LLM, c'est une inférence sur un accélérateur (GPU ou TPU) : de l'énergie, de l'eau de refroidissement, du matériel amorti. Un `if` déterministe, lui, coûte quelques microjoules sur un CPU banal. Entre les deux, plusieurs ordres de grandeur. Router chaque décision par un modèle quand un champ typé suffisait, ce n'est pas seulement payer trop cher : c'est dépenser de l'énergie pour recréer une structure qu'on avait déjà.

La vraie question n'est donc pas *« agent ou workflow ? »*, mais :

> Ce cas est-il réellement non-normé, ou est-ce qu'on refuse simplement de le structurer ?

## **Regardez les frameworks, pas les slides**

Cette lecture se vérifie dès qu'on ouvre le capot. LangGraph, Semantic Kernel, AutoGen, l'OpenAI Agents SDK : tous reposent sur une **boucle d'orchestration, un graphe d'états ou une machine à états.**

La seule différence, c'est que certaines arêtes du graphe ne sont plus codées en dur : elles s'appuient sur le modèle à l'exécution, via une boucle *Observe → Reason → Act*, un plan modifiable, une sélection dynamique d'outils et un peu de mémoire de travail.

D'où ma synthèse :

> Un runtime d'agent est souvent un runtime de workflow dont une partie de la logique de contrôle utilise un LLM. C'est davantage une évolution de l'orchestration qu'une rupture architecturale.

C'est probablement pour ça que beaucoup d'ingénieurs restent sceptiques face au terme « agent » : techniquement, ce n'est pas un nouveau paradigme d'exécution, mais une nouvelle façon de décider des transitions dans un moteur d'orchestration.

Et ce n'est pas une mode d'implémentation qui changera demain. C'est une contrainte de **substrat** : un LLM est un générateur de texte. Il ne peut ni exécuter, ni appeler, ni décider *par lui-même*, il ne peut que produire une sortie qu'un système déterministe transformera en action. Tant que le substrat reste la génération de tokens, le modèle sera toujours un **nœud** dans une orchestration, jamais l'orchestrateur.

## **Et la « version forte » de l'agent ?**

L'objection sérieuse n'est pas « l'agent réfléchit ». C'est celle-ci : un vrai agent **planifie**, **s'auto-évalue** et **découvre des outils** non prévus. Là, on tiendrait un primitif d'exécution nouveau.

Regardons les trois, une par une.

**La planification autonome n'est pas une exécution. C'est du texte.**
Un LLM ne possède qu'une seule primitive : produire le token suivant. Quand un agent « planifie », il génère un texte qui *ressemble* à un plan, puis on le réinjecte. Ce plan n'a **aucun pouvoir causal** tant qu'un orchestrateur, déterministe, codé par un humain, ne le parse pas et n'appelle pas un outil. La planification ne vit donc pas dans le modèle. Elle vit dans la boucle qui interprète le texte généré. Le modèle produit du texte ; le workflow, lui, agit.

**La « découverte d'outils » est un mythe opérationnel.**
En production, on fait exactement l'inverse. On **réduit** le nombre d'outils exposés, pour alléger le contexte, baisser le coût et fiabiliser le routage. Le développeur sélectionne en amont un sous-ensemble d'outils « probablement utiles ». L'agent ne découvre rien : il **choisit dans un catalogue fermé**, défini par l'humain. L'espace d'action reste borné par le dev, précisément comme les transitions d'un workflow. La seule différence, c'est *qui* fait la sélection finale dans cet ensemble.

**L'auto-réflexion est un retry conditionnel déguisé.**
Reflection, critique, replanning : encore de la génération de texte, évaluée par un critère ou par un second appel au modèle, le tout piloté par une boucle écrite à la main. « Tant que la critique n'est pas satisfaite, regénérer », on a déjà vu ce motif plus haut. Ce n'est pas un nouveau paradigme d'exécution. C'est une boucle d'amélioration dont le critère d'arrêt est flou.

Autrement dit, même dans sa version forte, l'agent ne sort pas du cadre. Ses trois « super-pouvoirs » se ramènent à :

1. générer du texte ;
2. choisir dans un ensemble d'outils **fermé et fourni par le développeur** ;
3. reboucler.

Trois primitives que le workflow possède déjà. La seule nouveauté, c'est que le **texte généré sert d'entrée de contrôle**.

## **La frontière est un continuum**

Soyons honnêtes : il n'y a pas de ligne nette. La plupart des systèmes qualifiés d'« agents » aujourd'hui sont des workflows avec des boucles, des outils et un LLM qui prend certaines décisions. La différence est souvent une question de **degré d'autonomie**, pas de nature.

Les agents deviennent réellement intéressants quand :

- le problème est peu structuré ;
- les étapes ne peuvent pas être définies à l'avance ;
- plusieurs outils doivent être combinés de façon adaptative ;
- il faut raisonner et s'adapter à des situations variées.

Partout ailleurs, un **workflow classique**, où toutes les transitions sont déterministes et codées explicitement, reste plus fiable, moins coûteux et plus facile à maintenir.

## **Ce que ça change pour nos décisions d'architecture**

Sortir du débat caricatural « workflows rigides vs agents intelligents » nous ramène aux vraies questions :

- Quelles décisions méritent réellement d'être prises par un modèle ?
- Lesquelles doivent rester déterministes ?
- Quel est le coût en tokens de cette autonomie ?
- Quel niveau de prédictibilité, d'auditabilité et de gouvernance attend-on ?

## **La sobriété est un argument d'architecture**

On oppose rarement « agent » et « sobriété ». On devrait.

Chaque décision confiée au modèle consomme une inférence. Multipliée par le volume d'un processus métier, des milliers, des millions d'exécutions, cette inférence devient une ligne significative du bilan énergétique du système. Le workflow déterministe, lui, tend vers le coût marginal nul.

Le réflexe « on met un agent » revient souvent à remplacer une logique gratuite et prévisible par une logique coûteuse et probabiliste, pour traiter des cas qu'un formulaire aurait résolus sans une seule inférence.

La question de sobriété rejoint alors exactement la question d'architecture :

> Chaque nœud LLM doit *gagner sa place*. Pas parce que c'est à la mode, mais parce que l'ambiguïté qu'il traite ne pouvait pas être structurée en amont.

Un bon système n'est pas celui qui « pense » le plus. C'est celui qui réserve le calcul coûteux aux seuls endroits où il crée une valeur qu'aucune structure déterministe ne pouvait produire. Partout ailleurs, le déterminisme n'est pas une contrainte : c'est un choix d'efficience, technique, économique et environnementale.

## **En résumé**

L'avenir n'est pas dans le remplacement des workflows par des agents. Il est dans des **workflows classiques qui intègrent des briques LLM là où c'est utile** : analyser une question, interpréter une entrée non structurée, formuler une réponse, arbitrer un cas ambigu.

Le LLM n'est pas *le* système. C'est un composant, appelé à un endroit précis de l'orchestration, avec une entrée et une sortie cadrées, au milieu d'étapes déterministes qui, elles, restent prévisibles, testables et bon marché.

Ce n'est pas « agent *vs* workflow ». C'est un workflow qui, à quelques nœuds bien choisis, utilise un modèle.

Moins de magie.
Plus d'ingénierie.
Et moins de watts pour le même résultat.
