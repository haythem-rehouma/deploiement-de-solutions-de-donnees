# Quiz Final - AWS CloudFormation (30 Questions)

> **Consignes** : Pour chaque question, choisissez la **meilleure** réponse parmi les choix proposés. Une seule réponse est correcte par question, sauf indication contraire.

---

## Question 1 - Concepts de base

Quel est le rôle principal d'AWS CloudFormation ?

- A) Surveiller les performances des instances EC2
- B) Déployer et gérer l'infrastructure AWS sous forme de code (IaC)
- C) Héberger des sites web statiques
- D) Gérer les utilisateurs et les permissions IAM

---

## Question 2 - Vocabulaire fondamental

Comment appelle-t-on l'ensemble des ressources AWS créées à partir d'un template CloudFormation ?

- A) Un Cluster
- B) Un Deployment
- C) Un Stack
- D) Un Blueprint

---

## Question 3 - Structure d'un template

Quelle est la **seule section obligatoire** dans un template CloudFormation ?

- A) `Parameters`
- B) `Outputs`
- C) `Resources`
- D) `Description`

---

## Question 4 - Format de template

Quels formats de fichier sont acceptés pour un template CloudFormation ?

- A) JSON uniquement
- B) YAML uniquement
- C) JSON et YAML
- D) JSON, YAML et XML

---

## Question 5 - VPC

Dans un template CloudFormation, quelle propriété est **obligatoire** pour créer un `AWS::EC2::VPC` ?

- A) `EnableDnsSupport`
- B) `CidrBlock`
- C) `VpcName`
- D) `InstanceTenancy`

---

## Question 6 - Réseau

Pour qu'une instance EC2 dans un subnet puisse accéder à internet, quelles ressources CloudFormation sont nécessaires ? *(plusieurs réponses)*

- A) Un Internet Gateway attaché au VPC, une Route Table avec une route vers 0.0.0.0/0, et un Subnet avec `MapPublicIpOnLaunch: true`
- B) Un NAT Gateway uniquement
- C) Un Security Group avec une règle sortante
- D) Un VPC Endpoint vers le service Internet

---

## Question 7 - Security Group

Que se passe-t-il si vous ne définissez aucune règle `SecurityGroupIngress` dans un Security Group CloudFormation ?

- A) Tout le trafic entrant est autorisé par défaut
- B) Tout le trafic entrant est bloqué par défaut
- C) Le stack échoue au déploiement
- D) Le Security Group est ignoré

---

## Question 8 - EC2

Quelle propriété du type `AWS::EC2::Instance` permet d'exécuter un script au démarrage de l'instance ?

- A) `StartupScript`
- B) `InitScript`
- C) `UserData`
- D) `BootstrapCommand`

---

## Question 9 - UserData

Quelle fonction intrinsèque est **généralement utilisée** pour encoder le `UserData` d'une instance EC2 ?

- A) `!Ref`
- B) `!Sub`
- C) `!Base64`
- D) `!GetAtt`

---

## Question 10 - Parameters

Quel type de paramètre CloudFormation permet de sélectionner automatiquement parmi les clés SSH existantes dans le compte AWS ?

- A) `String`
- B) `AWS::EC2::KeyPair::KeyName`
- C) `AWS::SSM::Parameter::Value`
- D) `CommaDelimitedList`

---

## Question 11 - Fonctions intrinsèques

Quelle est la différence entre `!Ref` et `!GetAtt` ?

- A) `!Ref` retourne un attribut spécifique, `!GetAtt` retourne l'identifiant logique
- B) `!Ref` retourne l'identifiant ou la valeur par défaut, `!GetAtt` retourne un attribut spécifique comme une IP ou un ARN
- C) Il n'y a aucune différence
- D) `!GetAtt` ne fonctionne qu'avec les paramètres

---

## Question 12 - Mappings

À quoi sert la section `Mappings` dans un template CloudFormation ?

- A) À définir des variables d'environnement
- B) À créer des tables de correspondance statiques (ex: AMI par région)
- C) À définir des paramètres dynamiques
- D) À mapper les ports réseau

---

## Question 13 - Conditions

Quel ensemble de fonctions permet de créer des conditions dans CloudFormation ?

- A) `!If`, `!Equals`, `!And`, `!Or`, `!Not`
- B) `!Switch`, `!Case`, `!Default`
- C) `!When`, `!Then`, `!Else`
- D) `!Condition`, `!Check`, `!Validate`

---

## Question 14 - Outputs et Export

À quoi sert la propriété `Export` dans la section `Outputs` ?

- A) À exporter les logs du stack
- B) À rendre une valeur disponible pour d'autres stacks via `!ImportValue`
- C) À sauvegarder la valeur dans S3
- D) À envoyer la valeur par email

---

## Question 15 - S3

Quelle propriété CloudFormation permet d'activer le versioning sur un bucket S3 ?

- A) `VersionControl: enabled`
- B) `BucketVersioning: true`
- C) `VersioningConfiguration` avec `Status: Enabled`
- D) `EnableVersioning: true`

---

## Question 16 - DeletionPolicy

