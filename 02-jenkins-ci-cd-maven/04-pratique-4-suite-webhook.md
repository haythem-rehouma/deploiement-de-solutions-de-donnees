# Cours – Pipeline Jenkins + GitHub, Webhooks et rôle de ngrok

## 0. Objectif du cours

À la fin de ce cours, vous devez être capable de :

1. Expliquer **à quoi sert un webhook** GitHub et pourquoi on en a besoin avec Jenkins.
2. Comprendre **dans quels cas ngrok est utile** et pourquoi, dans notre scénario Azure, il ne l’est pas.
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

---

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

#### Situation A – Serveur Jenkins avec IP publique (ton cas : VM Azure)

* Jenkins est sur une **VM Azure**.
* La VM a une **IP publique**.
* Le port `8080` est ouvert.
* GitHub peut appeler directement :

  ```text
  http://IP_PUBLIQUE_VM:8080/github-webhook/
  ```

Dans ce cas :
**Aucun besoin de ngrok.**
Ton serveur est déjà « dans le monde » avec une IP publique.

#### Situation B – Jenkins sur un poste local (PC perso, laptop, VM interne)

* Jenkins tourne sur ton laptop chez toi ou sur une VM dans un réseau privé.
* Pas d’IP publique.
* GitHub ne peut pas atteindre `http://localhost:8080`.

Dans ce cas, on utilise **ngrok** :

* Ngrok ouvre un **tunnel sécurisé** de GitHub vers ta machine.

* Tu obtiens une URL publique du type :

  ```text
  https://xxxxx.ngrok.io/github-webhook/
  ```

* GitHub envoie la requête vers cette URL.

* Ngrok la transfère vers `http://localhost:8080/github-webhook/`.

Donc, **ngrok est une béquille pour exposer temporairement un Jenkins local** sans gérer IP publique, firewall, DNS, etc.

---

### 1.3. Conclusion pédagogique

* Sur **VM Azure** avec IP publique :
  ➜ On configure directement le webhook GitHub vers `http://IP_PUBLIQUE:8080/github-webhook/`.

* Sur **laptop / réseau local sans IP publique** :
  ➜ On utilise **ngrok** pour obtenir une URL publique temporaire.

Dans ton cours, tu peux expliquer les deux, mais le TP « officiel » repose sur **Azure (sans ngrok)**.

---

## 2. Mise en place complète du pipeline Jenkins + GitHub (cas VM Azure, sans ngrok)

Je vais procéder comme en cours, étape par étape.

### 2.1. Pré-requis techniques

Sur la **VM Jenkins (Ubuntu 24.04 sur Azure)**, on doit disposer de :

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

3. Jenkins installé et en fonctionnement (ce que tu as déjà fait) :

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

* Soit tu colles le Jenkinsfile directement dans le job,
* Soit tu le mets dans le repo (`Jenkinsfile`) et tu configures Jenkins pour le lire depuis Git.



#### Option A – Pipeline Script (plus simple pour débuter)

1. Connecte-toi à Jenkins : `http://IP_PUBLIQUE_VM:8080/`.
2. Clique sur **New Item**.
3. Donne un nom au job, par exemple :
   `01-hello-python-linux-pipeline`.
4. Choisis **Pipeline** puis clique sur **OK**.
5. Dans la page de configuration :

   * Descends jusqu’à **Pipeline**.
   * Sélectionne **Definition: Pipeline script**.
   * Colle **le Jenkinsfile complet** dans la zone Script (celui que tu as fourni).
6. Sauvegarde (**Save**).

À ce stade, ton job sait :

* Sur quel agent s’exécuter (`agent any`),
* Comment configurer l’environnement (`JAVA_HOME`, `PYTHON_HOME`, `PATH`),
* Quoi faire dans les stages (`Checkout` + `Build`).

Tu peux tester un premier run manuel avec **Build Now** pour vérifier que tout est correct avant d’introduire le webhook.



### 2.3. Étape 2 – Tester le pipeline manuellement (sans webhook)

1. Dans la page du job, clique sur **Build Now**.
2. Va dans **Console Output** du build en cours.

Tu dois voir :

* Le checkout Git du repo `hello-python-linux`.
* Le message `Running on Unix`.
* L’exécution de `javac` / `java` (si les fichiers existent) et `python3 hello.py`.

Si quelque chose échoue, tu corriges :

* Les chemins Java/Python,
* Le Jenkinsfile (par exemple commenter `javac` si tu n’as pas de `HelloWorld.java` dans ce repo).

