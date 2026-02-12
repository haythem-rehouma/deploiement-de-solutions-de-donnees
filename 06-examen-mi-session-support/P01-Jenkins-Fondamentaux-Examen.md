# EXAMEN — Jenkins (Débutant / Facile)

## CONSIGNES GÉNÉRALES

* Répondez clairement et simplement.
* Quand on demande une commande, écrivez-la exactement.
* Si une question vous demande “expliquer”, faites **2–4 lignes max**.

<br/>

## PARTIE 1 — QCM (10 points)

Cochez **une seule** bonne réponse par question.

1. Jenkins est principalement utilisé pour :<br/>
   A. Stocker des fichiers dans le cloud<br/>
   B. Automatiser des tâches CI/CD<br/>
   C. Remplacer Git<br/>
   D. Héberger des bases de données

2. Dans Jenkins, un “Job” est :<br/>
   A. Un serveur<br/>
   B. Une tâche automatisée (build/test/deploy)<br/>
   C. Un type de base de données<br/>
   D. Un plugin obligatoire

3. Le fichier **Jenkinsfile** sert à :<br/>
   A. Décrire un pipeline en code<br/>
   B. Chiffrer des mots de passe<br/>
   C. Installer Jenkins<br/>
   D. Sauvegarder les logs

4. Dans un pipeline, une **stage** représente :<br/>
   A. Une machine virtuelle<br/>
   B. Une étape logique (Build, Test, Deploy…)<br/>
   C. Un utilisateur admin<br/>
   D. Un port réseau

5. Le composant qui exécute réellement les builds (sur une machine distante) s’appelle :<br/>
   A. Controller (Master)<br/>
   B. Agent (Node)<br/>
   C. Plugin Manager<br/>
   D. Workspace Cleaner

6. Un “Workspace” dans Jenkins, c’est :<br/>
   A. Un tableau Excel de suivi<br/>
   B. Le dossier de travail où le code est cloné et compilé<br/>
   C. Un plugin<br/>
   D. Une base de données

7. Le plugin le plus souvent lié à Git s’appelle :<br/>
   A. Git Plugin<br/>
   B. Docker Plugin<br/>
   C. Maven Plugin<br/>
   D. Linux Plugin

8. Un “build” est :<br/>
   A. Une suppression du dépôt<br/>
   B. Une exécution du job (compilation/tests/etc.)<br/>
   C. Une mise à jour du système<br/>
   D. Une connexion SSH

9. Pour lancer un job manuellement dans l’interface Jenkins, on clique souvent sur :<br/>
   A. Configure System<br/>
   B. Build Now<br/>
   C. Manage Users<br/>
   D. Quiet Down

10. Un webhook GitHub sert généralement à :<br/>
    A. Bloquer les commits<br/>
    B. Déclencher Jenkins automatiquement lors d’un push/PR<br/>
    C. Supprimer les branches<br/>
    D. Créer des tags

<br/>

## PARTIE 2 — Vrai / Faux (6 points)

Écrivez **Vrai** ou **Faux**.

11. Jenkins peut exécuter des pipelines déclaratifs ou scriptés.  
12. Un agent Jenkins peut être une machine Linux, Windows ou un conteneur Docker.  
13. Un Jenkinsfile doit obligatoirement être écrit en Python.  
14. Jenkins peut déclencher un build automatiquement suite à un push Git.  
15. Les plugins Jenkins servent à ajouter des fonctionnalités.  
16. Un pipeline ne peut contenir qu’une seule stage.  

<br/>

## PARTIE 3 — Questions courtes (8 points)

17. Donnez **2 avantages** de l’automatisation CI/CD avec Jenkins. (2–4 lignes)  
18. Expliquez la différence **Controller vs Agent**. (2–4 lignes)  
19. Citez **3 exemples** de stages classiques dans un pipeline.  
20. Qu’est-ce qu’un **plugin** Jenkins ? Donnez **1 exemple**.  
21. Qu’est-ce qu’un **trigger** dans Jenkins ? Donnez **1 exemple**.  
22. À quoi sert la section **post** dans un pipeline (idée générale) ?  

<br/>

## PARTIE 4 — Mini Jenkinsfile (6 points)

Complétez ce pipeline Jenkins **simple** (déclaratif) :

**Objectif :** afficher un message, puis exécuter une commande `echo`, puis finir.

Complétez les parties `_____` :

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
````

* (a) Complétez la première ligne `_____ 'Bonjour Jenkins'`
* (b) Complétez la commande du `sh "_____"` pour afficher `Je suis dans Jenkins`

<br/>

## BARÈME

* Partie 1 : 10 pts
* Partie 2 : 6 pts
* Partie 3 : 8 pts
* Partie 4 : 6 pts

**Total : 30 pts**

```
```
