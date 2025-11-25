# Cours – Pipeline Jenkins + GitHub, Webhooks et rôle de ngrok

## 0. Objectif du cours

À la fin de ce cours, vous devez être capable de :

1. Expliquer **à quoi sert un webhook** GitHub et pourquoi on en a besoin avec Jenkins.
2. Comprendre **dans quels cas ngrok est utile** et pourquoi, dans un scénario Azure, il ne l’est pas.
3. Configurer **de A à Z** un pipeline Jenkins qui se déclenche automatiquement quand on modifie `README.md` dans un dépôt GitHub :

   * Jenkinsfile fonctionnel (Java + Python),
   * Jenkins relié à GitHub,
   * Webhook configuré et testé.

Nous partons du Jenkinsfile suivant (déjà prêt) :

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
                        // sinon uniquement une seule commande : sh "java HelloWorld.java"

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

Ce Jenkinsfile est **le cœur du pipeline**.
Ce qui manque pour la pratique, c’est **le déclenchement automatique via webhook GitHub**.

<br/>

## 1. Webhooks, Jenkins, ngrok : qui fait quoi ?

### 1.1. Pourquoi un webhook ?

Sans webhook, Jenkins fonctionne souvent ainsi :

* Soit on clique sur **Build Now** à la main,
* Soit on configure un **polling** (“toutes les 5 minutes, va voir si le dépôt GitHub a changé”).

Problème :

* Polling = consommation inutile, latence, pas « temps réel ».

Un **webhook** est un mécanisme **push** :

1. Un événement se produit sur GitHub (push sur `main`, modification de `README.md`, etc.).
2. GitHub envoie immédiatement une requête HTTP vers Jenkins :
   `POST http://jenkins/.../github-webhook/`
3. Jenkins reçoit l’événement et déclenche le pipeline.

C’est la logique moderne des pipelines CI/CD en entreprise :
**l’événement déclenche le pipeline, pas un cron.**

---

### 1.2. Où intervient ngrok dans tout ça ?

Un webhook, c’est GitHub (dans le cloud) qui doit appeler une URL publique, par exemple :

```text
http://ADRESSE_PUBLIQUE:8080/github-webhook/
```

Deux situations :

#### Situation A – Serveur Jenkins avec IP publique (VM Azure)

* Jenkins est sur une **VM Azure**.
* La VM a une **IP publique**.
* Le port `8080` est ouvert.
* GitHub peut appeler directement :

  ```text
  http://IP_PUBLIQUE_VM:8080/github-webhook/
  ```

Dans ce cas :
**Aucun besoin de ngrok.**
Le serveur est déjà exposé sur Internet avec une IP publique.

#### Situation B – Jenkins sur un poste local (PC perso, laptop, VM interne)

* Jenkins tourne sur un ordinateur personnel ou une VM dans un réseau privé.
* Pas d’IP publique.
* GitHub ne peut pas atteindre `http://localhost:8080`.

Dans ce cas, on utilise **ngrok** :

* Ngrok ouvre un **tunnel sécurisé** de GitHub vers la machine locale.

* On obtient une URL publique du type :

  ```text
  https://xxxxx.ngrok.io/github-webhook/
  ```

* GitHub envoie la requête vers cette URL.

* Ngrok la transfère vers `http://localhost:8080/github-webhook/`.

Donc, **ngrok est une béquille pour exposer temporairement un Jenkins local** sans gérer IP publique, firewall, DNS, etc.

---

### 1.3. Conclusion

* Sur **VM Azure** avec IP publique :
  ➜ Configurer directement le webhook GitHub vers `http://IP_PUBLIQUE:8080/github-webhook/`.

* Sur **laptop / réseau local sans IP publique** :
  ➜ Utiliser **ngrok** pour obtenir une URL publique temporaire.

---

## 2. Mise en place complète du pipeline Jenkins + GitHub (cas VM Azure, sans ngrok)

La configuration se fait étape par étape.

### 2.1. Pré-requis techniques

Sur la **VM Jenkins (Ubuntu 24.04 sur Azure)**, il faut disposer de :

