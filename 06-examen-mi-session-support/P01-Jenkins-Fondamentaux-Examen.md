# Examen formatif -- Jenkins : fondamentaux

## Modules couverts

Cet examen porte sur les notions des modules suivants :
- **01 - Introduction à Git et DevOps** : culture DevOps, CI/CD, rôle de Git
- **02 - Jenkins CI/CD** : installation, jobs, pipelines, agents, webhooks, Jenkinsfile

Avant de commencer, assurez-vous d'avoir lu ces modules et réalisé les pratiques associées.

## Consignes

- Répondez à chaque question, puis cliquez sur "Voir la réponse" pour vérifier.
- Comptez vos bonnes réponses pour évaluer votre niveau.
- Score total : /30

---

## Partie 1 -- QCM (10 points)

Cochez **une seule** bonne réponse par question.

---

**Q1.** Jenkins est principalement utilisé pour :

- [ ] A. Héberger des sites web statiques
- [ ] B. Automatiser les tâches CI/CD (build, test, déploiement)
- [ ] C. Remplacer Git
- [ ] D. Gérer des bases de données

<details>
<summary>Voir la réponse</summary>

**B.** Jenkins est un serveur d'automatisation CI/CD. Il orchestre le build, les tests et le déploiement.

</details>

---

**Q2.** Qu'est-ce qu'un "job" dans Jenkins ?

- [ ] A. Un serveur physique
- [ ] B. Une tâche automatisée (build, test, déploiement)
- [ ] C. Un plugin obligatoire
- [ ] D. Un fichier de configuration réseau

<details>
<summary>Voir la réponse</summary>

**B.** Un job est une tâche configurée dans Jenkins qui peut être exécutée manuellement ou automatiquement.

</details>

---

**Q3.** Le fichier Jenkinsfile sert à :

- [ ] A. Installer Jenkins sur le serveur
- [ ] B. Stocker les mots de passe
- [ ] C. Décrire un pipeline en code (Pipeline as Code)
- [ ] D. Configurer le réseau

<details>
<summary>Voir la réponse</summary>

**C.** Le Jenkinsfile est un fichier texte versionné dans Git qui définit les étapes du pipeline (stages, steps, etc.).

</details>

---

**Q4.** Dans un pipeline Jenkins, une "stage" représente :

- [ ] A. Une machine virtuelle
- [ ] B. Une étape logique du pipeline (Build, Test, Deploy...)
- [ ] C. Un utilisateur administrateur
- [ ] D. Un port réseau

<details>
<summary>Voir la réponse</summary>

**B.** Une stage est une étape nommée qui regroupe un ensemble de steps. Exemples : Checkout, Build, Test, Deploy.

</details>

---

**Q5.** Le composant qui exécute les builds (sur une machine distante ou locale) s'appelle :

- [ ] A. Controller (Master)
- [ ] B. Agent (Node)
- [ ] C. Plugin Manager
- [ ] D. Workspace Cleaner

<details>
<summary>Voir la réponse</summary>

**B.** L'agent (ou node) est la machine sur laquelle Jenkins exécute réellement les commandes de build.

</details>

---

**Q6.** Un "workspace" dans Jenkins, c'est :

- [ ] A. Un tableau de bord graphique
- [ ] B. Le dossier de travail où le code est cloné et les commandes exécutées
- [ ] C. Un plugin
- [ ] D. Une base de données

<details>
<summary>Voir la réponse</summary>

**B.** Le workspace est le répertoire local sur l'agent où Jenkins clone le code et exécute les étapes du pipeline.

</details>

---

**Q7.** Pour lancer un job manuellement dans Jenkins, on clique sur :

- [ ] A. Configure System
- [ ] B. Build Now
- [ ] C. Manage Users
- [ ] D. Quiet Down

<details>
<summary>Voir la réponse</summary>

**B.** Le bouton "Build Now" lance une exécution manuelle du job.

</details>

---

**Q8.** Un webhook GitHub sert à :

- [ ] A. Bloquer les commits non autorisés
- [ ] B. Déclencher Jenkins automatiquement lors d'un push ou d'une pull request
- [ ] C. Supprimer les branches distantes
- [ ] D. Créer des tags Git

<details>
<summary>Voir la réponse</summary>

