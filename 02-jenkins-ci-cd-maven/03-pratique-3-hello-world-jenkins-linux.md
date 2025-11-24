# **PRATIQUE 2 — Hello World Jenkins (Linux)**


## **Table des matières**

* **1. Configuration sans Jenkinsfile (Freestyle + commandes Linux)**

  * 1.1 Ajouter une étape de build — p.2
  * 1.2 Entrer les commandes bash — p.2
  * 1.3 Sauvegarder et exécuter le build — p.2
* **2. Configuration avec Jenkinsfile (Pipeline-as-Code)**

  * 2.1 Créer un Jenkinsfile — p.3
  * 2.2 Configurer le projet Jenkins pour utiliser le Jenkinsfile — p.3
  * 2.3 Sauvegarder et exécuter le pipeline — p.3
* **Conclusion — p.4**



## **Introduction**

Ce premier exercice présente la mise en place d’un projet minimal dans Jenkins sous **Linux**.
L’objectif est d’exécuter des commandes bash simples, d’abord via un **Freestyle Project**, puis via un **Jenkinsfile** pour introduire le modèle Pipeline-as-Code.



# **1. Configuration sans Jenkinsfile**

*Projet Freestyle — exécution de commandes Linux (bash)*

Après avoir créé un projet de type **Freestyle project**, vous pouvez configurer un build exécutant des commandes bash.



## **1.1 — Ajouter une étape de build**

1. Dans la section **Build**, cliquez sur **Add build step**.
2. Sélectionnez **Execute shell** (équivalent Linux du batch Windows).



## **1.2 — Entrer les commandes bash**

Saisissez exactement :

```bash
#!/bin/bash
cd /
ls -la
```

**Signification :**

* `#!/bin/bash`
  Indique que le script doit être interprété par bash (bonne pratique nationale).

* `cd /`
  Change le répertoire courant vers la racine du système Linux.

* `ls -la`
  Affiche la liste détaillée des fichiers et répertoires présents à la racine.



## **1.3 — Sauvegarder et exécuter le build**

* Cliquez sur **Save**
* Cliquez sur **Build Now**

Le résultat complet du script s’affiche dans **Console Output**.



# **2. Configuration avec Jenkinsfile**

*Pipeline Jenkins — version Linux*

Cette section montre comment exécuter le même script Linux au moyen d’un **Jenkinsfile** stocké dans votre dépôt Git.



## **2.1 — Créer un fichier Jenkinsfile**

Dans votre dépôt SCM, créez un fichier nommé **Jenkinsfile** contenant :

```groovy
pipeline {
    agent any

    stages {
        stage('List Files') {
            steps {
                sh '''
                #!/bin/bash
                cd /
                ls -la
                '''
            }
        }
    }
}
```

Ce pipeline reproduit exactement les mêmes commandes que la version Freestyle, cette fois via `sh` (shell Linux).



## **2.2 — Configurer Jenkins pour utiliser le Jenkinsfile**

1. Créez un projet de type **Pipeline**.
2. Dans la section **Pipeline**, choisissez :
   **Pipeline script from SCM**
3. Sélectionnez votre SCM (ex. Git).
4. Renseignez l’URL du dépôt + la branche.
5. Laissez le chemin du Jenkinsfile par défaut :
   `Jenkinsfile`



## **2.3 — Sauvegarder et exécuter le pipeline**

* Cliquez sur **Save**
* Cliquez sur **Build Now**

Les résultats de toutes les étapes apparaîtront dans la **Console** du pipeline.



# **Conclusion**

Dans ce laboratoire, vous avez appris à :

1. Exécuter des **commandes bash** via un projet Freestyle Linux.
2. Migrer la même logique vers un **pipeline Jenkins déclaratif**, versionné dans un dépôt Git.

Ces deux approches constituent les fondations de l’automatisation sous Linux dans Jenkins avant d’aborder les pipelines multi-stages, les tests automatisés et la CI/CD complète.