Que fait `DeletionPolicy: Retain` sur une ressource CloudFormation ?

- A) La ressource est supprimée avec le stack
- B) La ressource est conservée même si le stack est supprimé
- C) La ressource est sauvegardée dans un snapshot puis supprimée
- D) La ressource est déplacée dans un autre stack

---

## Question 17 - IAM

Quel est le rôle d'un `AWS::IAM::InstanceProfile` dans CloudFormation ?

- A) Créer un utilisateur IAM pour l'instance
- B) Permettre à une instance EC2 d'assumer un rôle IAM
- C) Définir les règles du Security Group
- D) Configurer l'accès SSH à l'instance

---

## Question 18 - RDS

Quelle propriété de `AWS::RDS::DBInstance` détermine si la base de données est accessible depuis internet ?

- A) `InternetAccess`
- B) `PubliclyAccessible`
- C) `ExternalAccess`
- D) `AllowPublicIP`

---

## Question 19 - GetAtt avec RDS

Quelle syntaxe permet de récupérer l'adresse du endpoint d'une instance RDS dans un template CloudFormation ?

- A) `!Ref MaDB`
- B) `!GetAtt MaDB.Endpoint.Address`
- C) `!GetAtt MaDB.PublicIP`
- D) `!Sub ${MaDB.URL}`

---

## Question 20 - Load Balancer

Quel type de ressource CloudFormation crée un Application Load Balancer (ALB) ?

- A) `AWS::EC2::LoadBalancer`
- B) `AWS::ELB::ApplicationLoadBalancer`
- C) `AWS::ElasticLoadBalancingV2::LoadBalancer`
- D) `AWS::ALB::LoadBalancer`

---

## Question 21 - Auto Scaling

Dans un `AWS::AutoScaling::AutoScalingGroup`, quelle propriété définit le nombre minimum d'instances ?

- A) `DesiredCapacity`
- B) `MinInstances`
- C) `MinSize`
- D) `MinCapacity`

---

## Question 22 - Nested Stacks

Quel type de ressource CloudFormation permet de créer un nested stack ?

- A) `AWS::CloudFormation::NestedStack`
- B) `AWS::CloudFormation::Stack`
- C) `AWS::CloudFormation::SubStack`
- D) `AWS::CloudFormation::Module`

---

## Question 23 - Nested Stacks (suite)

Où doit être stocké le template d'un nested stack pour être référencé par `TemplateURL` ?

- A) Sur le disque local
- B) Dans un bucket S3
- C) Dans un repository CodeCommit
- D) Dans un paramètre SSM

---

## Question 24 - Change Sets

Quel est l'avantage principal d'un Change Set dans CloudFormation ?

- A) Il déploie automatiquement les modifications
- B) Il permet de prévisualiser les changements avant de les appliquer
- C) Il annule automatiquement les erreurs
- D) Il crée une copie de sauvegarde du stack

---

## Question 25 - Drift Detection

Que détecte la fonctionnalité Drift Detection de CloudFormation ?

- A) Les erreurs de syntaxe dans le template
- B) Les différences entre la configuration actuelle des ressources et celle définie dans le template
- C) Les ressources non utilisées
- D) Les failles de sécurité

---

## Question 26 - Bonnes pratiques

Pourquoi est-il recommandé de ne **jamais** écrire des mots de passe en clair dans un template CloudFormation ?

- A) Cela provoque une erreur de déploiement
- B) Les templates sont souvent versionnés dans Git et les valeurs seraient exposées
- C) CloudFormation ne supporte pas les chaînes de caractères
- D) Les mots de passe sont automatiquement supprimés par AWS

---

## Question 27 - Validation

Quelle commande AWS CLI permet de valider la syntaxe d'un template CloudFormation **avant** le déploiement ?

- A) `aws cloudformation check-template`
- B) `aws cloudformation validate-template`
- C) `aws cloudformation lint-template`
- D) `aws cloudformation test-template`

---

## Question 28 - CI/CD

Dans un pipeline CI/CD avec CodePipeline et CloudFormation, quelle étape vient **avant** le déploiement ?

- A) Le monitoring
- B) Le rollback
- C) La source (récupération du code depuis un repository)
- D) La mise à l'échelle automatique

---

## Question 29 - SAM vs CloudFormation

Quel est l'avantage principal d'AWS SAM par rapport à CloudFormation natif ?

- A) SAM remplace complètement CloudFormation
- B) SAM fournit une syntaxe simplifiée pour les applications serverless (Lambda, API Gateway, DynamoDB)
- C) SAM est le seul moyen de déployer des fonctions Lambda
- D) SAM ne nécessite pas de template YAML

---

## Question 30 - CDK

Quelle affirmation est **vraie** concernant AWS CDK ?

- A) CDK génère des templates CloudFormation à partir de code dans un langage de programmation (Python, TypeScript, etc.)
- B) CDK remplace complètement CloudFormation et ne l'utilise pas
- C) CDK ne supporte que le langage Java
- D) CDK est un service de monitoring d'infrastructure

---

> **Bonne chance !**
