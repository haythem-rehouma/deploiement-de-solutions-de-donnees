---
title: "Chapitre 9 - Nettoyage de contexte et creation d'un nouveau cluster Kind"
description: "Apprenez a supprimer un cluster Kind existant et a en creer un nouveau proprement."
---

# Chapitre 9 - Nettoyage de contexte et creation d'un nouveau cluster Kind

Ce guide explique comment supprimer un cluster Kind existant et en creer un nouveau avec un fichier de configuration personnalise.

---

## 1. Supprimer l'ancien contexte

### 1.1. Lister les clusters et contextes existants

```bash
# Lister les clusters Kind
kind get clusters

# Lister les contextes Kubernetes
kubectl config get-contexts
```

### 1.2. Supprimer le cluster Kind

```bash
# Supprimer un cluster specifique
kind delete cluster --name <NOM_CLUSTER>

# Exemple : supprimer le cluster par defaut
kind delete cluster --name kind
```

Cette commande supprime :
- Tous les pods, services et volumes du cluster
- Le contexte kubeconfig associe
- Les conteneurs Docker du cluster

### 1.3. Verifier la suppression

```bash
kubectl config get-contexts
kind get clusters
```

---

## 2. Creer un nouveau cluster Kind

### 2.1. Preparer le fichier de configuration

Creez un fichier `kind-config.yaml` :

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
```

### 2.2. Creer le cluster

```bash
kind create cluster --name mon-cluster --config kind-config.yaml
```

### 2.3. Verifier le nouveau cluster

```bash
# Verifier le contexte actif
kubectl config current-context

# Lister les noeuds
kubectl get nodes

# Lister les pods systeme
kubectl get pods -A
```

---

## 3. Commandes utiles

| Action | Commande |
|--------|----------|
| Lister les clusters | `kind get clusters` |
| Supprimer un cluster | `kind delete cluster --name <nom>` |
| Changer de contexte | `kubectl config use-context kind-<nom>` |
| Supprimer tous les pods | `kubectl delete pods --all -n default` |

### Supprimer tous les clusters Kind

```bash
for cluster in $(kind get clusters); do
  kind delete cluster --name "$cluster"
done
```

---

## 4. Variantes de configuration

### Cluster simple (1 master, 1 worker)

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
```

### Cluster multi-workers (1 master, 3 workers)

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
  - role: worker
```

### Cluster HA (3 masters, 2 workers)

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: control-plane
  - role: control-plane
  - role: worker
  - role: worker
```

### Cluster avec port forwarding

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30000
        hostPort: 30000
        protocol: TCP
  - role: worker
```

