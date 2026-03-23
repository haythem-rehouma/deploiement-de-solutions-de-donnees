# Quiz Final - AWS CloudFormation (30 Questions)

> **Consignes** : Pour chaque question, choisissez la **meilleure** reponse parmi les choix proposes. Une seule reponse est correcte par question, sauf indication contraire.

---

## Question 1 - Concepts de base

Quel est le role principal d'AWS CloudFormation ?

- A) Surveiller les performances des instances EC2
- B) Deployer et gerer l'infrastructure AWS sous forme de code (IaC)
- C) Heberger des sites web statiques
- D) Gerer les utilisateurs et les permissions IAM

---

## Question 2 - Vocabulaire fondamental

Comment appelle-t-on l'ensemble des ressources AWS creees a partir d'un template CloudFormation ?

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

Quels formats de fichier sont acceptes pour un template CloudFormation ?

- A) JSON uniquement
- B) YAML uniquement
- C) JSON et YAML
- D) JSON, YAML et XML

---

## Question 5 - VPC

Dans un template CloudFormation, quelle propriete est **obligatoire** pour creer un `AWS::EC2::VPC` ?

- A) `EnableDnsSupport`
- B) `CidrBlock`
- C) `VpcName`
- D) `InstanceTenancy`

---

## Question 6 - Reseau

Pour qu'une instance EC2 dans un subnet puisse acceder a internet, quelles ressources CloudFormation sont necessaires ? *(plusieurs reponses)*

- A) Un Internet Gateway attache au VPC, une Route Table avec une route vers 0.0.0.0/0, et un Subnet avec `MapPublicIpOnLaunch: true`
- B) Un NAT Gateway uniquement
- C) Un Security Group avec une regle sortante
- D) Un VPC Endpoint vers le service Internet

---

## Question 7 - Security Group

Que se passe-t-il si vous ne definissez aucune regle `SecurityGroupIngress` dans un Security Group CloudFormation ?

- A) Tout le trafic entrant est autorise par defaut
- B) Tout le trafic entrant est bloque par defaut
- C) Le stack echoue au deploiement
- D) Le Security Group est ignore

---

## Question 8 - EC2

Quelle propriete du type `AWS::EC2::Instance` permet d'executer un script au demarrage de l'instance ?

- A) `StartupScript`
- B) `InitScript`
- C) `UserData`
- D) `BootstrapCommand`

---

## Question 9 - UserData

Quelle fonction intrinseque est **generalement utilisee** pour encoder le `UserData` d'une instance EC2 ?

- A) `!Ref`
- B) `!Sub`
- C) `!Base64`
- D) `!GetAtt`

---

## Question 10 - Parameters

Quel type de parametre CloudFormation permet de selectionner automatiquement parmi les cles SSH existantes dans le compte AWS ?

- A) `String`
- B) `AWS::EC2::KeyPair::KeyName`
- C) `AWS::SSM::Parameter::Value`
- D) `CommaDelimitedList`

---

## Question 11 - Fonctions intrinseques

Quelle est la difference entre `!Ref` et `!GetAtt` ?

- A) `!Ref` retourne un attribut specifique, `!GetAtt` retourne l'identifiant logique
- B) `!Ref` retourne l'identifiant ou la valeur par defaut, `!GetAtt` retourne un attribut specifique comme une IP ou un ARN
- C) Il n'y a aucune difference
- D) `!GetAtt` ne fonctionne qu'avec les parametres

---

## Question 12 - Mappings

A quoi sert la section `Mappings` dans un template CloudFormation ?

- A) A definir des variables d'environnement
- B) A creer des tables de correspondance statiques (ex: AMI par region)
- C) A definir des parametres dynamiques
- D) A mapper les ports reseau

---

## Question 13 - Conditions

Quel ensemble de fonctions permet de creer des conditions dans CloudFormation ?

- A) `!If`, `!Equals`, `!And`, `!Or`, `!Not`
- B) `!Switch`, `!Case`, `!Default`
- C) `!When`, `!Then`, `!Else`
- D) `!Condition`, `!Check`, `!Validate`

---

## Question 14 - Outputs et Export

A quoi sert la propriete `Export` dans la section `Outputs` ?

- A) A exporter les logs du stack
- B) A rendre une valeur disponible pour d'autres stacks via `!ImportValue`
- C) A sauvegarder la valeur dans S3
- D) A envoyer la valeur par email

---

## Question 15 - S3

Quelle propriete CloudFormation permet d'activer le versioning sur un bucket S3 ?

- A) `VersionControl: enabled`
- B) `BucketVersioning: true`
- C) `VersioningConfiguration` avec `Status: Enabled`
- D) `EnableVersioning: true`

---

## Question 16 - DeletionPolicy

Que fait `DeletionPolicy: Retain` sur une ressource CloudFormation ?

