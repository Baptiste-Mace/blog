---
layout: post
title: "GitHub Copilot passe aux AI Credits : quand l'ingénierie logicielle rejoint le FinOps et le Green IT"
date: 2026-06-01
author: Baptiste Macé
categories: [IA, ingénierie, Green IT]
tags: [GitHub Copilot, AI Credits, FinOps, Green IT, LLM, sobriété numérique, contexte]
---

# **GitHub Copilot passe aux AI Credits : quand l'ingénierie logicielle rejoint le FinOps et le Green IT**

💡 GitHub Copilot change de modèle économique. Et c'est probablement une excellente nouvelle pour les ingénieurs.

À partir du 1er juin 2026, GitHub abandonne les *Premium Requests* au profit d'un système basé sur les **AI Credits**. Le changement paraît anodin sur la facture. Il l'est beaucoup moins dans ce qu'il révèle.

Dit autrement : on ne paie plus une requête, on paie ce qu'elle consomme réellement.

* le nombre de tokens ;
* le modèle utilisé ;
* la quantité de contexte envoyée ;
* la taille de la réponse générée.

Et ce déplacement, de la requête vers la consommation réelle, va remettre quelques fondamentaux de l'ingénierie logicielle au centre du jeu.

## **La fin de l'illusion de l'IA "gratuite"**

Jusqu'à présent, l'IA donnait l'impression d'être une ressource presque infinie. Tant qu'on payait au forfait, le coût marginal d'une requête de plus était invisible.

Un repo complet dans le prompt ? Pas grave.
50 fichiers de contexte ? Pas grave.
Une demande floue qui génère trois pages de réponse ? Pas grave.

Demain, tout cela aura un coût visible. Et quand on regarde les grilles tarifaires, l'écart entre modèles est énorme : certains coûtent quelques dizaines de crédits par million de tokens, d'autres plusieurs centaines. Même constat côté génération : selon le modèle choisi, le coût peut être multiplié par plus de dix.

Le réflexe naturel serait de conclure : « il faut choisir le bon modèle ». C'est vrai, mais c'est secondaire.

## **Le vrai facteur de coût n'est pas le modèle, c'est le contexte**

Le prix du modèle est une variable. Le volume de contexte qu'on lui envoie en est une autre, et c'est elle qui fait la différence à l'échelle d'une équipe.

Parce qu'un mauvais découpage du problème coûte désormais de l'argent.

Un développeur qui balance toute la codebase à un agent parce qu'il ne sait pas isoler son sujet va consommer davantage. À l'inverse, un développeur qui sait :

* identifier la zone réellement impactée ;
* réduire le contexte au strict nécessaire ;
* découper un problème complexe en sous-problèmes ;
* choisir le bon modèle pour la bonne tâche ;

consommera moins. Et obtiendra souvent de meilleurs résultats, car un contexte resserré, c'est aussi moins de bruit et moins d'hallucinations.

On retrouve ici exactement la logique des **tâches atomiques** et du contexte minimal défendue dans mon article *Empirical Product Development* : l'IA n'est performante que lorsqu'on la contraint. Ce qui était hier une bonne pratique de qualité devient aujourd'hui une bonne pratique de coût.

## **Le deuxième sujet, dont on parle beaucoup moins : l'énergie**

Il y a un second angle, plus discret : l'impact environnemental.

Chaque token traité mobilise des ressources de calcul. Chaque contexte inutile consomme du GPU. Chaque réponse générée mobilise de l'énergie qui n'apporte parfois aucune valeur.

Depuis des années, le **Green IT** nous enseigne qu'il faut optimiser la valeur produite par watt consommé. Nous avons appris à :

* dimensionner correctement nos infrastructures ;
* éviter les calculs inutiles ;
* limiter les transferts de données ;
* réduire les surconsommations cloud.

Avec l'IA générative, le problème réapparaît sous une autre forme. Envoyer 100 000 tokens quand 10 000 suffisent, c'est l'équivalent d'un serveur surdimensionné qui tourne à vide.

## **L'effet rebond, déjà à l'œuvre**

L'[effet rebond](https://fr.wikipedia.org/wiki/Effet_rebond_(%C3%A9conomie)) est d'ailleurs déjà visible. Parce que l'IA coûte peu à l'utilisateur final, nous avons tendance à lui déléguer davantage de tâches, à envoyer plus de contexte et à générer toujours plus de contenu.

C'est le vieux **paradoxe de Jevons** : améliorer l'efficacité d'une ressource n'en réduit pas la consommation, cela l'augmente. Comme souvent en informatique, lorsque le coût marginal semble disparaître, les usages explosent.

Et c'est précisément ce que ce nouveau modèle tarifaire va rendre de nouveau visible.

## **L'ingénierie logicielle rejoint le FinOps — et le Green IT**

C'est finalement assez amusant.

Pendant des années, on a expliqué que les compétences d'abstraction, de conception et de découpage étaient essentielles pour produire du logiciel de qualité. Avec l'arrivée des AI Credits, elles deviennent aussi des **compétences d'optimisation budgétaire**. Et peut-être même des **compétences de sobriété numérique**.

L'ingénierie logicielle rejoint le **FinOps**. Et rejoint aussi le **Green IT**. Les trois disent au fond la même chose : produire la bonne valeur avec le minimum de ressources.

Je soupçonne que beaucoup d'entreprises vont découvrir dans les prochains mois que le principal facteur de coût de l'IA n'est pas le modèle. C'est la manière dont les humains l'utilisent.

## **Conclusion**

Les meilleurs ingénieurs ne seront probablement pas ceux qui utilisent le plus l'IA. Ce seront ceux qui savent lui donner **le minimum de contexte nécessaire pour obtenir la bonne réponse**.

Le passage aux AI Credits ne change pas la nature du métier. Il rend simplement visible, sur la facture comme sur la consommation d'énergie, une vérité que l'ingénierie répète depuis toujours : savoir cadrer un problème vaut mieux que savoir tout envoyer.
