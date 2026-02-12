# Examen blanc -- Jenkins, Groovy, Kubernetes (sans corrige)

## Modules couverts

Cet examen porte sur l'ensemble des notions des modules suivants :
- **01 - Introduction a Git et DevOps** : culture DevOps, CI/CD, strategies de deploiement
- **02 - Jenkins CI/CD** : installation, jobs, pipelines, agents, webhooks, Jenkinsfile
- **03 - Jenkinsfile, Docker et Registry** : Jenkinsfile avance, variables d'environnement, detection d'OS
- **05 - Kubernetes : deploiement de base** : concepts fondamentaux, architecture, kubectl, Pods, Deployments
- **06 - Kubernetes suite** : Services, Kind, namespaces, labels, selectors

## Consignes

- Repondez a toutes les questions directement sur cette feuille.
- Aucune reponse n'est fournie : cet examen simule les conditions reelles.
- Duree estimee : 60 minutes.
- Score total : /60

---

# Partie A -- Jenkins : fondamentaux (15 points)

---

## Section A1 -- QCM (5 points, 1 point par question)

Cochez **une seule** bonne reponse par question.

---

**Q1.** Quel est le role principal de Jenkins ?

- [ ] A. Gerer des bases de donnees relationnelles
- [ ] B. Automatiser les taches CI/CD (build, test, deploiement)
- [ ] C. Heberger des depots Git
- [ ] D. Remplacer Docker

Votre reponse : ______

---

**Q2.** Comment s'appelle le fichier (sans extension) place a la racine d'un depot pour definir un pipeline Jenkins ?

- [ ] A. pipeline.groovy
- [ ] B. Jenkinsfile
- [ ] C. build.xml
- [ ] D. .jenkins.yml

Votre reponse : ______

---

**Q3.** Dans l'architecture Jenkins, quel composant execute reellement les commandes de build ?

- [ ] A. Le Controller (Master)
- [ ] B. L'Agent (Node)
- [ ] C. Le Plugin Manager
- [ ] D. Le Workspace

Votre reponse : ______

---

**Q4.** Quelle commande Groovy permet d'executer un script shell sur un agent Linux ?

- [ ] A. `bat 'commande'`
- [ ] B. `run 'commande'`
- [ ] C. `sh 'commande'`
- [ ] D. `exec 'commande'`

Votre reponse : ______

---

**Q5.** Quelle est la difference entre un pipeline "Declaratif" et un pipeline "Scripte" ?

- [ ] A. Le declaratif utilise Python, le scripte utilise Groovy
- [ ] B. Le declaratif a une structure imposee (pipeline/agent/stages), le scripte est du Groovy libre
- [ ] C. Le declaratif ne supporte pas les stages
- [ ] D. Il n'y a aucune difference

Votre reponse : ______

---

## Section A2 -- Vrai ou Faux (5 points, 1 point par question)

Ecrivez **Vrai** ou **Faux** pour chaque affirmation.

---

**Q6.** Le Controller Jenkins est la machine qui execute les commandes de build.

Votre reponse : ______

---

**Q7.** Un webhook permet a GitHub d'envoyer une notification a Jenkins immediatement apres un push.

Votre reponse : ______

---

**Q8.** La commande `bat` dans un Jenkinsfile s'execute sur un agent Linux.

Votre reponse : ______

---

**Q9.** Le bloc `post { always { } }` dans un Jenkinsfile s'execute uniquement si le build reussit.

Votre reponse : ______

---

**Q10.** Le Jenkinsfile est ecrit en langage Groovy.

Votre reponse : ______

---

## Section A3 -- Reponses courtes (5 points)

---

**Q11.** (2 points) Expliquez la difference entre le **polling SCM** et un **webhook** pour declencher un build Jenkins. Donnez un avantage de chaque methode.

Votre reponse :

```
_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________
```

---

**Q12.** (1 point) Quel est le chemin de l'executable Git sur Windows que l'on configure dans Jenkins ?

Votre reponse : ______

---

**Q13.** (2 points) Nommez 3 plugins essentiels de Jenkins et decrivez brievement leur role.

Votre reponse :

```
1. _______________________________________________________________

2. _______________________________________________________________

3. _______________________________________________________________
```

---

# Partie B -- Jenkinsfile et Groovy (15 points)

---

## Section B1 -- Completion de Jenkinsfile (10 points)

