# **PRATIQUE 1 — Hello World avec Jenkins (Windows)**


## **Table des matières**

* **1. Configuration sans Jenkinsfile**

  * 1.1 Ajouter une étape de build — p.2
  * 1.2 Entrer les commandes batch — p.2
  * 1.3 Sauvegarder et exécuter le build — p.2
* **2. Configuration avec Jenkinsfile**

  * 2.1 Créer un Jenkinsfile — p.3
  * 2.2 Configurer le projet Jenkins pour utiliser le Jenkinsfile — p.3
  * 2.3 Sauvegarder et exécuter le pipeline — p.3
* **Conclusion — p.4**

---

## **Introduction**

Ce premier exercice vise à comprendre la mise en place d’un projet minimal dans Jenkins.
La démarche est volontairement progressive :

1. **Exécuter des commandes Windows simples** dans un projet freestyle,
2. Puis **automatiser la même logique** grâce à un **Jenkinsfile** intégré dans un pipeline moderne.

Cette pratique constitue la base de tout pipeline CI/CD sous Windows.

---

# **1. Configuration sans Jenkinsfile**

*Projet Freestyle — exécution de commandes Windows*

Après avoir créé un projet de type **Freestyle project**, vous pouvez configurer un build simple exécutant des commandes batch.

---

## **1.1 — Ajouter une étape de build**

1. Accédez à la section **Build**.
2. Cliquez sur **Add build step**.
3. Sélectionnez **Execute Windows batch command**.

---

## **1.2 — Entrer les commandes batch**

Saisissez exactement les instructions suivantes :

```
C:
cd \
dir
```

**Rôle de chaque commande :**

* **C:**
  Change le lecteur actif vers le lecteur C:.

* cd \
  Déplace le répertoire courant à la racine du lecteur.

* **dir**
  Liste les fichiers et répertoires présents à cet emplacement.

---

## **1.3 — Sauvegarder et exécuter le build**

* Cliquez sur **Save**.
* Cliquez ensuite sur **Build Now**.

Le résultat détaillé de l’exécution se trouve dans **Console Output**.

---

# **2. Configuration avec Jenkinsfile**

*Pipeline-as-Code — définition du pipeline avec fichier Jenkinsfile*

Cette section montre comment reproduire la même logique au moyen d’un **pipeline déclaré** dans un fichier Jenkinsfile, stocké dans votre dépôt SCM.

---

## **2.1 — Créer un fichier Jenkinsfile**

Dans votre dépôt Git, créez un fichier nommé **Jenkinsfile** contenant :

```groovy
pipeline {
    agent any

    stages {
        stage('List Files') {
            steps {
                bat '''
                C:
                cd \\
                dir
                '''
            }
        }
    }
}
```

Ce pipeline déclare une seule étape, *List Files*, qui exécute les commandes batch identiques à celles de la configuration freestyle.

---

## **2.2 — Configurer Jenkins pour utiliser le Jenkinsfile**

1. Créez un nouveau projet de type **Pipeline**.
2. Dans la section **Pipeline**, sélectionnez **Pipeline script from SCM**.
3. Choisissez le type de SCM (ex. Git).
4. Renseignez l’URL du dépôt et la branche.
5. Laissez le chemin du Jenkinsfile par défaut (`Jenkinsfile` à la racine), sauf si vous l’avez placé ailleurs.

---

## **2.3 — Sauvegarder et exécuter le pipeline**

* Cliquez sur **Save**.
* Cliquez ensuite sur **Build Now**.

L’exécution du pipeline ainsi que les sorties du script apparaissent dans la console du pipeline.

---

# **Conclusion**

Cette pratique a permis d’établir une distinction claire entre :

1. **Un projet Freestyle**, basé sur l’exécution directe de commandes Windows,
2. **Un pipeline moderne**, défini par un **Jenkinsfile**, permettant une configuration stable, versionnée, et reproductible.

Ces deux approches constituent les fondations de l’automatisation logicielle sous Jenkins, avant d’aborder les pipelines multi-stages, l’intégration de tests et l’automatisation complète CI/CD.


