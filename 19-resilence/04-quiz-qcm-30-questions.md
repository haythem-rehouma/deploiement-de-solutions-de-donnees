---
title: "Chapitre 04 - Quiz QCM : Résilience dans le Cloud (30 questions)"
description: "Quiz à choix multiples couvrant les patterns de résilience : Circuit Breaker, Rate Limiter, Retry, Backoff, Bulkhead, Timeout et Health Checks."
---

### **Quiz – Résilience dans le Cloud (30 questions)**

---

#### **1. Quel pattern protège un service en COUPANT les appels vers une dépendance défaillante ?**

* A. Rate Limiter
* B. Bulkhead
* C. Circuit Breaker
* D. Retry

**Réponse : C**
→ Le Circuit Breaker coupe les appels vers un service défaillant pour éviter les cascades de pannes.

---

#### **2. Combien d'états possède un Circuit Breaker ?**

* A. 2 (Ouvert / Fermé)
* B. 3 (Fermé / Ouvert / Semi-ouvert)
* C. 4 (Fermé / Ouvert / Semi-ouvert / Désactivé)
* D. 1 (Ouvert uniquement)

**Réponse : B**
→ Fermé (normal), Ouvert (service défaillant, fail fast), Semi-ouvert (test de rétablissement).

---

#### **3. Dans quel état un Circuit Breaker laisse-t-il passer UNE seule requête de test ?**

* A. Fermé (Closed)
* B. Ouvert (Open)
* C. Semi-ouvert (Half-Open)
* D. Inactif (Inactive)

**Réponse : C**
→ En état Semi-ouvert, une requête test est autorisée. Si elle réussit, le circuit repasse Fermé.

---

#### **4. Quel code HTTP standard indique que le Rate Limiter a rejeté une requête ?**

* A. 400 Bad Request
* B. 403 Forbidden
* C. 503 Service Unavailable
* D. 429 Too Many Requests

**Réponse : D**
→ HTTP 429 "Too Many Requests" est le code standard pour les rejets par rate limiting.

---

#### **5. L'algorithme "Token Bucket" pour le Rate Limiting permet :**

* A. Un débit de sortie parfaitement constant
* B. Des pics de trafic (bursts) tout en maintenant un débit moyen
* C. De rejeter toutes les requêtes au-delà d'une fenêtre horaire
* D. De mettre en file d'attente toutes les requêtes

**Réponse : B**
→ Le Token Bucket autorise des bursts : si le seau est plein, un pic peut être absorbé d'un coup.

---

#### **6. Quelle est la PRINCIPALE différence entre "Fixed Window" et "Sliding Window" pour le Rate Limiting ?**

* A. La fenêtre fixe est plus rapide
* B. La fenêtre glissante calcule sur les N dernières secondes, évitant les pics en bordure de fenêtre
* C. La fenêtre fixe permet des bursts, la glissante non
* D. Elles sont identiques

**Réponse : B**
→ La fenêtre fixe peut être exploitée (2x les requêtes en quelques secondes à cheval sur deux fenêtres). La fenêtre glissante élimine ce problème.

---

#### **7. Le pattern Retry doit être appliqué EN PRIORITÉ sur quel type d'erreur ?**

* A. HTTP 401 Unauthorized
* B. HTTP 404 Not Found
* C. HTTP 503 Service Unavailable (transitoire)
* D. HTTP 400 Bad Request

**Réponse : C**
→ Le 503 est souvent transitoire (pod en redémarrage, surcharge momentanée). Les erreurs 4xx sont des erreurs client non réessayables.

---

#### **8. Une opération est "idempotente" si :**

* A. Elle peut être exécutée plusieurs fois sans changer le résultat
* B. Elle est exécutée une seule fois
* C. Elle est synchrone
* D. Elle retourne toujours HTTP 200

**Réponse : A**
→ Idempotente = même résultat peu importe le nombre d'exécutions. Critique pour décider si on peut réessayer.

---

#### **9. Parmi ces opérations, laquelle EST idempotente ?**

* A. `POST /commandes` (créer une commande)
* B. `POST /paiements` (débiter une carte)
* C. `GET /produits/42` (lire un produit)
* D. `POST /emails/envoyer` (envoyer un email)

**Réponse : C**
→ Un GET est toujours idempotent : lire la même ressource plusieurs fois ne change pas son état.

---

#### **10. Quel est le problème d'un Retry sans Backoff exponentiel quand des milliers de clients échouent simultanément ?**

* A. Les clients attendent trop longtemps
* B. Les clients bombardent le service défaillant au même moment, l'empêchant de récupérer
* C. Les clients épuisent leur mémoire locale
* D. Les clients ignorent les erreurs

**Réponse : B**
→ C'est le "Thundering Herd" : des milliers de retries simultanés aggravent la surcharge.

