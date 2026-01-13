---
title: "Chapitre 3 - Installer Minikube sur Windows"
description: "Guide pratique d'installation de kubectl et Minikube sur une machine Windows."
---

# Chapitre 3 - Installer Minikube sur Windows

Ce guide couvre l'installation de kubectl et Minikube sur Windows pour creer un environnement Kubernetes local.



---
## Étape 1 : Installation et Configuration de kubectl sur Windows



### Installation de kubectl sur Windows

Cette section explique comment installer et configurer kubectl sur Windows, l'outil en ligne de commande indispensable pour interagir avec les clusters Kubernetes.

Pour plus de détails, consultez la [documentation officielle](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/).


### 1.1. Téléchargement de kubectl

Pour commencer, nous devons créer un dossier dédié pour kubectl :

  ```powershell
  mkdir C:\Users\hrehouma\Documents\kubectl
  cd C:\Users\hrehouma\Documents\kubectl
  ```


> Remplacez `hrehouma` par votre nom d'utilisateur (commande `whoami`).

- Téléchargez l'exécutable kubectl :

```powershell
curl.exe -LO "https://dl.k8s.io/release/v1.30.0/bin/windows/amd64/kubectl.exe"
```

1.2. Téléchargement du fichier de vérification
- Pour garantir l'intégrité du binaire, téléchargez le fichier de somme de contrôle :

```powershell 
curl.exe -LO "https://dl.k8s.io/v1.30.0/bin/windows/amd64/kubectl.exe.sha256"
```

### 1.3. Valider les binaires

```powershell 
CertUtil -hashfile kubectl.exe SHA256
```

Pour vérifier l'intégrité du fichier kubectl.exe téléchargé, utilisez la commande suivante :

```powershell 
type kubectl.exe.sha256
```

### 1.4. Tester l'outil kubectl avant de l'ajouter au path




```powershell 
kubectl version --client
```
> *Affiche la version du client kubectl en format texte*

##### Affiche la version du client kubectl en format yaml

```powershell 
kubectl version --client --output=yaml
```
> *Affiche la version du client kubectl en format yaml*

### 1.5. Ajouter kubectl au PATH système

Pour ajouter kubectl aux variables d'environnement système :

1. Ouvrez les "Variables d'environnement système" :
   - Appuyez sur `Windows + R`
   - Tapez `sysdm.cpl`
   - Cliquez sur "Variables d'environnement"

2. Dans la section "Variables système", sélectionnez "Path" et cliquez sur "Modifier"

3. Ajoutez le chemin complet vers kubectl :
   ```
   C:\Users\<votre_utilisateur>\Documents\kubectl
   ```

> Remplacez `<votre_utilisateur>` par votre nom d'utilisateur Windows.

4. Cliquez sur "OK" pour sauvegarder les modifications

5. **Important**: Redémarrez votre terminal PowerShell pour que les changements prennent effet



### 1.6. Accéder au répertoire utilisateur

- Pour accéder à votre répertoire utilisateur Windows :


```powershell 
cd %USERPROFILE%  
```

> par exemple, ça me ramene à mon répertoire d'utilisateur cd C:\Users\hrehouma


- Vérifiez si le dossier .kube existe dans votre répertoire utilisateur. Si ce n'est pas le cas, nous allons le créer :


> Le dossier .kube stocke la configuration kubectl et les informations de connexion aux clusters.


```powershell 
mkdir .kube
cd .kube
powershell
New-Item config -type file
```




### 1.7. Vérification finale


```powershell 
kubectl cluster-info
kubectl cluster-info dump
```
> ⚠️ Attention ! Ces commandes peuvent ne pas fonctionner étant donné que nous n'avons pas encore créé de cluster 🚫



### 1.8. Vérification finale 

Fermer la ligne de commande et réouvrir

```powershell 
kubectl version --client
```
> *Affiche la version du client kubectl en format texte*
```powershell 
kubectl version --client --output=yaml
```
> *Affiche la version du client kubectl en format yaml*


---

## Étape 2 : Installation et Configuration de Minikube sur Windows


### 2.1. Installation de Minikube sur Windows