---

**Q14.** (3 points) Completez ce Jenkinsfile pour qu'il clone un depot Git et affiche un message :

```groovy
pipeline {
    ______ any

    stages {
        stage('Checkout') {
            steps {
                ______ branch: 'main', url: 'https://github.com/user/projet.git'
            }
        }

        stage('Info') {
            steps {
                ______ 'Pipeline demarre avec succes'
            }
        }
    }
}
```

Vos reponses :
- Blanc 1 : ______
- Blanc 2 : ______
- Blanc 3 : ______

---

**Q15.** (4 points) Completez ce pipeline multi-OS qui compile Java et execute Python :

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                ______ {
                    if (______) {
                        sh 'javac HelloWorld.java'
                        sh 'java HelloWorld'
                        ______ 'python3 hello.py'
                    } else {
                        bat 'javac HelloWorld.java'
                        bat 'java HelloWorld'
                        ______ 'python hello.py'
                    }
                }
            }
        }
    }
}
```

Vos reponses :
- Blanc 1 : ______
- Blanc 2 : ______
- Blanc 3 : ______
- Blanc 4 : ______

---

**Q16.** (3 points) Completez le bloc `environment` pour configurer Java et Python :

```groovy
pipeline {
    agent any

    environment {
        JAVA_HOME   = '/usr/lib/jvm/java-21-openjdk-amd64'
        PYTHON_HOME = '/usr/bin'
        PATH = "${______}:${JAVA_HOME}/bin:${______}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'javac HelloWorld.java'
            }
        }
    }

    ______ {
        always {
            echo 'Pipeline termine.'
        }
    }
}
```

Vos reponses :
- Blanc 1 : ______
- Blanc 2 : ______
- Blanc 3 : ______

---

## Section B2 -- Questions ouvertes (5 points)

---

**Q17.** (2 points) Quels sont les 3 fichiers minimum qu'un depot GitHub doit contenir pour fonctionner avec un pipeline Jenkins ?

Votre reponse :

```
1. ______
2. ______
3. ______
```

---

**Q18.** (3 points) Remettez ces etapes dans le bon ordre (numerotez de 1 a 7) pour configurer un job Pipeline dans Jenkins :

- ( ) Cliquer sur Apply puis Save
- ( ) Creer un nouveau item de type Pipeline et cliquer sur OK
- ( ) Dans Pipeline, choisir "Pipeline script from SCM"
- ( ) Choisir Git comme SCM
- ( ) Indiquer le Repository URL
- ( ) Selectionner les Credentials (username + token)
- ( ) Dans Branch Specifier, ecrire `*/main`

---

# Partie C -- Kubernetes : bases (15 points)

---

## Section C1 -- Concepts fondamentaux (6 points)

---

**Q19.** (1 point) Qu'est-ce que Kubernetes en une phrase ?

Votre reponse :

```
_______________________________________________________________
```

---

**Q20.** (1 point) Que signifie l'abreviation "K8s" ?

Votre reponse : ______

---

**Q21.** (2 points) Nommez les 4 composants du Control Plane et donnez le role de chacun en une phrase.

Votre reponse :

```
1. _______________________________________________________________

2. _______________________________________________________________

3. _______________________________________________________________

4. _______________________________________________________________
```

---

**Q22.** (2 points) Qu'est-ce que l'"etat desire" (desired state) dans Kubernetes ? Comment Kubernetes reagit-il si l'etat actuel ne correspond plus a l'etat desire ?

Votre reponse :

```
_______________________________________________________________

_______________________________________________________________

