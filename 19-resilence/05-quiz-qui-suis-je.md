---
title: "Chapitre 05 - Quiz : Qui suis-je ? (Devinettes sur la Résilience)"
description: "Quiz devinettes pour identifier les patterns de résilience à partir de leur description fonctionnelle."
---

# Quiz – Qui suis-je ?

> Chaque question décrit un comportement ou un mécanisme. Identifiez le pattern ou concept correspondant.

---

### Question 1 :

Je suis un mécanisme qui surveille les appels vers un service distant. Quand je détecte que trop d'appels échouent, je cesse complètement de les transmettre pendant un certain temps. Je laisse ensuite passer une seule requête test pour vérifier si le service s'est rétabli.

**Qui suis-je ?**

**Réponse :** Circuit Breaker

---

### Question 2 :

Je suis l'état dans lequel se trouve le Circuit Breaker pendant son fonctionnement NORMAL, quand tout se passe bien et que les appels passent librement.

**Qui suis-je ?**

**Réponse :** État Fermé (Closed)

---

### Question 3 :

Je suis l'état temporaire du Circuit Breaker quand il autorise une UNIQUE requête de test vers un service potentiellement rétabli. Si cette requête réussit, le circuit revient à la normale ; si elle échoue, je retourne en mode blocage total.

**Qui suis-je ?**

**Réponse :** État Semi-ouvert (Half-Open)

---

### Question 4 :

Je suis la réponse préparée à l'avance que retourne un service lorsque son appel vers une dépendance est bloqué (par exemple, une liste vide, des données en cache, ou un message d'erreur explicite). Je suis la réponse de repli.

**Qui suis-je ?**

**Réponse :** Fallback (réponse de repli)

---

### Question 5 :

Je suis un mécanisme qui limite le nombre de requêtes qu'un service peut recevoir par unité de temps. Quand cette limite est dépassée, je rejette immédiatement les requêtes supplémentaires avec un code HTTP spécifique.

**Qui suis-je ?**

**Réponse :** Rate Limiter (limiteur de débit)

---

### Question 6 :

Je suis l'algorithme de Rate Limiting qui fonctionne comme un seau rempli de jetons. Chaque requête consomme un jeton, et des jetons sont ajoutés à intervalle régulier. Quand le seau est plein, je peux absorber un pic de trafic d'un coup.

**Qui suis-je ?**

**Réponse :** Token Bucket (seau de jetons)

---

### Question 7 :

Je suis l'algorithme de Rate Limiting qui calcule le nombre de requêtes sur les N dernières secondes, peu importe l'heure exacte, éliminant les abus possibles en bordure de fenêtre temporelle fixe.

**Qui suis-je ?**

**Réponse :** Sliding Window (fenêtre glissante)

---

### Question 8 :

Je suis le code HTTP standard envoyé au client quand un Rate Limiter rejette sa requête parce qu'il a dépassé le quota autorisé.

**Qui suis-je ?**

**Réponse :** HTTP 429 Too Many Requests

---

### Question 9 :

Je suis un pattern qui consiste à réexécuter automatiquement une opération qui a échoué, en espérant que l'erreur était temporaire. Je ne dois être appliqué qu'aux opérations dont le résultat est identique quelle que soit la répétition.

**Qui suis-je ?**

**Réponse :** Retry (nouvelle tentative) sur opérations idempotentes

---

### Question 10 :

Je suis une propriété d'une opération : si on m'exécute une, deux, ou dix fois, le résultat est toujours le même. Je suis essentielle pour déterminer si une opération peut être réessayée en toute sécurité.

**Qui suis-je ?**

**Réponse :** Idempotence

---

### Question 11 :

Je suis une stratégie de Retry où le délai entre les tentatives double à chaque échec. Si la première attente est d'1 seconde, la deuxième est de 2 secondes, la troisième de 4 secondes, etc.

**Qui suis-je ?**

**Réponse :** Backoff exponentiel (Exponential Backoff)

---

### Question 12 :

Je suis le phénomène désastreux qui se produit quand des milliers de clients font tous leurs Retries au MÊME moment, créant un pic de charge qui empêche le service défaillant de récupérer.

**Qui suis-je ?**

**Réponse :** Thundering Herd (effet de troupeau)

---

### Question 13 :

Je suis la solution au Thundering Herd : j'ajoute une valeur aléatoire au délai de Backoff exponentiel, ce qui désynchronise les tentatives des différents clients.

**Qui suis-je ?**

**Réponse :** Jitter (randomisation du délai)

---

### Question 14 :

Je suis une limite de temps imposée sur une opération. Si l'opération ne se termine pas avant moi, elle est abandonnée et une erreur est retournée. Sans moi, un appel réseau peut bloquer un thread indéfiniment.

**Qui suis-je ?**

**Réponse :** Timeout

---

### Question 15 :

Je suis un type de Timeout qui limite uniquement le temps pour ÉTABLIR la connexion initiale avec un service distant, avant même d'envoyer la requête.

**Qui suis-je ?**

**Réponse :** Connection Timeout

---

### Question 16 :