---

#### **11. Quelle technique résout le problème du "Thundering Herd" dans le contexte des retries ?**

* A. Augmenter le nombre de retries
* B. Réessayer immédiatement
* C. Ajouter un délai aléatoire (Jitter) au Backoff exponentiel
* D. Désactiver le Retry

**Réponse : C**
→ Le Jitter désynchronise les tentatives des clients, évitant les pics de charge synchronisés.

---

#### **12. Avec un Backoff exponentiel de base 1 seconde et un multiplicateur de 2, quel est le délai AVANT la 4ème tentative ?**

* A. 4 secondes
* B. 6 secondes
* C. 8 secondes
* D. 16 secondes

**Réponse : C**
→ Tentative 1→2s, 2→4s, 3→8s. Formule : base × 2^(n-1). Tentative 4 = 1 × 2^3 = 8 secondes.

---

#### **13. Quel type de timeout limite le TEMPS TOTAL d'une opération de bout en bout ?**

* A. Connection timeout
* B. Read timeout
* C. Write timeout
* D. Request timeout

**Réponse : D**
→ Le Request Timeout couvre l'opération complète. Les autres types couvrent des phases spécifiques.

---

#### **14. Si le Service A a un timeout de 5s et appelle le Service B qui a un timeout de 10s, quel problème peut survenir ?**

* A. Aucun, c'est la configuration correcte
* B. B peut continuer à traiter après qu'A a déjà échoué, causant des incohérences
* C. A attendra toujours que B finisse
* D. B sera plus rapide qu'A

**Réponse : B**
→ A abandonne après 5s mais B continue 5s de plus. Si B réussit, le résultat est perdu et l'action peut avoir eu lieu (paiement effectué mais A n'a pas eu la réponse).

---

#### **15. Le pattern Bulkhead est inspiré de :**

* A. Les disjoncteurs électriques
* B. Les cloisons étanches d'un navire
* C. Les feux de circulation
* D. Les sauvegardes de base de données

**Réponse : B**
→ Les cloisons étanches isolent les compartiments d'un navire. En panne dans un compartiment, les autres survivent.

---

#### **16. Sans Bulkhead, si le Service A appelle une base de données LENTE, quel est l'impact sur les Services B et C qui partagent le même pool de threads ?**

* A. Aucun impact
* B. Les services B et C sont accélérés
* C. A consomme tous les threads, B et C manquent de ressources et deviennent indisponibles
* D. La base de données redémarre automatiquement

**Réponse : C**
→ Sans isolation, la ressource partagée (pool de threads) est épuisée par A, affamant B et C.

---

#### **17. Quelle probe Kubernetes est responsable de REDÉMARRER un conteneur défaillant ?**

* A. Readiness Probe
* B. Liveness Probe
* C. Startup Probe
* D. Network Probe

**Réponse : B**
→ La Liveness Probe détecte si le conteneur est vivant. En cas d'échec, Kubernetes le redémarre.

---

#### **18. Quelle probe Kubernetes RETIRE un pod du load balancer sans le redémarrer ?**

* A. Liveness Probe
* B. Startup Probe
* C. Readiness Probe
* D. Health Probe

**Réponse : C**
→ La Readiness Probe retire le pod du trafic entrant. Utile pendant les démarrages ou surcharges.

---

#### **19. À quoi sert la Startup Probe en Kubernetes ?**

* A. Vérifier si le nœud est disponible
* B. Empêcher la Liveness Probe de tuer un conteneur qui n'a pas encore fini de démarrer
* C. Démarrer automatiquement un nouveau pod
* D. Effectuer un backup au démarrage

**Réponse : B**
→ La Startup Probe donne le temps nécessaire au démarrage avant que la Liveness Probe ne commence ses vérifications.

---

#### **20. Dans quel ordre correct faut-il appliquer les patterns de résilience (de l'entrée vers l'appel) ?**

* A. Circuit Breaker → Rate Limiter → Bulkhead → Timeout → Retry
* B. Rate Limiter → Bulkhead → Circuit Breaker → Timeout → Retry
* C. Retry → Timeout → Circuit Breaker → Bulkhead → Rate Limiter
* D. Timeout → Retry → Bulkhead → Rate Limiter → Circuit Breaker

**Réponse : B**
→ Le Rate Limiter filtre en entrée, le Bulkhead isole les ressources, le Circuit Breaker protège les dépendances, le Timeout limite l'attente, le Retry gère les erreurs transitoires.

---

#### **21. Une stratégie de "fallback" dans le contexte du Circuit Breaker désigne :**

* A. Un mécanisme de logging des erreurs
* B. La réponse de repli retournée quand le circuit est ouvert
* C. Un deuxième Circuit Breaker de secours
* D. Une alerte envoyée à l'équipe DevOps

