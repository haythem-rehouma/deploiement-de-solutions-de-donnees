**PRATIQUE – Pipeline Jenkins + GitHub : énoncé, corrections et ressources**

Bonjour à toutes et à tous,

Dans le cadre de notre module DevOps / CI-CD, vous trouverez ci-dessous l’ensemble des ressources pour la **01-PRATIQUE – Déclencher un Pipeline Jenkins avec GitHub**.
L’objectif est de mettre en place un pipeline réellement exploitable en entreprise : déclenchement automatique à chaque modification du fichier `README.md` sur GitHub.

<br/>

# 1. Énoncé officiel de la pratique

**01-PRATIQUE – Déclencher un Pipeline Jenkins avec GitHub**
Lien :
[https://docs.google.com/document/d/1XGoJfMBTSElPrzYUwePjkyHpjlFVbExP/edit?usp=sharing&ouid=114388549516551190899&rtpof=true&sd=true](https://docs.google.com/document/d/1XGoJfMBTSElPrzYUwePjkyHpjlFVbExP/edit?usp=sharing&ouid=114388549516551190899&rtpof=true&sd=true)

Veuillez lire attentivement toutes les étapes avant de commencer.

<br/>

# 2. Corrections détaillées

Ces corrections sont fournies comme **références de qualité professionnelle**. Elles ne remplacent pas votre propre travail, mais doivent vous servir à comparer, valider et améliorer votre solution.

* **Correction 1 – Implémentation complète du pipeline Jenkins**
  [https://drive.google.com/file/d/1YOmdvqE-2pEXH_fBTkzBIPcHgyzV2bZW/view?usp=sharing](https://drive.google.com/file/d/1YOmdvqE-2pEXH_fBTkzBIPcHgyzV2bZW/view?usp=sharing)

* **Correction 2 – Variante / autre approche de pipeline**
  [https://drive.google.com/file/d/1OcBz5OKRWyeg4eiJTbyNTR28RGK20Jgf/view?usp=sharing](https://drive.google.com/file/d/1OcBz5OKRWyeg4eiJTbyNTR28RGK20Jgf/view?usp=sharing)

Je vous recommande de :

1. Tenter d’abord la pratique **sans** regarder les corrections.
2. Ensuite, comparer votre travail aux deux versions proposées.
3. Noter les différences (structure du Jenkinsfile, webhooks GitHub, triggers, logs, etc.).

<br/>

# 3. Dossier complet – Ressources de la pratique

Vous pouvez également accéder à l’ensemble des fichiers liés à cette pratique (documents, exemples, matériaux complémentaires) via le dossier suivant :

**Dossier Drive – 01-PRATIQUE Jenkins + GitHub**
[https://drive.google.com/drive/folders/1bREC1A7cg7uSPxDAW_QoNxtCS9N9AxW5?usp=sharing](https://drive.google.com/drive/folders/1bREC1A7cg7uSPxDAW_QoNxtCS9N9AxW5?usp=sharing)

<br/>






<br/>

# 4. Exemple de Jenkinsfile pour Windows


- Dépôt : https://github.com/haythem-rehouma/hello-python

```groovy
pipeline {
    agent any
    environment {
        JAVA_HOME = 'C:\\Program Files\\Java\\jdk1.8.0_202'
        PYTHON_HOME = 'C:\\Users\\rehou\\AppData\\Local\\Programs\\Python\\Python39'
        PATH = "${env.PATH};${JAVA_HOME}\\bin;${PYTHON_HOME}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/haythem-rehouma/hello-python.git'
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


Le premier bloc est un **bloc `environment` de Jenkinsfile** 👉 ça sert à définir des **variables d’environnement** pour notre pipeline Jenkins.

```groovy
environment {
    JAVA_HOME   = 'C:\\Program Files\\Java\\jdk1.8.0_202'
    PYTHON_HOME = 'C:\\Users\\rehou\\AppData\\Local\\Programs\\Python\\Python39'
    PATH        = "${env.PATH};${JAVA_HOME}\\bin;${PYTHON_HOME}"
}
```

### 4.1. Ça fait quoi ?

* `JAVA_HOME` → indique à Jenkins où se trouve ton JDK Java.
* `PYTHON_HOME` → indique où se trouve ton interpréteur Python.
* `PATH` → on prend le PATH actuel (`${env.PATH}`) et on **ajoute** :

  * `...;${JAVA_HOME}\bin`
  * `...;${PYTHON_HOME}`
    👉 Comme ça, les commandes `java`, `javac`, `python`, etc. sont trouvées automatiquement pendant le build.

### 4.2. Windows ou Linux ?

Ici l'environnement c’est **Windows** :

* Chemins avec `C:\...`
* Séparateur dans `PATH` = `;` (sur Linux c’est `:`)
* Antislash `\` au lieu de `/`

Sur **Linux**, ce serait plutôt une syntaxe comme :

```groovy
environment {
    JAVA_HOME   = '/usr/lib/jvm/java-17-openjdk-amd64'
    PYTHON_HOME = '/usr/bin/python3'
    PATH        = "${env.PATH}:${JAVA_HOME}/bin:${PYTHON_HOME}"
}
```

Donc :
➡️ C’est un **bloc Jenkins declarative pipeline**, et notre version est prévue pour **un agent Jenkins sous Windows**.






<br/>

# 5. Exemple de Jenkinsfile pour Linux


- Dépôt : https://github.com/haythem-rehouma/hello-python-linux
- Regardez l'annexe 1 pour les commandes

```groovy
pipeline {
    agent any

    environment {
        // Java 21 sur ta VM - commande readlink -f $(which java) va donner /usr/lib/jvm/java-21-openjdk-amd64/bin/java donc  JAVA_HOME   = '/usr/lib/jvm/java-21-openjdk-amd64'
        JAVA_HOME   = '/usr/lib/jvm/java-21-openjdk-amd64'
        // Python sera dans /usr/bin (python3) - commande which python3 ==> /usr/bin/python3 donc PYTHON_HOME = '/usr/bin'
        PYTHON_HOME = '/usr/bin'
        PATH = "${env.PATH}:${JAVA_HOME}/bin:${PYTHON_HOME}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/haythem-rehouma/hello-python-linux.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'echo "Running on Unix"'
                        // Java (si tu as un HelloWorld.java dans le repo)
                        sh 'javac HelloWorld.java'
                        sh 'java HelloWorld'
                        // sinon  uniquemnet une seule commande sh 'java HelloWorld.java'

                        // Python
                        sh 'python3 --version'
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




# 5. Attendus 

À l’issue de cette pratique, vous devez être capable de :

* Créer et configurer un dépôt GitHub dédié au pipeline.
* Mettre en place un **Jenkinsfile** minimal mais robuste (script “Hello World” exécuté via pipeline).
* Configurer l’intégration GitHub ↔ Jenkins (webhook / polling) pour déclencher le pipeline à chaque modification du `README.md`.
* Lire et interpréter les **logs Jenkins** pour diagnostiquer un échec de build.

Cette pratique se rapproche fortement de ce que l’on trouve dans des pipelines CI-CD **en production** (déclenchement sur commit, traçabilité, logs, corrections itératives).


Si vous avez des questions précises (erreur dans Jenkins, problème de webhook, build qui ne se déclenche pas, etc.), merci de me communiquer :

* une capture d’écran de l’erreur,
* un extrait de votre Jenkinsfile,
* et l’URL de votre dépôt GitHub.

Bonne pratique et prenez le temps de soigner votre pipeline.


<br/>

# Annexe 1 - commandes linux

```groovy
root@jenkinsVM:/# history
    1  apt update
    2  sudo apt update
    3  sudo apt install fontconfig openjdk-21-jre
    4  java -version
    5  which java
    6  which python
    7  sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc   https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    8  echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]"   https://pkg.jenkins.io/debian-stable binary/ | sudo tee   /etc/apt/sources.list.d/jenkins.list > /dev/null
    9  sudo apt update
   10  sudo apt install jenkins
   11  sudo systemctl status jenkins
   12  sudo systemctl enable jenkins
   13  sudo systemctl start jenkins
   14  cat /var/lib/jenkins/secrets/initialAdminPassword
   15  cd /
   16  pwd
   17  ls
   18  which java
   19  ls /usr/bin/java
   20  cat /usr/bin/java
   21  ls -la /usr/bin/java
   22  javac
   23  java
   24  which java
   25  /usr/bin/java --version
   26  javac
   27  readlink -f $(which java)
   28  which java
   29  sudo apt update
   30  sudo apt install -y python3 python3-pip default-jdk
   31  which python3
   32  readlink -f $(which java)
   33  which python3
   34  ls /usr/lib/jvm/java-21-openjdk-amd64/bin/java
   35  cd /usr/lib/jvm/java-21-openjdk-amd64/bin/java/bin
   36  ls /usr/lib/jvm/java-21-openjdk-amd64
   37  which python3
   38  ls /usr/lib/jvm/java-21-openjdk-amd64/bin
   39  which python3
   40  ls /usr/bin/python3
   41  ls /usr/bin
   42  readlink -f $(which java)
   43  which python3
   44  history
```






<br/>

# Annexe 2 - Installer Python proprement sur Ubuntu 24.04 (si ce n’est pas déjà fait)

Sur votre VM Jenkins (Ubuntu 24.04) :

```bash
sudo apt update
sudo apt install -y python3 python3-pip
```

Vérifiez :

```bash
which python3
python3 --version
```

Vous devriez voir :

```text
/usr/bin/python3
```

python3 est le binaire ou exécutable, c’est pour ça que dans le Jenkinsfile je mets :

```groovy
PYTHON_HOME = '/usr/bin'
sh 'python3 hello.py'
```


<br/>

# Annexe 3 - commandes utiles


### Annexe 3.1. Cas n°1 – Ubuntu **ne connaît pas** `javac` (même dans le terminal)

Si, dans ton terminal, tu fais :

```bash
javac -version
```

et que tu obtiens `command not found`, ça veut dire que tu n’as **que le JRE** (java pour exécuter) mais pas le **JDK** (avec le compilateur `javac`).

Dans ce cas, installe le JDK complet :

```bash
sudo apt update
sudo apt install -y openjdk-21-jdk
```

Puis vérifie :

```bash
javac -version
which javac
```

Tu devrais voir un résultat du genre :

```text
javac 21...
/usr/lib/jvm/java-21-openjdk-amd64/bin/javac
```

Là tu es bon : `JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64` est correct, et `javac` existe bien.

---

### Annexe 3.2. Cas n°2 – `javac` marche **en terminal**, mais pas dans Jenkins

Dans ce cas, le problème n’est pas Ubuntu, c’est l’**environnement Jenkins** :

* soit `JAVA_HOME` est mal défini dans le Jenkinsfile,
* soit le `PATH` n’inclut pas `${JAVA_HOME}/bin` au moment où Jenkins exécute le `sh`.

Dans ton Jenkinsfile, avec ce que tu m’as donné :

```groovy
environment {
    JAVA_HOME   = '/usr/lib/jvm/java-21-openjdk-amd64'
    PYTHON_HOME = '/usr/bin'
    PATH = "${env.PATH}:${JAVA_HOME}/bin:${PYTHON_HOME}"
}
```

Et dans le stage Build, tu peux tester :

```groovy
sh 'echo $JAVA_HOME'
sh 'which java || echo "java introuvable"'
sh 'which javac || echo "javac introuvable"'
sh 'javac -version || echo "Erreur javac"'
```

Si `which javac` renvoie vide dans Jenkins mais OK dans ton terminal, c’est que Jenkins n’a pas le même PATH / JAVA_HOME.



### Annexe 3.3. Résumé pour votre TP

3.3.1. **Sur Ubuntu :**

```bash
sudo apt update
sudo apt install -y openjdk-21-jdk
javac -version
```

3.3.2. **Dans le Jenkinsfile :**

```groovy
environment {
    JAVA_HOME   = '/usr/lib/jvm/java-21-openjdk-amd64'
    PATH = "${env.PATH}:${JAVA_HOME}/bin"
}
```