Pour installer Minikube sur Windows, nous allons suivre les étapes suivantes :

1. Téléchargez l'exécutable Minikube depuis le site officiel :
   [Télécharger Minikube pour Windows](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)

**Prerequis** : Windows 10 64-bit, 4GB RAM minimum (8GB recommande), 20GB espace disque, un hyperviseur.

### 2.2. Créer un dossier minikube dans Documents

> Écrire cmd ensuite powershell

```powershell 
mkdir C:\Users\%USERPROFILE%\Documents\minikube
cd C:\Users\%USERPROFILE%\Documents\minikube
```


### 2.3. Création et téléchargement de minikube dans le dossier C:\minikube 



```powershell 
New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
```


### 2.4. Ajout de Minikube au PATH système

Il existe deux méthodes pour ajouter Minikube au PATH système :

#### Méthode 1 : Via l'interface graphique Windows

1. Ouvrez les "Paramètres système avancés"
2. Cliquez sur "Variables d'environnement"
3. Dans la section "Variables système", sélectionnez "Path"
4. Cliquez sur "Modifier"
5. Ajoutez le chemin `C:\minikube`
6. Cliquez sur "OK" pour sauvegarder

#### Méthode 2 : Via PowerShell (recommandée)

Exécutez la commande PowerShell ci-dessous pour ajouter automatiquement Minikube au PATH :

```powershell 
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)


if ($oldPath.Split(';') -inotcontains 'C:\minikube'){
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine)
}
```

### 2.5. Vérifier l'ajout de Minikube au PATH système

Pour vérifier que Minikube a bien été ajouté au PATH système, vous pouvez utiliser l'une des méthodes suivantes :

#### Via l'invite de commandes (CMD)

```cmd
path
```

#### Via PowerShell

```powershell
$env:Path
```

---

## Étape 3 : Démarrer le cluster Minikube


### 3.1. Démarrer le cluster

Pour démarrer votre cluster Minikube, exécutez la commande suivante dans PowerShell :

```powershell
minikube start
```

**En cas d'erreur**, essayez :
```powershell
minikube delete
minikube start --no-vtx-check
```


##### 📝 Voir l'annexe 1  pour les Détails des erreurs rencontrées




## Étape 4 : Tests et commandes pour tester le cluster

1. **Utilisation directe de kubectl** :

```powershell
minikube kubectl -- get po -A
```
> *Affiche les pods dans tous les namespaces*

2. **Création d'un alias pour kubectl** :


```powershell
alias kctl="minikube kubectl"
```
> *Crée un alias pour kubectl*
3. **Utilisation de minikube kubectl** :

```powershell
kctl get po -A
```
> *Utilise l'alias pour afficher les pods dans tous les namespaces*

4. **Refaire les mêmes manipulations avec kubectl** :
```powershell
alias kctl="minikube kubectl"
kubectl get po -A
```
> *Affiche les pods dans tous les namespaces*

5. **Ouvrir le dashboard** :

> *excécuter la commande ci-bas dans un nouvel terminal cmd et laisser le dashborad ouvert, ouvrez un navigateur et tapez localhost:30000*

```powershell
minikube dashboard 
```
---

## Etape 5 : Gerer le cycle de vie du cluster Minikube

| Action | Commande |
|--------|----------|
| Pause | `minikube pause` |
| Reprendre | `minikube unpause` |
| Arreter | `minikube stop` |
| Configurer memoire | `minikube config set memory 9001` |
| Lister addons | `minikube addons list` |
| Supprimer tout | `minikube delete --all` |

### Drivers disponibles

| Driver | Commande |
|--------|----------|
| Docker | `minikube start --driver=docker` |
| VirtualBox | `minikube start --driver=virtualbox` |
| Hyper-V | `minikube start --driver=hyperv` |







---
# Références et ressources utiles :

