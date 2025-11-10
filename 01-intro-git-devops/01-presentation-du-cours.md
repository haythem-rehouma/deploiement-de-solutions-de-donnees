# 1. Présentation générale du cours

Ce cours a pour objectif d’initier les étudiantes et étudiants aux pratiques modernes de **DevOps** et de **CI/CD (Intégration et Déploiement Continus)**, à travers un parcours concret allant de Git jusqu’au déploiement automatisé d’applications dans le cloud.

Au terme de la session, les étudiantes et étudiants auront mis en place **un pipeline complet**, de la validation du code source jusqu’au déploiement automatisé sur une infrastructure containerisée (Docker, Swarm, Kubernetes, Helm, GitHub Actions, CloudFormation, etc.).

Ce cours se situe au croisement du **développement logiciel** et de **l’administration système / cloud**, et prépare directement au travail en équipe dans un contexte professionnel.

<br/>

# 2. Objectifs du cours

## 2.1 Objectifs généraux

À la fin du cours, les étudiantes et étudiants seront capables de :

1. **Expliquer la culture DevOps** : collaboration, automatisation, feedback rapide, observabilité, sécurité.
2. **Utiliser Git** de manière professionnelle : gestion de branches, historique, revues de code, résolution de conflits.
3. **Configurer une chaîne CI/CD** avec Jenkins et GitHub Actions pour :

   * cloner un dépôt,
   * exécuter les tests,
   * produire des artefacts (jar/war, images Docker),
   * déployer dans des environnements de staging et production.
4. **Construire et déployer des images Docker** et orchestrer des services avec **Docker Swarm** et **Nginx** (reverse proxy, load balancing).
5. **Déployer des applications sur Kubernetes** (Deployments, Services, Ingress) et gérer des mises à jour applicatives (rollouts, probes).
6. **Paramétrer Helm** pour empaqueter et versionner des déploiements Kubernetes.
7. **Mettre en place un pipeline de déploiement automatisé** (GitHub Actions, webhooks, registry, staging → prod).
8. **Superviser un système en production** avec **Prometheus, Grafana et Alertmanager**, et intégrer les notifications (Slack, Teams, email, etc.).
9. **Utiliser Ansible et Terraform** pour gérer l’infrastructure comme du code (IaC) et appliquer des changements reproductibles et traçables.
10. **Décrire, documenter et présenter un projet DevOps complet**, incluant l’architecture, les choix techniques et la stratégie de CI/CD.

<br/>

## 2.2 Compétences visées (synthèse)

Le cours contribue au développement des compétences suivantes :

* **Concevoir, automatiser et fiabiliser** un pipeline de livraison logicielle.
* **Collaborer efficacement** dans une équipe de développement/ops avec des outils modernes (Git, CI/CD, cloud).
* **Opérationnaliser une application** (build, test, déploiement, monitoring, rollback).
* **Documenter et communiquer** clairement l’architecture DevOps mise en place.

<br/>

# 3. Approche pédagogique

L’enseignement de ce cours s’appuie principalement sur une **approche par projets et par compétences** :

* Les notions sont d’abord **introduites par l’enseignant** sous forme de courts exposés ciblés.
* Elles sont ensuite **rapidement mises en pratique** dans des laboratoires guidés, directement alignés sur des scénarios réels de déploiement (pipelines CI/CD, conteneurs, Kubernetes, Helm, webhooks, monitoring, IaC, etc.).
* Les étudiantes et étudiants travaillent fréquemment en **petites équipes** sur un **projet fil rouge**, qui évolue tout au long de la session pour intégrer de nouvelles briques DevOps.
* L’enseignant agit comme **accompagnateur** :

  * circule en classe,
  * répond aux questions,
  * aide au dépannage,
  * met en lumière les **bonnes pratiques DevOps** (qualité du code, sécurité, documentation, logs, gestion de configuration).

Cette approche vise à reproduire au plus près la **réalité d’une équipe DevOps** en entreprise.

<br/>

# 4. Règles de fonctionnement et attentes

## 4.1 Assiduité et participation

* La **présence active** en cours et en laboratoire est essentielle.
* Une grande partie des apprentissages passe par la **manipulation des outils** (Git, Jenkins, Docker, Kubernetes, etc.), ce qui est difficile à rattraper uniquement avec les notes.
* Les étudiantes et étudiants sont responsables de :

  * rattraper le contenu manqué en cas d’absence,
  * consulter les ressources mises à disposition (dépôts Git, documents, enregistrements s’il y a lieu).

