# EXERCICES — Jenkins Pipeline + GitHub (Compléter ce qui manque)

## CONSIGNES
- Complétez uniquement les zones `_____`.
- Respectez la syntaxe Groovy / Jenkins.
- Ne changez pas le reste du code.
- Objectif : réussir un build Jenkins qui exécute Java + Python.

<br/>

## EXERCICE 1 — Dépôt GitHub (fichiers à créer)

Complétez la liste des fichiers obligatoires à mettre dans le dépôt GitHub :

1) `_____`  
2) `_____`  
3) `_____` (sans extension)

<br/>

## EXERCICE 2 — Configuration Jenkins (ordre des étapes)

Remettez les étapes suivantes **dans le bon ordre** en écrivant les numéros 1→9.

- ( ) Choisir **Pipeline Script from SCM**  
- ( ) Créer une pipeline et cliquer sur **OK**  
- ( ) Dans SCM choisir **GIT**  
- ( ) Dans **Branch Specifier**, remplacer `*/master` par `*/_____` si nécessaire  
- ( ) Cliquer sur **Apply** puis **Save**  
- ( ) Activer **Poll SCM**  
- ( ) Ajouter des **Credentials** : Username + password (token GitHub)  
- ( ) Indiquer le **Repository URL**  
- ( ) Sélectionner le **Credentials** créé (ne pas oublier)

<br/>

## EXERCICE 3 — Jenkinsfile minimal (Checkout)

Complétez la partie `Checkout` pour cloner le dépôt **sans utiliser** `git clone`.

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
````

<br/>

## EXERCICE 4 — Détection Unix/Windows (Build)

Complétez les instructions `sh` et `bat` pour afficher un message différent selon l’OS.

```groovy
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
```

<br/>

## EXERCICE 5 — Exécuter Java (compilation + exécution)

Complétez les commandes pour **compiler** puis **exécuter** `HelloWorld.java` sous Linux.

```groovy
if (isUnix()) {
  sh '_____ HelloWorld.java'
  sh '_____ HelloWorld'
}
```

<br/>

## EXERCICE 6 — Exécuter Python (Linux)

Complétez la commande Linux pour exécuter `hello.py`.

```groovy
if (isUnix()) {
  sh '_____ hello.py'
}
```

<br/>

## EXERCICE 7 — Exécuter Java + Python (Windows)

Complétez les commandes Windows pour compiler/exécuter Java et lancer Python.

```groovy
else {
  bat '_____ HelloWorld.java'
  bat '_____ HelloWorld'
  bat '_____ hello.py'
}
```

<br/>

## EXERCICE 8 — Variables d’environnement (Linux + Windows) avec `withEnv`

Complétez les variables d’environnement pour un pipeline “2 en 1”.

```groovy
script {
  if (isUnix()) {
    withEnv([
      "JAVA_HOME=_____",
      "PYTHON_HOME=_____",
      "PATH=${env.PATH}:_____:_____"
    ]) {
      sh 'javac HelloWorld.java'
      sh 'java HelloWorld'
      sh 'python3 hello.py'
    }
  } else {
    withEnv([
      "JAVA_HOME=_____",
      "PYTHON_HOME=_____",
      "PATH=${env.PATH};_____\\bin;_____"
    ]) {
      bat 'javac HelloWorld.java'
      bat 'java HelloWorld'
      bat 'python hello.py'
    }
  }
}
```

<br/>

## EXERCICE 9 — Correction d’une erreur fréquente (Branch)

Un étudiant a laissé `*/master` mais son dépôt utilise `main`.
Complétez la correction exacte à faire dans Jenkins :

Branch Specifier : `*/_____`

<br/>

## EXERCICE 10 — Outils Jenkins : chemin Git

Complétez le chemin Git pour Linux **et** Windows (valeurs typiques) :

* Linux : `Path to git executable = _____`
* Windows : `Path to git executable = _____`

<br/>

## EXERCICE 11 — Compléter un Jenkinsfile “fonctionnel” (Linux + Windows)

Complétez toutes les zones `_____` pour obtenir un Jenkinsfile complet qui :

* clone le dépôt,
* affiche l’OS,
* compile/exécute Java,
* exécute Python.

```groovy
pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: '_____', url: '_____'
      }
    }

    stage('Build') {
      steps {
        script {
          if (isUnix()) {
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

<br/>

## EXERCICE 12 — Analyse (courte)

En 2–4 lignes : expliquez pourquoi on évite d’écrire `git clone ...` dans un Jenkinsfile et pourquoi on utilise :

```groovy
git branch: 'main', url: 'https://...'
```