_______________________________________________________________
```

---

## Section C2 -- Commandes kubectl (5 points, 1 point par question)

Donnez la commande kubectl pour chaque action.

---

**Q23.** Lister tous les pods du namespace actuel.

Votre reponse : ______

---

**Q24.** Voir les details complets d'un pod nomme `web-app`.

Votre reponse : ______

---

**Q25.** Ouvrir un shell interactif dans un pod nomme `web-app`.

Votre reponse : ______

---

**Q26.** Appliquer (creer ou mettre a jour) une ressource a partir d'un fichier YAML `deployment.yaml`.

Votre reponse : ______

---

**Q27.** Changer le nombre de replicas du deployment `mon-app` a 5.

Votre reponse : ______

---

## Section C3 -- Pods et Deployments (4 points)

---

**Q28.** (1 point) Quelle est la plus petite unite deployable dans Kubernetes ?

Votre reponse : ______

---

**Q29.** (1 point) Pourquoi ne cree-t-on presque jamais un Pod seul en production ? Que cree-t-on a la place ?

Votre reponse :

```
_______________________________________________________________
```

---

**Q30.** (1 point) Quelle est la hierarchie des objets : Deployment, ReplicaSet, Pod ? (du plus haut au plus bas)

Votre reponse : ______  →  ______  →  ______

---

**Q31.** (1 point) Quelle commande permet de revenir a la version precedente d'un Deployment ?

Votre reponse : ______

---

# Partie D -- Kubernetes : Services et avance (10 points)

---

## Section D1 -- Les Services (5 points)

---

**Q32.** (1 point) Quel type de Service est cree par defaut si aucun type n'est specifie ?

Votre reponse : ______

---

**Q33.** (2 points) Completez ce tableau :

| Type de Service | Accessible depuis | Cas d'usage |
|-----------------|-------------------|-------------|
| ClusterIP       | _________________ | _________________ |
| NodePort        | _________________ | _________________ |
| LoadBalancer    | _________________ | _________________ |

---

**Q34.** (1 point) Dans un Service de type NodePort, quelle est la plage de ports autorisee ?

Votre reponse : ______

---

**Q35.** (1 point) Quelle commande permet d'obtenir l'URL d'un Service dans Minikube ?

Votre reponse : ______

---

## Section D2 -- Kind, namespaces et labels (5 points)

---

**Q36.** (1 point) Quelle commande Kind permet de creer un cluster nomme `test-cluster` ?

Votre reponse : ______

---

**Q37.** (1 point) Quelle commande permet de basculer vers le contexte du cluster Kind nomme `test-cluster` ?

Votre reponse : ______

---

**Q38.** (1 point) A quoi servent les **namespaces** dans Kubernetes ?

Votre reponse :

```
_______________________________________________________________
```

---

**Q39.** (1 point) Quelle commande liste les pods de **tous** les namespaces ?

Votre reponse : ______

---

**Q40.** (1 point) A quoi servent les **labels** dans Kubernetes ? Comment un Service utilise-t-il les labels ?

Votre reponse :

```
_______________________________________________________________

_______________________________________________________________
```

---

# Partie E -- CI/CD, strategies de deploiement et culture DevOps (25 points)

---

## Section E1 -- Culture DevOps et CI/CD : concepts (8 points)

---

**Q41.** (2 points) Qu'est-ce que DevOps ? Nommez les 3 piliers fondamentaux de la culture DevOps et expliquez chacun en une phrase.

Votre reponse :

```
Definition : ______________________________________________________

_______________________________________________________________

Pilier 1 : ________________________________________________________

_______________________________________________________________

Pilier 2 : ________________________________________________________

_______________________________________________________________

Pilier 3 : ________________________________________________________

_______________________________________________________________
```

---

**Q42.** (2 points) Expliquez la difference entre **Integration continue (CI)**, **Livraison continue (CD - Continuous Delivery)** et **Deploiement continu (CD - Continuous Deployment)**.

Votre reponse :

```
Integration continue : _____________________________________________

_______________________________________________________________

Livraison continue : _______________________________________________

_______________________________________________________________

Deploiement continu : ______________________________________________

_______________________________________________________________
```

---

**Q43.** (2 points) Decrivez les 5 etapes classiques d'un pipeline CI/CD, de la reception du code jusqu'a la mise en production.

Votre reponse :

```
1. _______________________________________________________________

2. _______________________________________________________________

3. _______________________________________________________________

4. _______________________________________________________________

5. _______________________________________________________________
```

---

**Q44.** (2 points) Citez 4 avantages concrets de l'automatisation CI/CD pour une equipe de developpement.

Votre reponse :

```
1. _______________________________________________________________

2. _______________________________________________________________

3. _______________________________________________________________