- [Manipulations et commandes avec minikube](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)
- [Documentation officielle de Minikube](https://minikube.sigs.k8s.io/docs/)
- [Guide d'installation de Minikube sur Windows](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)
- [Documentation des drivers supportés](https://minikube.sigs.k8s.io/docs/drivers/)
- [Tutoriels et exemples d'utilisation](https://minikube.sigs.k8s.io/docs/tutorials/)


<br/>
# Annexe 1 : Détails des erreurs rencontrées

---
### Tentative 1 et 2 
---

```ssh
Message d'erreur: 
X Exiting due to HOST_VIRT_UNAVAILABLE: Failed to start host: creating host: create: precreate: This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory
* Suggestion: Virtualization support is disabled on your computer. If you are running minikube within a VM, try '--driver=docker'. Otherwise, consult your systems BIOS manual for how to enable virtualization.
* Related issues:
  - https://github.com/kubernetes/minikube/issues/3900
  - https://github.com/kubernetes/minikube/issues/4730
```

### Solution: https://stackoverflow.com/questions/70813386/how-to-resolve-minikube-start-error-this-computer-doesnt-have-vt-x-amd-v-enab

--------------------
### Tentative 3 
--------------------

```ssh
Message d'erreur:

X Exiting due to GUEST_NOT_FOUND: Failed to start host: error loading existing host. Please try running [minikube delete], then run [minikube start] again: filestore "minikube": open C:\Users\me\.minikube\machines\minikube\config.json: The system cannot find the file specified.
* Suggestion: minikube is missing files relating to your guest environment. This can be fixed by running 'minikube delete'
* Related issue: https://github.com/kubernetes/minikube/issues/9130
```

### Solution: https://github.com/kubernetes/minikube/issues/13498

```ssh
I had same problem and I resolved my problem with
1-) minikube delete
2-) minikube start --no-vtx-check (Without specifying driver)
Magically it worked maybe it will help you too.
```



# Annexe 1



## Option A — Reset propre (recommandé)

### 1) Tout fermer (VirtualBox + terminaux)

Ferme VirtualBox (s’il est ouvert) et tes terminaux Minikube.

### 2) Supprimer le profil Minikube cassé

Dans **PowerShell** (pas besoin admin la plupart du temps) :

```powershell
minikube stop
minikube delete --all --purge
```

> `--purge` nettoie aussi les fichiers/profils locaux qui traînent.

### 3) Vérifier/Nettoyer côté VirtualBox (au cas où)

Liste les VMs :

```powershell
VBoxManage list vms SINON
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" --version
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" list vms
```

S’il reste une VM qui ressemble à **"minikube"**, supprime-la via VirtualBox UI **ou** :

```powershell
VBoxManage unregistervm "minikube" --delete  SINON
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" unregistervm "minikube" --delete
```

*(si le nom exact diffère, copie-colle le nom retourné par `VBoxManage list vms`)*

### 4) Redémarrer Minikube

Ensuite :

```powershell
minikube start --driver=virtualbox --no-vtx-check
```

Puis vérifie :

```powershell
minikube status
kubectl get nodes
```

## Option B — Réparer sans tout supprimer (si tu veux tenter)

Si tu veux essayer de “réparer” le profil sans delete, c’est moins fiable, mais tu peux :

1. Vérifier les profils :

```powershell
minikube profile list
```

2. Si le profil `minikube` est listé mais cassé, souvent le plus simple reste :

```powershell
minikube delete
minikube start --driver=virtualbox --no-vtx-check
```


## Si ça re-plante encore après delete

Les causes fréquentes sur Windows 11 + VirtualBox :

### 1) Hyper-V / WSL2 / “Virtual Machine Platform” qui interfère

VirtualBox peut être instable si Hyper-V est actif (selon config). Tu peux **tester** l’état :

```powershell
systeminfo | Select-String "Hyper-V Requirements"
```

Si tu vois que Hyper-V est actif et que VirtualBox galère, le plus simple est de **switcher de driver** (option C).

### 2) Option C — Utiliser le driver Docker (souvent le plus smooth)

Si Docker Desktop est installé :

```powershell
minikube delete --all --purge
minikube start --driver=docker
minikube status
```

---

## Pourquoi le `--no-vtx-check` n’aide pas parfois

Parce que ton erreur n’est pas VT-x : c’est **un profil Minikube incomplet/corrompu** (fichier `config.json` manquant).


```powershell
minikube profile list
VBoxManage list vms
```
