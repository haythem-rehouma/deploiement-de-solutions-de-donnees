# Examen formatif -- Jenkins : pipelines et Groovy

## Modules couverts

Cet examen porte sur les notions des modules suivants :
- **02 - Jenkins CI/CD** : configuration de jobs, pipelines déclaratifs, Jenkinsfile, Git dans Jenkins
- **03 - Jenkinsfile, Docker et Registry** : Jenkinsfile avancé, variables d'environnement, détection d'OS

Avant de commencer, assurez-vous d'avoir réalisé les pratiques 2 à 6 du module 02.

## Consignes

- Complétez les zones `_____` dans chaque exercice.
- Respectez la syntaxe Groovy / Jenkins.
- Cliquez sur "Voir la réponse" pour vérifier.
- Score total : /30

---

## Exercice 1 -- Fichiers obligatoires dans un dépôt (3 points)

Pour qu'un pipeline Jenkins puisse cloner et exécuter un projet, quels fichiers doivent être dans le dépôt GitHub ?

Complétez :

1. `_____` (le script à exécuter, ex. Python ou Java)
2. `_____` (la description du projet)
3. `_____` (sans extension, définit le pipeline)

<details>
<summary>Voir la réponse</summary>

1. `hello.py` (ou `HelloWorld.java`, ou tout script du projet)
2. `README.md`
3. `Jenkinsfile`

</details>

---

## Exercice 2 -- Ordre de configuration Jenkins (3 points)

Remettez ces étapes dans le bon ordre (numérotez de 1 à 7) pour configurer un job Pipeline dans Jenkins :

- ( ) Cliquer sur Apply puis Save
- ( ) Créer un nouveau item de type Pipeline et cliquer sur OK
- ( ) Dans Pipeline, choisir "Pipeline script from SCM"
- ( ) Choisir Git comme SCM
- ( ) Indiquer le Repository URL
- ( ) Sélectionner les Credentials (username + token)
- ( ) Dans Branch Specifier, écrire `*/main`

<details>
<summary>Voir la réponse</summary>

1. Créer un nouveau item de type Pipeline et cliquer sur OK
2. Dans Pipeline, choisir "Pipeline script from SCM"
3. Choisir Git comme SCM
4. Indiquer le Repository URL
5. Sélectionner les Credentials (username + token)
6. Dans Branch Specifier, écrire `*/main`
7. Cliquer sur Apply puis Save

</details>

---

## Exercice 3 -- Checkout d'un dépôt (2 points)

Complétez pour cloner le dépôt `https://github.com/moncompte/mon-app.git` sur la branche `main` :

```groovy
pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: '_____', url: '_____'
      }
    }
  }
}
```

<details>
<summary>Voir la réponse</summary>

```groovy
git branch: 'main', url: 'https://github.com/moncompte/mon-app.git'
```

</details>

---

## Exercice 4 -- Détection Unix/Windows (2 points)

Le pipeline suivant doit afficher un message différent selon le système d'exploitation de l'agent.
Remplacez les `_____` par la bonne commande Jenkins (`sh` ou `bat`) :

```groovy
pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        script {
          if (isUnix()) {
            _____ 'echo "Running on Unix"'
          } else {
            _____ 'echo "Running on Windows"'
          }
        }
      }
    }
  }
}
```

Rappel : `isUnix()` retourne `true` si l'agent est Linux/macOS, `false` si Windows.

<details>
<summary>Voir la réponse</summary>

```groovy
pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        script {
          if (isUnix()) {
            sh 'echo "Running on Unix"'
          } else {
            bat 'echo "Running on Windows"'
          }
        }
      }
    }
  }
}
```

- `sh` : exécute une commande shell (Linux/macOS)
- `bat` : exécute une commande batch (Windows)

</details>

---

## Exercice 5 -- Compilation et exécution Java (2 points)

Le dépôt contient un fichier `HelloWorld.java`. Ce pipeline doit le compiler puis l'exécuter sous Linux.
Complétez les commandes dans les `_____` :

```groovy
pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/moncompte/hello-java.git'
      }
    }

    stage('Build Java') {
      steps {
        sh '_____ HelloWorld.java'
        sh '_____ HelloWorld'
      }
    }
  }
}
```

Rappel : en Java, on compile avec une commande puis on exécute avec une autre.

<details>
<summary>Voir la réponse</summary>

```groovy
sh 'javac HelloWorld.java'
sh 'java HelloWorld'
```