**Réponse : B**
→ Le fallback peut être une valeur par défaut, des données en cache, ou un service alternatif.

---

#### **22. Le Rate Limiter "par client" permet de :**

* A. Bloquer un seul client malveillant sans impacter les autres
* B. Accélérer les requêtes des clients prioritaires
* C. Authentifier les clients
* D. Chiffrer les communications

**Réponse : A**
→ Un Rate Limiter par IP ou par token API isole le comportement abusif d'un seul client.

---

#### **23. Quel en-tête HTTP informe le client DU TEMPS À ATTENDRE avant de réessayer après un 429 ?**

* A. `X-Wait-Time`
* B. `Retry-After`
* C. `X-RateLimit-Reset`
* D. `Delay-Until`

**Réponse : B**
→ L'en-tête `Retry-After` indique le nombre de secondes avant de pouvoir réessayer.

---

#### **24. Dans un déploiement Kubernetes, où peut-on configurer le Rate Limiting au niveau INFRASTRUCTURE (sans code applicatif) ?**

* A. Dans un ConfigMap
* B. Dans les annotations d'un Ingress Controller (ex. NGINX)
* C. Dans un Secret Kubernetes
* D. Dans le fichier Dockerfile

**Réponse : B**
→ Des annotations comme `nginx.ingress.kubernetes.io/limit-rps` permettent le rate limiting au niveau Ingress.

---

#### **25. Qu'est-ce que la "dégradation gracieuse" (Graceful Degradation) ?**

* A. Éteindre le service proprement lors d'une mise à jour
* B. Maintenir un service partiel plutôt qu'une panne totale en cas de défaillance
* C. Réduire les logs en production
* D. Diminuer le nombre de replicas en dehors des heures de pointe

**Réponse : B**
→ La dégradation gracieuse préfère une réponse dégradée (ex. liste vide, données en cache) à une erreur totale.

---

#### **26. Un service met 45 secondes à répondre au lieu de 200ms habituels. Quel pattern est le PLUS APPROPRIÉ pour détecter ce problème et protéger les appelants ?**

* A. Retry avec Jitter
* B. Bulkhead par sémaphore
* C. Circuit Breaker avec `slowCallThreshold`
* D. Rate Limiter par fenêtre glissante

**Réponse : C**
→ Le Circuit Breaker peut configurer un seuil pour les appels LENTS (pas seulement les échecs). Un service lent est aussi dangereux qu'un service en panne.

---

#### **27. Les `Resource Limits` (CPU/Mémoire) dans un Pod Kubernetes constituent une forme de quel pattern ?**

* A. Circuit Breaker
* B. Rate Limiter
* C. Bulkhead
* D. Retry

**Réponse : C**
→ Les Resource Limits isolent chaque pod dans ses propres ressources, empêchant un pod d'affamer les autres (Bulkhead au niveau infrastructure).

---

#### **28. Pendant un Rolling Update Kubernetes, quel mécanisme empêche le trafic d'être envoyé vers un nouveau pod qui n'a pas encore fini de démarrer ?**

* A. Liveness Probe
* B. Readiness Probe
* C. PodDisruptionBudget
* D. HorizontalPodAutoscaler

**Réponse : B**
→ La Readiness Probe retire le nouveau pod du load balancer jusqu'à ce qu'il soit prêt à recevoir du trafic.

---

#### **29. Quel est l'avantage principal d'implémenter les patterns de résilience dans un Service Mesh (comme Istio) plutôt que dans chaque service applicatif ?**

* A. Le Service Mesh est plus rapide
* B. Centralisation de la configuration sans modifier le code des services
* C. Le Service Mesh chiffre automatiquement toutes les données
* D. Réduction du nombre de pods nécessaires

**Réponse : B**
→ Un Service Mesh externalise la résilience (Circuit Breaker, Retry, Timeout) hors du code applicatif, dans la configuration d'infrastructure.

---

#### **30. Quel principe résume le mieux la philosophie de la résilience dans le cloud ?**

* A. "Éviter toutes les pannes grâce à un code parfait"
* B. "Redémarrer manuellement les services en cas de panne"
* C. "Accepter que les pannes arrivent et concevoir le système pour s'en remettre automatiquement"
* D. "Toujours dupliquer les données pour éviter les pertes"

**Réponse : C**
→ La résilience n'est pas l'absence de pannes mais la capacité à s'en remettre — "Design for Failure".

---

> **Score suggéré :**
> - 27-30 / 30 : Excellent — maîtrise solide des patterns de résilience
> - 22-26 / 30 : Bien — quelques concepts à approfondir
> - 15-21 / 30 : À revoir — relire les chapitres 02 et 03
> - < 15 / 30 : Reprendre depuis le chapitre 01
