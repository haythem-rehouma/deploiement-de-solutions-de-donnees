---
title: "Chapitre 8 - Kind : Kubernetes in Docker"
description: "Installez et utilisez Kind pour creer des clusters Kubernetes locaux avec Docker."
---

# Chapitre 8 - démonstration-kind-linux commandes de gestion rapide avec kubectl (pratique6)

<a name="table-des-matieres"></a>
# Table des matières

1. [Introduction à Kind (Kubernetes in Docker)](#introduction-à-kind-kubernetes-in-docker)
   - [Qu'est-ce que Kind ?](#quest-ce-que-kind-)
   - [Pourquoi utiliser Kind ?](#pourquoi-utiliser-kind-)
     - [Avantages principaux](#avantages-principaux)
     - [Cas d'utilisation typiques](#cas-dutilisation-typiques)
2. [Installation de Docker et de Kubernetes avec Kind sur votre machine locale UBUNTU22.04](#installation-de-docker-et-de-kubernetes-avec-kind-sur-vm-locale-ubuntu22.04)
   - [Étapes d'installation](#etapes-dinstallation)
   - [Installation de Docker](#installation-de-docker)
   - [Installation de Kind](#installation-de-kind)
   - [Installation de kubectl](#installation-de-kubectl)
3. [Création d'un cluster Kubernetes avec Kind](#creation-dun-cluster-kubernetes-avec-kind)
4. [Autres commandes utiles](#autres-commandes-utiles)
5. [Références pour le dépannage](#references-pour-le-depannage)
6. [Annexe 1 - Les contextes dans KIND](#les-contextes-dans-kind)
7. [Annexe 2 - Historique des commandes](#historique-des-commandes)
8. [Annexe 3 - Autres commandes utiles](#autres-commandes-utiles)
9. [Références](#references)




---
<a name="introduction-à-kind-kubernetes-in-docker"></a>
# 1. Introduction à Kind (Kubernetes in Docker)
---

## Qu'est-ce que Kind ?

Kind (Kubernetes IN Docker) est un outil qui permet d'exécuter des clusters Kubernetes locaux en utilisant des conteneurs Docker comme "nœuds". Développé initialement pour tester Kubernetes lui-même, Kind est devenu un outil précieux pour le développement local et les tests.

### Pourquoi utiliser Kind ?

##### Avantages principaux :

1. **Légèreté et Rapidité**
   - Création rapide de clusters
   - Faible consommation de ressources
   - Parfait pour le développement local

2. **Environnement proche de la production**
   - Simule un véritable cluster Kubernetes
   - Support des fonctionnalités Kubernetes standards
   - Idéal pour tester des déploiements

3. **Facilité d'utilisation**
   - Installation simple
   - Configuration minimale requise
   - Interface en ligne de commande intuitive

4. **Multi-clusters**
   - Possibilité de créer plusieurs clusters
   - Isolation complète entre les clusters
   - Parfait pour tester différentes configurations


##### Cas d'utilisation typiques :

- Développement local d'applications Kubernetes
- Tests d'intégration continue (CI)
- Formation et apprentissage de Kubernetes
- Validation rapide des manifestes Kubernetes
- Expérimentation de nouvelles fonctionnalités

Kind est particulièrement adapté aux développeurs souhaitant travailler avec Kubernetes localement sans la complexité et les ressources nécessaires d'un cluster de production.

#### [Retour a la table des matieres](#table-des-matieres)

---
<a name="installation-de-docker-et-de-kubernetes-avec-kind-sur-vm-locale-ubuntu22.04"></a>
# 2. Installation de Docker et de Kubernetes avec Kind sur votre machine locale UBUNTU22.04
---

Ce guide vous aidera à installer Docker et à configurer un cluster Kubernetes local en utilisant Kind sur Ubuntu 22.04 Desktop. Ce processus comprend l'installation de Docker, Kind, et kubectl sur une machine avec une architecture AMD64 (x86_64) ou ARM64.

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="etapes-d-installation"></a>
# 3.Étapes d'installation
---
> Prérequis
>- Une machine sous Linux
>- Accès à un terminal
>- Droits d'administrateur (pour l'installation de packages)

<a name="installation-de-docker"></a>
## 3.1. Installation de Docker

#### 3.1.1. Ouvrez un terminal.
#### 3.1.2. Naviguez vers le bureau avec `cd Desktop/`.
#### 3.1.3. Clonez le dépôt contenant les scripts d'installation de Docker :

   ```bash
   apt update
   apt install git
   git clone https://github.com/hrhouma/install-docker.git
   cd install-docker/
   chmod +x install-docker.sh
   ./install-docker.sh
   docker --version
   apt install docker-compose
   docker-compose --version
   ```

<a name="installation-de-kind"></a>
## 3.2. Installation de Kind

#### 3.2.1. Téléchargez la version appropriée de Kind selon l'architecture de votre machine :

   - Pour AMD64 / x86_64 :

     ```bash
     [ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
     ```

   - Pour ARM64 :

     ```bash
     [ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-arm64
     ```

#### 3.2.2. Rendez le fichier `kind` exécutable et déplacez-le dans un répertoire accessible globalement :

   ```bash
   chmod +x ./kind
   sudo mv ./kind /usr/local/bin/kind
   ```

<a name="installation-de-kubectl"></a>
## 3.3. Installation de kubectl

#### 3.3.1.Installez `kubectl` pour interagir avec votre cluster Kubernetes :

    ```bash
    sudo apt-get install ca-certificates
    curl -LO -k "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    chmod +x kubectl
    mv kubectl /usr/local/bin/
    ```
    
#### 3.3.2. Vérification de la configuration de `kubectl`

    ```bash
    kubectl config get-contexts
    ```

#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="creation-dun-cluster-kubernetes-avec-kind"></a>
# 4. Créez un cluster Kubernetes avec Kind pour configurer un cluster avec un fichier de configuration spécifique :
---


#### 4.1. Créez un fichier de configuration :

 ```bash
nano kind-config.yaml
 ```

#### 4.2. Ajoutez le contenu suivant au fichier :

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

#### 4.3. Créez le cluster avec le fichier de configuration :

```bash
kind create cluster --config kind-config.yaml
```

#### 4.4. Vérification de la configuration créée

```bash
kubectl cluster-info --context kind-kind
```




#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="references-pour-le-depanage"></a>
# 5. Références pour le dépannage
---

Si vous rencontrez des problèmes lors de l'installation ou de l'utilisation de Docker, Kind, ou kubectl, consultez les liens suivants pour obtenir de l'aide :

- [Guide de démarrage rapide de Kind](https://kind.sigs.k8s.io/docs/user/quick-start)
- [Problèmes avec les certificats SSL dans curl](https://stackoverflow.com/questions/24611640/curl-60-ssl-certificate-problem-unable-to-get-local-issuer-certificate)
- [Erreurs lors de l'exécution de la version de kubectl](https://stackoverflow.com/questions/73866855/i-am-getting-an-error-while-running-kubectl-version-although-i-installed-it-by-f)

- [Quick start](https://kind.sigs.k8s.io/docs/user/quick-start)
- [SSL certificate problem](https://stackoverflow.com/questions/24611640/curl-60-ssl-certificate-problem-unable-to-get-local-issuer-certificate)
- [Error while running kubectl version](https://stackoverflow.com/questions/73866855/i-am-getting-an-error-while-running-kubectl-version-although-i-installed-it-by-f)


#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="annexes-les-contextes-dans-kind"></a>
# 6. Annexe 1 - Les contextes dans KIND
---

#### 6.1. Création d'un fichier de configuration pour le premier cluster
```ssh
cat <<EOT > kind-config-1.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
EOT
```
<a name="creation-du-premier-cluster-avec-une-configuration-specifique"></a>
#### 6.2. Création du premier cluster avec une configuration spécifique
```ssh
kind create cluster --name kind-1 --config kind-config-1.yaml
```
<a name="creation-d-un-fichier-de-configuration-pour-le-second-cluster"></a>
#### 6.3. Création d'un fichier de configuration pour le second cluster
```ssh
cat <<EOT > kind-config-2.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
EOT
```
<a name="creation-du-second-cluster-avec-la-configuration-specifique"></a>
#### 6.4. Création du second cluster avec la configuration spécifique
```ssh
kind create cluster --name kind-2 --config kind-config-2.yaml
```
<a name="affichage-de-tous-les-contextes-disponibles-pour-verification"></a>
#### 6.5. Affichage de tous les contextes disponibles pour vérification
```ssh
kubectl config get-contexts
```
<a name="basculer-vers-le-contexte-du-premier-cluster-kind-1-et-verifier-les-infos"></a>
#### 6.6. Basculer vers le contexte du premier cluster (kind-1) et vérifier les infos
```ssh
kubectl config use-context kind-kind-1
kubectl cluster-info --context kind-kind-1
```
<a name="basculer-vers-le-contexte-du-second-cluster-kind-2-et-verifier-les-infos"></a>
#### 6.7. Basculer vers le contexte du second cluster (kind-2) et vérifier les infos
```ssh
kubectl config use-context kind-kind-2
kubectl cluster-info --context kind-kind-2
```
<a name="execution-de-commandes-kubectl-specifiques-au-cluster-selectionne"></a>
#### 6.8. À ce stade, vous pouvez exécuter des commandes kubectl spécifiques au cluster sélectionné, comme la création de déploiements, services, etc.

```ssh
kubectl get nodes -o wide
kubectl get po -o wide
kubectl run nginx --image=nginx --dry-run=client -o yaml > haythem.yaml
kubectl get po -o wide
cat haythem.yaml
kubectl create -f haythem.yaml
kubect get po -o wide
kubectl get po -o wide
kubectl delete -f haythem.yaml
kubectl get po -o wide
kubectl run nginx --image=nginx --dry-run=client -o yaml
kubectl run nginx --image=nginx -- --dry-run=client -o yaml
kubectl delete -f haythem.yaml
kubectl get po -o wide
kubectl run nginx --image=nginx  -o yaml
kubectl get po -o wide
kubect get n
kubectl get nodes
kubectl get namespaces
```

<a name="pour-basculer-a-nouveau-vers-le-contexte-du-premier-cluster"></a>
#### 6.9. Pour basculer à nouveau vers le contexte du premier cluster
```ssh
kubectl config use-context kind-kind-1
```

#### 6.10. Et pour revenir au second
```ssh
kubectl config use-context kind-kind-2
```

#### 6.11. Lorsque vous avez terminé, vous pouvez supprimer les clusters si souhaité

```ssh
kind delete cluster --name kind-1
kind delete cluster --name kind-2
```

#### 6.12. Commandes utiles
```ssh
kubectl config get-contexts
kubectl config current-context
kubectl config use-context <NOM-DE-VOTRE-CONTEXTE>   
kubectl cluster-info --context <NOM-DE-VOTRE-CONTEXTE>
```



#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="historique-des-commandes"></a>
# 7 - Annexe 2 - Historique des commandes
---

```ssh
cd Desktop/
git clone https://github.com/hrhouma/install-docker.git
apt install git
git clone https://github.com/hrhouma/install-docker.git
cd install-docker/
chmod +x install-docker.sh
./install-docker.sh
# For AMD64 / x86_64


[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
# For ARM64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-arm64

chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
kind create cluster

sudo apt-get install ca-certificates
curl -LO -k "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
ls
chmod +x kubectl
mv kubectl /usr/local/bin/
kubectl
kubectl config get-contexts
kind create cluster --config kind-config.yaml
ls
kubectl cluster-info --context kind-kind
reboot
apt install openssh-server
ip a
apt update
apt install curl
ip a
kubectl
curl -LO "https://dl.k8s.io/release/v1.24.7/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/v1.24.7/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
sudo install -o root -g root -m 0755 kubectl /home/$USER/.local/bin/kubectl
kubectl
kubectl config get-contexts
kubectl get nodes -o wide
cat <<EOT > kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
EOT

ls
kind create cluster --config kind-config.yaml
cat kind-config.yaml
kubectl cluster-info --context kind-kind
kind create cluster --name kind-2 --config kind-config.yaml
ls
cat kind-config.yaml
kubectl config get-contexts
kubectl get nodes -o wide
kubectl get po -o wide
kubectl run nginx --image=nginx --dry-run=client -o yaml > haythem.yaml
kubectl get po -o wide
cat haythem.yaml
kubectl create -f haythem.yaml
kubect get po -o wide
kubectl get po -o wide
kubectl delete -f haythem.yaml
kubectl get po -o wide
kubectl run nginx --image=nginx --dry-run=client -o yaml
kubectl run nginx --image=nginx -- --dry-run=client -o yaml
kubectl delete -f haythem.yaml
kubectl get po -o wide
kubectl run nginx --image=nginx  -o yaml
kubectl get po -o wide
kubect get n
kubectl get nodes
kubectl get namespaces
```

#### [Retour a la table des matieres](#table-des-matieres)

---
<a name="autres-commandes-utiles"></a>
# 8 - Annexe 3 - Autres commandes utiles
---

- Redémarrage de la machine : `reboot`
- Installation du serveur SSH : `apt install openssh-server`
- Vérification de l'adresse IP : `ip a`
- Mise à jour des paquets : `apt update`
- Installation de curl : `apt install curl`


#### [Retour a la table des matieres](#table-des-matieres)
---
<a name="references"></a>
# Références : 

- https://kind.sigs.k8s.io/