- `javac` : compile le fichier .java en .class
- `java` : exécute la classe compilée (sans l'extension .class)

</details>

---

## Exercice 6 -- Exécution Python sous Linux (2 points)

Le dépôt contient un fichier `hello.py`. Ce pipeline doit l'exécuter sous Linux.
Complétez la commande dans le `_____` :

```groovy
pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/moncompte/hello-python.git'
      }
    }

    stage('Run Python') {
      steps {
        sh '_____ hello.py'
      }
    }
  }
}
```

Rappel : sur Linux, la commande pour exécuter Python n'est pas la même que sur Windows.

<details>
<summary>Voir la réponse</summary>

```groovy
sh 'python3 hello.py'
```

Sur Linux, on utilise `python3` (et non `python` qui pointe parfois vers Python 2).

</details>

---

## Exercice 7 -- Pipeline multi-OS : Java et Python (2 points)

Ce pipeline doit fonctionner sur Linux ET sur Windows. Il compile Java puis exécute Python.
Complétez les 6 zones `_____` (commandes `sh` ou `bat`, et exécutables) :

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
            _____ '_____ HelloWorld.java'
            _____ '_____ HelloWorld'
            _____ '_____ hello.py'
          } else {
            bat 'javac HelloWorld.java'
            bat 'java HelloWorld'
            bat '_____ hello.py'
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
if (isUnix()) {
    sh 'javac HelloWorld.java'
    sh 'java HelloWorld'
    sh 'python3 hello.py'
} else {
    bat 'javac HelloWorld.java'
    bat 'java HelloWorld'
    bat 'python hello.py'
}
```

- Linux : `sh` + `python3`
- Windows : `bat` + `python`
- `javac` et `java` sont identiques sur les deux OS

</details>

---

## Exercice 8 -- Variables d'environnement (4 points)

Un pipeline Linux a besoin de définir les chemins vers Java et Python pour que Jenkins trouve les exécutables.

Voici les informations :
- Java 21 est installé dans `/usr/lib/jvm/java-21-openjdk-amd64`
- Python 3 est dans `/usr/bin`

Complétez les 4 zones `_____` :

```groovy
pipeline {
  agent any

  environment {
    JAVA_HOME   = '(a) _____'
    PYTHON_HOME = '(b) _____'
    PATH = "${env.PATH}:(c) _____:(d) _____"
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

(a) Chemin complet vers le dossier du JDK
(b) Chemin complet vers le dossier contenant python3
(c) Sous-dossier `bin` de JAVA_HOME (utilisez la variable Groovy `${JAVA_HOME}`)
(d) Le chemin de PYTHON_HOME (utilisez la variable Groovy `${PYTHON_HOME}`)

<details>
<summary>Voir la réponse</summary>

```groovy
environment {
    JAVA_HOME   = '/usr/lib/jvm/java-21-openjdk-amd64'
    PYTHON_HOME = '/usr/bin'
    PATH = "${env.PATH}:${JAVA_HOME}/bin:${PYTHON_HOME}"
}
```

- (a) `/usr/lib/jvm/java-21-openjdk-amd64` -- le chemin donné dans l'énoncé
- (b) `/usr/bin` -- le chemin donné dans l'énoncé
- (c) `${JAVA_HOME}/bin` -- on réutilise la variable définie juste au-dessus avec la syntaxe Groovy `${}`
- (d) `${PYTHON_HOME}` -- même principe

Astuce : on peut trouver ces chemins avec `readlink -f $(which java)` et `which python3`.

</details>

---

## Exercice 9 -- Correction d'erreur de branche (1 point)

Un étudiant a laissé `*/master` dans le Branch Specifier mais son dépôt utilise la branche `main`.
Quelle est la correction à faire ?

Branch Specifier : `*/_____`

<details>
<summary>Voir la réponse</summary>

Branch Specifier : `*/main`

Beaucoup de dépôts récents utilisent `main` au lieu de `master` comme branche par défaut.

</details>

---

## Exercice 10 -- Chemin de Git (2 points)

Complétez le chemin typique vers l'exécutable Git :

- Linux : Path to git executable = `_____`
- Windows : Path to git executable = `_____`

<details>
<summary>Voir la réponse</summary>

- Linux : `/usr/bin/git`
- Windows : `C:\Program Files\Git\bin\git.exe`

Ces chemins se configurent dans Manage Jenkins > Tools > Git.

</details>

---

## Exercice 11 -- Jenkinsfile complet (5 points)

Complétez toutes les zones `_____` pour obtenir un Jenkinsfile fonctionnel qui clone, compile Java et exécute Python :

```groovy
pipeline {
  agent _____

  stages {
    stage('Checkout') {
      steps {
        git branch: '_____', url: 'https://github.com/moncompte/hello-app.git'
      }
    }

    stage('Build') {
      steps {
        script {
          if (_____) {
            sh 'echo "Running on Unix"'
            sh '_____ HelloWorld.java'
            sh '_____ HelloWorld'
            sh '_____ hello.py'
          } else {
            bat 'echo "Running on Windows"'
            bat '_____ HelloWorld.java'
            bat '_____ HelloWorld'
            bat '_____ hello.py'
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
}
```

</details>

---

## Exercice 12 -- Question d'analyse (2 points)

En 2 à 4 lignes : pourquoi utilise-t-on `git branch: 'main', url: '...'` dans un Jenkinsfile plutôt que `sh 'git clone ...'` ?

<details>
<summary>Voir la réponse</summary>

La directive `git` de Jenkins est intégrée au système de pipeline. Elle gère automatiquement :
- le checkout propre du workspace (nettoyage des fichiers précédents),
- la gestion des credentials (tokens, clés SSH) de manière sécurisée,
- la compatibilité avec les triggers (webhook, polling).

Un simple `sh 'git clone ...'` ne profite pas de ces fonctionnalités et risque de poser des problèmes (dossier déjà existant, credentials en clair dans les logs, etc.).

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
