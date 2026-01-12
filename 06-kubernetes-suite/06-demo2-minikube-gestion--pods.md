---
title: "Chapitre 6 - Gestion des Pods avec Minikube"
description: "Apprenez a creer, gerer et supprimer des Pods dans Kubernetes."
---

# Chapitre 6 - démonstration-minikube du gestion des pods (pratique4)

<a name="table-des-matieres"></a>
# Table des matières

- [Introduction](#introduction)
- [Prérequis](#prerequis)
- [Étape 1: Création du fichier YAML](#etape-1-creation-du-fichier-yaml)
- [Étape 2: Déploiement du Pod](#etape-2-deploiement-du-pod)
- [Étape 3: Vérification du Pod](#etape-3-verification-du-pod)
- [Étape 4: Suppression du Pod](#etape-4-suppression-du-pod)
- [Conclusion](#conclusion)
- [Résumé](#resume)
- [Autres commandes](#autres-commandes)
- [Annexe 1 - Troubleshooting](#annexe-1-troubleshooting)
- [Références](#references)


Dans ce chapitre pratique, nous allons explorer la gestion des pods dans Kubernetes en utilisant Minikube. Les pods sont les plus petites unités déployables dans Kubernetes et comprendre leur gestion est essentiel pour tout administrateur Kubernetes. Cette pratique vous permettra de :

- **Créer et déployer des pods** en utilisant des fichiers YAML
- **Gérer le cycle de vie des pods** (création, modification, suppression)
- **Observer le comportement des pods** dans différentes situations
- **Comprendre les concepts fondamentaux** de la gestion des conteneurs dans Kubernetes

Cette démonstration pratique vous donnera une base solide pour comprendre comment Kubernetes orchestre et gère les conteneurs au niveau le plus élémentaire.


---
<a name="introduction"></a>
# Introduction
---

Ce tutoriel vous guide  à travers les étapes de base pour la création, la gestion et la suppression de Pods dans un environnement Kubernetes. En utilisant l'outil en ligne de commande `kubectl` et un fichier de configuration YAML, nous apprendrons comment déployer un serveur web Nginx simple.

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="prerequis"></a>
# Prérequis
---

- Avoir installé `kubectl`, l'interface en ligne de commande pour Kubernetes.
- Avoir accès à un cluster Kubernetes, comme Minikube ou un cluster configuré via un fournisseur de cloud.

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="etape-1-creation-du-fichier-yaml"></a>
# 1 - Étape 1: Création du fichier YAML
---

Un Pod est l'unité la plus petite et la plus simple dans Kubernetes. Il représente un ou plusieurs conteneurs qui doivent être gérés ensemble. Commencez par créer un fichier YAML qui définit un Pod contenant un serveur web Nginx.

1. **Ouvrez votre éditeur de texte ou IDE** (ici, Visual Studio Code).
2. **Créez un fichier nommé** `mypod.yaml`.
3. **Ajoutez le contenu suivant au fichier** :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    ports:
    - containerPort: 80
```
> **Note**
> 
> Ce fichier définit un Pod nommé `nginx` utilisant l'image Docker `nginx:alpine`, qui est une version légère de Nginx.

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="etape-2-deploiement-du-pod"></a>
# 2 - Étape 2: Déploiement du Pod
---

Avec le fichier YAML prêt, vous pouvez maintenant créer le Pod dans votre cluster Kubernetes.

- **Ouvrez un terminal**.
- **Naviguez au répertoire contenant votre fichier** `mypod.yaml`.
- **Exécutez la commande suivante** pour créer le Pod :

```bash
kubectl apply -f mypod.yaml
```
> **Note**
> 
> Cette commande crée le Pod dans le cluster Kubernetes.

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="etape-3-verification-du-pod"></a>
# 3 - Étape 3: Vérification du Pod
---

Après la création du Pod, il est important de vérifier son état et de s'assurer qu'il fonctionne correctement.

- **Pour lister tous les Pods, exécutez** :

```bash
kubectl get pods
```
> **Note**
> 
> Cette commande récupère la liste des Pods dans le cluster.

- **Pour obtenir des détails plus spécifiques sur le Pod 'nginx', utilisez** :

```bash
kubectl describe pod nginx
```
> **Note**
> 
> Cette commande fournit des informations sur l'état du Pod, les conteneurs qu'il contient, et d'autres détails utiles comme l'adresse IP du Pod et les ports ouverts.

```bash
kubectl describe pod nginx | grep -i nginx
```
> **Note**
> 
> Cette commande fournit des informations sur l'état du Pod, les conteneurs qu'il contient, et d'autres détails utiles comme l'adresse IP du Pod et les ports ouverts.

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="etape-4-suppression-du-pod"></a>
# 4 - Étape 4: Suppression du Pod
---

Une fois que vous avez fini avec le Pod, ou si vous voulez recommencer le processus, vous pouvez supprimer le Pod.

- **Pour supprimer le Pod 'nginx', exécutez** :

```bash
kubectl delete pod nginx
```
> **Note**
> 
> Cette commande supprime le Pod 'nginx' du cluster Kubernetes.

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="conclusion"></a>
# 5 - Conclusion
---

Ce tutoriel vous a guidé à travers les étapes de base de la gestion des Pods dans Kubernetes, en utilisant `kubectl`. Vous avez appris comment définir un Pod avec un fichier YAML, comment le déployer, vérifier son état, et enfin, comment le supprimer proprement. Utilisez ces compétences pour explorer plus avant Kubernetes et gérer des applications plus complexes.

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="resume"></a>
# 8 - Résumé
---


```bash
# Créer le fichier r YAML pour le Pod
echo "
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    ports:
    - containerPort: 80
" > mypod.yaml

# Déployer le Pod
kubectl apply -f mypod.yaml

# Lister tous les Pods pour vérifier l'état
kubectl get pods

# Obtenir des détails sur le Pod 'nginx'
kubectl describe pod nginx

# Supprimer le Pod
kubectl delete pod nginx
```

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="autres-commandes"></a>
# 9 - Autres commandes
---

#### 9.1. Commandes pour entrer dans le POD
```bash
minikube ssh
docker ps
docker ps | grep -i nginx  (récupérer le ID)
docker exec -it 53209 sh
ls
cat /etc/os-release
exit
exit
```


<a name="autres-commandes"></a>
#### 9.2. Ouvrir un autre terminal pour vérifier le dashboard 


```bash
minikube dashboard
```
> [!NOTE]
> 
> Cette commande ouvre le dashboard Kubernetes dans votre navigateur.
> #### ⚠️ IL faut le laisser OUVERT !!!

#### [Retour a la table des matieres](#table-des-matieres)

<a name="annexe-1-troubleshooting"></a>
# 10 - Annexe 1 - TROUBLESHOOTING

```bash
minikube start 
```
> **Note**
> 
> Cette commande démarre le cluster Kubernetes.

```bash
minikube profile list
```
> **Note**
> 
> Cette commande liste les profils de cluster disponibles.

```bash
minikube delete --profile minikube
```
> **Note**
> 
> Cette commande supprime le cluster Kubernetes.

```bash
minikube start --vm-driver=virtualbox --cpus 8 --memory 16g
```
> **Note**
> 
> Cette commande démarre le cluster Kubernetes avec des paramètres spécifiques.

```bash
minikube stop
```
> **Note**
> 
> Cette commande arrête le cluster Kubernetes.


<a name="references"></a>
# Références : 
- https://stackoverflow.com/questions/72147700/deleting-minikube-cluster-so-i-can-create-a-larger-cluster-with-more-cpus