Je suis un pattern inspiré des cloisons étanches d'un navire. Je divise les ressources (pools de threads, pools de connexions) en groupes isolés, de sorte qu'une défaillance dans un groupe ne peut pas épuiser les ressources des autres groupes.

**Qui suis-je ?**

**Réponse :** Bulkhead (cloison étanche)

---

### Question 17 :

Je suis la capacité d'un système à continuer à fonctionner avec des fonctionnalités RÉDUITES plutôt que de tomber complètement en panne. Par exemple, afficher des recommandations génériques quand le moteur de recommandations est indisponible.

**Qui suis-je ?**

**Réponse :** Dégradation gracieuse (Graceful Degradation)

---

### Question 18 :

Je suis une probe Kubernetes qui vérifie si un conteneur est encore **en vie**. Si je détecte un problème (ex. : deadlock), Kubernetes redémarre automatiquement le conteneur.

**Qui suis-je ?**

**Réponse :** Liveness Probe

---

### Question 19 :

Je suis une probe Kubernetes qui vérifie si un conteneur est **prêt à recevoir du trafic**. Si je détecte qu'il n'est pas prêt, Kubernetes le retire du load balancer sans le redémarrer.

**Qui suis-je ?**

**Réponse :** Readiness Probe

---

### Question 20 :

Je suis une probe Kubernetes qui permet à une application avec un long temps de démarrage de s'initialiser correctement, sans que la Liveness Probe ne la tue prématurément.

**Qui suis-je ?**

**Réponse :** Startup Probe

---

### Question 21 :

Je suis le pattern qui représente le phénomène où une panne dans un service se propage à tous les services qui en dépendent, causant une panne en chaîne dans tout le système.

**Qui suis-je ?**

**Réponse :** Défaillance en cascade (Cascading Failure)

---

### Question 22 :

Je suis une couche d'infrastructure dans Kubernetes qui peut prendre en charge des mécanismes de résilience (Circuit Breaker, Retry, Rate Limiting) de façon centralisée, sans modifier le code des services eux-mêmes.

**Qui suis-je ?**

**Réponse :** Service Mesh (ex : Istio, Linkerd)

---

### Question 23 :

Je suis une ressource Kubernetes configurée dans les annotations d'un Ingress Controller. Je peux appliquer des règles de Rate Limiting au niveau du trafic entrant, avant même que la requête n'atteigne le service.

**Qui suis-je ?**

**Réponse :** Ingress Controller avec annotations de Rate Limiting (ex. : NGINX Ingress)

---

### Question 24 :

Je suis un identifiant unique envoyé par le client avec chaque requête. Le serveur me conserve pour détecter les doublons. Si la même requête est envoyée deux fois (ex. : suite à un Retry), le serveur ne l'exécute qu'une seule fois grâce à moi.

**Qui suis-je ?**

**Réponse :** Token d'idempotence (Idempotency Key)

---

### Question 25 :

Je suis la stratégie de déploiement Kubernetes qui met à jour les pods **progressivement** (un par un ou par petits groupes), maintenant le service disponible pendant toute la durée de la mise à jour.

**Qui suis-je ?**

**Réponse :** Rolling Update (stratégie de mise à jour progressive)

---

### Question 26 :

Je suis la limite de ressources configurée dans un manifest Kubernetes (`resources.limits.cpu` et `resources.limits.memory`). Je constitue une forme de Bulkhead au niveau infrastructure : un pod ne peut pas consommer plus que ce que je spécifie, protégeant les autres pods du nœud.

**Qui suis-je ?**

**Réponse :** Resource Limits Kubernetes

---

### Question 27 :

Je suis une technique de test de résilience consistant à **provoquer intentionnellement des pannes** dans un système en production (ou en staging) pour vérifier que les mécanismes de résilience fonctionnent comme prévu.

**Qui suis-je ?**

**Réponse :** Chaos Engineering (ex : Chaos Monkey, LitmusChaos)

---

### Question 28 :

Je suis la réponse HTTP envoyée par un service TEMPORAIREMENT indisponible (pod en redémarrage, maintenance, surcharge momentanée). Je suis souvent réessayable, contrairement à une erreur client.

**Qui suis-je ?**

**Réponse :** HTTP 503 Service Unavailable

---

### Question 29 :

Je suis le délai configuré dans un Circuit Breaker qui définit combien de temps le circuit reste en état Ouvert avant de passer en état Semi-ouvert pour tester si le service s'est rétabli.

**Qui suis-je ?**

**Réponse :** Délai de récupération (Wait Duration / Recovery Timeout)

---

### Question 30 :

Je suis le principe architectural qui dit : "Conçois ton système en ASSUMANT que chaque composant peut tomber en panne à tout moment." C'est le fondement de la résilience dans le cloud.

**Qui suis-je ?**

**Réponse :** Design for Failure (Concevoir pour la défaillance)

---

> **Conseils d'utilisation**
> - Utiliser ce quiz en format oral pour stimuler la discussion en classe
> - Les questions 1 à 16 couvrent les patterns applicatifs
> - Les questions 17 à 26 couvrent les aspects déploiement et infrastructure
> - Les questions 27 à 30 couvrent les concepts avancés et philosophiques
