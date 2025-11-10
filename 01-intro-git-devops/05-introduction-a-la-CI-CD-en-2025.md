# Intégration Continue (CI) et Déploiement Continu (CD) en 2025



## 1. Pourquoi CI/CD ? Problème de départ

### 1.1 Situation “avant CI/CD”

Dans un fonctionnement traditionnel :

* Le code est développé pendant des semaines ou des mois sur des postes locaux.
* L’intégration se fait tard, juste avant une date de livraison.
* Les tests sont majoritairement **manuels** et exécutés en fin de projet.
* Le déploiement en production est :

  * rare (quelques fois par an),
  * lourd (scripts manuels, check-list papier),
  * risqué (pannes, rollback difficile),
  * stressant pour les équipes.

**Conséquences** :

* Découverte tardive des bugs et des incompatibilités.
* Intégrations douloureuses (“big bang merge”).
* Release rares, grosses, difficiles à stabiliser.
* Relation tendue entre Dev, QA, Ops et métier.

### 1.2 Objectif de CI/CD

L’intégration continue et le déploiement continu visent à :

* **Réduire la taille des changements** (petits incréments fréquents).
* **Automatiser** le plus possible :

  * compilation,
  * tests,
  * packaging,
  * déploiement,
  * vérifications post-déploiement.
* Transformer la livraison logicielle en **flux régulier**, plutôt qu’en gros “chocs” ponctuels.

En 2025, CI/CD est un **standard industriel** : pratiquement toutes les équipes sérieuses ont un pipeline automatisé, même minimal.



## 2. Définitions modernes : CI, Continuous Delivery, Continuous Deployment

### 2.1 Intégration Continue (CI)

> **Définition**
> L’intégration continue est une pratique de développement où les développeurs **intègrent fréquemment** leurs modifications de code dans une branche commune (souvent `main` ou `develop`), et où **chaque intégration déclenche automatiquement** un processus de build et de tests.

Principes clés :

* Un dépôt Git central (GitHub, GitLab, Bitbucket, Azure DevOps).
* Des **commits fréquents** (plusieurs fois par jour).
* Un serveur CI (Jenkins, GitHub Actions, GitLab CI, Azure Pipelines, etc.) qui :

  * récupère les sources,
  * compile le code,
  * exécute les tests automatiques,
  * génère des rapports (succès/échec, couverture, qualité).

Objectif principal :

> Détecter les erreurs **le plus tôt possible** pour éviter les “intégrations catastrophes”.



### 2.2 Continuous Delivery (CD – Livraison Continue)

> **Définition**
> La livraison continue est la capacité à garder le logiciel dans un état tel qu’il peut être **déployé à tout moment** en production, sur décision humaine.

Caractéristiques :

* Le pipeline va au-delà du build et des tests :

  * packaging d’un **artefact versionné** (image Docker, jar/war, package npm, etc.),
  * déploiement automatique sur un environnement de **test**, puis **staging**,
  * exécution de tests supplémentaires (tests d’intégration, tests end-to-end, tests de performance basiques).
* La mise en production est **quasi triviale** :

  * clic sur un bouton (“Deploy to Production”),
  * ou exécution d’une simple commande.

La différence avec la CI :

* CI s’arrête souvent après les tests automatiques.
* Continuous Delivery va jusqu’à un **logiciel prêt à être déployé**, avec un pipeline testé sur des environnements proches de la prod.



### 2.3 Continuous Deployment (Déploiement Continu)

> **Définition**
> Le déploiement continu est l’extension de la livraison continue où chaque changement validé (qui passe toutes les étapes automatisées) est **déployé en production automatiquement, sans intervention humaine**.

Conditions nécessaires :

* Pipeline de tests **très robuste**.
* Monitoring et alertes en place.
* Stratégies de déploiement sûres :

  * déploiement progressif (canary),
  * blue/green,
  * feature flags.

En pratique, beaucoup d’organisations font :

* CI + Continuous Delivery (mise en prod sur décision).
* Continuous Deployment seulement dans certains contextes (services internes, forte automatisation, culture mature).



