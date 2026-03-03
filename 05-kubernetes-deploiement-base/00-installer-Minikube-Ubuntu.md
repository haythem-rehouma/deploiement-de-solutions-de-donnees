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

-si vous utilisez ubuntu22.04

```bash
git clone https://github.com/hrhouma/install-docker.git
ls
chmod +x install-docker/
cd install-docker/
chmod +x install-docker.sh
sh install-docker.sh
docker ps
apt install docker-compose
docker --version
docker-compose --version
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

### Troubleshooting

- Erreur 1 : ne pas exécuter avec root

```bash
su
sudo usermod -aG sudo eleve
```

- Erreur 2 : Add your user to the 'docker' group
  
<img width="1149" height="545" alt="image" src="https://github.com/user-attachments/assets/42363d1b-4908-444b-92ee-961e3985e0b5" />

<br/>
<br/>

- Erreur = **droits insuffisants sur le socket Docker** (`/var/run/docker.sock`).
- Minikube (driver docker) essaie d’appeler l’API Docker, mais ton user n’a pas la permission.

Fais ça (dans cet ordre) :

```bash
# 1) Vérifie que Docker tourne
sudo systemctl status docker --no-pager
```

Si c’est “inactive”, démarre-le :

```bash
sudo systemctl enable --now docker
```

Ensuite, ajoute ton utilisateur au groupe docker :

```bash
sudo usermod -aG docker $USER
```

Recharge les groupes **dans ta session** (option A) :

```bash
newgrp docker
```

Ou (option B, souvent plus simple) : **déconnecte-toi / reconnecte-toi** (ou redémarre) puis continue.

Vérifie que ça marche sans sudo :

```bash
docker ps
```

Puis relance minikube :

```bash
minikube start --driver=docker
```

Si tu es dans une VM VirtualBox (vu `vbox/amd64`) et que `docker ps` échoue encore, envoie-moi la sortie de :

```bash
id
ls -l /var/run/docker.sock
docker version
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
kubectl get pods -A -l 'app!=webapp'
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


### Exercice: Trouvez les erreurs dans l'exercice ci-haut. Pourquoi la page de wordpress ne s'affiche pas ?
<details>
<summary> Résolution de problèmes</summary>

- On va **troubleshooter sans corriger**. 
- Objectif: **identifier la cause** de `ContainerCreating` avec une démarche propre, et utiliser `grep` pour aller vite.


## 13.0) Lecture rapide : `ContainerCreating` veut dire quoi ?

Ça veut dire que **le conteneur n’a même pas démarré**. Donc **ce n’est pas encore** un problème “WordPress ne se connecte pas à MySQL” (ça, ce serait plutôt `CrashLoopBackOff` ou `Running` avec logs d’erreur).

Les causes typiques de `ContainerCreating` :

* image en cours de pull / erreur pull
* problème réseau/registry
* problème CNI (réseau pods)
* problème de montage volume (PVC, hostPath, permissions)
* problème secret/configmap manquant (mais souvent ça devient `CreateContainerConfigError`, pas toujours)
* nœud pas prêt / kubelet / runtime (containerd) en souci

---

## 13.1) Voir *où* ça bloque : `describe` + Events (c’est LA base)

### a) Pour MySQL

```bash
kubectl describe pod mysql-847b5f5fb-2ptbc | sed -n '/Events:/,$p'
```

### b) Pour WordPress

```bash
kubectl describe pod wordpress-55497dccb5-brxk4 | sed -n '/Events:/,$p'
kubectl describe pod wordpress-55497dccb5-cnp7q | sed -n '/Events:/,$p'
```

### Avec `grep` (filtrer les lignes utiles)

```bash
kubectl describe pod mysql-847b5f5fb-2ptbc | egrep -i "Events:|Warning|Failed|Err|Image|Pull|Back-off|mount|secret|config|cni|network|timeout"
```

👉 **Ce que tu cherches dans Events** (mots clés):

