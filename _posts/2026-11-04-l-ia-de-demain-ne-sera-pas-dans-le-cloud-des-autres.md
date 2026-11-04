---
layout: post
title: "L'IA de demain ne sera pas dans le cloud des autres"
date: 2026-11-04
author: Baptiste Macé
categories: [IA, souveraineté, cloud-native]
tags: [souveraineté numérique, IA, LLM, Ollama, DeepSeek, Kubernetes, KEDA, HPA, open source, sobriété, kata]
---

*Un kata pour apprendre à garder son IA dans l'enceinte de l'entreprise*

Depuis quelques mois, la même question revient dans presque toutes mes discussions, avec des équipes, des clients, des collègues : « Peut-on utiliser l'IA sans envoyer nos données ailleurs ? » Souvent suivie d'une autre, plus inquiète : « Et est-ce qu'on saurait le faire nous-mêmes ? »

Je n'avais pas de réponse toute faite. Alors j'ai eu envie de tester, par moi-même, sur du code réel. Ce texte raconte ce que j'en ai retenu, et **je vous propose le kata qui en est sorti, en open source** : [github.com/Baptiste-Mace/kata-ia-souveraine](https://github.com/Baptiste-Mace/kata-ia-souveraine). Parce que protéger ses données ne devrait pas être un privilège réservé aux grandes structures qui ont les moyens d'acheter cette compétence. Chacun, une PME, une association, une collectivité, une équipe isolée dans un grand groupe, doit pouvoir refaire le chemin, le critiquer et l'améliorer.

## Louer son intelligence

On nous a vendu l'IA comme un robinet. Une clé d'API, une ligne de code, et l'intelligence coule. Pas de serveur, pas de modèle à gérer, pas de question à se poser. C'est pratique. C'est aussi, à bien y regarder, **une manière de louer sa capacité à penser à quelqu'un d'autre**.

Car que mettons-nous dans ces prompts ? Du code source, des contrats, des comptes rendus de comité, des données clients, des stratégies. Exactement ce qui fait la valeur et le secret d'une entreprise. En 2023, Samsung l'a découvert à ses dépens : des ingénieurs avaient collé du code interne dans un assistant conversationnel public, et l'entreprise a dû en restreindre l'usage dans l'urgence [1]. Personne n'avait voulu fuiter quoi que ce soit. L'outil était simplement là, et le geste semblait anodin.

Je crois que le défi d'aujourd'hui, et plus encore de demain, ne sera pas d'*accéder* à l'IA. Ce sera de **la garder dans l'enceinte**.

## Une vieille histoire de dépendance

Ce débat n'est pas neuf. En mai 1966, les États-Unis refusent temporairement de livrer au Commissariat à l'énergie atomique deux supercalculateurs CDC 6600. La même année, la France lance le *Plan Calcul* [2]. Les historiens nuancent le lien : l'embargo fut contourné et n'est pas la cause du plan. Mais l'épisode a marqué les esprits, parce qu'il rendait visible une évidence qu'on préfère oublier : **quand une capacité devient stratégique, en dépendre totalement d'un tiers revient à lui confier une partie de ses décisions**.

Soixante ans plus tard, la dépendance a changé de forme. Elle est juridique : le *CLOUD Act* de 2018 permet aux autorités américaines de requérir des données détenues par des entreprises américaines, où que soient leurs serveurs [3]. La Cour de justice de l'Union européenne a invalidé le *Privacy Shield* en 2020 précisément pour cette raison [4]. Et le 10 juin 2025, devant une commission d'enquête du Sénat, le directeur des affaires publiques et juridiques de Microsoft France a reconnu sous serment ne pas pouvoir garantir que les données des citoyens français hébergées par Microsoft France ne seraient jamais transmises à des autorités étrangères sans l'accord des autorités françaises [5].

Héberger ses données « en Europe » ne suffit donc pas. La question n'est pas *où* sont les serveurs. C'est **qui en détient les clés**.

## Souveraineté veut dire réversibilité

On confond souvent souveraineté et autarcie. Il ne s'agit pas de tout refaire seul, ni de rejeter par principe les services des autres. Il s'agit de *pouvoir* s'en passer. De garder, à tout moment, une porte de sortie.

C'est cela, la *réversibilité* : la capacité de reprendre en interne ce qu'on a confié à l'extérieur, sans tout perdre ni tout réécrire. Et pour l'IA, cette capacité existe enfin.