## 4.2 Travail en équipe

* Plusieurs activités se font **en équipe**, en particulier le **projet de session**.
* Chaque personne est responsable :

  * de sa contribution réelle au travail,
  * de la compréhension de l’ensemble du projet.
* Les équipes doivent adopter des **pratiques professionnelles** :

  * usage de branches Git,
  * revues de code,
  * conventions de nommage,
  * documentation minimale (README, schémas, commentaires).

## 4.3 Matériel requis

Les étudiantes et étudiants doivent avoir accès à :

* Un ordinateur capable de faire tourner :

  * Git, Docker, un IDE (IntelliJ, VS Code, etc.),
  * éventuellement une VM Linux ou WSL2 selon la configuration du poste.
* Une connexion internet fiable (pour les repos Git, Docker Hub, etc.).
* Un compte GitHub (ou autre plate-forme Git précisée par l’enseignant).

Les outils spécifiques (Jenkins, Kubernetes, etc.) seront :

* soit fournis via une infrastructure de laboratoire,
* soit installés localement selon les consignes du cours.

## 4.4 Règles pour Git et les dépôts

* Tous les travaux liés au cours doivent être **versionnés avec Git**.
* Les commits doivent être :

  * fréquents,
  * explicites (messages clairs et datés),
  * reflétant un travail progressif (éviter un gros commit à la dernière minute).
* En cas de travail d’équipe :

  * chaque membre doit avoir une trace de participation dans l’historique Git,
  * les merges doivent être faits proprement (pull request / merge request si applicable).

## 4.5 Remise des travaux et délais

* Les **consignes de remise** (plate-forme, format, nommage des dépôts, tag/branch à utiliser, etc.) seront clairement spécifiées pour chaque travail.
* Sauf indication contraire :

  * toute remise après la date/heure limite est **considérée en retard**,
  * les pénalités pour retard seront précisées dans l’énoncé (par exemple, pourcentage par jour de retard ou non-acceptation après un certain délai).
* Les problèmes techniques doivent être documentés (captures, messages d’erreur, logs) pour pouvoir être évalués et éventuellement pris en compte.

## 4.6 Plagiat et intégrité académique

* Le **plagiat**, le partage de solutions entre équipes lorsque cela n’est pas autorisé et toute forme de triche sont **strictement interdits**.
* Sont considérés comme plagiat, entre autres :

  * copier le dépôt d’un collègue,
  * reprendre un pipeline, un Dockerfile, un chart Helm, une configuration Ansible ou Terraform d’autrui sans le citer clairement,
  * soumettre un travail généré majoritairement par un outil d’IA sans compréhension ni adaptation réelle.
* En cas de doute, l’enseignant peut convoquer une **entrevue technique** pour vérifier la compréhension.
* Toute infraction sera traitée selon les **politiques officielles de l’établissement** (note zéro, sanction disciplinaire, etc.).

## 4.7 Communication et support

* Les communications officielles se feront via :

  * la plate-forme institutionnelle (Omnivox, Moodle, Teams, etc.),
  * le courriel institutionnel.
* Les questions techniques peuvent être :

  * posées en classe,
  * envoyées par message sur la plate-forme indiquée,
  * éventuellement discutées sur un canal dédié (Teams ou autre, si mis en place).
* Il est attendu que les étudiantes et étudiants :

  * consultent régulièrement les annonces,
  * lisent attentivement les consignes avant de poser une question,
  * formulent des questions claires et précises (contexte, ce qui a été tenté, message d’erreur…).

<br/>

# 5. Évaluations et pondération

L’évaluation du cours est structurée pour mesurer à la fois :

* la **compréhension des concepts** (examens),
* la **capacité à les appliquer** dans un contexte proche de la réalité (projet de session),
* et la **progression** tout au long de la session (travaux formatifs).

## 5.1 Vue d’ensemble des évaluations