* `Failed to pull image`, `ImagePullBackOff`, `ErrImagePull`
* `failed to create pod sandbox` (CNI)
* `MountVolume.SetUp failed`
* `FailedMount`
* `secret "xxx" not found`
* `context deadline exceeded`
* `node not ready`

---

## 13.2) Regarder les événements du namespace (vue globale)

Souvent plus clair que pod par pod.

```bash
kubectl get events --sort-by=.lastTimestamp
```

Avec filtre `grep` :

```bash
kubectl get events --sort-by=.lastTimestamp | egrep -i "warning|failed|pull|image|mount|cni|network|sandbox|secret|config"
```

---

## 13.3) Vérifier si c’est un problème d’image (pull)

### a) Voir l’état exact côté conteneur

```bash
kubectl get pod mysql-847b5f5fb-2ptbc -o wide
kubectl get pod wordpress-55497dccb5-brxk4 -o wide
```

### b) Si tu vois dans Events “Pulling image …” puis rien → attendre/registry lent.

### c) Si tu vois “ErrImagePull / ImagePullBackOff” → le détail est dans `describe`.

---

## 13.4) Vérifier le nœud (souvent la source de ContainerCreating)

Comme tu es sur une VM (`eleve@k8s-vm`), ça peut venir du node.

```bash
kubectl get nodes -o wide
kubectl describe node $(kubectl get nodes -o name | head -n1) | egrep -i "Ready|NotReady|NetworkUnavailable|MemoryPressure|DiskPressure|PIDPressure"
```

Si `NotReady` ou `NetworkUnavailable` → c’est normal que tout reste en `ContainerCreating`.

---

## 13.5) Vérifier rapidement Secrets/Services existent (pas pour corriger, juste confirmer)

```bash
kubectl get secret
kubectl get svc
kubectl get deploy
```

Avec grep :

```bash
kubectl get secret | grep -i mysql
kubectl get svc | grep -i mysql
kubectl get deploy | egrep -i "mysql|wordpress"
```

---

## 13.6) Si Events parlent de volumes/mount → check PVC/PV (même si tu n’en as pas, ça peut révéler un souci)

```bash
kubectl get pvc
kubectl get pv
```

---

## 13.7) Si Events parlent de CNI / “pod sandbox” (réseau)

Exemples de messages typiques :

* `Failed to create pod sandbox`
* `cni plugin not initialized`
* `failed to set up sandbox`

Commandes:

```bash
kubectl -n kube-system get pods -o wide
kubectl -n kube-system get pods | egrep -i "cni|calico|flannel|weave|cilium"
```

Puis (si tu identifies le plugin):

```bash
kubectl -n kube-system logs <pod-cni> | tail -n 50
```

---

## 13.8) Pattern de diagnostic (méthode “pro”)

1. **Regarde Events** (`describe pod …`)
2. **Confirme au niveau namespace** (`kubectl get events`)
3. **Valide le node** (`kubectl get nodes`, `describe node`)
4. **Si c’est image** → focus `pull`
5. **Si c’est mount** → focus `pvc/pv`
6. **Si c’est sandbox/CNI** → focus `kube-system`

---




```bash
kubectl describe pod mysql-847b5f5fb-2ptbc | sed -n '/Events:/,$p' | tail -n 20
```

#### Version finale 

```bash
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  # username = wordpress (base64)
  username: d29yZHByZXNz
  # password = mypassword (base64)
  password: bXlwYXNzd29yZA==

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
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
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
        - name: WORDPRESS_DB_NAME
          value: wordpress
        - name: WORDPRESS_DB_USER
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: username
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




La ligne **problématique manquante** dans ta version initiale (côté WordPress) était **le user DB** :

```yaml
- name: WORDPRESS_DB_USER
  valueFrom:
    secretKeyRef:
      name: mysql-secret
      key: username