- A) La ressource est supprimee avec le stack
- B) La ressource est conservee meme si le stack est supprime
- C) La ressource est sauvegardee dans un snapshot puis supprimee
- D) La ressource est deplacee dans un autre stack

---

## Question 17 - IAM

Quel est le role d'un `AWS::IAM::InstanceProfile` dans CloudFormation ?

- A) Creer un utilisateur IAM pour l'instance
- B) Permettre a une instance EC2 d'assumer un role IAM
- C) Definir les regles du Security Group
- D) Configurer l'acces SSH a l'instance

---

## Question 18 - RDS

Quelle propriete de `AWS::RDS::DBInstance` determine si la base de donnees est accessible depuis internet ?

- A) `InternetAccess`
- B) `PubliclyAccessible`
- C) `ExternalAccess`
- D) `AllowPublicIP`

---

## Question 19 - GetAtt avec RDS

Quelle syntaxe permet de recuperer l'adresse du endpoint d'une instance RDS dans un template CloudFormation ?

- A) `!Ref MaDB`
- B) `!GetAtt MaDB.Endpoint.Address`
- C) `!GetAtt MaDB.PublicIP`
- D) `!Sub ${MaDB.URL}`

---

## Question 20 - Load Balancer

Quel type de ressource CloudFormation cree un Application Load Balancer (ALB) ?

- A) `AWS::EC2::LoadBalancer`
- B) `AWS::ELB::ApplicationLoadBalancer`
- C) `AWS::ElasticLoadBalancingV2::LoadBalancer`
- D) `AWS::ALB::LoadBalancer`

---

## Question 21 - Auto Scaling

Dans un `AWS::AutoScaling::AutoScalingGroup`, quelle propriete definit le nombre minimum d'instances ?

- A) `DesiredCapacity`
- B) `MinInstances`
- C) `MinSize`
- D) `MinCapacity`

---

## Question 22 - Nested Stacks

Quel type de ressource CloudFormation permet de creer un nested stack ?

- A) `AWS::CloudFormation::NestedStack`
- B) `AWS::CloudFormation::Stack`
- C) `AWS::CloudFormation::SubStack`
- D) `AWS::CloudFormation::Module`

---

## Question 23 - Nested Stacks (suite)

Ou doit etre stocke le template d'un nested stack pour etre reference par `TemplateURL` ?

- A) Sur le disque local
- B) Dans un bucket S3
- C) Dans un repository CodeCommit
- D) Dans un parametre SSM

---

## Question 24 - Change Sets

Quel est l'avantage principal d'un Change Set dans CloudFormation ?

- A) Il deploie automatiquement les modifications
- B) Il permet de previsualiser les changements avant de les appliquer
- C) Il annule automatiquement les erreurs
- D) Il cree une copie de sauvegarde du stack

---

## Question 25 - Drift Detection

Que detecte la fonctionnalite Drift Detection de CloudFormation ?

- A) Les erreurs de syntaxe dans le template
- B) Les differences entre la configuration actuelle des ressources et celle definie dans le template
- C) Les ressources non utilisees
- D) Les failles de securite

---

## Question 26 - Bonnes pratiques

Pourquoi est-il recommande de ne **jamais** ecrire des mots de passe en clair dans un template CloudFormation ?

- A) Cela provoque une erreur de deploiement
- B) Les templates sont souvent versionnes dans Git et les valeurs seraient exposees
- C) CloudFormation ne supporte pas les chaines de caracteres
- D) Les mots de passe sont automatiquement supprimes par AWS

---

## Question 27 - Validation

Quelle commande AWS CLI permet de valider la syntaxe d'un template CloudFormation **avant** le deploiement ?

- A) `aws cloudformation check-template`
- B) `aws cloudformation validate-template`
- C) `aws cloudformation lint-template`
- D) `aws cloudformation test-template`

---

## Question 28 - CI/CD

Dans un pipeline CI/CD avec CodePipeline et CloudFormation, quelle etape vient **avant** le deploiement ?

- A) Le monitoring
- B) Le rollback
- C) La source (recuperation du code depuis un repository)
- D) La mise a l'echelle automatique

---

## Question 29 - SAM vs CloudFormation

Quel est l'avantage principal d'AWS SAM par rapport a CloudFormation natif ?

- A) SAM remplace completement CloudFormation
- B) SAM fournit une syntaxe simplifiee pour les applications serverless (Lambda, API Gateway, DynamoDB)
- C) SAM est le seul moyen de deployer des fonctions Lambda
- D) SAM ne necessite pas de template YAML

---

## Question 30 - CDK

Quelle affirmation est **vraie** concernant AWS CDK ?

- A) CDK genere des templates CloudFormation a partir de code dans un langage de programmation (Python, TypeScript, etc.)
- B) CDK remplace completement CloudFormation et ne l'utilise pas
- C) CDK ne supporte que le langage Java
- D) CDK est un service de monitoring d'infrastructure

---

> **Bonne chance !**
