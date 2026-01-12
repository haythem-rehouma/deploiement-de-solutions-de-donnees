---
title: "Chapitre 14 - Quiz Final : 30 Questions"
description: "Quiz de revision couvrant tous les concepts du module Kubernetes Suite."
---

# Quiz Final - 30 Questions sur Kubernetes

Ce quiz couvre l'ensemble des concepts abordes dans ce module : Pods, Deployments, Services, Minikube, Kind, et plus encore.

---

## Question 1

Quel est le role principal de Kubernetes ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Kubernetes est une plateforme d'orchestration de conteneurs qui automatise le deploiement, la mise a l'echelle et la gestion d'applications conteneurisees.

</details>

---

## Question 2

Quelle est la plus petite unite deployable dans Kubernetes ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Le **Pod**. C'est l'unite de base qui peut contenir un ou plusieurs conteneurs partageant le meme reseau et stockage.

</details>

---

## Question 3

Quelle est la difference entre un Pod et un Deployment ?

<details>
<summary>Voir la reponse</summary>

**Reponse** :
- **Pod** : Unite d'execution de base, ephemere, pas de gestion automatique
- **Deployment** : Gere plusieurs Pods via des ReplicaSets, permet le scaling, les mises a jour et les rollbacks

</details>

---

## Question 4

Quel composant maintient un nombre specifie de replicas de Pods en cours d'execution ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Le **ReplicaSet**. Il s'assure qu'un nombre defini de Pods identiques sont toujours actifs.

</details>

---

## Question 5

Quelle commande permet de lister tous les Pods dans tous les namespaces ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl get pods -A` ou `kubectl get pods --all-namespaces`

</details>

---

## Question 6

Quelle est la plage de ports valide pour un NodePort ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : **30000 a 32767**. C'est la plage par defaut pour les ports NodePort dans Kubernetes.

</details>

---

## Question 7

Quel type de Service est cree par defaut si aucun type n'est specifie ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : **ClusterIP**. C'est le type par defaut qui fournit une IP interne accessible uniquement dans le cluster.

</details>

---

## Question 8

Quelle est la difference entre `targetPort` et `nodePort` dans un Service ?

<details>
<summary>Voir la reponse</summary>

**Reponse** :
- **targetPort** : Port sur lequel l'application ecoute dans le conteneur
- **nodePort** : Port expose sur chaque noeud du cluster (30000-32767)

</details>

---

## Question 9

Quelle commande permet de demarrer un cluster Minikube ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `minikube start`

Options courantes :
- `minikube start --driver=docker`
- `minikube start --driver=virtualbox`

</details>

---

## Question 10

Qu'est-ce que Kind (Kubernetes in Docker) ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Kind est un outil qui permet d'executer des clusters Kubernetes locaux en utilisant des conteneurs Docker comme "noeuds". Il est leger et ideal pour les tests et le developpement.

</details>

---

## Question 11

Quelle commande permet de creer un cluster Kind avec un fichier de configuration ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kind create cluster --name mon-cluster --config kind-config.yaml`

</details>

---

## Question 12

Quel est le role d'un Service de type LoadBalancer ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Il expose le service via un load balancer externe fourni par le cloud provider (AWS, GCP, Azure). Il distribue le trafic entrant vers les Pods et fournit une IP publique.

</details>

---

## Question 13

Dans quel cas utiliseriez-vous un Service ClusterIP ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Pour la communication **interne** entre microservices dans le cluster. Exemples :
- Backend vers base de donnees
- Service de cache interne
- Communication inter-services

</details>

---

## Question 14

Quelle commande permet d'obtenir des details sur un Pod specifique ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl describe pod <nom-du-pod>`

Cette commande affiche les evenements, l'etat, les conteneurs, les labels, etc.

</details>

---

## Question 15

Comment supprimer un Deployment nomme "nginx" ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl delete deployment nginx`

Cela supprime egalement les ReplicaSets et Pods associes.

</details>

---

## Question 16

Quelle est la difference entre `kubectl apply` et `kubectl create` ?

<details>
<summary>Voir la reponse</summary>

**Reponse** :
- **kubectl create** : Cree une ressource (erreur si elle existe deja)
- **kubectl apply** : Cree ou met a jour une ressource (idempotent)

`apply` est prefere pour la gestion declarative.

</details>

---

## Question 17

Quelle commande permet de scaler un Deployment a 5 replicas ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl scale deployment <nom> --replicas=5`