## 3. Pipeline CI/CD moderne : vue d’ensemble

### 3.1 Chaîne “du commit à la production”

Aujourd’hui, un pipeline typique ressemble à ceci :

1. **Source** : dépôt Git (GitHub / GitLab / Azure DevOps).
2. **CI** :

   * déclenchée sur commit / pull request / merge,
   * build + tests unitaires + analyse de qualité.
3. **Artefact** :

   * création d’un binaire ou d’une image Docker,
   * push vers un **registry** (Docker Hub, GitHub Container Registry, ECR, etc.).
4. **CD** :

   * déploiement automatisé sur un environnement de test/staging,
   * tests complémentaires (intégration, end-to-end).
5. **Production** :

   * déploiement planifié ou automatique,
   * surveillance du système (logs, métriques, traces),
   * rollback possible si problème.
6. **Feedback** :

   * retour vers les développeurs (statut du pipeline),
   * retour métier (KPIs, usage, incidents).

### 3.2 Rôle des conteneurs et de Kubernetes en 2025

En 2010 on parlait surtout de serveurs applicatifs (Tomcat, JBoss, IIS, etc.).
En 2025, la majorité des pipelines modernes :

* construisent une **image Docker**,
* déploient sur :

  * un cluster **Kubernetes** (on-premise ou cloud),
  * ou une solution containerisée gérée (EKS, AKS, GKE, ECS…),
* utilisent des outils comme **Helm** pour gérer les manifests.



## 4. Principes clés d’une bonne CI

### 4.1 Commits fréquents sur une branche partagée

* Privilégier des branches de courte durée.
* Intégrer souvent (daily merge, trunk-based development).
* Éviter les branches qui vivent des semaines.

### 4.2 Build reproductible et automatisé

* Script de build unique :

  * `mvn clean test`,
  * `npm ci && npm test`,
  * `dotnet test`,
  * etc.
* Le build doit être **rejouable** en local et sur la CI.

### 4.3 Tests automatisés

* Au minimum :

  * tests unitaires,
  * quelques tests d’intégration,
  * lint / formatage.
* Idéalement :

  * analyse statique (SAST),
  * mesure de couverture.

### 4.4 Pipeline rapide

* Une CI qui dure 45 minutes sur chaque commit **décourage** les développeurs.
* Objectif : un feedback en quelques minutes (5–10 min si possible).
* Division des tests :

  * tests rapides sur chaque commit,
  * tests plus lourds la nuit ou à intervalles réguliers.



## 5. Principes clés de la Livraison / Déploiement Continu

### 5.1 Artefacts versionnés

* Chaque build produit un **artefact immutable** :

  * jar/war, image Docker, package nuget, etc.
* On ne reconstruit pas en production : on déploie l’artefact déjà validé.

### 5.2 Environnements structurés

Environnements typiques :

* `dev` : pour le développement et les premiers tests.
* `test` / `qa` : pour les tests d’intégration, fonctionnels, end-to-end.
* `staging` / `preprod` : le plus proche possible de la production.
* `prod` : environnement réel, utilisateurs finaux.

Le pipeline doit :

* garder la même image / artefact,
* simplement changer la **configuration** (variables d’environnement, secrets, fichiers de config, Helm values…).

### 5.3 Stratégies de déploiement

En 2025, on évite les “big bang deploy” et on préfère :

* **Rolling update** : mise à jour progressive des instances.
* **Blue/Green** : deux environnements quasi identiques, on bascule le trafic.
* **Canary release** : déployer sur un petit pourcentage d’utilisateurs, puis augmenter si tout va bien.
* **Feature flags** : activer/désactiver des fonctionnalités sans redéployer.



## 6. Pipeline as Code

### 6.1 Principe

> **Pipeline as Code** = la définition du pipeline (build, tests, déploiements) est stockée dans le dépôt Git, sous forme de fichier texte versionné.

Exemples :

