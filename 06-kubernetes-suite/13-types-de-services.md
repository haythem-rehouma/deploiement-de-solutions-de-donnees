---
title: "Chapitre 13 - Types de Services Kubernetes"
description: "Guide complet des differents types de Services Kubernetes avec exemples pratiques."
---

# Chapitre 13 - Types de Services Kubernetes

## Table des matieres

1. [Rappel : Qu'est-ce qu'un Service](#1-rappel)
2. [Cas concret : NodePort](#2-nodeport-cas-concret)
3. [Implementation d'un Service NodePort](#3-implementation-nodeport)
4. [Service ClusterIP](#4-clusterip)
5. [Service LoadBalancer](#5-loadbalancer)
6. [Resume comparatif](#6-resume)

---

## 1. Rappel : Qu'est-ce qu'un Service Kubernetes {#1-rappel}

Les Services Kubernetes permettent la communication entre les composantes d'une application :

- **Frontend vers Backend** : Les utilisateurs accedent au frontend
- **Backend vers Base de donnees** : Le backend communique avec la DB
- **Couplage lache** : Les microservices restent independants

```
Utilisateurs --> [Service Frontend] --> [Service Backend] --> [Service DB]
```

### Roles des Services

| Role | Description |
|------|-------------|
| Abstraction reseau | IP stable malgre les pods ephemeres |
| Load Balancing | Distribution du trafic entre pods |
| Service Discovery | Decouverte automatique des ressources |
| Exposition externe | Acces depuis l'exterieur du cluster |

---

## 2. Cas concret : Probleme de communication externe {#2-nodeport-cas-concret}

### Probleme

- Node Kubernetes : `192.168.1.2`
- Pod avec application web : `10.240.0.2`
- Utilisateur externe : `192.168.1.10`

L'utilisateur ne peut pas acceder directement au Pod (reseau interne).

### Solution : NodePort

Le Service NodePort cree un pont entre :
- **Reseau externe** (192.168.x.x) : accessible par l'utilisateur
- **Reseau interne** (10.244.x.x) : reseau prive des Pods

```bash
# L'utilisateur accede via le NodePort
curl http://192.168.1.2:30008

# Le service redirige vers le Pod interne
# 192.168.1.2:30008 --> 10.244.0.2:80
```

---

## 3. Implementation d'un Service NodePort {#3-implementation-nodeport}

### Les 3 ports impliques

| Port | Nom | Description |
|------|-----|-------------|
| 80 | targetPort | Port du conteneur (application) |
| 80 | port | Port du service |
| 30008 | nodePort | Port expose sur le Node (30000-32767) |

### Fichier de definition

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```

### Commandes

```bash
# Creer le service
kubectl apply -f service-definition.yaml

# Verifier le service
kubectl get services

# Acceder via Minikube
minikube service my-app-service --url
```

---

## 4. Service ClusterIP {#4-clusterip}

ClusterIP est le type par defaut. Il fournit une IP interne stable accessible uniquement dans le cluster.

### Cas d'usage

- Communication entre microservices
- Backend vers base de donnees
- Services internes (cache, files d'attente)

### Fichier de definition

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  type: ClusterIP  # Type par defaut
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 80
```

### Acces au service

```bash
# Depuis un autre Pod
curl http://backend-service:80

# Ou via l'IP du cluster
curl http://10.96.0.1:80
```

---

## 5. Service LoadBalancer {#5-loadbalancer}

LoadBalancer utilise le load balancer natif du cloud provider.

### Cas d'usage

- Applications de production exposees au public
- Haute disponibilite requise
- Environnements cloud (AWS, GCP, Azure)

### Fichier de definition

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
    - port: 80
      targetPort: 80
```

### Comportement selon l'environnement

| Environnement | Resultat |
|---------------|----------|
| AWS/GCP/Azure | Load balancer cloud cree automatiquement |
| Minikube/On-premise | Equivalent a NodePort |

---

## 6. Resume comparatif {#6-resume}

| Type | Accessibilite | Cas d'usage | IP |
|------|---------------|-------------|-----|
| ClusterIP | Interne seulement | Microservices | IP cluster |
| NodePort | Externe via port node | Dev/Test | IP node:30000-32767 |
| LoadBalancer | Externe via LB cloud | Production | IP publique |
| ExternalName | Alias DNS | Services externes | DNS externe |

### Choix du type de service

```
Communication interne ? --> ClusterIP
Developpement/Test ?    --> NodePort
Production cloud ?      --> LoadBalancer
Service externe ?       --> ExternalName
```

### Hierarchie des types

```
LoadBalancer
    |
    +-- NodePort (inclus)
           |
           +-- ClusterIP (inclus)
```

Un LoadBalancer inclut les fonctionnalites de NodePort qui inclut ClusterIP.

---

## Exercice pratique

1. Creez un Deployment nginx avec 3 replicas
2. Creez un Service NodePort pour y acceder
3. Testez l'acces depuis votre navigateur

```bash
# 1. Creer le Deployment
kubectl create deployment nginx --image=nginx --replicas=3

# 2. Exposer via NodePort
kubectl expose deployment nginx --type=NodePort --port=80

# 3. Obtenir l'URL (Minikube)
minikube service nginx --url
```
