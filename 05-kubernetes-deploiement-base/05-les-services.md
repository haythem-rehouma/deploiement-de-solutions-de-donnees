# Cours 5 : Les Services

## Objectifs

- Comprendre le problème que les Services résolvent
- Créer différents types de Services
- Exposer une application au monde extérieur
- Faire communiquer des applications entre elles

---

## 5.1 Le problème

### Les Pods ont des IPs... qui changent !

Chaque Pod a une adresse IP. Mais :
- Cette IP est **interne** au cluster
- Elle change si le Pod est recréé
- Elle est différente pour chaque replica

**Comment accéder à une application si l'IP change tout le temps ?**

```mermaid
graph TB
    CLIENT[Client] --> |???| P1[Pod 1<br/>IP: 10.1.1.5]
    CLIENT --> |???| P2[Pod 2<br/>IP: 10.1.1.8]
    CLIENT --> |???| P3[Pod 3<br/>IP: 10.1.1.12]
    
    DEAD[Pod 1 meurt]
    NEW[Nouveau Pod 1<br/>IP: 10.1.1.99]
```

### La solution : les Services

Un **Service** fournit :
- Une **IP stable** qui ne change jamais
- Un **nom DNS** pour accéder facilement
- Du **load balancing** entre les pods

```mermaid
graph TB
    CLIENT[Client] --> SERVICE[Service<br/>mon-app<br/>IP: 10.0.0.50]
    SERVICE --> P1[Pod 1]
    SERVICE --> P2[Pod 2]
    SERVICE --> P3[Pod 3]
```

---

## 5.2 Types de Services

Il existe 4 types de Services. On va voir les 3 plus importants :

| Type | Accès | Cas d'usage |
|------|-------|-------------|
| **ClusterIP** | Interne seulement | Communication entre apps |
| **NodePort** | Depuis l'extérieur (port fixe) | Tests, développement |
| **LoadBalancer** | Depuis l'extérieur (cloud) | Production dans le cloud |

```mermaid
graph TB
    subgraph "Cluster Kubernetes"
        CIP[ClusterIP<br/>Accès interne]
        NP[NodePort<br/>:30000-32767]
        APP[Pods]
    end
    
    LB[LoadBalancer<br/>IP publique]
    
    INTERNAL[Apps internes] --> CIP
    CIP --> APP
    
    EXT[Utilisateurs externes] --> NP
    NP --> APP
    
    EXT2[Utilisateurs externes] --> LB
    LB --> APP
```

---

## 5.3 Service ClusterIP (défaut)

### C'est quoi ?

- Accessible **uniquement depuis le cluster**
- Parfait pour la communication entre applications
- Type par défaut

### Exemple

```yaml
# service-clusterip.yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-service
spec:
  type: ClusterIP           # Optionnel (c'est le défaut)
  selector:
    app: mon-app            # Cible les pods avec ce label
  ports:
  - port: 80                # Port du service
    targetPort: 80          # Port du container
```

### Créer et tester

```bash
# Créer le service
kubectl apply -f service-clusterip.yaml

# Voir le service
kubectl get services

# Tester depuis un pod
kubectl run test --rm -it --image=busybox -- wget -qO- http://mon-service
```

---

## 5.4 Service NodePort

### C'est quoi ?

- Accessible depuis **l'extérieur du cluster**
- Ouvre un port sur **chaque node** (30000-32767)
- Simple pour les tests

### Exemple

```yaml
# service-nodeport.yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-service-externe
spec:
  type: NodePort
  selector:
    app: mon-app
  ports:
  - port: 80                # Port du service (interne)
    targetPort: 80          # Port du container
    nodePort: 30080         # Port sur le node (optionnel, sinon auto)
```

### Accéder

```bash
# Avec Minikube
minikube service mon-service-externe --url

# Ou manuellement
minikube ip   # Donne l'IP du node
# Puis accéder à http://<IP>:30080
```

---

## 5.5 Exercice pratique 1 (15 minutes)

### Exposer une application

1. **Créer un Deployment :**

```yaml
# app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
```

```bash
kubectl apply -f app-deployment.yaml
```

2. **Créer un Service NodePort :**

```yaml
# app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  type: NodePort
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

```bash
kubectl apply -f app-service.yaml
```

3. **Vérifier :**
```bash
kubectl get services
kubectl get endpoints web-service    # Voir les pods ciblés
```

4. **Accéder à l'application :**
```bash
# Avec Minikube
minikube service web-service --url

# Ouvrir l'URL dans le navigateur
```

5. **Tester le load balancing :**
```bash
# Modifier nginx pour afficher le hostname
kubectl exec -it <pod-1> -- sh -c "echo Pod-1 > /usr/share/nginx/html/index.html"
kubectl exec -it <pod-2> -- sh -c "echo Pod-2 > /usr/share/nginx/html/index.html"
kubectl exec -it <pod-3> -- sh -c "echo Pod-3 > /usr/share/nginx/html/index.html"

