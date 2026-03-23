<a id="top"></a>

# AWS CloudFormation — Série complète de cours

## Table of Contents

| #  | Section |
|----|---------|
| 1  | [Présentation de la série](#section-1) |
| 2  | [Objectifs de la série](#section-2) |
| 3  | [Public cible](#section-3) |
| 4  | [Prérequis](#section-4) |
| 5  | [Structure complète de la série](#section-5) |
| 5a | &nbsp;&nbsp;&nbsp;↳ [Chapitre 1 — Introduction à AWS CloudFormation et concepts fondamentaux](#section-5a) |
| 5b | &nbsp;&nbsp;&nbsp;↳ [Chapitre 2 — Réseau de base avec VPC, Subnet, Internet Gateway et Security Group](#section-5b) |
| 5c | &nbsp;&nbsp;&nbsp;↳ [Chapitre 3 — Déployer une instance EC2 avec CloudFormation](#section-5c) |
| 5d | &nbsp;&nbsp;&nbsp;↳ [Chapitre 4 — Parameters, Outputs et fonctions intrinsèques](#section-5d) |
| 5e | &nbsp;&nbsp;&nbsp;↳ [Chapitre 5 — Conditions, Mappings et pseudo parameters](#section-5e) |
| 5f | &nbsp;&nbsp;&nbsp;↳ [Chapitre 6 — S3 avec CloudFormation : buckets, versioning, outputs et protections](#section-5f) |
| 5g | &nbsp;&nbsp;&nbsp;↳ [Chapitre 7 — IAM Roles, Instance Profiles et permissions de base](#section-5g) |
| 5h | &nbsp;&nbsp;&nbsp;↳ [Chapitre 8 — Déployer une base de données RDS avec CloudFormation](#section-5h) |
| 5i | &nbsp;&nbsp;&nbsp;↳ [Chapitre 9 — Load Balancer et Auto Scaling](#section-5i) |
| 5j | &nbsp;&nbsp;&nbsp;↳ [Chapitre 10 — Structurer les projets CloudFormation : organisation, modularité et nested stacks](#section-5j) |
| 5k | &nbsp;&nbsp;&nbsp;↳ [Chapitre 11 — Change Sets, Drift Detection et Stack Policies](#section-5k) |
| 5l | &nbsp;&nbsp;&nbsp;↳ [Chapitre 12 — Bonnes pratiques, validation, debugging et troubleshooting](#section-5l) |
| 5m | &nbsp;&nbsp;&nbsp;↳ [Chapitre 13 — Intégrer CloudFormation dans une logique CI/CD](#section-5m) |
| 5n | &nbsp;&nbsp;&nbsp;↳ [Chapitre 14 — CloudFormation vs AWS SAM vs AWS CDK](#section-5n) |
| 5o | &nbsp;&nbsp;&nbsp;↳ [Chapitre 15 — Projet final complet](#section-5o) |
| 6  | [Progression pédagogique recommandée](#section-6) |
| 7  | [Résultat attendu à la fin de la série](#section-7) |
| 8  | [Format recommandé pour chaque chapitre](#section-8) |
| 9  | [Conclusion](#section-9) |

---

<a id="section-1"></a>

<details>
<summary>1 - Présentation de la série</summary>

<br/>

Cette série de cours a pour objectif de vous apprendre à utiliser **AWS CloudFormation** de manière progressive, claire et professionnelle, en partant des bases jusqu’à la construction d’infrastructures complètes sur AWS.

L’approche suivie dans cette série repose sur une logique simple :

* comprendre les concepts fondamentaux
* apprendre la structure des templates YAML
* déployer des ressources AWS étape par étape
* organiser des projets CloudFormation propres
* adopter les bonnes pratiques d’Infrastructure as Code

Cette série est pensée pour des débutants motivés, mais elle monte progressivement vers un niveau intermédiaire solide, avec une logique très pratique.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-2"></a>

<details>
<summary>2 - Objectifs de la série</summary>

<br/>

À la fin de cette série, l’apprenant sera capable de :

* comprendre le rôle de CloudFormation dans AWS
* écrire des templates YAML lisibles et structurés
* créer et gérer des stacks
* déployer des ressources réseau, calcul, stockage et sécurité
* utiliser les paramètres, outputs et fonctions intrinsèques
* structurer des projets CloudFormation plus complets
* éviter les erreurs fréquentes
* intégrer CloudFormation dans une logique DevOps et CI/CD

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-3"></a>

<details>
<summary>3 - Public cible</summary>

<br/>

Cette série s’adresse à :

* étudiants en cloud computing
* débutants sur AWS
* administrateurs systèmes
* développeurs souhaitant apprendre l’Infrastructure as Code
* enseignants et formateurs préparant du contenu sur AWS
* professionnels voulant automatiser leurs déploiements AWS

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-4"></a>

<details>
<summary>4 - Prérequis</summary>

<br/>

Avant de commencer, il est recommandé d’avoir :

* des bases générales sur le cloud
* une compréhension minimale des services AWS
* quelques notions sur EC2, S3 et VPC
* AWS CLI installé
* un compte AWS actif
* un éditeur comme VS Code

Aucune expérience avancée en Infrastructure as Code n’est requise pour démarrer.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5"></a>

<details>
<summary>5 - Structure complète de la série</summary>

<br/>

Cette série est composée de 15 chapitres progressifs, allant des bases absolues jusqu’à un projet final complet.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5a"></a>

<details>
<summary>5a - Chapitre 1 — Introduction à AWS CloudFormation et concepts fondamentaux</summary>

<br/>

Dans ce premier chapitre, l’apprenant découvre ce qu’est AWS CloudFormation, pourquoi ce service est important dans une approche Infrastructure as Code, et comment il permet de décrire une infrastructure AWS dans un template YAML ou JSON.

**Contenu prévu :**

* définition de CloudFormation
* notion de template
* notion de stack
* différence entre création manuelle et Infrastructure as Code
* avantages de l’automatisation
* premières notions de change set, drift detection et StackSets
* premier exemple très simple avec un bucket S3

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5b"></a>

<details>
<summary>5b - Chapitre 2 — Réseau de base avec VPC, Subnet, Internet Gateway et Security Group</summary>

<br/>

Ce chapitre introduit les ressources réseau essentielles dans AWS et montre comment les modéliser avec CloudFormation.

**Contenu prévu :**

* définition d’un VPC
* public subnet et private subnet
* route table
* Internet Gateway
* Security Group
* architecture réseau minimale
* premier template réseau complet

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5c"></a>

<details>
<summary>5c - Chapitre 3 — Déployer une instance EC2 avec CloudFormation</summary>

<br/>

Ce chapitre montre comment créer une instance EC2 dans un réseau défini par CloudFormation.

**Contenu prévu :**

* ressource `AWS::EC2::Instance`
* AMI
* instance type
* subnet et security group
* clé SSH
* adresse IP publique
* outputs utiles
* premier déploiement EC2 complet

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5d"></a>

<details>
<summary>5d - Chapitre 4 — Parameters, Outputs et fonctions intrinsèques</summary>

<br/>

Ce chapitre explique comment rendre les templates réutilisables et dynamiques.

**Contenu prévu :**

* section `Parameters`
* section `Outputs`
* fonction `Ref`
* fonction `Fn::GetAtt`
* fonction `Fn::Sub`
* bonnes pratiques d’utilisation
* templates paramétrables

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5e"></a>

<details>
<summary>5e - Chapitre 5 — Conditions, Mappings et pseudo parameters</summary>

<br/>

Dans ce chapitre, l’apprenant apprend à introduire de la logique dans les templates.

**Contenu prévu :**

* section `Mappings`
* section `Conditions`
* fonctions conditionnelles
* pseudo parameters AWS
* adaptation selon région ou environnement
* création conditionnelle de ressources

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5f"></a>

<details>
<summary>5f - Chapitre 6 — S3 avec CloudFormation : buckets, versioning, outputs et protections</summary>

<br/>

Ce chapitre approfondit la gestion des buckets S3 avec CloudFormation.

**Contenu prévu :**

* création de bucket S3
* nommage
* versioning
* tags
* outputs
* `DeletionPolicy`
* points d’attention sur la suppression
* template S3 plus réaliste

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5g"></a>

<details>
<summary>5g - Chapitre 7 — IAM Roles, Instance Profiles et permissions de base</summary>

<br/>

Ce chapitre introduit les ressources IAM dans CloudFormation, avec prudence et structure.

**Contenu prévu :**

* rôle IAM
* policies
* instance profile
* association à une instance EC2
* permissions minimales
* principe du moindre privilège
* exemple EC2 + IAM Role

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5h"></a>

<details>
<summary>5h - Chapitre 8 — Déployer une base de données RDS avec CloudFormation</summary>

<br/>

Ce chapitre montre comment automatiser une base de données relationnelle.

**Contenu prévu :**

* ressource RDS
* subnet group
* security group pour base de données
* paramètres sensibles
* outputs
* précautions de suppression
* architecture simple application + base de données

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5i"></a>

<details>
<summary>5i - Chapitre 9 — Load Balancer et Auto Scaling</summary>

<br/>

Ce chapitre fait passer la série vers une infrastructure plus proche d’un environnement professionnel.

**Contenu prévu :**

* Application Load Balancer
* Target Groups
* Launch Template ou configuration associée
* Auto Scaling Group
* intégration réseau
* règles de sécurité
* déploiement d’une architecture scalable

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5j"></a>

<details>
<summary>5j - Chapitre 10 — Structurer les projets CloudFormation : organisation, modularité et nested stacks</summary>

<br/>

À ce stade, les templates deviennent plus grands. Ce chapitre apprend à organiser proprement les projets.

**Contenu prévu :**

* découpage des templates
* modularité
* nested stacks
* séparation réseau / calcul / stockage
* logique de projet professionnel
* maintenance et lisibilité

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5k"></a>

<details>
<summary>5k - Chapitre 11 — Change Sets, Drift Detection et Stack Policies</summary>

<br/>

Ce chapitre est centré sur la sécurité opérationnelle et la maîtrise des changements.

**Contenu prévu :**

* change sets
* aperçu avant mise à jour
* drift detection
* lecture des écarts
* stack policies
* prévention des erreurs de mise à jour
* gestion plus sûre des stacks

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5l"></a>

<details>
<summary>5l - Chapitre 12 — Bonnes pratiques, validation, debugging et troubleshooting</summary>

<br/>

Ce chapitre aide l’apprenant à devenir autonome face aux erreurs.

**Contenu prévu :**

* erreurs fréquentes
* lecture des événements CloudFormation
* causes classiques d’échec
* ordre de création des ressources
* validation YAML
* stratégie de test
* conventions de nommage
* recommandations AWS

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5m"></a>

<details>
<summary>5m - Chapitre 13 — Intégrer CloudFormation dans une logique CI/CD</summary>

<br/>

Ce chapitre relie CloudFormation au DevOps moderne.

**Contenu prévu :**

* CloudFormation dans un pipeline
* déploiement automatisé
* Git et contrôle de version
* validation avant déploiement
* logique de promotion dev / test / prod
* place de CloudFormation dans une chaîne CI/CD

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5n"></a>

<details>
<summary>5n - Chapitre 14 — CloudFormation vs AWS SAM vs AWS CDK</summary>

<br/>

Ce chapitre ouvre vers les outils complémentaires de l’écosystème AWS.

**Contenu prévu :**

* comparaison CloudFormation / SAM / CDK
* cas d’usage de chaque outil
* limites et avantages
* comment CloudFormation reste la base commune
* orientation pédagogique pour aller plus loin

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-5o"></a>

<details>
<summary>5o - Chapitre 15 — Projet final complet</summary>

<br/>

Le dernier chapitre sert de synthèse pratique.

**Contenu prévu :**

* réseau complet
* instance EC2
* sécurité
* stockage
* outputs
* organisation du template
* déploiement complet
* lecture et vérification de l’infrastructure

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-6"></a>

<details>
<summary>6 - Progression pédagogique recommandée</summary>

<br/>

La progression suit une logique de complexité croissante :

1. comprendre CloudFormation
2. construire le réseau
3. déployer du calcul
4. rendre les templates dynamiques
5. ajouter de la logique
6. gérer le stockage
7. gérer les permissions
8. ajouter une base de données
9. passer à une architecture scalable
10. organiser les gros projets
11. maîtriser les mises à jour
12. apprendre à corriger les erreurs
13. automatiser en CI/CD
14. comparer avec les outils voisins
15. réaliser un projet final complet

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-7"></a>

<details>
<summary>7 - Résultat attendu à la fin de la série</summary>

<br/>

À la fin de cette série, l’apprenant pourra :

* lire un template CloudFormation avec assurance
* écrire ses propres templates
* déployer une infrastructure simple à intermédiaire sur AWS
* comprendre la logique de dépendance entre ressources
* utiliser CloudFormation dans une démarche professionnelle
* préparer la suite vers des sujets plus avancés en IaC et DevOps

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-8"></a>

<details>
<summary>8 - Format recommandé pour chaque chapitre</summary>

<br/>

Pour garder une cohérence pédagogique, chaque chapitre peut suivre la structure suivante :

* introduction
* table des matières
* explication conceptuelle
* schémas Mermaid
* exemples YAML
* déploiement console et CLI
* erreurs fréquentes
* résumé des commandes
* conclusion

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="section-9"></a>

<details>
<summary>9 - Conclusion</summary>

<br/>

Cette série sur **AWS CloudFormation** est conçue comme un parcours complet, progressif et professionnalisant. Elle commence par les bases absolues, puis avance vers des déploiements plus structurés et plus proches des réalités du terrain.

Le but n’est pas seulement de savoir écrire un template, mais de comprendre comment **penser une infrastructure AWS comme du code**, de manière propre, lisible, maintenable et réutilisable.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>
