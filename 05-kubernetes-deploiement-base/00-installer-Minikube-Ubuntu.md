# Installation de Minikube sur une VM Ubuntu 22.04 / 24.04

## 1. Mise à jour du système

```bash
sudo apt update && sudo apt upgrade -y
```

## 2. Installer les dépendances

```bash
sudo apt install -y curl wget apt-transport-https ca-certificates conntrack
```

## 3. Installer Docker

```bash
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker
```

Vérification :

```bash
docker --version
```

## 4. Installer kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install kubectl /usr/local/bin/kubectl
rm kubectl
```

Vérification :

```bash
kubectl version --client
```

## 5. Installer Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
rm minikube-linux-amd64
```

Vérification :

```bash
minikube version
```

## 6. Démarrer le cluster

```bash
minikube start --driver=docker
```

## 7. Vérifications

```bash
minikube status
kubectl cluster-info
kubectl get nodes
```

---

## 8. Dashboard Kubernetes

```bash
minikube dashboard --url
```

Pour accéder au dashboard depuis une machine distante (si la VM n'a pas de GUI) :

```bash
kubectl proxy --address='0.0.0.0' --accept-hosts='.*'
```

Le dashboard sera accessible sur `http://<IP_VM>:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/`

---

## 9. Activer les addons utiles

```bash
minikube addons list
minikube addons enable metrics-server
minikube addons enable ingress
minikube addons enable dashboard
```

Vérification :

```bash
minikube addons list | grep enabled
```

---

## 10. Test rapide — Déployer Nginx

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --type=NodePort --port=80
```

Accéder au service :

```bash
minikube service nginx --url
```

Vérifier :

```bash
kubectl get pods
kubectl get svc
kubectl get deployments
```

Nettoyer :

```bash
kubectl delete svc nginx
kubectl delete deployment nginx
```

---

## 11. Déploiement via fichier YAML — Application web

Créer le fichier `webapp.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nginx:alpine
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    app: webapp
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

Appliquer et tester :

```bash
kubectl apply -f webapp.yaml
kubectl get pods -l app=webapp
kubectl get svc webapp-service
minikube service webapp-service --url
```

Nettoyer :

```bash
kubectl delete -f webapp.yaml
```

---

## 12. Déploiement avec scaling et mise à jour

```bash
kubectl create deployment myapp --image=httpd:2.4
kubectl scale deployment myapp --replicas=4
kubectl get pods -o wide
```

Mise à jour de l'image (rolling update) :

```bash
kubectl set image deployment/myapp httpd=httpd:2.4-alpine
kubectl rollout status deployment/myapp
```

Rollback si problème :

```bash
kubectl rollout undo deployment/myapp
kubectl rollout history deployment/myapp
```

Nettoyer :

```bash
kubectl delete deployment myapp
```

---

## 13. Déploiement multi-conteneurs — WordPress + MySQL

Créer le fichier `wordpress.yaml` :

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  password: bXlwYXNzd29yZA==  # "mypassword" en base64

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
        - name: MYSQL_DATABASE
          value: wordpress
        ports:
        - containerPort: 3306

---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql
  ports:
  - port: 3306
    targetPort: 3306

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
      - name: wordpress
        image: wordpress:latest
        env:
        - name: WORDPRESS_DB_HOST
          value: mysql-service
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: wordpress-service
spec:
  type: NodePort
  selector:
    app: wordpress
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30088
```

Déployer et accéder :

```bash
kubectl apply -f wordpress.yaml
kubectl get pods
kubectl get svc
minikube service wordpress-service --url
```

Nettoyer :

```bash
kubectl delete -f wordpress.yaml
```

---

## 14. Commandes d'inspection et débogage

```bash
# Voir les logs d'un pod
kubectl logs <nom-du-pod>
kubectl logs <nom-du-pod> -f          # suivre en temps réel

# Entrer dans un pod
kubectl exec -it <nom-du-pod> -- /bin/sh

# Détails complets d'un pod
kubectl describe pod <nom-du-pod>

# Voir les événements du cluster
kubectl get events --sort-by='.lastTimestamp'

# Utilisation des ressources (nécessite metrics-server)
kubectl top nodes
kubectl top pods
```

---

## 15. Commandes utiles — Résumé

| Commande | Description |
|----------|-------------|
| `minikube start` | Démarrer le cluster |
| `minikube stop` | Arrêter le cluster |
| `minikube delete` | Supprimer le cluster |
| `minikube dashboard --url` | URL du dashboard |
| `minikube service <svc> --url` | URL d'un service |
| `minikube ssh` | SSH dans le node |
| `minikube addons enable <addon>` | Activer un addon |
| `kubectl get pods,svc,deploy` | Voir toutes les ressources |
| `kubectl apply -f <fichier>` | Appliquer un manifeste |
| `kubectl delete -f <fichier>` | Supprimer un manifeste |
| `kubectl scale deploy <name> --replicas=N` | Scaler un déploiement |
| `kubectl rollout undo deploy/<name>` | Rollback |
| `kubectl logs <pod>` | Voir les logs |
| `kubectl exec -it <pod> -- sh` | Entrer dans un pod |
| `kubectl top pods` | Utilisation CPU/RAM |

---

## En cas de problème

```bash
# Redémarrer Docker
sudo systemctl restart docker

# Supprimer et recréer le cluster
minikube delete
minikube start --driver=docker

# Vérifier les permissions Docker (pas besoin de sudo)
docker ps

# Vérifier l'espace disque
df -h

# Nettoyer les images Docker inutilisées
docker system prune -a
```