# Faire plusieurs requêtes (observer l'alternance)
for i in 1 2 3 4 5 6; do curl -s $(minikube service web-service --url); done
```

---

## 5.6 Service LoadBalancer

### C'est quoi ?

- Crée un **load balancer externe** (dans le cloud)
- Obtient une **IP publique** automatiquement
- Utilisé en production (AWS, GCP, Azure)

### Exemple

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-service-public
spec:
  type: LoadBalancer
  selector:
    app: mon-app
  ports:
  - port: 80
    targetPort: 80
```

### Avec Minikube

Minikube n'a pas de vrais load balancers, mais peut les simuler :

```bash
# Dans un terminal séparé
minikube tunnel

# Le service obtient une IP externe
kubectl get services
```

---

## 5.7 DNS interne

### Comment ça marche ?

Kubernetes crée automatiquement des entrées DNS pour chaque Service.

**Format :** `<nom-service>.<namespace>.svc.cluster.local`

**Version courte :** `<nom-service>` (dans le même namespace)

### Exemple

```bash
# Depuis un pod, vous pouvez appeler :
curl http://web-service           # Dans le même namespace
curl http://web-service.default   # Namespace spécifié
```

---

## 5.8 Exercice pratique 2 (15 minutes)

### Communication entre applications

1. **Créer un backend :**

```yaml
# backend.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: hashicorp/http-echo
        args: ["-text=Reponse du Backend!"]
        ports:
        - containerPort: 5678
---
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
  - port: 80
    targetPort: 5678
```

2. **Créer un frontend qui appelle le backend :**

```yaml
# frontend.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: busybox
        command: ['sh', '-c', 'while true; do wget -qO- http://backend-service; sleep 5; done']
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30081
```

3. **Déployer :**
```bash
kubectl apply -f backend.yaml
kubectl apply -f frontend.yaml
```

4. **Vérifier la communication :**
```bash
# Voir les logs du frontend
kubectl logs -f deployment/frontend

# Doit afficher "Reponse du Backend!" toutes les 5 secondes
```

5. **Nettoyer :**
```bash
kubectl delete -f backend.yaml -f frontend.yaml
```

---

## 5.9 Résumé des types

```mermaid
graph TB
    subgraph "ClusterIP"
        CI[Service] --> CIP[IP interne<br/>10.0.0.X]
        CIP --> CIPODS[Pods]
        NOTE1[Accès interne seulement]
    end
    
    subgraph "NodePort"
        NP[Service] --> NPP[Node IP:30XXX]
        NPP --> NPPODS[Pods]
        NOTE2[Accès via port du node]
    end
    
    subgraph "LoadBalancer"
        LB[Service] --> LBP[IP publique]
        LBP --> LBPODS[Pods]
        NOTE3[Accès via load balancer cloud]
    end
```

---

## 5.10 Commandes essentielles

```bash
# Créer un service
kubectl apply -f service.yaml

# Lister les services
kubectl get services
kubectl get svc        # raccourci

# Voir les détails
kubectl describe service <nom>

# Voir les endpoints (pods ciblés)
kubectl get endpoints <nom>

# Exposer rapidement un deployment
kubectl expose deployment <nom> --type=NodePort --port=80

# Supprimer
kubectl delete service <nom>

# Avec Minikube - obtenir l'URL
minikube service <nom> --url
```

---

## 5.11 Résumé

```mermaid
mindmap
  root((Services))
    Problème
      IPs des pods changent
      Besoin d'un point d'accès stable
    Solution
      IP stable
      Nom DNS
      Load balancing
    Types
      ClusterIP
        Interne seulement
        Défaut
      NodePort
        Port sur chaque node
        Tests
      LoadBalancer
        IP publique
        Production cloud
    DNS
      nom-service
      nom-service.namespace
```

---

## 5.12 Quiz de validation

**1. Pourquoi a-t-on besoin de Services ?**

<details>
<summary>Voir la réponse</summary>

Parce que les IPs des Pods changent à chaque fois qu'ils sont recréés. Un Service fournit une IP stable et un nom DNS pour accéder aux Pods, plus du load balancing automatique.

</details>

**2. Quelle est la différence entre ClusterIP et NodePort ?**

<details>
<summary>Voir la réponse</summary>

- **ClusterIP** : Accessible uniquement depuis l'intérieur du cluster (pour communication entre apps)
- **NodePort** : Accessible depuis l'extérieur via un port sur chaque node (30000-32767)

</details>

**3. Comment un pod peut-il appeler un service par son nom ?**

<details>
<summary>Voir la réponse</summary>

Kubernetes crée automatiquement une entrée DNS. Un pod peut simplement faire :
```bash
curl http://nom-du-service
# ou
curl http://nom-du-service.namespace
```

</details>

**4. Quelle commande pour exposer rapidement un Deployment ?**

<details>
<summary>Voir la réponse</summary>

```bash
kubectl expose deployment mon-app --type=NodePort --port=80
```

</details>

**5. Comment accéder à un service NodePort avec Minikube ?**

<details>
<summary>Voir la réponse</summary>

```bash
minikube service nom-du-service --url
```
Cela affiche l'URL complète pour accéder au service.

</details>

---

## Prochaine étape

Vous connaissez maintenant les bases : Pods, Deployments, Services. Dans le prochain cours, on met tout ensemble dans un **projet pratique** !

