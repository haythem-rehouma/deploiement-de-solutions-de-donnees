# Cours complet -- Jenkins, Groovy, Kubernetes et Services

Ce document est un cours de référence qui couvre toutes les notions nécessaires pour réussir les examens formatifs P01 à P04.

---

## Table des matières

1. [Culture DevOps et CI/CD](#1--culture-devops-et-cicd)
2. [Stratégies de déploiement](#2--stratégies-de-déploiement)
3. [Le rôle de Git dans DevOps](#3--le-rôle-de-git-dans-devops)
4. [Jenkins : architecture et concepts](#4--jenkins--architecture-et-concepts)
5. [Jenkinsfile et syntaxe Groovy](#5--jenkinsfile-et-syntaxe-groovy)
6. [Variables d'environnement dans Jenkins](#6--variables-denvironnement-dans-jenkins)
7. [Webhooks et déclencheurs](#7--webhooks-et-déclencheurs)
8. [Introduction à Kubernetes](#8--introduction-à-kubernetes)
9. [Architecture de Kubernetes](#9--architecture-de-kubernetes)
10. [kubectl : l'outil en ligne de commande](#10--kubectl--loutil-en-ligne-de-commande)
11. [Minikube et Kind : clusters locaux](#11--minikube-et-kind--clusters-locaux)
12. [Les Pods](#12--les-pods)
13. [Les Deployments et ReplicaSets](#13--les-deployments-et-replicasets)
14. [Les Services Kubernetes](#14--les-services-kubernetes)
15. [Namespaces et labels](#15--namespaces-et-labels)
16. [Aide-mémoire des commandes](#16--aide-mémoire-des-commandes)

---

# 1 -- Culture DevOps et CI/CD

## 1.1 -- Qu'est-ce que DevOps ?

DevOps est une **culture** et un ensemble de **pratiques** qui rapprochent les équipes de développement (Dev) et d'opérations (Ops). L'objectif est de :

- **Collaborer** : les développeurs et les administrateurs système travaillent ensemble plutôt qu'en silos.
- **Automatiser** : toutes les tâches répétitives (build, test, déploiement) sont automatisées.
- **Obtenir un feedback rapide** : on sait en quelques minutes si un changement casse quelque chose.

## 1.2 -- Qu'est-ce que CI/CD ?

CI/CD est l'épine dorsale technique du DevOps. Il se décompose en trois concepts :

| Concept | Signification | Description |
|---------|---------------|-------------|
| **CI** | Intégration continue | Les développeurs fusionnent leur code fréquemment. Chaque fusion déclenche automatiquement un build et des tests. |
| **CD** | Livraison continue | Le code testé est automatiquement prêt à être déployé en production (mais le déploiement reste manuel). |
| **CD** | Déploiement continu | Le code testé est automatiquement déployé en production sans intervention humaine. |

## 1.3 -- Le pipeline CI/CD

Un pipeline est une suite d'étapes automatisées qui transforment le code source en application déployée :

```
Code source → Build → Tests → Staging → Production
```

Chaque étape est appelée une **stage**. Si une étape échoue, le pipeline s'arrête et les développeurs sont notifiés.

## 1.4 -- Avantages de l'automatisation CI/CD

1. **Réduction des erreurs humaines** : les tâches répétitives sont automatisées, moins de risques d'oubli.
2. **Feedback rapide** : les développeurs savent en quelques minutes si leur code casse quelque chose.
3. **Déploiements plus fréquents** : on peut déployer plusieurs fois par jour en toute confiance.
4. **Reproductibilité** : chaque build est identique, pas de "ça marchait sur ma machine".
5. **Traçabilité** : chaque changement est enregistré, on peut savoir qui a changé quoi et quand.

---

# 2 -- Stratégies de déploiement

## 2.1 -- Pourquoi des stratégies de déploiement ?

Quand on met à jour une application en production, on veut éviter :
- les **temps d'arrêt** (downtime) : l'application est inaccessible pendant la mise à jour,
- les **erreurs en production** : la nouvelle version a un bug qui affecte tous les utilisateurs,
- les **rollbacks longs** : il faut des heures pour revenir à l'ancienne version.

Les stratégies de déploiement définissent **comment** on passe de l'ancienne version (v1) à la nouvelle version (v2) de manière contrôlée.

## 2.2 -- Vue d'ensemble des stratégies

| Stratégie | Temps d'arrêt | Risque | Complexité | Rollback |
|-----------|---------------|--------|------------|----------|
| **Recreate** | Oui | Élevé | Faible | Lent |
| **Rolling Update** | Non | Moyen | Faible | Rapide |
| **Blue/Green** | Non | Faible | Moyenne | Instantané |
| **Canary** | Non | Très faible | Élevée | Rapide |
| **A/B Testing** | Non | Très faible | Élevée | Rapide |

## 2.3 -- Recreate (Tout supprimer, tout recréer)

La stratégie la plus simple : on arrête **toutes** les instances de v1, puis on démarre **toutes** les instances de v2.

```
Étape 1 : v1 v1 v1  (3 instances actives)
Étape 2 : --- --- ---  (tout est arrêté → DOWNTIME)
Étape 3 : v2 v2 v2  (3 nouvelles instances)
```

**Avantages** :
- Simple à comprendre et à mettre en place.
- Pas de problème de compatibilité entre versions (v1 et v2 ne coexistent jamais).

**Inconvénients** :
- **Temps d'arrêt** : l'application est indisponible entre l'arrêt de v1 et le démarrage de v2.
- Mauvaise expérience utilisateur.

**Quand l'utiliser** : environnements de développement ou de test, applications non critiques.

En Kubernetes :

```yaml
spec:
  strategy:
    type: Recreate
```

## 2.4 -- Rolling Update (Mise à jour progressive)

C'est la stratégie **par défaut** dans Kubernetes. Les instances sont remplacées **une par une** (ou par petits groupes), sans interruption de service.

```
Étape 1 : v1 v1 v1      (état initial)
Étape 2 : v2 v1 v1      (1 instance remplacée)
Étape 3 : v2 v2 v1      (2 instances remplacées)
Étape 4 : v2 v2 v2      (terminé, aucun downtime)
```

**Avantages** :
- **Aucun temps d'arrêt** : il y a toujours des instances actives.
- Simple à configurer (c'est le défaut Kubernetes).
- Rollback rapide avec `kubectl rollout undo`.

**Inconvénients** :
- Pendant la mise à jour, v1 et v2 coexistent : il faut que les deux versions soient compatibles (surtout pour les API et les bases de données).
- Le déploiement est plus lent qu'un Recreate (on remplace une par une).

**Quand l'utiliser** : la majorité des cas en production.

En Kubernetes :

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # combien d'instances en plus pendant la mise à jour
      maxUnavailable: 0   # combien d'instances peuvent être indisponibles
```

- **maxSurge** : nombre d'instances supplémentaires autorisées pendant la mise à jour. Ex. si replicas=3 et maxSurge=1, il peut y avoir temporairement 4 instances.
- **maxUnavailable** : nombre d'instances qui peuvent être indisponibles. `0` signifie qu'on ne retire une vieille instance qu'après avoir vérifié que la nouvelle fonctionne.

### Commandes associées

```bash
kubectl rollout status deployment/mon-app      # Suivre la progression
kubectl rollout undo deployment/mon-app        # Rollback
kubectl rollout history deployment/mon-app     # Historique des versions
```

## 2.5 -- Blue/Green (Bleu/Vert)

On maintient **deux environnements identiques** :
- **Blue** : l'environnement actuel (v1), qui reçoit le trafic en production.
- **Green** : le nouvel environnement (v2), déployé en parallèle mais sans trafic.

Quand Green est prêt et testé, on **bascule le trafic** de Blue vers Green en un seul changement (généralement en changeant le Service Kubernetes ou le load balancer).

```
Avant la bascule :
  Utilisateurs → [Service] → Blue (v1)   ← trafic actif
                              Green (v2)  ← en attente, testé

Après la bascule :
  Utilisateurs → [Service] → Green (v2)  ← trafic actif
                              Blue (v1)   ← ancien, prêt pour rollback
```

**Avantages** :
- **Aucun temps d'arrêt**.
- **Rollback instantané** : il suffit de rebasculer le trafic vers Blue.
- On peut tester Green en conditions réelles avant la bascule.

**Inconvénients** :
- **Coût doublé** : il faut deux fois plus de ressources (deux environnements complets).
- Plus complexe à gérer que le Rolling Update.

**Quand l'utiliser** : applications critiques où le rollback doit être instantané, mises à jour majeures.

En Kubernetes, on peut le faire en changeant le selector du Service :

```yaml
# Avant (pointe vers Blue)
spec:
  selector:
    app: mon-app
    version: blue

# Après (pointe vers Green)
spec:
  selector:
    app: mon-app
    version: green
```

## 2.6 -- Canary (Déploiement canari)

On déploie la nouvelle version (v2) sur **un petit pourcentage** des instances (ex. 10%), et on observe le comportement. Si tout va bien, on augmente progressivement jusqu'à 100%.

Le nom vient des canaris dans les mines de charbon : si le canari meurt, il y a un danger.

```
Étape 1 : v1 v1 v1 v1 v1 v1 v1 v1 v1 v1   (100% v1)
Étape 2 : v2 v1 v1 v1 v1 v1 v1 v1 v1 v1   (10% v2, on observe)
Étape 3 : v2 v2 v2 v1 v1 v1 v1 v1 v1 v1   (30% v2, ça va bien)
Étape 4 : v2 v2 v2 v2 v2 v2 v2 v2 v2 v2   (100% v2, terminé)
```

Si un problème est détecté à l'étape 2 ou 3, on supprime les instances v2 et on revient à 100% v1. Seul un petit pourcentage d'utilisateurs a été impacté.

**Avantages** :
- **Risque minimal** : seuls quelques utilisateurs sont exposés à la nouvelle version.
- Permet de valider en conditions réelles avant le déploiement complet.
- Détection précoce des bugs.

**Inconvénients** :
- Plus complexe à mettre en place.
- Nécessite du monitoring pour observer les métriques (erreurs, latence, etc.).
- v1 et v2 coexistent : compatibilité requise.

**Quand l'utiliser** : applications à fort trafic, changements risqués, nouvelles fonctionnalités sensibles.

## 2.7 -- A/B Testing

Similaire au Canary, mais le routage est basé sur des **critères utilisateur** plutôt qu'un pourcentage aléatoire. Par exemple :
- Les utilisateurs en France voient la v2, les autres voient la v1.
- Les utilisateurs premium voient la v2, les gratuits voient la v1.
- Les utilisateurs avec un certain cookie voient la v2.

**Avantages** :
- Permet de mesurer l'impact d'un changement sur un groupe ciblé.
- Idéal pour tester des fonctionnalités UI/UX.

**Inconvénients** :
- Très complexe à configurer.
- Nécessite un routage intelligent (Istio, Nginx Ingress Controller, etc.).

**Quand l'utiliser** : tests de fonctionnalités, optimisation de l'expérience utilisateur.

## 2.8 -- Shadow (Déploiement fantôme)

Le trafic est **dupliqué** : les requêtes sont envoyées à v1 (qui répond aux utilisateurs) ET à v2 (qui traite les requêtes mais dont les réponses sont ignorées).

```
Utilisateurs → v1 (répond)
            → v2 (traite mais réponse ignorée, on compare les résultats)
```

**Avantages** :
- **Zéro risque** : les utilisateurs ne voient jamais v2.
- On peut comparer les résultats de v1 et v2 en conditions réelles.

**Inconvénients** :
- Consomme le double de ressources.
- Complexe à mettre en place.
- Ne fonctionne pas pour les opérations qui modifient des données (écriture en BDD).

**Quand l'utiliser** : migration de systèmes critiques, refactoring majeur.

## 2.9 -- Tableau récapitulatif

| Stratégie | Principe | Downtime | Rollback | Coût | Cas d'usage |
|-----------|----------|----------|----------|------|-------------|
| **Recreate** | Tout arrêter, tout redémarrer | Oui | Lent | Faible | Dev, test |
| **Rolling Update** | Remplacement progressif un par un | Non | Rapide | Normal | Production standard |
| **Blue/Green** | Deux environnements, bascule du trafic | Non | Instantané | Double | Applications critiques |
| **Canary** | Petite portion d'abord, puis augmentation | Non | Rapide | Normal+ | Fort trafic, changements risqués |
| **A/B Testing** | Routage par critères utilisateur | Non | Rapide | Normal+ | Tests UX, fonctionnalités |
| **Shadow** | Trafic dupliqué, v2 invisible | Non | N/A | Double | Migrations, refactoring |

## 2.10 -- Stratégies dans Kubernetes

Kubernetes supporte nativement deux stratégies dans un Deployment :

| Valeur | Comportement |
|--------|-------------|
| `RollingUpdate` | Par défaut. Remplacement progressif. |
| `Recreate` | Tout supprimer puis tout recréer. |

Pour Blue/Green, Canary, A/B Testing et Shadow, il faut utiliser des outils supplémentaires :
- **Kubernetes Services** (changer les selectors pour Blue/Green),
- **Istio** (service mesh pour le routage avancé),
- **Argo Rollouts** (extension Kubernetes pour Canary et Blue/Green),
- **Nginx Ingress Controller** (routage basé sur les headers).

---

# 3 -- Le rôle de Git dans DevOps

## 2.1 -- Git comme fondation

Git est le système de contrôle de version le plus utilisé. Dans un contexte DevOps, Git joue un rôle central :

- **Versionnement du code** : chaque modification est enregistrée avec un commit.
- **Collaboration** : les branches permettent de travailler en parallèle.
- **Déclencheur de pipelines** : un push sur Git peut déclencher automatiquement un build Jenkins.
- **Infrastructure as Code** : les fichiers de configuration (Jenkinsfile, Dockerfile, YAML Kubernetes) sont versionnés dans Git.

## 2.2 -- Les branches

- **main** (anciennement **master**) : la branche principale, contient le code stable.
- **Branches de fonctionnalités** : créées pour développer une nouvelle fonctionnalité, puis fusionnées dans main.

Attention : beaucoup de dépôts récents utilisent `main` au lieu de `master`. Dans Jenkins, vérifiez toujours le Branch Specifier (`*/main` ou `*/master`).

## 2.3 -- GitHub et les webhooks

GitHub est une plateforme d'hébergement de dépôts Git. Elle offre des fonctionnalités supplémentaires dont les **webhooks** : un mécanisme qui envoie une requête HTTP à une URL externe (par exemple Jenkins) lorsqu'un événement se produit (push, pull request, etc.).

---

# 4 -- Jenkins : architecture et concepts

## 4.1 -- Qu'est-ce que Jenkins ?

Jenkins est un **serveur d'automatisation CI/CD** open source. Il permet d'orchestrer automatiquement les étapes de build, test et déploiement d'un projet.

## 4.2 -- Architecture Controller / Agent

Jenkins fonctionne avec une architecture maître/esclave :

| Composant | Rôle |
|-----------|------|
| **Controller (Master)** | Le serveur Jenkins central. Il gère l'interface web, planifie les jobs, distribue le travail aux agents et stocke les résultats. |
| **Agent (Node)** | Une machine (locale ou distante) qui exécute réellement les commandes de build et de test. Un agent peut être Linux, Windows, macOS, ou même un conteneur Docker. |

Le Controller décide **quoi** faire. L'Agent fait **le travail**.

## 4.3 -- Les jobs

Un **job** (ou projet) est une tâche configurée dans Jenkins. Il existe plusieurs types :

| Type | Description |
|------|-------------|
| **Freestyle** | Job simple, configuré via l'interface graphique. Bon pour commencer. |
| **Pipeline** | Job défini en code (Jenkinsfile). Plus puissant, versionnable dans Git. |
| **Multibranch Pipeline** | Pipeline qui s'exécute automatiquement pour chaque branche d'un dépôt. |

Pour créer un job : **New Item** → choisir le type → **OK**.

Pour lancer un job manuellement : cliquer sur **Build Now**.

## 4.4 -- Le workspace

Le **workspace** est le répertoire local sur l'agent où Jenkins :
- clone le code source depuis Git,
- exécute les commandes de build et de test,
- stocke les fichiers générés.

Chaque job a son propre workspace. Il est nettoyé ou réutilisé selon la configuration.

## 4.5 -- Les plugins

Un **plugin** est une extension qui ajoute des fonctionnalités à Jenkins. Jenkins seul ne fait pas grand-chose ; ce sont les plugins qui le rendent puissant.

Exemples de plugins essentiels :

| Plugin | Rôle |
|--------|------|
| **Git Plugin** | Permet de cloner des dépôts Git |
| **Pipeline** | Active la syntaxe Pipeline (Jenkinsfile) |
| **Docker Pipeline** | Permet de manipuler Docker dans un pipeline |
| **GitHub Integration** | Intégration avec GitHub (webhooks, statuts) |
| **Credentials** | Gestion sécurisée des mots de passe et tokens |

Les plugins se gèrent dans **Manage Jenkins > Plugins**.

## 4.6 -- Les credentials

Les **credentials** sont des informations sensibles (mots de passe, tokens, clés SSH) stockées de manière sécurisée dans Jenkins. Elles sont utilisées pour :
- se connecter à GitHub (username + Personal Access Token),
- se connecter à Docker Hub,
- accéder à des serveurs distants.

On les configure dans **Manage Jenkins > Credentials**.

## 4.7 -- Configuration de Git dans Jenkins

Pour que Jenkins puisse cloner des dépôts, il faut configurer le chemin vers l'exécutable Git :

- **Linux** : `/usr/bin/git`
- **Windows** : `C:\Program Files\Git\bin\git.exe`

Cela se configure dans **Manage Jenkins > Tools > Git**.

---

# 5 -- Jenkinsfile et syntaxe Groovy

## 5.1 -- Qu'est-ce qu'un Jenkinsfile ?

Un **Jenkinsfile** est un fichier texte (sans extension) placé à la racine du dépôt Git. Il décrit le pipeline en code : c'est le principe du **Pipeline as Code**.

Avantages :
- **Versionné** : le pipeline est dans Git, on voit son historique.
- **Reproductible** : le même Jenkinsfile donne toujours le même pipeline.
- **Partageable** : tout le monde peut lire et modifier le pipeline.

Le Jenkinsfile est écrit en **Groovy**, un langage de la JVM (pas en Python, pas en Bash).

## 5.2 -- Les deux syntaxes

Jenkins supporte deux syntaxes de pipeline :

| Syntaxe | Description | Recommandation |
|---------|-------------|----------------|
| **Déclarative** | Structurée, avec des blocs obligatoires (`pipeline`, `agent`, `stages`). Plus facile à lire. | Recommandée pour la plupart des cas |
| **Scriptée (Scripted)** | Plus flexible, basée directement sur Groovy. Permet des logiques complexes. | Pour les cas avancés |

Dans ce cours, on utilise la syntaxe **déclarative**.

## 5.3 -- Structure d'un pipeline déclaratif

Voici la structure minimale :

```groovy
pipeline {
    agent any

    stages {
        stage('Nom de l étape') {
            steps {
                // commandes ici
            }
        }
    }
}
```

### Les blocs obligatoires

| Bloc | Rôle |
|------|------|
| `pipeline { }` | Enveloppe tout le pipeline |
| `agent any` | Indique sur quel agent exécuter le pipeline. `any` = n'importe quel agent disponible. |
| `stages { }` | Contient toutes les étapes (stages) |
| `stage('Nom') { }` | Une étape logique nommée (Checkout, Build, Test, Deploy...) |
| `steps { }` | Les commandes à exécuter dans cette étape |

## 5.4 -- Les commandes principales dans steps

### echo -- Afficher un message

```groovy
steps {
    echo 'Bonjour Jenkins'
}
```

`echo` est une commande Jenkins (pas shell) qui affiche un message dans les logs du build.

### sh -- Exécuter une commande shell (Linux/macOS)

```groovy
steps {
    sh 'echo "Hello from Linux"'
    sh 'python3 hello.py'
    sh 'javac HelloWorld.java'
    sh 'java HelloWorld'
}
```

`sh` exécute une commande dans un shell Bash sur un agent Linux ou macOS.

### bat -- Exécuter une commande batch (Windows)

```groovy
steps {
    bat 'echo "Hello from Windows"'
    bat 'python hello.py'
    bat 'javac HelloWorld.java'
    bat 'java HelloWorld'
}
```

`bat` exécute une commande dans un shell CMD sur un agent Windows.

Attention :
- Sur **Linux** : utiliser `python3` (car `python` pointe parfois vers Python 2).
- Sur **Windows** : utiliser `python` (car `python3` n'existe souvent pas).

### git -- Cloner un dépôt

```groovy
steps {
    git branch: 'main', url: 'https://github.com/moncompte/mon-projet.git'
}
```

La directive `git` de Jenkins est intégrée au système de pipeline. Elle est préférable à `sh 'git clone ...'` car elle :
- nettoie le workspace avant le checkout,
- gère les credentials de manière sécurisée,
- est compatible avec les triggers (webhook, polling).

### script -- Bloc de code Groovy

```groovy
steps {
    script {
        if (isUnix()) {
            sh 'echo "Linux"'
        } else {
            bat 'echo "Windows"'
        }
    }
}
```

Le bloc `script` permet d'écrire du code Groovy libre (conditions, boucles, variables).

## 5.5 -- Détection du système d'exploitation

La fonction `isUnix()` retourne :
- `true` si l'agent est Linux ou macOS
- `false` si l'agent est Windows

Patron classique pour un pipeline multi-OS :

```groovy
stage('Build') {
    steps {
        script {
            if (isUnix()) {
                sh 'javac HelloWorld.java'
                sh 'java HelloWorld'
                sh 'python3 hello.py'
            } else {
                bat 'javac HelloWorld.java'
                bat 'java HelloWorld'
                bat 'python hello.py'
            }
        }
    }
}
```

## 5.6 -- Stages classiques d'un pipeline

Voici les étapes que l'on retrouve le plus souvent :

| Stage | Description |
|-------|-------------|
| **Checkout** | Cloner le dépôt Git (`git branch: 'main', url: '...'`) |
| **Build** | Compiler le code (`javac`, `gcc`, `npm install`, etc.) |
| **Test** | Exécuter les tests automatisés (`pytest`, `junit`, etc.) |
| **Archive** | Archiver les artefacts produits (`archiveArtifacts`) |
| **Deploy** | Déployer l'application (copie sur serveur, push Docker, etc.) |
| **Cleanup** | Nettoyer les fichiers temporaires |

## 5.7 -- Le bloc post

Le bloc `post` s'exécute **après** toutes les stages, selon le résultat du build :

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'echo "Building..."'
            }
        }
    }

    post {
        always {
            echo 'Ce message apparaît toujours.'
        }
        success {
            echo 'Le build a réussi !'
        }
        failure {
            echo 'Le build a échoué.'
        }
    }
}
```

| Section | Quand elle s'exécute |
|---------|---------------------|
| `always` | Toujours, que le build réussisse ou échoue |
| `success` | Seulement si le build a réussi |
| `failure` | Seulement si le build a échoué |
| `unstable` | Si le build est instable (tests échoués mais build OK) |
| `cleanup` | Toujours, en tout dernier (nettoyage final) |

## 5.8 -- Fichiers obligatoires dans un dépôt pour Jenkins

Pour qu'un pipeline Jenkins fonctionne correctement, le dépôt GitHub doit contenir au minimum :

1. **Le code source** : `hello.py`, `HelloWorld.java`, ou tout script/programme du projet.
2. **README.md** : description du projet (bonne pratique).
3. **Jenkinsfile** : le fichier (sans extension) qui définit le pipeline.

## 5.9 -- Configurer un job Pipeline dans Jenkins

Voici les étapes dans l'ordre pour configurer un job Pipeline lié à un dépôt GitHub :

1. Créer un nouveau item de type **Pipeline** et cliquer sur OK.
2. Dans la section Pipeline, choisir **"Pipeline script from SCM"**.
3. Choisir **Git** comme SCM.
4. Indiquer le **Repository URL** (ex. `https://github.com/moncompte/mon-projet.git`).
5. Sélectionner les **Credentials** (username + Personal Access Token).
6. Dans **Branch Specifier**, écrire `*/main` (ou `*/master` selon le dépôt).
7. Cliquer sur **Apply** puis **Save**.

## 5.10 -- Exemple complet de Jenkinsfile

Voici un Jenkinsfile complet qui clone un dépôt, compile Java et exécute Python :

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/moncompte/hello-app.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'echo "Running on Unix"'
                        sh 'javac HelloWorld.java'
                        sh 'java HelloWorld'
                        sh 'python3 hello.py'
                    } else {
                        bat 'echo "Running on Windows"'
                        bat 'javac HelloWorld.java'
                        bat 'java HelloWorld'
                        bat 'python hello.py'
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline terminé.'
        }
    }
}
```

---

# 6 -- Variables d'environnement dans Jenkins

## 6.1 -- Le bloc environment

Le bloc `environment` permet de définir des variables d'environnement accessibles dans tout le pipeline :

```groovy
pipeline {
    agent any

    environment {
        JAVA_HOME   = '/usr/lib/jvm/java-21-openjdk-amd64'
        PYTHON_HOME = '/usr/bin'
        PATH = "${env.PATH}:${JAVA_HOME}/bin:${PYTHON_HOME}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'javac HelloWorld.java'
                sh 'java HelloWorld'
                sh 'python3 hello.py'
            }
        }
    }
}
```

## 6.2 -- Explications

- **JAVA_HOME** : chemin vers le dossier du JDK. On le trouve avec `readlink -f $(which java)`.
- **PYTHON_HOME** : chemin vers le dossier contenant `python3`. On le trouve avec `which python3`.
- **PATH** : on ajoute les chemins de Java et Python au PATH existant pour que Jenkins trouve les exécutables.
- **`${env.PATH}`** : référence au PATH existant de l'agent.
- **`${JAVA_HOME}`** : référence à la variable définie juste au-dessus (syntaxe Groovy avec `${}`).

## 6.3 -- Variables prédéfinies de Jenkins

Jenkins fournit automatiquement des variables :

| Variable | Description |
|----------|-------------|
| `env.BUILD_NUMBER` | Numéro du build actuel |
| `env.JOB_NAME` | Nom du job |
| `env.WORKSPACE` | Chemin vers le workspace |
| `env.BRANCH_NAME` | Nom de la branche (Multibranch Pipeline) |
| `env.BUILD_URL` | URL complète du build dans Jenkins |

---

# 7 -- Webhooks et déclencheurs

## 7.1 -- Les triggers (déclencheurs)

Un **trigger** est un mécanisme qui lance un build automatiquement, sans cliquer sur "Build Now".

Les deux principaux triggers :

| Trigger | Mécanisme | Avantage | Inconvénient |
|---------|-----------|----------|--------------|
| **Polling SCM** | Jenkins vérifie le dépôt à intervalle régulier (ex. toutes les 5 min) | Simple à configurer | Consomme des ressources, délai avant détection |
| **Webhook** | GitHub envoie une requête HTTP à Jenkins dès qu'un push a lieu | Réaction immédiate, pas de gaspillage | Nécessite que Jenkins soit accessible depuis Internet |

## 7.2 -- Polling SCM

Le polling interroge le dépôt Git à intervalle régulier. On le configure avec une expression cron dans le Jenkinsfile :

```groovy
pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *')  // vérifie toutes les 5 minutes
    }

    stages {
        // ...
    }
}
```

Ou dans l'interface Jenkins : **Build Triggers > Poll SCM** avec la syntaxe cron.

## 7.3 -- Webhook GitHub

Un webhook est un mécanisme **push** : c'est GitHub qui contacte Jenkins (et non l'inverse).

Fonctionnement :
1. Un développeur fait un push sur GitHub.
2. GitHub envoie une requête HTTP POST à l'URL de Jenkins (ex. `http://jenkins.example.com/github-webhook/`).
3. Jenkins reçoit la notification et lance le build.

Pour configurer :
1. Dans Jenkins : activer **GitHub hook trigger for GITScm polling** dans Build Triggers.
2. Dans GitHub : Settings > Webhooks > Add webhook > Payload URL = `http://<jenkins>/github-webhook/`.

Si Jenkins n'est pas accessible depuis Internet (ex. machine locale), on peut utiliser **ngrok** pour créer un tunnel temporaire.

---

# 8 -- Introduction à Kubernetes

## 8.1 -- Qu'est-ce que Kubernetes ?

Kubernetes (abrégé **K8s** : K + 8 lettres + s) est une plateforme d'orchestration de conteneurs qui automatise :
- le **déploiement** des applications conteneurisées,
- la **mise à l'échelle** (scaling),
- la **gestion** (redémarrage, load balancing, mises à jour).

Kubernetes a été créé par **Google**, basé sur leur système interne "Borg". Il est maintenant open source et géré par la **CNCF** (Cloud Native Computing Foundation).

## 8.2 -- Pourquoi Kubernetes ?

Sans Kubernetes, gérer des conteneurs en production est complexe :
- Que faire si un conteneur plante ? Il faut le redémarrer manuellement.
- Comment répartir la charge entre plusieurs conteneurs ?
- Comment mettre à jour sans interruption de service ?

Kubernetes résout tous ces problèmes automatiquement.

## 8.3 -- Docker Swarm vs Kubernetes

| Aspect | Docker Swarm | Kubernetes |
|--------|-------------|------------|
| Complexité | Plus simple à configurer | Plus complexe |
| Fonctionnalités | Basiques | Très riches |
| Communauté | Petite | Énorme, standard de l'industrie |
| Intégration Docker | Native | Via CRI (Container Runtime Interface) |
| Cas d'usage | Petits projets | Production à grande échelle |

---

# 9 -- Architecture de Kubernetes

## 9.1 -- Le cluster

Un **cluster** Kubernetes est un ensemble de machines (appelées **nodes**) qui travaillent ensemble. Le cluster est composé de :

- **Le Control Plane** (anciennement Master) : le cerveau.
- **Les Workers** : les bras.

```
                    ┌─────────────────┐
                    │  Control Plane  │
                    │  (cerveau)      │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
        ┌─────┴─────┐ ┌─────┴─────┐ ┌─────┴─────┐
        │  Worker 1 │ │  Worker 2 │ │  Worker 3 │
        │  (pods)   │ │  (pods)   │ │  (pods)   │
        └───────────┘ └───────────┘ └───────────┘
```

## 9.2 -- Le Control Plane

Le Control Plane prend les décisions pour le cluster :

| Composant | Rôle |
|-----------|------|
| **API Server** | Point d'entrée de toutes les commandes kubectl |
| **Scheduler** | Décide sur quel Worker placer un nouveau Pod |
| **Controller Manager** | Surveille l'état du cluster et corrige les écarts (ex. recrée un Pod mort) |
| **etcd** | Base de données clé-valeur qui stocke l'état du cluster |

## 9.3 -- Les Workers (Nodes)

Les Workers exécutent les conteneurs :

| Composant | Rôle |
|-----------|------|
| **kubelet** | Agent qui tourne sur chaque Worker, gère les Pods locaux |
| **kube-proxy** | Gère le réseau, achemine le trafic vers les bons Pods |
| **Container Runtime** | Le moteur qui exécute les conteneurs (Docker, containerd, CRI-O) |

## 9.4 -- Un Node, c'est quoi ?

Un **Node** est une machine (physique ou virtuelle) dans le cluster. Il peut être :
- Un node **Master** (Control Plane)
- Un node **Worker**

## 9.5 -- L'état désiré (Desired State)

Kubernetes fonctionne sur le principe de l'**état désiré** :

1. Vous décrivez ce que vous voulez dans un fichier **YAML** (ex. "je veux 3 copies de mon application").
2. Kubernetes compare en permanence l'**état actuel** du cluster à l'**état désiré**.
3. S'il y a un écart (ex. un Pod a planté, il n'en reste que 2), Kubernetes agit pour le corriger (il recrée le Pod manquant).

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mon-app
spec:
  replicas: 3        # <-- état désiré : 3 copies
  selector:
    matchLabels:
      app: mon-app
  template:
    metadata:
      labels:
        app: mon-app
    spec:
      containers:
      - name: mon-app
        image: nginx:latest
        ports:
        - containerPort: 80
```

---

# 10 -- kubectl : l'outil en ligne de commande

## 10.1 -- Qu'est-ce que kubectl ?

**kubectl** (prononcé "kube-control" ou "kube-C-T-L") est l'outil en ligne de commande pour communiquer avec un cluster Kubernetes. C'est la commande principale pour créer, modifier, supprimer et observer les ressources.

## 10.2 -- Commandes essentielles

### Voir les ressources

```bash
kubectl get pods                  # Lister les pods
kubectl get pods -A               # Lister les pods de TOUS les namespaces
kubectl get deployments           # Lister les deployments
kubectl get services              # Lister les services
kubectl get nodes                 # Lister les nodes du cluster
```

### Détails d'une ressource

```bash
kubectl describe pod <nom>        # Détails complets d'un pod
kubectl describe deployment <nom> # Détails d'un deployment
kubectl describe service <nom>    # Détails d'un service
```

### Créer et appliquer

```bash
kubectl apply -f <fichier.yaml>   # Créer ou mettre à jour (recommandé)
kubectl create -f <fichier.yaml>  # Créer seulement (erreur si existe déjà)
```

Différence importante :
- `kubectl create` : crée une ressource. **Échoue** si elle existe déjà.
- `kubectl apply` : crée la ressource si elle n'existe pas, ou la **met à jour** si elle existe déjà. C'est la méthode **recommandée** (idempotente).

### Supprimer

```bash
kubectl delete pod <nom>
kubectl delete deployment <nom>
kubectl delete service <nom>
kubectl delete -f <fichier.yaml>  # Supprime tout ce qui est dans le fichier
```

### Logs et debugging

```bash
kubectl logs <nom-du-pod>         # Voir les logs d'un pod
kubectl logs -f <nom-du-pod>      # Suivre les logs en temps réel
kubectl exec -it <nom> -- /bin/sh # Ouvrir un shell dans un pod
```

---

# 11 -- Minikube et Kind : clusters locaux

## 11.1 -- Minikube

**Minikube** est un outil qui crée un cluster Kubernetes local sur un seul ordinateur. Idéal pour apprendre et tester.

### Commandes Minikube

```bash
minikube start                    # Démarrer le cluster
minikube start --driver=docker    # Démarrer avec Docker comme backend
minikube stop                     # Arrêter sans perdre les données
minikube delete                   # Supprimer complètement le cluster
minikube status                   # Vérifier l'état
minikube dashboard                # Ouvrir le dashboard web
minikube service <nom> --url      # Obtenir l'URL d'un service
```

## 11.2 -- Kind (Kubernetes in Docker)

**Kind** est un outil qui exécute des clusters Kubernetes en utilisant des conteneurs Docker comme "noeuds". Plus léger que Minikube.

### Commandes Kind

```bash
kind create cluster --name mon-cluster                       # Créer un cluster
kind create cluster --name mon-cluster --config config.yaml  # Avec configuration
kind delete cluster --name mon-cluster                       # Supprimer un cluster
kind get clusters                                            # Lister les clusters
```

## 11.3 -- Minikube vs Kind

| Aspect | Minikube | Kind |
|--------|----------|------|
| Backend | VM ou Docker | Docker uniquement |
| Multi-noeuds | Limité | Facile |
| Ressources | Plus lourd | Plus léger |
| Dashboard | Intégré (`minikube dashboard`) | À configurer manuellement |
| Cas d'usage | Apprentissage, développement | Tests, CI/CD |

## 11.4 -- Changer de contexte

Si vous avez plusieurs clusters (un Minikube et un Kind par exemple), vous pouvez basculer de l'un à l'autre :

```bash
kubectl config get-contexts           # Lister les contextes disponibles
kubectl config use-context <nom>      # Basculer vers un contexte
kubectl config use-context minikube   # Exemple : revenir à Minikube
kubectl config use-context kind-mon-cluster  # Exemple : aller sur Kind
```

---

# 12 -- Les Pods

## 12.1 -- Qu'est-ce qu'un Pod ?

Un **Pod** est la plus petite unité déployable dans Kubernetes. C'est un groupe d'un ou plusieurs conteneurs qui partagent :
- le même **réseau** (même IP, mêmes ports),
- le même **stockage** (volumes partagés).

Dans la majorité des cas (90%), un Pod contient **un seul conteneur**.

## 12.2 -- Créer un Pod

### Par commande

```bash
kubectl run mon-pod --image=nginx
```

### Par fichier YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mon-pod
  labels:
    app: mon-app
spec:
  containers:
  - name: mon-conteneur
    image: nginx:latest
    ports:
    - containerPort: 80
```

```bash
kubectl apply -f mon-pod.yaml
```

## 12.3 -- Gérer les Pods

```bash
kubectl get pods                      # Lister
kubectl describe pod mon-pod          # Détails
kubectl logs mon-pod                  # Logs
kubectl logs -f mon-pod               # Logs en temps réel
kubectl exec -it mon-pod -- /bin/sh   # Shell interactif (ici nous supposons que le Pod a un seul conteneur sinon ajouter -c nom-du-conteneur , exemple kubectl exec -it mon-pod -c nom-du-conteneur -- /bin/sh)
kubectl delete pod mon-pod            # Supprimer
```

## 12.4 -- Cycle de vie d'un Pod

Un Pod est **éphémère** : il peut être créé, détruit et recréé à tout moment. C'est pourquoi on ne crée presque jamais un Pod seul en production : on utilise un **Deployment**.

---

# 13 -- Les Deployments et ReplicaSets

## 13.1 -- Pourquoi un Deployment ?

Un Pod seul n'offre aucune garantie :
- S'il plante, personne ne le recrée.
- On ne peut pas avoir plusieurs copies.
- Pas de mise à jour progressive.

Un **Deployment** résout tous ces problèmes :

| Fonctionnalité | Pod seul | Deployment |
|----------------|----------|------------|
| Plusieurs copies (replicas) | Non | Oui |
| Redémarrage automatique | Non | Oui |
| Mise à jour progressive (rolling update) | Non | Oui |
| Rollback (retour en arrière) | Non | Oui |
| Historique des versions | Non | Oui |

## 13.2 -- Créer un Deployment

### Par fichier YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mon-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mon-app
  template:
    metadata:
      labels:
        app: mon-app
    spec:
      containers:
      - name: mon-app
        image: nginx:1.25
        ports:
        - containerPort: 80
```

```bash
kubectl apply -f mon-deployment.yaml
```

## 13.3 -- Le ReplicaSet

Le **ReplicaSet** est le composant qui maintient un nombre spécifié de replicas de Pods en cours d'exécution. En pratique, on ne crée **jamais** de ReplicaSet directement : c'est le Deployment qui le gère automatiquement.

### Hiérarchie des objets

```
Deployment (gestion des mises à jour et rollbacks)
    │
    └── ReplicaSet (gestion des replicas)
            │
            └── Pod (unité d'exécution)
            └── Pod
            └── Pod
```

## 13.4 -- Scaler un Deployment

Changer le nombre de replicas :

```bash
kubectl scale deployment mon-app --replicas=5
```

Ou modifier le YAML :

```yaml
spec:
  replicas: 5
```

```bash
kubectl apply -f mon-deployment.yaml
```

## 13.5 -- Mettre à jour un Deployment

Changer l'image d'un Deployment :

```bash
kubectl set image deployment/mon-app mon-app=nginx:1.26
```

Cela déclenche un **rolling update** : Kubernetes remplace les anciens Pods par les nouveaux un par un (ou par petits groupes), **sans interruption de service**.

### Suivre la progression

```bash
kubectl rollout status deployment/mon-app
```

## 13.6 -- Rollback (retour en arrière)

Si la nouvelle version pose problème, on peut revenir à la version précédente :

```bash
kubectl rollout undo deployment/mon-app
```

Voir l'historique des versions :

```bash
kubectl rollout history deployment/mon-app
```

## 13.7 -- Que se passe-t-il quand un Pod plante ?

Si un Deployment est configuré avec `replicas: 3` et qu'un Pod est supprimé ou plante :

1. Le ReplicaSet détecte qu'il n'y a plus que 2 Pods (au lieu de 3).
2. Il crée automatiquement un nouveau Pod pour revenir à l'état désiré (3 replicas).
3. Aucune intervention humaine n'est nécessaire.

---

# 14 -- Les Services Kubernetes

## 14.1 -- Pourquoi les Services ?

Les adresses IP des Pods **changent** à chaque fois qu'ils sont recréés. On ne peut pas compter sur l'IP d'un Pod pour communiquer.

Un **Service** fournit :
- une **IP stable** qui ne change pas,
- un **nom DNS** (ex. `mon-service.default`),
- du **load balancing** automatique entre les Pods.

## 14.2 -- Les 4 types de Services

| Type | Accès | Cas d'usage |
|------|-------|-------------|
| **ClusterIP** | Interne au cluster uniquement | Communication entre microservices (backend vers BDD, etc.) |
| **NodePort** | Externe, via un port sur chaque node (30000-32767) | Tests, développement, accès simple depuis l'extérieur |
| **LoadBalancer** | Externe, via un load balancer cloud (IP publique) | Production sur AWS, GCP, Azure |
| **ExternalName** | Alias DNS vers un service externe | Accéder à une BDD cloud via un nom interne |

### Type par défaut

Si aucun type n'est spécifié, Kubernetes crée un **ClusterIP**.

## 14.3 -- ClusterIP (par défaut)

Accessible uniquement depuis l'intérieur du cluster. Utilisé pour la communication interne entre services.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-service
spec:
  type: ClusterIP       # optionnel, c'est le défaut
  selector:
    app: mon-app         # cible les Pods avec le label app=mon-app
  ports:
  - port: 80             # port du Service
    targetPort: 8080     # port dans le conteneur
```

## 14.4 -- NodePort

Expose le service sur un port de chaque node du cluster (plage 30000 à 32767).

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-service
spec:
  type: NodePort
  selector:
    app: mon-app
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30080      # port externe (optionnel, Kubernetes peut le choisir)
```

### Différence entre les ports

| Port | Description |
|------|-------------|
| `port` | Le port du Service (interne au cluster) |
| `targetPort` | Le port sur lequel l'application écoute à l'intérieur du conteneur |
| `nodePort` | Le port exposé sur chaque noeud du cluster (accessible de l'extérieur) |

## 14.5 -- LoadBalancer

Expose le service via un load balancer externe fourni par le fournisseur cloud (AWS, GCP, Azure). Il distribue le trafic entrant vers les Pods et fournit une IP publique.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-service
spec:
  type: LoadBalancer
  selector:
    app: mon-app
  ports:
  - port: 80
    targetPort: 8080
```

## 14.6 -- ExternalName

Mappe un service Kubernetes à un nom DNS externe :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ma-bdd
spec:
  type: ExternalName
  externalName: db.example.com
```

Les Pods du cluster peuvent appeler `ma-bdd` et être redirigés vers `db.example.com`.

## 14.7 -- Comment un Service trouve ses Pods : selectors et labels

Les **labels** sont des paires clé/valeur attachées aux objets Kubernetes :

```yaml
metadata:
  labels:
    app: mon-app
    env: production
```

Le Service utilise un **selector** pour cibler les Pods :

```yaml
spec:
  selector:
    app: mon-app    # tous les Pods avec label app=mon-app
```

Tous les Pods ayant le label `app: mon-app` recevront le trafic du Service.

## 14.8 -- DNS interne

Kubernetes a un DNS interne. Un Pod peut appeler un autre service par son nom :

```bash
curl http://mon-service              # même namespace
curl http://mon-service.production   # namespace différent
```

## 14.9 -- Créer un Service rapidement

Sans fichier YAML, on peut exposer un Deployment directement :

```bash
kubectl expose deployment mon-app --type=NodePort --port=80
```

### Accéder au service dans Minikube

```bash
minikube service mon-service --url
```

Cette commande retourne l'URL complète pour accéder au service depuis la machine hôte.

---

# 15 -- Namespaces et labels

## 15.1 -- Les namespaces

Un **namespace** est un mécanisme d'isolation logique qui partitionne les ressources d'un cluster. Il permet de :

- séparer les environnements (dev, staging, production),
- séparer les équipes,
- appliquer des quotas de ressources par namespace.

### Namespaces par défaut

| Namespace | Contenu |
|-----------|---------|
| `default` | Les ressources créées sans spécifier de namespace |
| `kube-system` | Les composants internes de Kubernetes |
| `kube-public` | Ressources accessibles publiquement |

### Commandes

```bash
kubectl create namespace production                # Créer un namespace
kubectl get namespaces                             # Lister les namespaces
kubectl get pods -n production                     # Pods d'un namespace
kubectl get pods -A                                # Pods de TOUS les namespaces
kubectl apply -f fichier.yaml -n production        # Appliquer dans un namespace
```

## 15.2 -- Les labels

Les **labels** sont des paires clé/valeur attachées à n'importe quel objet Kubernetes. Ils servent à :

- **organiser** les ressources (par application, environnement, équipe),
- **filtrer** les ressources avec des selectors,
- **lier** un Service à des Pods.

### Exemples de labels

```yaml
metadata:
  labels:
    app: mon-app
    env: production
    team: backend
```

### Filtrer par label

```bash
kubectl get pods -l app=mon-app              # Pods avec label app=mon-app
kubectl get pods -l env=production           # Pods avec label env=production
kubectl get pods -l app=mon-app,env=prod     # Combinaison
```

---

# 16 -- Aide-mémoire des commandes

## Jenkins / Jenkinsfile

| Commande Groovy | Description |
|-----------------|-------------|
| `echo 'message'` | Afficher un message dans les logs Jenkins |
| `sh 'commande'` | Exécuter une commande shell (Linux/macOS) |
| `bat 'commande'` | Exécuter une commande batch (Windows) |
| `git branch: 'main', url: '...'` | Cloner un dépôt Git |
| `isUnix()` | Retourne `true` si l'agent est Linux/macOS |
| `script { ... }` | Bloc de code Groovy libre |
| `environment { ... }` | Définir des variables d'environnement |
| `post { always { } }` | Actions post-build |

## Minikube

```bash
minikube start                    # Démarrer
minikube stop                     # Arrêter (conserve les données)
minikube delete                   # Supprimer
minikube status                   # État
minikube dashboard                # Dashboard web
minikube service <nom> --url      # URL d'un service
```

## Kind

```bash
kind create cluster --name <nom>         # Créer
kind delete cluster --name <nom>         # Supprimer
kind get clusters                        # Lister
kubectl config use-context kind-<nom>    # Changer de contexte
```

## kubectl -- Ressources

```bash
kubectl get pods / deployments / services / nodes
kubectl get pods -A                      # Tous les namespaces
kubectl get pods -n <namespace>          # Un namespace spécifique
kubectl get pods -l app=mon-app          # Filtrer par label
kubectl describe <type> <nom>            # Détails
```

## kubectl -- Pods

```bash
kubectl run <nom> --image=<image>        # Créer un pod
kubectl logs <nom>                       # Logs
kubectl logs -f <nom>                    # Logs en temps réel
kubectl exec -it <nom> -- /bin/sh        # Shell interactif
kubectl delete pod <nom>                 # Supprimer
```

## kubectl -- Deployments

```bash
kubectl apply -f <fichier.yaml>                               # Créer / mettre à jour
kubectl scale deployment <nom> --replicas=<n>                  # Scaler
kubectl set image deployment/<nom> <conteneur>=<image:tag>     # Mettre à jour l'image
kubectl rollout status deployment/<nom>                        # Suivre le déploiement
kubectl rollout undo deployment/<nom>                          # Rollback
kubectl rollout history deployment/<nom>                       # Historique
```

## kubectl -- Services

```bash
kubectl expose deployment <nom> --type=NodePort --port=80      # Créer un service
kubectl get services                                           # Lister
kubectl describe service <nom>                                 # Détails
kubectl delete service <nom>                                   # Supprimer
```

## kubectl -- Namespaces

```bash
kubectl create namespace <nom>                                 # Créer
kubectl get namespaces                                         # Lister
kubectl config use-context <nom>                               # Changer de contexte
kubectl config get-contexts                                    # Lister les contextes
```


