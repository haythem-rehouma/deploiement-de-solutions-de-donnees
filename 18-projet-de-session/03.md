**Sujet : Concevoir une chaîne CI/CD complète pour un projet applicatif libre.**

## 1. Règles générales

* Le type d’application est libre.
* Le sujet doit être proposé par l’équipe et validé par le professeur.
* Le dépôt Git est obligatoire (GitHub, GitLab ou équivalent).
* Le pipeline CI/CD doit être entièrement automatisé.



## 2. Exigences techniques minimales

1. **Git et workflow**

   * Dépôt distant.
   * Branches structurées (main + feature).
   * Pull requests obligatoires.

2. **Pipeline CI**

   * Build ou installation des dépendances.
   * Tests automatisés.
   * Analyse de qualité (lint ou équivalent).
   * Production d’un artefact (image Docker ou archive).

3. **Pipeline CD**

   * Déploiement automatique vers un environnement cible (serveur, conteneur, cloud ou VM).
   * Déclenchement contrôlé (push sur main, tag, ou job manuel approuvé).

4. **Gestion des secrets**

   * Secrets via GitHub/GitLab/Jenkins Credentials.
   * Interdiction absolue de secrets en clair dans le dépôt.

5. **Documentation obligatoire**

   * Description du projet (5–10 lignes).
   * Explication du pipeline CI/CD.
   * Procédure de déploiement.
   * Captures d’écran du pipeline en exécution.



## 3. Format de validation du sujet

À remettre avant de commencer :

1. Nom du projet.
2. Description courte.
3. Stack technologique.
4. Outil CI/CD choisi.
5. Environnement de déploiement cible.

Le projet débute uniquement après validation.


## 4. Livrables

* Dépôt Git complet.
* Fichiers CI/CD.
* Scripts de déploiement.
* Documentation synthétique.
* Preuves d’exécution (captures).



## 5. Évaluation (100 points)

* Workflow Git : 20
* Pipeline CI : 25
* Pipeline CD : 25
* Gestion des secrets : 10
* Documentation : 20