Une fois que le pipeline passe **manuellement**, on peut passer au webhook.



### 2.4. Étape 3 – Activer le déclencheur GitHub dans Jenkins

Dans le même job :

1. Clique sur **Configure**.

2. Descends à la section **Build Triggers**.

3. Coche :

   * **GitHub hook trigger for GITScm polling**
     (ou éventuellement **Build when a change is pushed to GitHub** selon la version).

4. Sauvegarde (**Save**).

Ce déclencheur dit à Jenkins :
« Si GitHub m’envoie un webhook, je dois consulter le dépôt Git et potentiellement lancer un build. »



### 2.5. Étape 4 – Créer le webhook dans GitHub

Sur GitHub, dans le dépôt `hello-python-linux` :

1. Ouvre la page du dépôt.
2. Clique sur **Settings** (onglet du repo).
3. Dans le menu de gauche, clique sur **Webhooks**.
4. Clique sur **Add webhook**.

Complète les champs :

* **Payload URL** :

  ```text
  http://IP_PUBLIQUE_DE_LA_VM:8080/github-webhook/
  ```

  Attention au `/` final, c’est l’URL standard de Jenkins pour GitHub.

* **Content type** :

  ```text
  application/json
  ```

* **Secret** (optionnel mais recommandé, même dans un TP) :

  * Par exemple : `devops-jenkins-2025`
  * Si tu le mets, pense à le configurer côté Jenkins (Manage Jenkins → Configure System → GitHub → ajouter un serveur GitHub et le secret).

* **Which events would you like to trigger this webhook?**

  * Choisis **Just the push event**.

* Coche **Active**.

* Clique sur **Add webhook**.

GitHub va envoyer immédiatement un **Ping** vers Jenkins.
Dans la page Webhooks, tu peux cliquer sur le webhook → **Recent deliveries** → tu dois voir un statut **200** si Jenkins a bien répondu.



### 2.6. Étape 5 – Tester le déclenchement automatique (modification de README.md)

Sur ta machine de développement :

1. Clone le dépôt s’il ne l’est pas déjà :

   ```bash
   git clone https://github.com/haythem-rehouma/hello-python-linux.git
   cd hello-python-linux
   ```

2. Modifie `README.md` :

   ```bash
   echo "Test webhook Jenkins - $(date)" >> README.md
   ```

3. Commit + push :

   ```bash
   git add README.md
   git commit -m "Test webhook Jenkins (README.md)"
   git push origin main
   ```

4. Retourne sur Jenkins :

   * Sur la page du job, tu dois voir un **nouveau build** se lancer automatiquement.
   * Vérifie la **Console Output** pour t’assurer que :

     * Le code a bien été mis à jour,
     * Les commandes Java/Python se sont bien exécutées.

5. Testez :

   > Lorsque vous modifiez `README.md` et poussez sur GitHub, un build Jenkins doit se déclencher automatiquement **sans cliquer sur Build Now**.



## 3. Partie pédagogique

1. **10–15 minutes de théorie**

   * Rappeler le rôle de Jenkins en CI/CD.
   * Expliquer la différence :

     * Build manuel
     * Polling Git (toutes les X minutes)
     * Webhook GitHub → Jenkins (réactif, moderne).
   * Expliquer pourquoi :

     * On n’a pas besoin de ngrok sur Azure.
     * On en aurait besoin sur un laptop local.

2. **30 minutes de démonstration guidée** (toi à l’écran)

   * Montre la création du job Jenkins.
   * Colle le Jenkinsfile, explique chaque bloc :

     * `agent any`
     * `environment`
     * `stages`
     * `Checkout` vs `Build`.
   * Lance un premier build manuel.
   * Puis montre la configuration du **Build Trigger** et du **Webhook GitHub**.
   * Fais un push en live sur `README.md` et montre le build qui part.

3. **Travail à réaliser**

   * Vous devez :

     * Créer votre propre job Jenkins,
     * Configurer le webhook,
     * Tester avec la modification de `README.md`.
   * Pour la remise :

     * une capture d’écran de la page Webhook GitHub (delivery 200),
     * une capture d’écran du build Jenkins réussi,
     * le Jenkinsfile (copié-collé dans un fichier texte).



## 4. Annexe – Quand et comment utiliser ngrok (cas Jenkins local)


### 4.1. Cas d’usage

* Jenkins tourne sur `http://localhost:8080` sur leur machine.
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

   * Build Trigger dans Jenkins.
   * Test avec modification de `README.md`.