1. Java installé (JDK 21 par exemple) :

```bash
sudo apt update
sudo apt install -y openjdk-21-jdk
java -version
javac -version
readlink -f $(which java)   # doit donner /usr/lib/jvm/java-21-openjdk-amd64/bin/java
```

2. Python 3 installé :

```bash
sudo apt install -y python3 python3-pip
which python3        # /usr/bin/python3
python3 --version
```

3. Jenkins installé et en fonctionnement :

```bash
sudo systemctl status jenkins
```

4. Jenkins accessible depuis l’extérieur :

* Dans Azure, port `8080/TCP` ouvert en inbound.
* Accès via :
  `http://IP_PUBLIQUE_DE_LA_VM:8080/`

5. Un dépôt GitHub, par exemple :

   * `https://github.com/haythem-rehouma/hello-python-linux.git`
   * Contenant :

     * `hello.py`
     * éventuellement `HelloWorld.java`
     * un `README.md` qui servira de déclencheur.

---

### 2.2. Étape 1 – Créer le job Pipeline dans Jenkins

Deux options :

* Coller le Jenkinsfile directement dans le job,
* Ou le déposer dans le repo (`Jenkinsfile`) et configurer Jenkins pour le lire depuis Git.

#### Option A – Pipeline Script (plus simple pour débuter)

1. Se connecter à Jenkins : `http://IP_PUBLIQUE_VM:8080/`.

2. Cliquer sur **New Item**.

3. Donner un nom au job, par exemple :
   `01-hello-python-linux-pipeline`.

4. Choisir **Pipeline** puis cliquer sur **OK**.

5. Dans la page de configuration :

   * Descendre jusqu’à **Pipeline**.
   * Sélectionner **Definition: Pipeline script**.
   * Coller **le Jenkinsfile complet** dans la zone Script (celui indiqué au début du cours).

6. Sauvegarder (**Save**).

À ce stade, le job sait :

* Sur quel agent s’exécuter (`agent any`),
* Comment configurer l’environnement (`JAVA_HOME`, `PYTHON_HOME`, `PATH`),
* Quoi faire dans les stages (`Checkout` + `Build`).

Un premier run manuel avec **Build Now** permet de vérifier que tout est correct avant d’introduire le webhook.

---

### 2.3. Étape 2 – Tester le pipeline manuellement (sans webhook)

1. Dans la page du job, cliquer sur **Build Now**.
2. Ouvrir **Console Output** du build en cours.

On doit voir :

* Le checkout Git du repo `hello-python-linux`.
* Le message `Running on Unix`.
* L’exécution de `javac` / `java` (si les fichiers existent) et `python3 hello.py`.

Si quelque chose échoue, corriger :

* Les chemins Java/Python,
* Le Jenkinsfile (par exemple commenter `javac` si `HelloWorld.java` n’existe pas dans ce repo).

Une fois que le pipeline passe **manuellement**, on peut passer au webhook.

---

### 2.4. Étape 3 – Activer le déclencheur GitHub dans Jenkins

Dans le même job :

1. Cliquer sur **Configure**.

2. Descendre à la section **Build Triggers**.

3. Cocher :

   * **GitHub hook trigger for GITScm polling**
     (ou éventuellement **Build when a change is pushed to GitHub** selon la version).

4. Sauvegarder (**Save**).

Ce déclencheur indique à Jenkins :
« Quand GitHub m’envoie un webhook, je vais consulter le dépôt Git et décider de lancer un build. »

---

### 2.5. Étape 4 – Créer le webhook dans GitHub

Sur GitHub, dans le dépôt `hello-python-linux` :

1. Ouvrir la page du dépôt.
2. Cliquer sur **Settings** (onglet du repo).
3. Dans le menu de gauche, cliquer sur **Webhooks**.
4. Cliquer sur **Add webhook**.

Compléter les champs :

* **Payload URL** :

  ```text
  http://IP_PUBLIQUE_DE_LA_VM:8080/github-webhook/
  ```

  Le `/` final est important : c’est l’URL standard de Jenkins pour GitHub.