**B.** Un webhook est un mécanisme push : GitHub envoie une requête HTTP à Jenkins dès qu'un événement se produit (push, PR, etc.).

</details>

---

**Q9.** Quelle est la différence entre "polling SCM" et un webhook ?

- [ ] A. Il n'y a aucune différence
- [ ] B. Le polling vérifie périodiquement le dépôt, le webhook réagit en temps réel
- [ ] C. Le webhook est plus lent que le polling
- [ ] D. Le polling nécessite une IP publique, le webhook non

<details>
<summary>Voir la réponse</summary>

**B.** Le polling interroge le dépôt à intervalle régulier (ex. toutes les 5 min) tandis que le webhook est déclenché immédiatement par GitHub au moment du push.

</details>

---

**Q10.** Quel est le rôle du bloc `post` dans un Jenkinsfile ?

- [ ] A. Définir les stages du pipeline
- [ ] B. Exécuter des actions après le pipeline (nettoyage, notifications) selon le résultat
- [ ] C. Configurer les variables d'environnement
- [ ] D. Installer les plugins

<details>
<summary>Voir la réponse</summary>

**B.** Le bloc `post` s'exécute après les stages. Il contient des sections comme `always`, `success`, `failure` pour réagir au résultat du build.

</details>

---

## Partie 2 -- Vrai ou Faux (6 points)

Écrivez **Vrai** ou **Faux** pour chaque affirmation.

---

**Q11.** Jenkins peut exécuter des pipelines déclaratifs ou scriptés.

<details>
<summary>Voir la réponse</summary>

**Vrai.** Jenkins supporte deux syntaxes : déclarative (plus structurée, recommandée) et scripted (plus flexible, basée sur Groovy).

</details>

---

**Q12.** Un agent Jenkins peut être une machine Linux, Windows ou un conteneur Docker.

<details>
<summary>Voir la réponse</summary>

**Vrai.** Jenkins est multi-plateforme. Les agents peuvent tourner sur différents OS ou même dans des conteneurs.

</details>

---

**Q13.** Un Jenkinsfile doit obligatoirement être écrit en Python.

<details>
<summary>Voir la réponse</summary>

**Faux.** Le Jenkinsfile utilise la syntaxe Groovy (langage de la JVM), pas Python.

</details>

---

**Q14.** Jenkins peut déclencher un build automatiquement suite à un push Git.

<details>
<summary>Voir la réponse</summary>

**Vrai.** Via un webhook GitHub ou un polling SCM, Jenkins peut réagir automatiquement aux changements dans le dépôt.

</details>

---

**Q15.** Un pipeline ne peut contenir qu'une seule stage.

<details>
<summary>Voir la réponse</summary>

**Faux.** Un pipeline contient généralement plusieurs stages (Checkout, Build, Test, Deploy, etc.).

</details>

---

**Q16.** La commande `sh` dans un Jenkinsfile exécute une commande shell Linux, tandis que `bat` exécute une commande Windows.

<details>
<summary>Voir la réponse</summary>

**Vrai.** `sh` est pour les agents Linux/Unix, `bat` est pour les agents Windows. On utilise `isUnix()` pour choisir.

</details>

---

## Partie 3 -- Questions courtes (8 points)

Répondez en 2 à 4 lignes maximum.

---

**Q17.** Donnez 2 avantages de l'automatisation CI/CD avec Jenkins.

<details>
<summary>Voir la réponse</summary>

- Réduction des erreurs humaines : les tâches répétitives sont automatisées.
- Feedback rapide : les développeurs savent en quelques minutes si leur code casse quelque chose.

Autres réponses acceptables : déploiements plus fréquents, reproductibilité, traçabilité.

</details>

---

**Q18.** Expliquez la différence entre Controller (Master) et Agent (Node).

<details>
<summary>Voir la réponse</summary>

- **Controller** : le serveur Jenkins central qui orchestre les jobs, gère l'interface web et distribue le travail.
- **Agent** : une machine (locale ou distante) qui exécute réellement les commandes de build et de test.

</details>

---

**Q19.** Citez 3 exemples de stages classiques dans un pipeline Jenkins.

<details>
<summary>Voir la réponse</summary>