En janvier 2025, DeepSeek a publié R1 sous licence MIT, avec des versions distillées assez légères pour tourner sur un simple serveur, parfois sur un portable [6]. Mistral, Qwen, Llama, Gemma : l'écosystème des modèles à poids ouverts est devenu foisonnant. Des outils comme *Ollama* ou *vLLM* rendent leur déploiement presque banal.

Il faut ici être honnête. Un modèle à poids ouverts n'est pas un modèle neutre. DeepSeek, par exemple, reproduit sur certains sujets les lignes de censure de son pays d'origine. Le faire tourner chez soi garantit que **les données ne sortent pas**. Cela ne garantit pas que **le modèle pense juste**. L'évaluation, le choix, la vigilance restent notre travail.

## Le kata : j'ai voulu essayer

Les discours sur la souveraineté, j'en entends beaucoup. J'avais besoin de savoir si, concrètement, une équipe ordinaire pouvait le faire. Pas un laboratoire, pas un hyperscaler : une équipe qui connaît déjà un peu Kubernetes. Je suis donc parti d'un cluster Minikube sur mon poste, sans GPU, et j'ai cherché ce qu'il fallait, au minimum, pour servir une IA réellement interne. Le résultat est plus simple que je ne le pensais, et c'est ce parcours que je vous propose, en deux heures environ :

- **Un modèle open source** (DeepSeek-R1 distillé 1.5B) servi par Ollama, téléchargé *une seule fois* dans un volume partagé.
- **Une passerelle web** de quelques dizaines de lignes, sans aucune dépendance, qui reçoit les questions des utilisateurs et les relaie au modèle. C'est le point unique où l'on peut authentifier, tracer, filtrer.
- **Un Service Kubernetes** qui répartit la charge entre les replicas du modèle, et un Ingress pour l'accès web.
- **Un passage à l'échelle** d'abord avec l'*HPA* natif, piloté par le CPU, puis avec *KEDA*, piloté par le nombre réel de requêtes en cours.
- **Une NetworkPolicy** qui interdit toute sortie hors du namespace une fois le modèle rapatrié. Une connexion vers Internet échoue, l'IA continue de répondre. **La souveraineté devient une propriété vérifiable du réseau**, plus seulement une promesse contractuelle.

En le jouant sur mon poste professionnel, j'ai vécu une ironie que je n'avais pas anticipée. Le proxy de sécurité de l'entreprise bloque le téléchargement du modèle : registre Ollama et Hugging Face refusés. Le dispositif qui protège nos données empêche aussi de rapatrier un modèle ouvert. Ce n'est pas une absurdité, c'est un rappel utile : **la souveraineté ne s'improvise pas sur un poste, elle s'organise**, avec un registre interne, un miroir de modèles validés, un circuit d'approbation. Pour que chacun puisse quand même jouer le kata, j'y ai ajouté un faux modèle qui expose la même API et consomme réellement du CPU. Tout le reste (passerelle, répartition de charge, HPA, KEDA, NetworkPolicy) fonctionne à l'identique.

Le kata est en open source [7], et c'est un choix. La souveraineté d'une seule organisation ne vaut pas grand-chose si le savoir-faire reste enfermé : **c'est en partageant la compétence qu'on aide chacun à se protéger**. Chaque étape se termine par des questions plutôt que par des réponses : quel modèle est *suffisant* ? Le CPU est-il un bon signal pour une charge d'inférence ? Que faudrait-il rouvrir pour mettre le modèle à jour ? Je ne prétends pas avoir trouvé la bonne architecture. L'objectif n'est pas la performance, c'est de vérifier ensemble que **reprendre la main est à la portée de toute équipe qui fait déjà du cloud-native**. Si vous le faites tourner, je serai très curieux de vos retours, de vos blocages et de vos variantes : ouvrez une issue, proposez une amélioration, faites-le vivre.

## Le piège : remplacer une dépendance par un gaspillage

Rapatrier l'IA ne la rend pas immatérielle. Elle tourne désormais sur *nos* processeurs, consomme *notre* électricité, occupe *nos* baies. L'impact environnemental du numérique se joue d'abord à la fabrication du matériel [8], et une IA interne mal dimensionnée, c'est du matériel acheté, alimenté et refroidi pour rien.