| Évaluation              | Description synthétique                                                                 |                    Pondération |
| ----------------------- | --------------------------------------------------------------------------------------- | -----------------------------: |
| Examen de mi-session    | Examen écrit et/ou pratique couvrant les notions de Git, Jenkins, Maven, Docker, Swarm  |                       **25 %** |
| Projet de session       | Projet fil rouge DevOps/CI-CD, réalisé en équipe, avec présentation finale              |                       **40 %** |
| Examen final            | Examen intégrant l’ensemble de la session (Kubernetes, Helm, IaC, monitoring…)          |                       **35 %** |
| Travaux formatifs 1 à 8 | Exercices pratiques réalisés en cours + maison (non notés ou notés en bonus, selon cas) | 0 % (ou bonus, selon consigne) |

> Remarque : la pondération exacte des travaux formatifs (0 % ou bonus) sera confirmée par l’enseignant en début de session. Leur réalisation demeure **fortement recommandée**, car ils préparent directement aux évaluations sommatives.

<br/>

## 5.2 Travaux formatifs (1 à 8)

* Chaque travail formatif est **introduit en classe** et **poursuivi à la maison**.
* Ils permettent de :

  * consolider les apprentissages,
  * construire progressivement le pipeline et l’infrastructure exigés dans le projet et les examens,
  * découvrir des cas d’erreur réels (builds qui échouent, pods en erreur, webhooks mal configurés, etc.).
* Même s’ils ne comptent pas directement dans la note finale (ou uniquement en bonus), ils sont conçus pour :

  * réduire fortement le risque d’échec à l’examen,
  * limiter la charge de travail en fin de session.

<br/>

## 5.3 Examen de mi-session (25 %)

* Couvre les semaines 1 à 5 :

  * Git et culture DevOps,
  * Jenkins (pipelines déclaratifs, Maven, intégration avec Git et Docker),
  * Docker, Swarm et Nginx (déploiement de base).
* Format possible :

  * questions écrites,
  * analyse de configuration,
  * mini-exercices pratiques (interpréter un Jenkinsfile, un Dockerfile, un log de déploiement, etc.).
* Objectif :

  * vérifier la maîtrise des **fondations techniques** nécessaires à la suite du cours.

<br/>

## 5.4 Projet de session (40 %)

* Projet réalisé en équipe (taille précisée par l’enseignant).
* Contenu typique :

  * dépôt Git structuré,
  * pipeline CI/CD complet,
  * déploiement sur Docker / Swarm puis Kubernetes,
  * monitoring avec Prometheus / Grafana,
  * usage de IaC (Ansible, Terraform, CloudFormation).
* Évaluation basée sur :

  * la **qualité technique** (pipelines, scripts, configuration),
  * la **robustesse** (capacité à déployer/rollback, gestion d’erreurs),
  * la **documentation** (README, diagrammes, instructions de déploiement),
  * la **présentation finale** (clarté, maîtrise du sujet, capacité à répondre aux questions).

<br/>

## 5.5 Examen final (35 %)

* Couvre l’ensemble du cours, avec accent sur :

  * Kubernetes avancé, Helm,
  * GitHub Actions,
  * webhooks, monitoring, observabilité,
  * IaC (Ansible, Terraform, CloudFormation).
* Peut inclure :

  * des questions d’architecture,
  * des analyses de pipelines,
  * des scénarios à corriger ou à améliorer.

<br/>

# 6. Attentes envers les étudiantes et étudiants

Pour réussir ce cours dans de bonnes conditions, il est attendu que les étudiantes et étudiants :

1. **Participent activement** aux cours et laboratoires.
2. **Travaillent régulièrement**, plutôt que de tout concentrer à la fin.
3. **Expérimentent** : tester, casser, corriger fait partie intégrante de l’apprentissage DevOps.
4. **Documentent** leurs travaux (README, captures d’écran, logs, schémas).
5. **Collaborent** de manière respectueuse et professionnelle, en particulier sur le projet de session.
6. **Demandent de l’aide tôt** en cas de difficultés : blocage technique, notions non comprises, surcharge, etc.

<br/>

# 7. Engagement de l’enseignant

L’enseignant s’engage à :

* Proposer des explications claires et structurées, en liant toujours la théorie à des **cas Concrets**.
* Fournir des **ressources de qualité** (dépôts d’exemples, scripts, configurations, documentation).
* Offrir un **feedback constructif** sur les travaux remis.
* Rester disponible, dans la mesure du possible, pour accompagner les étudiantes et étudiants tout au long de la session.