1. Checkout (cloner le dépôt Git)
2. Build (compiler, construire l'image Docker)
3. Test (exécuter les tests automatisés)

Autres réponses acceptables : Deploy, Archive, Cleanup, Push.

</details>

---

**Q20.** Qu'est-ce qu'un plugin Jenkins ? Donnez 1 exemple.

<details>
<summary>Voir la réponse</summary>

Un plugin est une extension qui ajoute des fonctionnalités à Jenkins. Exemple : **Git Plugin** (pour cloner des dépôts Git) ou **Docker Pipeline** (pour manipuler Docker dans un pipeline).

</details>

---

**Q21.** Qu'est-ce qu'un trigger dans Jenkins ? Donnez 1 exemple.

<details>
<summary>Voir la réponse</summary>

Un trigger est un déclencheur qui lance un build automatiquement. Exemple : **GitHub hook trigger for GITScm polling** (lance le build quand GitHub envoie un webhook après un push).

</details>

---

**Q22.** À quoi sert la directive `agent any` dans un Jenkinsfile ?

<details>
<summary>Voir la réponse</summary>

`agent any` indique à Jenkins que le pipeline peut s'exécuter sur n'importe quel agent disponible. Jenkins choisit automatiquement un agent libre pour lancer le build.

</details>

---

## Partie 4 -- Compléter un Jenkinsfile (6 points)

---

**Q23.** Complétez les parties `_____` de ce pipeline simple :

```groovy
pipeline {
  agent any

  stages {
    stage('Hello') {
      steps {
        _____ 'Bonjour Jenkins'
      }
    }

    stage('Run') {
      steps {
        sh "_____"
      }
    }
  }
}
```

(a) Complétez la première ligne pour afficher le message.
(b) Complétez la commande `sh` pour afficher "Je suis dans Jenkins".

<details>
<summary>Voir la réponse</summary>

```groovy
pipeline {
  agent any

  stages {
    stage('Hello') {
      steps {
        echo 'Bonjour Jenkins'
      }
    }

    stage('Run') {
      steps {
        sh "echo Je suis dans Jenkins"
      }
    }
  }
}
```

(a) `echo` -- la commande Jenkins pour afficher un message dans les logs.
(b) `echo Je suis dans Jenkins` -- commande shell passée à `sh`.

</details>

---

**Q24.** Complétez ce Jenkinsfile pour cloner un dépôt et exécuter un script Python :

```groovy
pipeline {
  agent any

  stages {
    stage('_____') {
      steps {
        git branch: '_____', url: 'https://github.com/moncompte/mon-projet.git'
      }
    }

    stage('Build') {
      steps {
        sh '_____ main.py'
      }
    }
  }
}
```

<details>
<summary>Voir la réponse</summary>

```groovy
pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/moncompte/mon-projet.git'
      }
    }

    stage('Build') {
      steps {
        sh 'python3 main.py'
      }
    }
  }
}
```

- Stage : `Checkout`
- Branche : `main`
- Commande : `python3`

</details>

---

**Q25.** Complétez le bloc `environment` et la détection d'OS :

```groovy
pipeline {
  agent any

  environment {
    JAVA_HOME = '/usr/lib/jvm/java-21-openjdk-amd64'
  }

  stages {
    stage('Build') {
      steps {
        script {
          if (_____) {
            _____ 'javac HelloWorld.java'
            _____ 'java HelloWorld'
          } else {
            _____ 'javac HelloWorld.java'
            _____ 'java HelloWorld'
          }
        }
      }
    }
  }
}
```

<details>
<summary>Voir la réponse</summary>

```groovy
pipeline {
  agent any

  environment {
    JAVA_HOME = '/usr/lib/jvm/java-21-openjdk-amd64'
  }

  stages {
    stage('Build') {
      steps {
        script {
          if (isUnix()) {
            sh 'javac HelloWorld.java'
            sh 'java HelloWorld'
          } else {
            bat 'javac HelloWorld.java'
            bat 'java HelloWorld'
          }
        }
      }
    }
  }
}
```

- Condition : `isUnix()`
- Linux : `sh`
- Windows : `bat`

</details>

---

## Barème

| Score | Niveau |
|-------|--------|
| 26-30 | Excellent |
| 21-25 | Très bien |
| 16-20 | Bien |
| 11-15 | À réviser |
| 0-10 | À reprendre depuis le début |