* **Content type** :

  ```text
  application/json
  ```

* **Secret** (optionnel mais recommandé, même dans une pratique encadrée) :

  * Par exemple : `devops-jenkins-2025`.
  * Si un secret est utilisé, il faut le configurer côté Jenkins (Manage Jenkins → Configure System → GitHub → ajouter un serveur GitHub et le secret).

* **Which events would you like to trigger this webhook?**

  * Choisir **Just the push event**.

* Cocher **Active**.

* Cliquer sur **Add webhook**.

GitHub envoie immédiatement un **Ping** vers Jenkins.
Dans la page Webhooks, il est possible de cliquer sur le webhook → **Recent deliveries** → un statut **200** indique que Jenkins a bien répondu.

---

### 2.6. Étape 5 – Tester le déclenchement automatique (modification de README.md)

Sur une machine de développement :

1. Cloner le dépôt si nécessaire :

   ```bash
   git clone https://github.com/haythem-rehouma/hello-python-linux.git
   cd hello-python-linux
   ```

2. Modifier `README.md` :

   ```bash
   echo "Test webhook Jenkins - $(date)" >> README.md
   ```

3. Commit + push :

   ```bash
   git add README.md
   git commit -m "Test webhook Jenkins (README.md)"
   git push origin main
   ```

4. Revenir sur Jenkins :

   * Sur la page du job, un **nouveau build** doit se lancer automatiquement.
   * Dans la **Console Output**, vérifier que :

     * Le code a bien été mis à jour,
     * Les commandes Java/Python se sont bien exécutées.

5. Critère de validation :

   > Lorsque `README.md` est modifié et poussé sur GitHub, un build Jenkins doit se déclencher automatiquement **sans cliquer sur Build Now**.

---

## 3. Partie pédagogique

Une séquence possible pour animer ce contenu :

1. **10–15 minutes de théorie**

   * Rappeler le rôle de Jenkins en CI/CD.

   * Expliquer la différence :

     * Build manuel,
     * Polling Git (toutes les X minutes),
     * Webhook GitHub → Jenkins (réactif, moderne).

   * Expliquer pourquoi :

     * ngrok n’est pas nécessaire sur une VM Azure exposée avec IP publique,
     * ngrok devient utile sur une installation locale sans IP publique.

2. **30 minutes de démonstration guidée**

   * Montrer la création du job Jenkins.

   * Coller le Jenkinsfile, expliquer chaque bloc :

     * `agent any`
     * `environment`
     * `stages`
     * `Checkout` vs `Build`.

   * Lancer un premier build manuel.

   * Montrer ensuite la configuration du **Build Trigger** et du **Webhook GitHub**.

   * Faire un push en direct sur `README.md` et montrer le build qui se déclenche.

3. **Travail à réaliser**

   * Créer un job Jenkins équivalent.
   * Configurer le webhook GitHub.
   * Tester avec la modification de `README.md`.

   Pour la remise, demander :

   * Une capture d’écran de la page Webhook GitHub (delivery 200),
   * Une capture d’écran d’un build Jenkins réussi déclenché par webhook,
   * Le contenu du Jenkinsfile.

---

## 4. Annexe – Quand et comment utiliser ngrok (cas Jenkins local)

### 4.1. Cas d’usage

* Jenkins tourne sur `http://localhost:8080` sur une machine locale.
* Pas d’IP publique, pas de port ouvert sur le routeur.
* GitHub ne peut pas envoyer de webhook vers `localhost`.

### 4.2. Principe

1. Installer ngrok sur la machine locale.

2. Lancer :

   ```bash
   ngrok http 8080
   ```

3. Ngrok affiche une URL publique, par exemple :

   ```text
   https://abcd-1234.ngrok-free.app -> http://localhost:8080
   ```

4. Dans GitHub, la **Payload URL** devient :

   ```text
   https://abcd-1234.ngrok-free.app/github-webhook/
   ```

5. Le reste est identique :

   * Configuration du Build Trigger dans Jenkins.
   * Test avec modification de `README.md`.