4. _______________________________________________________________
```

---

## Section E2 -- Strategies de deploiement : QCM (5 points, 1 point par question)

Cochez **une seule** bonne reponse par question.

---

**Q45.** Quelle strategie de deploiement est utilisee **par defaut** dans Kubernetes ?

- [ ] A. Recreate
- [ ] B. Blue/Green
- [ ] C. Rolling Update
- [ ] D. Canary

Votre reponse : ______

---

**Q46.** Quelle strategie de deploiement cause un **temps d'arret** (downtime) ?

- [ ] A. Rolling Update
- [ ] B. Blue/Green
- [ ] C. Canary
- [ ] D. Recreate

Votre reponse : ______

---

**Q47.** Dans une strategie Blue/Green, comment effectue-t-on le basculement du trafic ?

- [ ] A. On arrete tous les pods un par un
- [ ] B. On change le selector du Service pour pointer vers le nouvel environnement
- [ ] C. On supprime l'ancien deployment
- [ ] D. On fait un rollback automatique

Votre reponse : ______

---

**Q48.** La strategie Canary consiste a :

- [ ] A. Deployer la nouvelle version sur 100% des instances immediatement
- [ ] B. Deployer la nouvelle version sur un petit pourcentage d'instances, observer, puis augmenter progressivement
- [ ] C. Arreter toutes les instances avant de deployer
- [ ] D. Dupliquer le trafic vers deux versions sans que les utilisateurs le voient

Votre reponse : ______

---

**Q49.** Quelle strategie permet un rollback **instantane** en production ?

- [ ] A. Recreate
- [ ] B. Rolling Update
- [ ] C. Blue/Green
- [ ] D. Shadow

Votre reponse : ______

---

## Section E3 -- Strategies de deploiement : developpement (12 points)

---

**Q50.** (3 points) Expliquez en detail la strategie **Rolling Update**. Comment fonctionne-t-elle etape par etape ? Que se passe-t-il pour les utilisateurs pendant la mise a jour ? Quel est le role des parametres `maxSurge` et `maxUnavailable` ?

Votre reponse :

```
Fonctionnement etape par etape : ___________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

Impact sur les utilisateurs : _______________________________________

_______________________________________________________________

maxSurge : ________________________________________________________

_______________________________________________________________

maxUnavailable : ___________________________________________________

_______________________________________________________________
```

---

**Q51.** (3 points) Comparez les strategies **Blue/Green** et **Canary**. Pour chacune, expliquez le principe, donnez un avantage et un inconvenient, et indiquez dans quel cas on l'utiliserait.

Votre reponse :

```
Blue/Green :

  Principe : ______________________________________________________

  _______________________________________________________________

  Avantage : ______________________________________________________

  Inconvenient : ___________________________________________________

  Cas d'usage : ____________________________________________________

Canary :

  Principe : ______________________________________________________

  _______________________________________________________________

  Avantage : ______________________________________________________

  Inconvenient : ___________________________________________________

  Cas d'usage : ____________________________________________________
```

---

**Q52.** (2 points) Qu'est-ce qu'un deploiement **Shadow** ? Pourquoi dit-on que le risque est nul pour les utilisateurs ? Donnez une limitation importante de cette strategie.

Votre reponse :

```
Definition : ______________________________________________________

_______________________________________________________________

Pourquoi risque nul : ______________________________________________

_______________________________________________________________

Limitation : ______________________________________________________

_______________________________________________________________
```

---

**Q53.** (2 points) Un responsable technique vous demande de deployer une mise a jour critique sur une application de commerce en ligne avec 10 000 utilisateurs actifs. Il veut pouvoir annuler immediatement en cas de probleme. Quelle strategie recommanderiez-vous et pourquoi ?

Votre reponse :

```
Strategie recommandee : ____________________________________________

Justification : ____________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________
```

---

**Q54.** (2 points) Completez le tableau suivant :

| Strategie | Downtime ? | Rollback | Cout en ressources | v1 et v2 coexistent ? |
|-----------|------------|----------|--------------------|-----------------------|
| Recreate  | __________ | ________ | __________________ | _____________________ |
| Rolling Update | __________ | ________ | __________________ | _____________________ |
| Blue/Green | __________ | ________ | __________________ | _____________________ |
| Canary    | __________ | ________ | __________________ | _____________________ |

---

# Bareme recapitulatif

| Partie | Sujet | Points |
|--------|-------|--------|
| A | Jenkins : fondamentaux | /15 |
| B | Jenkinsfile et Groovy | /15 |
| C | Kubernetes : bases | /15 |
| D | Kubernetes : Services et avance | /10 |
| E | CI/CD, strategies de deploiement et culture DevOps | /25 |
| **Total** | | **/80** |

---

Bonne chance.