C'est là que le kata m'a le plus appris. Avec l'HPA, le modèle garde au moins une replica allumée en permanence, même la nuit, même le week-end, quand personne ne pose de question. Avec KEDA, on peut descendre à **zéro**. Le premier utilisateur du matin attend quelques secondes de plus que le modèle se réveille. Les suivants ne voient rien.

Sur mon poste, avec le faux modèle, les ordres de grandeur parlent d'eux-mêmes. L'HPA, qui regarde le CPU, a mis plus d'une minute à ajouter une replica : la vague de requêtes était déjà passée. KEDA, qui regarde la demande réelle, est monté à trois replicas en une douzaine de secondes, puis est redescendu à zéro une fois le calme revenu. Le premier appel après le réveil a pris 12 secondes au lieu de 4. Avec un vrai modèle à charger en mémoire, ce délai sera plus long ; c'est précisément ce que je vous invite à mesurer chez vous.

Ce compromis dit quelque chose de profond. Le cloud public nous a habitués à une disponibilité infinie et instantanée, dont le coût physique est invisible parce qu'il est mutualisé et facturé ailleurs. Héberger soi-même oblige à **regarder ce coût en face**, et donc à choisir :

- le *juste* modèle, et non le plus gros (un 1.5B suffit souvent pour résumer, classer, reformuler) ;
- le *juste* nombre de replicas, piloté par la demande réelle et non par la peur du pic ;
- la *juste* latence, en acceptant qu'une IA endormie mette quelques secondes à se lever.

**La souveraineté et la sobriété ne s'opposent pas. Elles relèvent de la même discipline : savoir ce dont on a vraiment besoin.**

## Ce que cela demande à notre métier

Nous, ingénieurs, avons pris l'habitude de répondre « c'est faisable » à chaque demande d'IA, en branchant une API de plus. Je crois que notre rôle est aussi de poser d'autres questions :

- Où partent ces données, et qui peut y accéder demain ?
- Pourrions-nous fonctionner si ce fournisseur changeait ses prix, ses conditions, ou disparaissait ?
- Quel est le plus petit modèle qui rend le service attendu ?
- Combien de ressources restent allumées quand personne ne s'en sert ?

Je n'ai pas de réponse définitive à ces questions. Je sais seulement qu'elles ne se posent plus si l'on n'a jamais appris à faire tourner un modèle soi-même. Savoir monter son propre cluster, son propre modèle, sa propre passerelle, ce n'est pas un repli. C'est **garder le choix**.

**Une IA qu'on ne peut pas débrancher n'est pas un outil. C'est une dépendance.**

Et si la prochaine compétence stratégique de nos équipes n'était pas d'utiliser l'IA des autres, mais de savoir s'en passer ? Le kata est là pour commencer à y répondre, ensemble.

## Sources

1. Bloomberg, « Samsung Bans Staff's AI Use After Spotting ChatGPT Data Leak », 2 mai 2023 : <https://www.bloomberg.com/news/articles/2023-05-02/samsung-bans-chatgpt-and-other-generative-ai-use-by-staff-after-leak>
2. Pierre-Éric Mounier-Kuhn, *L'Informatique en France, de la Seconde Guerre mondiale au plan Calcul*, PUPS, 2010 ; voir aussi l'article Wikipédia « Plan Calcul », section sur l'embargo contre le CEA.
3. *Clarifying Lawful Overseas Use of Data Act* (CLOUD Act), loi fédérale américaine, mars 2018.
4. CJUE, arrêt C-311/18 « Schrems II », 16 juillet 2020.
5. Sénat, commission d'enquête sur les coûts et les modalités effectifs de la commande publique, audition de M. Anton Carniaux (Microsoft France), 10 juin 2025 : <https://videos.senat.fr/video.5460497_6847c70b82594.commande-publique--audition-de-microsoft?timecode=1971000> ; rapport n° 830 (2024-2025) : <https://www.senat.fr/rap/r24-830-1/r24-830-11.pdf>
6. DeepSeek-AI, « DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning », janvier 2025, licence MIT.
7. Le kata « IA souveraine », en open source : <https://github.com/Baptiste-Mace/kata-ia-souveraine>
8. Ademe et Arcep, « Évaluation de l'impact environnemental du numérique en France », 2022 et mise à jour 2025.
