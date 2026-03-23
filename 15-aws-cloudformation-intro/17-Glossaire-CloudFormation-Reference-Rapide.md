<a id="top"></a>

# Glossaire & Référence rapide — AWS CloudFormation

> Ce document regroupe **tous les termes techniques, types de ressources, fonctions, mots-clés et commandes** utilisés dans les chapitres 01 à 16 de ce cours. Utilisez-le comme aide-mémoire.

---

## Table des matières

| # | Section |
|---|---------|
| 1 | [Concepts fondamentaux](#section-1) |
| 2 | [Sections d'un template CloudFormation](#section-2) |
| 3 | [Types de ressources AWS](#section-3) |
| 4 | [Fonctions intrinsèques](#section-4) |
| 5 | [Pseudo-paramètres AWS](#section-5) |
| 6 | [Propriétés de paramètres](#section-6) |
| 7 | [Attributs de ressource](#section-7) |
| 8 | [Propriétés importantes par service](#section-8) |
| 9 | [Templates minimaux de référence](#section-9) |
| 10 | [Commandes AWS CLI essentielles](#section-10) |
| 11 | [Fonctions conditionnelles](#section-11) |
| 12 | [Statuts de stack](#section-12) |
| 13 | [Erreurs et pièges courants](#section-13) |

---

<a id="section-1"></a>

<details>
<summary>1 — Concepts fondamentaux</summary>

<br/>

| Terme | Définition | Analogie simple | Chapitres |
|-------|-----------|-----------------|-----------|
| **Template** | Fichier YAML ou JSON qui décrit l'infrastructure souhaitée | La recette de cuisine | 01 |
| **Stack** | Le déploiement concret d'un template (les ressources réellement créées) | Le plat cuisiné à partir de la recette | 01 |
| **Change Set** | Aperçu des modifications avant de les appliquer à une stack | Le devis avant les travaux | 01, 11 |
| **Drift** | Écart entre le template et la configuration réelle (modifiée manuellement) | Quelqu'un a déplacé les meubles sans prévenir | 01, 11 |
| **StackSet** | Déploiement d'un template sur plusieurs comptes AWS / régions | Ouvrir des franchises dans plusieurs villes avec le même plan | 01 |
| **Nested Stack** | Stack enfant appelée depuis une stack parent via `AWS::CloudFormation::Stack` | Un chapitre dans un livre | 10 |
| **Cross-stack reference** | Partage de valeurs entre stacks via Export / ImportValue | Donner son numéro de téléphone pour que les autres stacks puissent appeler | 04, 10 |
| **Infrastructure as Code (IaC)** | Décrire l'infrastructure dans du code versionnable | Plan d'architecte au lieu de construire à vue d'oeil | 01, 13 |
| **Logical ID** | Nom donné à une ressource dans le template (ex: `MonVPC`) | L'étiquette sur le bocal | 01 |
| **Physical ID** | Identifiant réel de la ressource créée par AWS | Le code-barres du produit fini | 01 |
| **Rollback** | Annulation automatique en cas d'échec de création/mise à jour | Ctrl+Z automatique | 12 |
| **Stack Policy** | Règle qui protège certaines ressources contre les mises à jour | Panneau "Ne pas toucher" | 11 |
| **CI/CD** | Intégration Continue / Livraison Continue — automatisation du déploiement | Chaîne de montage automatisée | 13, 16 |

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-2"></a>

<details>
<summary>2 — Sections d'un template CloudFormation</summary>

<br/>

```yaml
AWSTemplateFormatVersion: '2010-09-09'   # Version du format (toujours cette valeur)
Description: Mon template                 # Description lisible par un humain
Parameters:                               # Valeurs d'entrée (formulaire)
Mappings:                                 # Tables de correspondance clé-valeur
Conditions:                               # Logique conditionnelle
Resources:                                # ⭐ SEULE SECTION OBLIGATOIRE — les ressources à créer
Outputs:                                  # Valeurs de sortie après déploiement
```

| Section | Obligatoire ? | Rôle | Chapitres |
|---------|:---:|------|-----------|
| `AWSTemplateFormatVersion` | Non | Déclare la version du format | 01 |
| `Description` | Non | Texte descriptif du template | 01 |
| `Parameters` | Non | Valeurs fournies au déploiement | 01, 04 |
| `Mappings` | Non | Tables de lookup (région → AMI, env → taille…) | 05 |
| `Conditions` | Non | Créer des ressources conditionnellement | 05 |
| **`Resources`** | **Oui** | Déclare les ressources AWS à créer | 01 |
| `Outputs` | Non | Expose des valeurs après déploiement | 01, 04 |

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-3"></a>

<details>
<summary>3 — Types de ressources AWS</summary>

<br/>

### Réseau (VPC)

| Type | Rôle | Chapitre |
|------|------|----------|
| `AWS::EC2::VPC` | Réseau virtuel isolé | 02 |
| `AWS::EC2::Subnet` | Sous-réseau dans un VPC | 02 |
| `AWS::EC2::InternetGateway` | Porte vers Internet | 02 |
| `AWS::EC2::VPCGatewayAttachment` | Attache l'IGW au VPC | 02 |
| `AWS::EC2::RouteTable` | Table de routage | 02 |
| `AWS::EC2::Route` | Règle de routage (ex: 0.0.0.0/0 → IGW) | 02 |
| `AWS::EC2::SubnetRouteTableAssociation` | Associe un subnet à une route table | 02 |
| `AWS::EC2::SecurityGroup` | Pare-feu virtuel (règles entrantes/sortantes) | 02 |

### Compute (EC2)

| Type | Rôle | Chapitre |
|------|------|----------|
| `AWS::EC2::Instance` | Machine virtuelle | 03 |
| `AWS::EC2::LaunchTemplate` | Modèle réutilisable pour lancer des instances | 09 |

### Auto Scaling & Load Balancing

| Type | Rôle | Chapitre |
|------|------|----------|
| `AWS::ElasticLoadBalancingV2::LoadBalancer` | Application Load Balancer (ALB) | 09 |
| `AWS::ElasticLoadBalancingV2::Listener` | Écoute le trafic sur un port (80, 443…) | 09 |
| `AWS::ElasticLoadBalancingV2::TargetGroup` | Groupe de cibles pour le load balancer | 09 |
| `AWS::AutoScaling::AutoScalingGroup` | Groupe qui ajuste le nombre d'instances | 09 |
| `AWS::AutoScaling::ScalingPolicy` | Règle de mise à l'échelle (CPU > 70% → +1) | 09 |

### Stockage (S3)

| Type | Rôle | Chapitre |
|------|------|----------|
| `AWS::S3::Bucket` | Bucket de stockage objet | 01, 06 |
| `AWS::S3::BucketPolicy` | Permissions d'accès au bucket | 06 |

### IAM (Permissions)

| Type | Rôle | Chapitre |
|------|------|----------|
| `AWS::IAM::Role` | Rôle avec permissions temporaires | 07 |
| `AWS::IAM::InstanceProfile` | Enveloppe qui attache un rôle IAM à EC2 | 07 |

### Base de données (RDS)

| Type | Rôle | Chapitre |
|------|------|----------|
| `AWS::RDS::DBInstance` | Instance de base de données managée | 08 |
| `AWS::RDS::DBSubnetGroup` | Groupe de subnets pour RDS (min 2 AZ) | 08 |

### CloudFormation

| Type | Rôle | Chapitre |
|------|------|----------|
| `AWS::CloudFormation::Stack` | Nested stack (stack enfant) | 10 |

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-4"></a>

<details>
<summary>4 — Fonctions intrinsèques</summary>

<br/>

### Les essentielles

| Fonction | Syntaxe courte | Rôle | Analogie | Chapitre |
|----------|---------------|------|----------|----------|
| **Ref** | `!Ref` | Retourne la valeur d'un paramètre ou la référence d'une ressource | Raccourci clavier | 01, 04 |
| **Fn::GetAtt** | `!GetAtt` | Récupère un attribut précis d'une ressource | Clic droit → Propriétés | 01, 04 |
| **Fn::Sub** | `!Sub` | Construit une chaîne dynamique avec des variables `${...}` | Lettre à trous | 01, 04 |
| **Fn::Join** | `!Join` | Concatène une liste de valeurs avec un séparateur | Coller des morceaux de phrase | 04 |
| **Fn::Base64** | `!Base64` | Encode en Base64 (obligatoire pour UserData) | Emballage pour le transport | 03, 04 |
| **Fn::FindInMap** | `!FindInMap` | Lit une valeur dans un Mapping | Chercher dans un dictionnaire | 05 |
| **Fn::ImportValue** | `!ImportValue` | Importe une valeur exportée par une autre stack | Appeler le numéro de téléphone partagé | 10 |

### Exemples rapides

```yaml
# !Ref — lire un paramètre
BucketName: !Ref NomDuBucket

# !Ref — référencer une ressource
VpcId: !Ref MonVPC

# !GetAtt — attribut précis
Value: !GetAtt MonServeurEC2.PublicIp

# !Sub — chaîne dynamique
Value: !Sub "Stack ${AWS::StackName} dans ${AWS::Region}"

# !Join — concaténation
Value: !Join [ "", [ "s3://", !Ref MonBucket, "/backup" ] ]

# !Base64 + !Sub — UserData
UserData:
  Fn::Base64: !Sub |
    #!/bin/bash
    echo "Stack = ${AWS::StackName}" > /tmp/info.txt

# !FindInMap — lookup dans un mapping
InstanceType: !FindInMap [ EnvToSize, !Ref Env, InstanceType ]
```

### Différence Ref vs GetAtt

| | `!Ref` | `!GetAtt` |
|---|--------|-----------|
| Retourne | La valeur par défaut (souvent l'ID) | Un attribut spécifique |
| Exemple EC2 | `!Ref MonEC2` → `i-0abc123` | `!GetAtt MonEC2.PublicIp` → `54.x.x.x` |
| Exemple S3 | `!Ref MonBucket` → `mon-bucket-123` | `!GetAtt MonBucket.Arn` → `arn:aws:s3:::...` |

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-5"></a>

<details>
<summary>5 — Pseudo-paramètres AWS</summary>

<br/>

Variables intégrées fournies automatiquement par CloudFormation. Pas besoin de les déclarer.

| Pseudo-paramètre | Retourne | Exemple | Chapitre |
|-------------------|----------|---------|----------|
| `AWS::Region` | La région de la stack | `us-east-1`, `ca-central-1` | 05 |
| `AWS::AccountId` | L'ID du compte AWS | `123456789012` | 05 |
| `AWS::StackName` | Le nom de la stack | `ma-stack-demo` | 05 |
| `AWS::StackId` | L'ARN de la stack | `arn:aws:cloudformation:...` | 05 |
| `AWS::Partition` | La partition AWS | `aws`, `aws-cn`, `aws-us-gov` | 05 |
| `AWS::URLSuffix` | Le suffixe DNS | `amazonaws.com` | 05 |
| `AWS::NoValue` | Supprime une propriété si utilisé avec `!If` | *(pas de valeur)* | 05 |

### Exemple d'utilisation

```yaml
# Construire un ARN portable
Value: !Sub "arn:${AWS::Partition}:s3:::${MonBucket}"

# Nom dynamique
BucketName: !Sub "logs-${AWS::StackName}-${AWS::AccountId}"

# Supprimer une propriété conditionnellement
KeyName: !If [ UseKeyPair, !Ref KeyPairName, !Ref "AWS::NoValue" ]
```

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-6"></a>

<details>
<summary>6 — Propriétés de paramètres</summary>

<br/>

| Propriété | Rôle | Exemple | Chapitre |
|-----------|------|---------|----------|
| `Type` | Type du paramètre | `String`, `Number`, `AWS::EC2::KeyPair::KeyName` | 04 |
| `Default` | Valeur par défaut si non fournie | `t2.micro` | 04 |
| `Description` | Texte d'aide affiché dans la console | `Nom du bucket S3` | 04 |
| `AllowedValues` | Liste de valeurs autorisées | `[dev, test, prod]` | 04 |
| `NoEcho` | Masque la valeur (mots de passe) | `true` | 04, 08 |

### Types de paramètres courants

| Type | Utilisé pour |
|------|-------------|
| `String` | Texte libre |
| `Number` | Valeur numérique |
| `CommaDelimitedList` | Liste séparée par des virgules |
| `AWS::EC2::KeyPair::KeyName` | Nom de paire de clés SSH existante |
| `AWS::EC2::Subnet::Id` | ID d'un subnet existant |
| `AWS::EC2::SecurityGroup::Id` | ID d'un security group existant |
| `AWS::EC2::VPC::Id` | ID d'un VPC existant |

### Exemple complet

```yaml
Parameters:
  EnvironmentName:
    Type: String
    Default: dev
    AllowedValues:
      - dev
      - test
      - prod
    Description: Environnement de déploiement

  DbPassword:
    Type: String
    NoEcho: true
    Description: Mot de passe administrateur

  MaCleSSH:
    Type: AWS::EC2::KeyPair::KeyName
    Description: Paire de clés SSH
```

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-7"></a>

<details>
<summary>7 — Attributs de ressource</summary>

<br/>

Attributs spéciaux qu'on place au même niveau que `Type` et `Properties`.

| Attribut | Rôle | Valeurs possibles | Chapitre |
|----------|------|-------------------|----------|
| `DeletionPolicy` | Que faire quand la stack est supprimée | `Delete` (défaut), `Retain`, `Snapshot` | 01, 06, 08 |
| `UpdateReplacePolicy` | Que faire quand une ressource est remplacée lors d'un update | `Delete`, `Retain`, `Snapshot` | 06, 11 |
| `DependsOn` | Forcer l'ordre de création | Nom d'une autre ressource | 02, 03 |
| `Condition` | Créer la ressource uniquement si la condition est vraie | Nom d'une condition | 05 |

### Quand utiliser quoi

```
Bucket S3 important   → DeletionPolicy: Retain
Base de données RDS   → DeletionPolicy: Snapshot
Route Internet        → DependsOn: MonAttachementIGW
Bucket logs prod      → Condition: EstProduction
```

### Exemple

```yaml
MaBaseRDS:
  Type: AWS::RDS::DBInstance
  DeletionPolicy: Snapshot
  DependsOn: MonDbSubnetGroup
  Condition: EstProduction
  Properties:
    Engine: mysql
    DBInstanceClass: db.t3.micro
```

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-8"></a>

<details>
<summary>8 — Propriétés importantes par service</summary>

<br/>

### EC2 (`AWS::EC2::Instance`)

| Propriété | Rôle |
|-----------|------|
| `ImageId` | ID de l'AMI (système d'exploitation) |
| `InstanceType` | Taille de la machine (`t2.micro`, `t3.small`…) |
| `KeyName` | Paire de clés pour SSH |
| `SubnetId` | Subnet où placer l'instance |
| `SecurityGroupIds` | Liste de security groups |
| `UserData` | Script exécuté au démarrage (doit être en Base64) |
| `IamInstanceProfile` | Instance profile pour attacher un rôle IAM |

**Attributs GetAtt :** `PublicIp`, `PublicDnsName`, `PrivateIp`

---

### VPC (`AWS::EC2::VPC`)

| Propriété | Rôle |
|-----------|------|
| `CidrBlock` | Plage d'adresses IP (ex: `10.0.0.0/16`) |
| `EnableDnsSupport` | Activer le DNS dans le VPC |
| `EnableDnsHostnames` | Activer les noms DNS |

---

### Subnet (`AWS::EC2::Subnet`)

| Propriété | Rôle |
|-----------|------|
| `VpcId` | VPC parent |
| `CidrBlock` | Plage d'adresses du subnet (ex: `10.0.1.0/24`) |
| `MapPublicIpOnLaunch` | Assigner automatiquement une IP publique (`true`/`false`) |
| `AvailabilityZone` | Zone de disponibilité |

---

### Security Group (`AWS::EC2::SecurityGroup`)

| Propriété | Rôle |
|-----------|------|
| `GroupDescription` | Description (obligatoire) |
| `VpcId` | VPC parent |
| `SecurityGroupIngress` | Règles entrantes |
| `SecurityGroupEgress` | Règles sortantes |

**Sous-propriétés d'une règle ingress :**

```yaml
SecurityGroupIngress:
  - IpProtocol: tcp
    FromPort: 80
    ToPort: 80
    CidrIp: 0.0.0.0/0          # Depuis une IP
  - IpProtocol: tcp
    FromPort: 3306
    ToPort: 3306
    SourceSecurityGroupId: !Ref SgApp   # Depuis un autre SG
```

---

### S3 (`AWS::S3::Bucket`)

| Propriété | Rôle |
|-----------|------|
| `BucketName` | Nom du bucket (optionnel, doit être unique) |
| `VersioningConfiguration` | Activer le versioning (`Status: Enabled`) |
| `Tags` | Tags pour organiser les ressources |

**Attributs GetAtt :** `Arn`, `DomainName`, `WebsiteURL`

---

### IAM Role (`AWS::IAM::Role`)

| Propriété | Rôle |
|-----------|------|
| `AssumeRolePolicyDocument` | Trust policy — qui peut assumer le rôle (obligatoire) |
| `ManagedPolicyArns` | Managed policies à attacher |
| `Policies` | Inline policies intégrées au rôle |
| `RoleName` | Nom du rôle (optionnel, requiert `CAPABILITY_NAMED_IAM`) |

**Attributs GetAtt :** `Arn`

---

### RDS (`AWS::RDS::DBInstance`)

| Propriété | Rôle |
|-----------|------|
| `Engine` | Moteur (`mysql`, `postgres`, `mariadb`…) |
| `DBInstanceClass` | Taille (`db.t3.micro`…) |
| `AllocatedStorage` | Stockage en Go |
| `MasterUsername` | Nom d'utilisateur admin |
| `MasterUserPassword` | Mot de passe admin |
| `DBSubnetGroupName` | Groupe de subnets (min 2 AZ) |
| `VPCSecurityGroups` | Security groups |
| `PubliclyAccessible` | `false` en général |

**Attributs GetAtt :** `Endpoint.Address`, `Endpoint.Port`

---

### ALB (`AWS::ElasticLoadBalancingV2::LoadBalancer`)

| Propriété | Rôle |
|-----------|------|
| `Subnets` | Subnets publics (min 2 AZ) |
| `SecurityGroups` | Security groups du ALB |
| `Type` | `application` (ALB) ou `network` (NLB) |

**Attributs GetAtt :** `DNSName`, `LoadBalancerArn`

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-9"></a>

<details>
<summary>9 — Templates minimaux de référence</summary>

<br/>

### Bucket S3 minimal

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Resources:
  MonBucket:
    Type: AWS::S3::Bucket
```

### Bucket S3 protégé avec output

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Parameters:
  NomDuBucket:
    Type: String
Resources:
  MonBucket:
    Type: AWS::S3::Bucket
    DeletionPolicy: Retain
    Properties:
      BucketName: !Ref NomDuBucket
Outputs:
  BucketArn:
    Value: !GetAtt MonBucket.Arn
```

### Instance EC2 minimale

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Parameters:
  AmiId:
    Type: String
Resources:
  MonEC2:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !Ref AmiId
      InstanceType: t2.micro
```

### VPC + Subnet public minimal

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Resources:
  MonVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
  MonSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MonVPC
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
  MonIGW:
    Type: AWS::EC2::InternetGateway
  AttachIGW:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MonVPC
      InternetGatewayId: !Ref MonIGW
  RouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MonVPC
  RouteInternet:
    Type: AWS::EC2::Route
    DependsOn: AttachIGW
    Properties:
      RouteTableId: !Ref RouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref MonIGW
  AssocSubnet:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MonSubnet
      RouteTableId: !Ref RouteTable
```

### Rôle IAM + Instance Profile minimal

```yaml
Resources:
  MonRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: ec2.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
  MonInstanceProfile:
    Type: AWS::IAM::InstanceProfile
    Properties:
      Roles:
        - !Ref MonRole
```

### RDS MySQL minimal

```yaml
Resources:
  MaBase:
    Type: AWS::RDS::DBInstance
    DeletionPolicy: Snapshot
    Properties:
      Engine: mysql
      DBInstanceClass: db.t3.micro
      AllocatedStorage: 20
      MasterUsername: admin
      MasterUserPassword: !Ref DbPassword
```

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-10"></a>

<details>
<summary>10 — Commandes AWS CLI essentielles</summary>

<br/>

### Cycle de vie d'une stack

```bash
# Créer une stack
aws cloudformation create-stack \
  --stack-name ma-stack \
  --template-body file://template.yaml \
  --parameters ParameterKey=Cle,ParameterValue=Valeur

# Créer une stack avec des ressources IAM
aws cloudformation create-stack \
  --stack-name ma-stack \
  --template-body file://template.yaml \
  --capabilities CAPABILITY_IAM

# Mettre à jour une stack
aws cloudformation update-stack \
  --stack-name ma-stack \
  --template-body file://template.yaml

# Supprimer une stack
aws cloudformation delete-stack \
  --stack-name ma-stack

# Décrire une stack (statut, outputs)
aws cloudformation describe-stacks \
  --stack-name ma-stack

# Lister les ressources d'une stack
aws cloudformation describe-stack-resources \
  --stack-name ma-stack
```

### Validation

```bash
# Valider la syntaxe d'un template
aws cloudformation validate-template \
  --template-body file://template.yaml
```

### Change Sets

```bash
# Créer un change set
aws cloudformation create-change-set \
  --stack-name ma-stack \
  --change-set-name mon-changeset \
  --template-body file://template.yaml

# Voir le détail du change set
aws cloudformation describe-change-set \
  --change-set-name mon-changeset \
  --stack-name ma-stack

# Exécuter le change set
aws cloudformation execute-change-set \
  --change-set-name mon-changeset \
  --stack-name ma-stack

# Supprimer le change set (sans appliquer)
aws cloudformation delete-change-set \
  --change-set-name mon-changeset \
  --stack-name ma-stack
```

### Drift Detection

```bash
# Lancer la détection de drift
aws cloudformation detect-stack-drift \
  --stack-name ma-stack

# Vérifier le statut de la détection
aws cloudformation describe-stack-drift-detection-status \
  --stack-drift-detection-id <ID>

# Voir les ressources en drift
aws cloudformation describe-stack-resource-drifts \
  --stack-name ma-stack
```

### Exports (cross-stack)

```bash
# Lister tous les exports
aws cloudformation list-exports
```

### Autres commandes utiles

```bash
# Trouver son Account ID
aws sts get-caller-identity

# Lister les buckets S3
aws s3 ls

# Créer un pipeline CodePipeline
aws codepipeline create-pipeline \
  --cli-input-json file://pipeline.json

# Trouver le domaine CloudFront
aws cloudfront list-distributions \
  --query DistributionList.Items[0].DomainName --output text
```

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-11"></a>

<details>
<summary>11 — Fonctions conditionnelles</summary>

<br/>

| Fonction | Rôle | Exemple |
|----------|------|---------|
| `!Equals` | Compare deux valeurs | `!Equals [!Ref Env, prod]` |
| `!If` | Choisit une valeur selon une condition | `!If [EstProd, t3.small, t2.micro]` |
| `!Not` | Inverse une condition | `!Not [!Equals [!Ref Env, prod]]` |
| `!And` | ET logique (toutes vraies) | `!And [Cond1, Cond2]` |
| `!Or` | OU logique (au moins une vraie) | `!Or [Cond1, Cond2]` |

### Exemple complet

```yaml
Parameters:
  Env:
    Type: String
    AllowedValues: [dev, test, prod]

Conditions:
  EstProd: !Equals [!Ref Env, prod]
  PasProd: !Not [!Equals [!Ref Env, prod]]

Resources:
  BucketLogs:
    Type: AWS::S3::Bucket
    Condition: EstProd

  MonEC2:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !If [EstProd, t3.small, t2.micro]

Outputs:
  BucketLogName:
    Condition: EstProd
    Value: !Ref BucketLogs
```

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-12"></a>

<details>
<summary>12 — Statuts de stack</summary>

<br/>

### Statuts de création

| Statut | Signification |
|--------|--------------|
| `CREATE_IN_PROGRESS` | Création en cours |
| `CREATE_COMPLETE` | Création terminée avec succès |
| `CREATE_FAILED` | Création échouée |
| `ROLLBACK_IN_PROGRESS` | Annulation en cours (suite à un échec) |
| `ROLLBACK_COMPLETE` | Annulation terminée |

### Statuts de mise à jour

| Statut | Signification |
|--------|--------------|
| `UPDATE_IN_PROGRESS` | Mise à jour en cours |
| `UPDATE_COMPLETE` | Mise à jour terminée |
| `UPDATE_ROLLBACK_IN_PROGRESS` | Annulation de la mise à jour en cours |
| `UPDATE_ROLLBACK_COMPLETE` | Annulation terminée |

### Statuts de suppression

| Statut | Signification |
|--------|--------------|
| `DELETE_IN_PROGRESS` | Suppression en cours |
| `DELETE_COMPLETE` | Suppression terminée |
| `DELETE_FAILED` | Suppression échouée (souvent : bucket S3 non vide) |

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>

---

<a id="section-13"></a>

<details>
<summary>13 — Erreurs et pièges courants</summary>

<br/>

| Piège | Explication | Solution |
|-------|-------------|----------|
| Bucket S3 non vide | CloudFormation ne peut pas supprimer un bucket qui contient des objets | Vider le bucket avant de supprimer la stack, ou utiliser `DeletionPolicy: Retain` |
| Oublier `DependsOn` sur la route | La route vers l'IGW peut échouer si l'IGW n'est pas encore attaché | Ajouter `DependsOn: AttachementIGW` |
| `UserData` sans `Base64` | CloudFormation attend du Base64 pour UserData | Toujours encapsuler avec `Fn::Base64` |
| Confondre `!Ref` et `!GetAtt` | `!Ref` retourne l'ID, pas l'IP ni l'ARN | Utiliser `!GetAtt` pour des attributs spécifiques |
| AMI invalide dans la région | Les AMI changent selon la région | Utiliser un paramètre ou un Mapping par région |
| Oublier `CAPABILITY_IAM` | CloudFormation refuse de créer des ressources IAM sans accusé de réception | Ajouter `--capabilities CAPABILITY_IAM` à la commande |
| `NoEcho` ne protège pas tout | Les valeurs masquées restent visibles dans Outputs et Metadata | Ne jamais exposer de secrets dans les Outputs |
| Nom de bucket / rôle IAM trop rigide | Le déploiement multi-région échoue (nom déjà pris) | Ajouter `${AWS::Region}` ou `${AWS::StackName}` au nom |
| DB subnet group avec un seul subnet | AWS exige au moins 2 subnets dans 2 AZ | Toujours créer 2 subnets dans 2 AZ différentes |
| Ouvrir SSH à `0.0.0.0/0` en prod | Risque de sécurité majeur | Restreindre à votre IP ou utiliser SSM Session Manager |
| Security Group ouvert mais pas d'IP publique | L'instance reste injoignable malgré les ports ouverts | Vérifier `MapPublicIpOnLaunch: true` sur le subnet |
| Modifier une ressource manuellement | Crée du drift entre le template et la réalité | Toujours passer par CloudFormation, utiliser `detect-stack-drift` pour vérifier |

</details>

<p align="right"><a href="#top">↑ Retour en haut</a></p>