```

Et, pour que cette ligne marche, il fallait aussi **la clé `username` dans le Secret** (elle n’existait pas avant) :

```yaml
username: d29yZHByZXNz
```

Sans `WORDPRESS_DB_USER` (et donc sans `username` dans le Secret), WordPress n’a pas le compte à utiliser pour se connecter à MySQL.



1. Constater que les Pods sont bloqués, puis lire la cause exacte : `kubectl describe pod <wordpress-pod> | sed -n '/Events:/,$p'` (chercher *Failed*, *Warning*).
2. Une fois les Pods *Running*, tester l’app : ouvrir le NodePort, puis si erreur, lire les logs : `kubectl logs deploy/wordpress | tail -n 80`.
3. Dans les logs, repérer l’erreur DB (access denied / user unknown) et extraire les mots clés : `kubectl logs deploy/wordpress | egrep -i "access denied|mysql|user|password|database|host"`.
4. Vérifier la config réellement injectée : `kubectl describe deploy wordpress | egrep -i "WORDPRESS_DB_|env:"` et vérifier le Secret : `kubectl describe secret mysql-secret`.
5. Comparer ce que WordPress attend (HOST/NAME/USER/PASSWORD) avec ce que le YAML fournit : tu vois que **DB_USER manque**, donc tu ajoutes `username` au Secret + `WORDPRESS_DB_USER` (et côté MySQL `MYSQL_USER/MYSQL_PASSWORD` si tu crées l’utilisateur).



  
</details>



<br/>

### Autres commandes - partie 1

<details>
  <summary> Autres commandes </summary>

```bash
kubectl get pods -o wide
kubectl get svc
kubectl get endpoints mysql-service wordpress-service -o wide
kubectl get pods --show-labels
kubectl describe svc wordpress-service
kubectl describe svc mysql-service
kubectl get pods -l app=wordpress
kubectl describe pod -l app=wordpress | sed -n '/Events:/,$p'
kubectl logs -l app=wordpress --tail=200
kubectl get pods -l app=mysql
kubectl describe pod -l app=mysql | sed -n '/Events:/,$p'
kubectl logs -l app=mysql --tail=200
kubectl run netdebug --rm -it --image=busybox:1.36 --restart=Never -- sh
```

Dans le shell busybox, teste :

```sh
nslookup mysql-service
nc -vz mysql-service 3306
```

* Si `nslookup` échoue → souci DNS/namespace (rare) ou service absent.
* Si `nc` échoue → MySQL pas prêt / endpoints vides / port pas exposé.


Dès que MySQL est **Running/Ready**, force juste un redémarrage de WordPress :

```bash
kubectl rollout restart deployment/wordpress
kubectl get pods -w
minikube service wordpress-service --url
kubectl get pods -o wide
kubectl get endpoints mysql-service wordpress-service -o wide
```


  
</details>




<br/>

### Autres commandes - partie 2

<details>
  <summary> Autres commandes - partie 2 </summary>

```bash
kubectl describe pod <wordpress-pod> | sed -n '/Events:/,$p'
kubectl get events --sort-by=.lastTimestamp | egrep -i "NotReady|etcd|refused|timeout|FailedScheduling"
curl -I https://registry-1.docker.io/v2/
minikube ssh -- "docker images | grep -E '^wordpress|^mysql'"
kubectl get pods -o wide
kubectl describe pod <wordpress-pod> | sed -n '/Events:/,$p'
kubectl get events --sort-by=.lastTimestamp | tail -n 30
minikube ssh -- "docker images | grep -E '^wordpress|^mysql'"
kubectl describe pod wordpress-5d45f7c646-lv6m9 | sed -n '/Events:/,$p'
```


  
</details>





<br/>


### Fichier final

<details>
  <summary> Fichier final </summary>

```bash
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  # username = wordpress (base64)
  username: d29yZHByZXNz
  # password = mypassword (base64)
  password: bXlwYXNzd29yZA==

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
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
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
        - name: WORDPRESS_DB_NAME
          value: wordpress
        - name: WORDPRESS_DB_USER
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: username
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

</details>


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