Exemple : `kubectl scale deployment nginx --replicas=5`

</details>

---

## Question 18

Qu'est-ce qu'un namespace dans Kubernetes ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Un **namespace** est un mecanisme d'isolation logique qui permet de partitionner les ressources d'un cluster. Il permet de separer les environnements (dev, staging, prod) ou les equipes.

</details>

---

## Question 19

Quelle commande permet de creer un namespace ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl create namespace <nom>`

Exemple : `kubectl create namespace production`

</details>

---

## Question 20

Comment specifier un namespace lors de l'execution d'une commande kubectl ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Avec l'option `-n` ou `--namespace`

Exemple : `kubectl get pods -n production`

</details>

---

## Question 21

Quel est le role des labels dans Kubernetes ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Les **labels** sont des paires cle/valeur attachees aux objets. Ils permettent :
- L'organisation des ressources
- La selection via des selectors
- Le filtrage des ressources
- La liaison Service-Pod

</details>

---

## Question 22

Comment un Service sait-il vers quels Pods rediriger le trafic ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Via les **selectors** et les **labels**. Le Service utilise un selector qui correspond aux labels des Pods cibles.

```yaml
selector:
  app: my-app
```

</details>

---

## Question 23

Quelle commande permet de voir les logs d'un Pod ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl logs <nom-du-pod>`

Options utiles :
- `-f` : suivre les logs en temps reel
- `--tail=100` : afficher les 100 dernieres lignes

</details>

---

## Question 24

Comment executer une commande dans un Pod en cours d'execution ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl exec -it <nom-du-pod> -- <commande>`

Exemple : `kubectl exec -it nginx -- /bin/bash`

</details>

---

## Question 25

Quelle est la difference entre Minikube et Kind ?

<details>
<summary>Voir la reponse</summary>

**Reponse** :
| Aspect | Minikube | Kind |
|--------|----------|------|
| Backend | VM ou Docker | Docker uniquement |
| Multi-noeuds | Limite | Facile |
| Ressources | Plus lourd | Plus leger |
| Dashboard | Integre | A configurer |

</details>

---

## Question 26

Quelle commande permet de changer de contexte Kubernetes ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl config use-context <nom-du-contexte>`

Pour lister les contextes : `kubectl config get-contexts`

</details>

---

## Question 27

Qu'est-ce qu'un Service ExternalName ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : Un Service **ExternalName** mappe un service a un nom DNS externe. Il permet d'acceder a des services externes (ex: base de donnees cloud) via un alias DNS interne.

```yaml
spec:
  type: ExternalName
  externalName: db.example.com
```

</details>

---

## Question 28

Quelle commande permet d'exposer rapidement un Deployment en tant que Service ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `kubectl expose deployment <nom> --type=<type> --port=<port>`

Exemple : `kubectl expose deployment nginx --type=NodePort --port=80`

</details>

---

## Question 29

Comment obtenir l'URL d'un service dans Minikube ?

<details>
<summary>Voir la reponse</summary>

**Reponse** : `minikube service <nom-du-service> --url`

Cette commande retourne l'URL complete pour acceder au service.

</details>

---

## Question 30

Quelle est la hierarchie des objets Kubernetes du plus simple au plus complexe ?

<details>
<summary>Voir la reponse</summary>

**Reponse** :

```
Pod (unite de base)
   |
   v
ReplicaSet (gestion des replicas)
   |
   v
Deployment (gestion des mises a jour et rollbacks)
```

En pratique, on travaille principalement avec des **Deployments** qui gerent automatiquement les ReplicaSets et les Pods.

</details>

---

## Bareme de correction

| Score | Niveau |
|-------|--------|
| 27-30 | Excellent |
| 22-26 | Tres bien |
| 17-21 | Bien |
| 12-16 | Passable |
| < 12 | A revoir |

---

## Ressources pour reviser

- Chapitre 01 : Introduction a Kubernetes
- Chapitre 02 : Pods, ReplicaSets, Deployments
- Chapitre 10 : Introduction aux Services
- Chapitre 13 : Types de Services

