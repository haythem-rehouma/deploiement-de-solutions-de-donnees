---
title: "Chapitre 5 - Demonstration du scaling sans YAML"
description: "Apprenez a scaler des applications Kubernetes directement en ligne de commande."
---

# Chapitre 5 - Demonstration Minikube : Scaling sans YAML

Ce chapitre explore le scaling d'applications sans fichiers YAML, pour une gestion plus dynamique de vos deploiements.

<a name="table-des-matieres"></a>
## Table des Matieres
1. [Partie 1 : Démo du scaling avec Docker-swarm](#partie-1)
   - [Objectif : Comprendre l'orchestration d'une manière visuelle](#objectif-1)

2. [Partie 2 : Démo du scaling avec un déploiement depuis la ligne de commande sans YAML](#partie-2)
   - [Objectif : Comprendre le scaling d'une application avec Kubernetes avec la ligne de commande](#objectif-2)
   - [Étape 1: Démarrer Minikube](#étape-1-démarrer-minikube)
   - [Étape 2: Créer un Namespace](#étape-2-créer-un-namespace)
   - [Étape 3: Créer un Déploiement](#03---étape-3-créer-un-déploiement)
   - [Étape 4: Vérifier le Déploiement](#étape-4-vérifier-le-déploiement)
   - [Étape 5: Simuler une Panne](#étape-5-simuler-la-panne-dun-pod)
   - [Étape 6: Observer la Recréation](#étape-6-observer-la-recréation-du-pod)
   - [Étape 7: Nettoyer](#étape-7-nettoyer)

3. [Partie 3 : Démo du scaling avec un déploiement depuis la ligne de commande avec YAML](#partie3)
   - [Objectif : Comprendre le scaling d'une application avec Kubernetes avec un fichier yaml](#objectif-1)
   - [Étape 1: Créer un Namespace](#01---étape-1-créer-un-namespace)
   - [Étape 2: Créer un fichier de déploiement YAML](#étape-2-créer-un-fichier-de-déploiement-yaml)
   - [Étape 3: Appliquer le Déploiement](#étape-3-appliquer-le-déploiement)
   - [Étape 4: Vérifier le Déploiement](#étape-4-vérifier-le-déploiement-1)
   - [Étape 5: Simuler une Panne](#étape-5-simuler-la-panne-dun-pod-1)
   - [Étape 6: Observer la Recréation](#étape-6-observer-la-recréation-du-pod-1)
   - [Étape 7: Nettoyer](#étape-7-nettoyer-1)

4. [Résumé des commandes utilisées](#04---résumé-des-commandes-utilisées-dans-ce-chapitre)



<a name="partie-1"></a>
---
# 01 - Partie 1 : Démo du scaling avec Docker-swarm
---
<a name="objectif-1"></a>
## Objectif : Comprendre l'orchestration d'une manière visuelle

Pour mieux comprendre le concept de scaling de manière visuelle, nous allons d'abord faire une démonstration avec Docker Swarm. Cette approche pédagogique nous permettra de :

- Visualiser concrètement comment fonctionne le scaling d'applications
- Comprendre les concepts de base de l'orchestration de conteneurs
- Avoir une première expérience pratique avant de passer à Kubernetes

Note : Cette partie avec Docker Swarm est optionnelle mais recommandée pour les débutants car elle offre une approche plus simple et visuelle de l'orchestration.

- https://github.com/hrhouma/docker-swarm-demo




[Retour a la table des matieres](#table-des-matieres)

<a name="partie-2"></a>
---
# 02 - Partie2 : Démo du scaling avec un déploiement depuis la ligne de commande sans passer par un fichier yaml
---
<a name="objectif-2"></a>
## Objectif : 

- Comprendre le scaling d'une application avec Kubernetes avec la ligne de commande
- L'objectif est de créer un déploiement avec plusieurs réplicas directement depuis la ligne de commande sans passer par un fichier YAML :

### Étape 1: Démarrer Minikube
Assurez-vous que Minikube est démarré :

```bash
minikube start
```
> Note : Si vous n'avez pas encore installé Minikube, suivez les instructions sur la page officielle de Minikube ou les pratiques précédentes 1 et 2.
Exécutez sur une autre ligne de commande
```bash
minikube dashboard
```
> Pour voir le dashboard de minikube

### Étape 2: Créer un Namespace
Créez un namespace pour isoler vos ressources :
```bash
kubectl create namespace demo-namespace
```
> un namespace est un espace de nommage pour isoler les ressources

### 03 - Étape 3: Créer un Déploiement

Utilisez `kubectl create deployment` pour lancer un déploiement avec plusieurs réplicas dans le namespace spécifié :
```bash
kubectl create deployment nginx-deployment --image=nginx --replicas=3 -n demo-namespace
```
> `--replicas` est une option pour spécifier le nombre de réplicas

Si `--replicas` n'est pas disponible directement dans `kubectl create deployment`, vous devrez d'abord créer le déploiement puis ajuster le nombre de réplicas avec `kubectl scale` :

```bash
kubectl create deployment nginx-deployment --image=nginx -n demo-namespace
kubectl scale deployment/nginx-deployment --replicas=3 -n demo-namespace
```
- Obserrvez le dashboard
  
### Étape 4: Vérifier les Pods
Pour voir les pods dans le namespace spécifique :
```bash
kubectl get pods -n demo-namespace
```
ou 
```bash
kubectl get pods -n demo-namespace -o wide -w -a -p -s -t -u -v -l app=nginx
```

> `-n demo-namespace` est une option pour spécifier le namespace

> `-o wide` est une option pour afficher plus d'informations sur les pods

> `-w` est une option pour afficher les pods en temps réel

> `-l` est une option pour filtrer les pods par label

> `-a` est une option pour afficher tous les pods, y compris les pods terminés

> `-p` est une option pour afficher les pods par phase

> `-s` est une option pour afficher les pods par statut

> `-t` est une option pour afficher les pods par type

> `-u` est une option pour afficher les pods par utilisateur

> `-v` est une option pour afficher les pods par version



### Étape 5: Simuler la panne d'un Pod
Choisissez un pod et supprimez-le pour simuler une panne :
```bash
kubectl delete pod <nom-du-pod> -n demo-namespace
```
> `<nom-du-pod>` est le nom du pod que vous souhaitez supprimer

- Observez le dashboard
  
### Étape 6: Observer la Recréation du Pod
Kubernetes va automatiquement recréer le pod pour maintenir le nombre de réplicas désirés :
```bash
kubectl get pods -n demo-namespace
```

> Cette commande permet de lister tous les pods dans le namespace spécifié :
> - `kubectl get pods` : Affiche la liste des pods
> - `-n demo-namespace` : Filtre les résultats pour ne montrer que les pods dans le namespace "demo-namespace"
>
> Le résultat affichera typiquement :
> - Le nom de chaque pod
> - L'état actuel (Running, Pending, etc.)
> - Le nombre de redémarrages
> - L'âge du pod
>
> Par exemple :
> ```
> NAME                                READY   STATUS    RESTARTS   AGE
> nginx-deployment-66b6c48dd5-7tzxs   1/1     Running   0          5m
> nginx-deployment-66b6c48dd5-8p4vx   1/1     Running   0          5m
> nginx-deployment-66b6c48dd5-9z7xw   1/1     Running   0          5m
> ```

### Étape 7: Nettoyer
Pour nettoyer vos ressources :
```bash
kubectl delete deployment nginx-deployment -n demo-namespace
kubectl delete namespace demo-namespace
```
> `kubectl delete deployment` est une commande pour supprimer un déploiement
> `kubectl delete namespace` est une commande pour supprimer un namespace
> `-n demo-namespace` est une option pour spécifier le namespace


Arrêtez Minikube si nécessaire :
```bash
minikube stop
```
> `minikube stop` est une commande pour arrêter Minikube


# Correction et Mise a Jour des Commandes

> **Important** : Changements dans les Versions Recentes

- cette commande ne fonctionne plus dans les nouvelles version :

```bash
kubectl run nginx-pod --image=nginx --replicas=3 --port=80 -n demo-namespace
```
> cette commande ne fonctionne plus dans les nouvelles version de kubernetes
> Dans les versions récentes de Kubernetes, la commande `kubectl run` a été simplifiée pour se concentrer principalement sur le lancement rapide de pods simples, sans support pour certaines options comme `--replicas`.

**Solution** : 

- Pour créer un déploiement avec plusieurs réplicas, vous devrez utiliser `kubectl create deployment` ou `kubectl apply` avec un fichier YAML.


Cette méthode utilise `kubectl create deployment` et `kubectl scale` pour gérer les déploiements avec des réplicas, conformément aux versions récentes de Kubernetes où `kubectl run` est plus limité en fonctionnalité.

[Retour a la table des matieres](#table-des-matieres)


<a name="partie-3"></a>
---
# 03 - Partie3 : Démo du scaling avec un déploiement depuis la ligne de commande avec un fichier yaml
---
<a name="objectif-3"></a>
## Objectif : 

- Comprendre le scaling d'une application avec Kubernetes avec un fichier yaml
- L'objectif est de créer un déploiement avec plusieurs réplicas directement depuis la ligne de commande avec un fichier yaml


### 01 - Étape 1: Créer un Namespace
Avant de créer des ressources, créez un namespace spécifique où toutes les opérations suivantes seront effectuées:
```bash
kubectl create namespace demo-namespace
```
> un namespace est un espace de nommage pour isoler les ressources

### Étape 2: Créer un fichier de déploiement YAML

Créez un fichier appelé `deployment.yaml` avec le contenu suivant pour définir un déploiement. Ce fichier spécifie le déploiement d'un serveur Nginx:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: demo-namespace
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
> `apiVersion: apps/v1` est la version de l'API pour les déploiements

> `kind: Deployment` est le type de ressource à créer

> `metadata: name: nginx-deployment` est le nom du déploiement

> `spec: replicas: 3` est le nombre de réplicas

> `spec: selector: matchLabels: app: nginx` est le sélecteur de pods

> `spec: template: metadata: labels: app: nginx` est le modèle de pod

> `spec: template: spec: containers: - name: nginx` est le conteneur

> `- containerPort: 80` est le port du conteneur

> ### Alternative : Créer le même déploiement via la ligne de commande
>Au lieu d'utiliser un fichier YAML, vous pouvez créer le même déploiement directement avec kubectl :
>```bash
> kubectl create deployment nginx-deployment --image=nginx --replicas=3 -n demo-namespace
>```


> Notez que `namespace: demo-namespace` est ajouté directement dans le fichier YAML, ce qui signifie que toutes les ressources créées à partir de ce fichier appartiendront automatiquement à ce namespace.

### Étape 3: Déployer l'application

Utilisez `kubectl` pour créer le déploiement à partir de votre fichier YAML:
```bash
kubectl apply -f deployment.yaml
```
> Aucun besoin d'ajouter `-n demo-namespace` car le namespace est déjà spécifié dans le fichier YAML.

### Étape 4: Vérifier le déploiement

Pour voir les pods dans le namespace spécifique:

```bash
kubectl get pods -n demo-namespace
```
> `kubectl get pods` est une commande pour lister les pods
> `-n demo-namespace` est une option pour spécifier le namespace demo-namespace
> Patre exemple, si on avait un namespace nommé `dev` et un déploiement nommé `nginx-deployment`, on aurait pu utiliser la commande suivante pour lister les pods :
> ```bash
> kubectl get pods -n dev
> ```

### Étape 5: Simuler la panne d'un pod
Choisissez un pod et simulez une panne en le supprimant:
```bash
kubectl delete pod <nom-du-pod> -n demo-namespace
```
> `<nom-du-pod>` est le nom du pod que vous souhaitez supprimer


### Étape 6: Observer la recréation du pod
Observez comment Kubernetes recrée automatiquement le pod:
```bash
kubectl get pods -n demo-namespace
```
> `kubectl get pods` est une commande pour lister les pods
> `-n demo-namespace` est une option pour spécifier le namespace demo-namespace

### Étape 7: Nettoyer
Supprimez le déploiement et, si désiré, le namespace:
```bash
kubectl delete deployment nginx-deployment -n demo-namespace
kubectl delete namespace demo-namespace
```
> `kubectl delete deployment` est une commande pour supprimer un déploiement
> `kubectl delete namespace` est une commande pour supprimer un namespace
> `-n demo-namespace` est une option pour spécifier le namespace

Minikube peut être arrêté avec:
```bash
minikube stop
```
> `minikube stop` est une commande pour arrêter Minikube

---
# 04 - Résumé des commandes utilisées dans ce chapitre
---

```bash
kubectl create deployment nginx-deployment --image=nginx --replicas=3 -n demo-namespace
kubectl apply -f deployment.yaml
kubectl get pods -n demo-namespace
kubectl delete deployment nginx-deployment -n demo-namespace
kubectl delete namespace demo-namespace
minikube stop
kubectl describe pod nginx-deployment-647677fc66-2wr6r -n demo-namespace
kubectl get pod nginx-deployment-647677fc66-2wr6r -o yaml -n demo-namespace
```

[Retour a la table des matieres](#table-des-matieres)