* `Jenkinsfile` pour Jenkins (pipeline déclaratif).
* `.github/workflows/*.yml` pour GitHub Actions.
* `.gitlab-ci.yml` pour GitLab CI.
* `azure-pipelines.yml` pour Azure DevOps.

Avantages :

* Historique du pipeline dans Git.
* Revue de code sur les modifications du pipeline.
* Reproductibilité : on sait exactement comment un build a été produit.

### 6.2 Exemple de structure (générique)

Un pipeline as code comporte souvent :

* **Triggers** : quand lancer le pipeline ? (push, PR, tag, cron…)
* **Jobs / stages** :

  * build,
  * tests,
  * packaging,
  * déploiement.
* **Conditions** : n’exécuter certains jobs que sur `main`, ou sur tag, etc.
* **Secrets** : gestion sécurisée des mots de passe, tokens, clés.



## 7. Qualité, tests et sécurité dans le pipeline

### 7.1 Pyramide de tests

En CI/CD, on vise :

* Beaucoup de **tests unitaires** (rapides, nombreux).
* Moins de tests d’intégration, mais bien ciblés.
* Quelques tests end-to-end, plus coûteux.

### 7.2 Qualité de code

Intégration de :

* Lint (formatage, style, règles de base).
* Analyse statique de code (complexité, duplications, bugs potentiels).
* Mesure de couverture : ne pas chercher 100 %, mais surveiller les tendances.

### 7.3 Sécurité (DevSecOps)

En 2025, un pipeline CI/CD sérieux inclut :

* Scan des dépendances (vulnérabilités connues).
* Scan des images Docker (vulnérabilités, configuration).
* Vérification de configuration (Kubernetes, IAM, etc.).



## 8. Observabilité et feedback

CI/CD **ne s’arrête pas au déploiement** : on doit observer ce qui se passe après.

* Collecte de **logs** (ELK, Loki, etc.).
* **Metrics** (Prometheus, Grafana) :

  * latence,
  * taux d’erreur,
  * utilisation CPU/RAM.
* **Traces distribuées** (OpenTelemetry, Jaeger, etc.).
* Intégration de ces signaux dans :

  * les décisions de déploiement,
  * les post-mortems,
  * l’amélioration du pipeline.



## 9. Métriques CI/CD (DORA et autres)

Pour mesurer l’efficacité de CI/CD, on suit souvent des métriques inspirées du rapport DORA :

* **Fréquence de déploiement** : combien de fois déploie-t-on en prod ?
* **Lead time for changes** : temps entre commit et déploiement en prod.
* **Mean Time To Restore (MTTR)** : temps moyen pour restaurer le service après un incident.
* **Change Failure Rate** : pourcentage de changements qui causent un incident.

Objectif :

> Livrer souvent, avec un faible taux d’échec, et corriger vite en cas de problème.



## 10. Bonnes pratiques et anti-patterns

### 10.1 Bonnes pratiques

* Versionner tout (code, pipeline, infra).
* Garder la CI **rapide**.
* Automatiser les étapes répétitives.
* Mettre en place des **post-mortems sans blâme** après incident.
* Documenter le pipeline (schémas, README, diagrammes).

### 10.2 Anti-patterns classiques

* CI qui ne tourne jamais (pipeline cassé en permanence).
* Tests uniquement en local, rien d’automatisé.
* Déploiements manuels en production (copie de fichiers par FTP).
* Un seul “expert pipeline” qui sait comment tout fonctionne.
* Utiliser Jenkins / GitHub Actions comme “super script shell” sans structurer.



## 12. Conclusion

En 2010, parler d’intégration continue était déjà un sujet avancé, centré sur Hudson, CruiseControl, Subversion, CVS.

En 2025 :

* **CI/CD est devenu la norme**, et non plus l’exception.
* Les outils ont évolué (Git, Docker, Kubernetes, Jenkins, GitHub Actions, GitLab CI, Azure DevOps).
* Les principes restent les mêmes :

  * intégrer tôt,
  * tester automatiquement,
  * déployer souvent,
  * mesurer et améliorer en continu.

