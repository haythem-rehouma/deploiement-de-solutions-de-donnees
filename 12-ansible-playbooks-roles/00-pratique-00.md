
#  Configurer Ansible et l'utiliser avec des conteneurs Docker pour une solution plus légère



# 🌍 Étape 1 : Installer Docker et Docker Compose

<details>
<summary> Étape 1 : Installer Docker et Docker Compose </summary>
  

Si Docker et Docker Compose ne sont pas déjà installés, voici les commandes pour les installer.

### Installation de Docker

```bash
su
apt update
git clone https://github.com/hrhouma/install-docker.git
cd install-docker/
chmod +x install-docker.sh
./install-docker.sh
#ou sh install-docker.sh
docker version
docker compose version
apt-install docker-compose
```

</details>

# 🌍 Étape 2 : Démarrer les conteneurs et Configurer l’accès SSH pour Ansible

<details>
<summary>  Étape 2 : Démarrer les conteneurs et Configurer l’accès SSH pour Ansible </summary>
  
2.1. Nous allons créer deux conteneurs Docker Ubuntu et les configurer pour accepter les connexions SSH. Ansible se connectera à ces conteneurs pour les gérer.

```bash
mkdir ansible1
cd ansible1
nano docker-compose.yaml
```


2.2. Créez un fichier `docker-compose.yaml` avec le contenu suivant :

```yaml
version: '3'
services:
  node1:
    image: ubuntu:latest
    container_name: node1
    networks:
      ansible_network:
        ipv4_address: 172.20.0.2
    volumes:
      - ./ansible_node1:/ansible_node
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

  node2:
    image: ubuntu:latest
    container_name: node2
    networks:
      ansible_network:
        ipv4_address: 172.20.0.3
    volumes:
      - ./ansible_node2:/ansible_node
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

networks:
  ansible_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/24
```

Ensuite, exécutez :

```bash
docker-compose up -d
```

Cela démarre deux conteneurs (`node1` et `node2`) et installe SSH sur chacun d’eux.

## Configurer l’accès SSH pour Ansible

Pour que Ansible puisse se connecter aux conteneurs, nous devons configurer l’accès SSH avec des clés.

1. **Générer une clé SSH** sur la machine hôte (si elle n'existe pas déjà) :

   ```bash
   ssh-keygen -t rsa -b 2048
   ```

2. **Copier la clé publique vers chaque conteneur** :

   ```bash
   docker exec -it node1 bash -c "mkdir -p /root/.ssh && echo '$(cat ~/.ssh/id_rsa.pub)' >> /root/.ssh/authorized_keys"
   docker exec -it node2 bash -c "mkdir -p /root/.ssh && echo '$(cat ~/.ssh/id_rsa.pub)' >> /root/.ssh/authorized_keys"
   ```

3. **Vérifier la connexion SSH** pour chaque conteneur :

   ```bash
   ssh root@$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node1)
   ssh root@$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node2)
   ```

</details>

# 🌍 Étape 3 : Créer l'inventaire Ansible

<details>
<summary> Étape 3 : Créer l'inventaire Ansible </summary>

Créez un fichier `inventory.ini` dans votre dossier de travail avec les adresses IP des conteneurs.

Exemple d'inventaire :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root
node2 ansible_host=172.20.0.3 ansible_user=root
```

> Remplacez les adresses IP par celles de `node1` et `node2` obtenues via `docker inspect`.

</details>


# 🌍 Étape 4 : Écrire le playbook Ansible

<details>
<summary>Étape 4 : Écrire le playbook Ansible </summary>

Nous allons écrire un playbook qui installe Apache et le démarre sur les conteneurs.

Créez un fichier `playbook.yml` avec le contenu suivant :

```yaml
---
- name: Configure Apache on Docker Containers
  hosts: node_containers
  become: yes
  tasks:
    - name: Update APT package manager
      apt:
        update_cache: yes

    - name: Install Apache2
      apt:
        name: apache2
        state: present

    - name: Start Apache Service
      service:
        name: apache2
        state: started
        enabled: true

    - name: Create index.html
      copy:
        content: "<h1>Bienvenue sur votre serveur web dans un conteneur Docker !</h1>"
        dest: /var/www/html/index.html
```

</details>

# 🌍 Étape 5 :  Exécuter le playbook

<details>
<summary>Étape 5 :  Exécuter le playbook </summary>

Lancez le playbook pour configurer Apache dans les conteneurs :

```bash
ansible-playbook -i inventory.ini playbook.yml
```



## 🔎 Vérifier le déploiement

Pour vérifier que le serveur web fonctionne, obtenez les IP de chaque conteneur et essayez de les atteindre via un navigateur :

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node1
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node2
```

Accédez à `http://<IP_du_conteneur>` dans votre navigateur. Vous devriez voir le message “Bienvenue sur votre serveur web dans un conteneur Docker !”


</details>


<br/>

# Annexe 1 - Créer un Fichier d'inventaire `inventory.ini`

Créer et configurer le fichier `inventory.ini` pour Ansible avec le contenu suivant :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root
node2 ansible_host=172.20.0.3 ansible_user=root

[web]
node1

[mail]
node2
```

### Commandes pour vérifier la configuration et exécuter des actions sur les nœuds :

1. **Lister les hôtes dans le groupe `web` :**
   ```bash
   ansible web -i inventory.ini --list-hosts
   ```

2. **Lister les hôtes dans le groupe `mail` :**
   ```bash
   ansible mail -i inventory.ini --list-hosts
   ```

3. **Lister tous les hôtes :**
   ```bash
   ansible all -i inventory.ini --list-hosts
   ```

4. **Lister les hôtes spécifiques :**

   - Pour `node1` :
     ```bash
     ansible node1 -i inventory.ini --list-hosts
     ```

   - Pour `node2` :
     ```bash
     ansible node2 -i inventory.ini --list-hosts
     ```

5. **Exécuter une commande sur `node1` pour afficher la date :**

   - Utilisant l'inventaire par défaut (`inventory`) :
     ```bash
     ansible node1 -m command -a "date" -i inventory
     ```

   - Utilisant l'inventaire `inventory.ini` :
     ```bash
     ansible node1 -m command -a "date" -i inventory.ini
     ```


---------



# Références : 



*Pour enrichir votre compréhension de l'automatisation avec Ansible pour la configuration de Docker et le déploiement de conteneurs, voici une liste de ressources structurées, incluant des articles, tutoriels et vidéos explicatives :*

---

### 📘 Articles Techniques et Guides

1. **[Automation Using Ansible to Configure Docker & Deploy](https://www.linkedin.com/pulse/automation-using-ansible-configure-docker-deploy-ashish-wakchaure/)**  
   Cet article sur LinkedIn par Ashish Wakchaure explore comment automatiser la configuration de Docker et le déploiement de conteneurs à l'aide d'Ansible. Il couvre l'initialisation des scripts et l'intégration avec Docker.

2. **[Install Docker and Portainer in a VM Using Ansible](https://dev.to/rimelek/install-docker-and-portainer-in-a-vm-using-ansible-21ib)**  
   Ce guide sur Dev.to par Rimelek explique comment installer Docker et Portainer sur une machine virtuelle avec Ansible. Un bon point de départ pour une configuration rapide de Docker et de son interface de gestion.

3. **[How to Use Ansible to Install and Set Up Docker on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-use-ansible-to-install-and-set-up-docker-on-ubuntu-20-04)**  
   Un tutoriel de DigitalOcean qui détaille étape par étape l'installation de Docker sur Ubuntu avec Ansible. Il est parfait pour les débutants en infrastructure Ansible.

4. **[Docker Containers with Ansible](https://medium.com/@Oskarr3/docker-containers-with-ansible-89e98dacd1e2)**  
   Article sur Medium qui vous guide à travers la gestion de conteneurs Docker en utilisant Ansible. Il couvre plusieurs commandes Ansible pratiques pour manipuler Docker.

5. **[Deploying Docker Container on Remote VM Using Ansible](https://www.linkedin.com/pulse/deploying-docker-container-remote-vm-using-ansible-nilesh-mathur/)**  
   Une ressource sur LinkedIn par Nilesh Mathur pour le déploiement automatisé de conteneurs Docker sur une VM distante avec Ansible, un choix pertinent pour les infrastructures en cloud.

---

### 🎥 Vidéos et Démos Pratiques

6. **[Automate Docker Installation and Configuration with Ansible (YouTube Video)](https://www.youtube.com/watch?v=-sLjgApgWOM&ab_channel=LinuxR)**  
   Une vidéo de la chaîne LinuxR expliquant l'automatisation de l'installation et de la configuration de Docker avec Ansible. Elle est très utile pour visualiser les étapes et suivre une démonstration en temps réel.


*Ces ressources combinées offrent une bonne couverture des concepts fondamentaux d'Ansible appliqué à Docker, depuis l'installation jusqu'à la gestion des conteneurs. Elles vous permettront de maîtriser progressivement l'automatisation des configurations de Docker dans divers environnements.*


# Annexe: 
- rm ~/.ssh/known_hosts
- sudo rm /var/root/.ssh/known_hosts
- ssh-keygen -R [hostname]

# Annexe 2 - exercice - trouvez l'équivalent du fichier playbook en ligne de commande (comparez les deux)



- *Pour installer et configurer Apache avec ces étapes en lignes de commande Linux, voici les commandes correspondantes :*

```bash
# Mettre à jour le gestionnaire de paquets APT
sudo apt update

# Installer Apache2
sudo apt install -y apache2

# Démarrer et activer le service Apache pour qu'il démarre au démarrage du système
sudo systemctl start apache2
sudo systemctl enable apache2

# Créer un fichier index.html avec le contenu HTML de bienvenue
echo "<h1>Bienvenue sur votre serveur web dans un conteneur Docker !</h1>" | sudo tee /var/www/html/index.html
```

Ces commandes réalisent les mêmes actions que les tâches dans le playbook Ansible.


------
## Explication du playbook

Voici le playbook Ansible correspondant aux étapes décrites pour installer et configurer Apache dans des conteneurs Docker :

```yaml
- name: Configure Apache on Docker Containers
  hosts: node_containers
  become: yes
  tasks:
    - name: Update APT package manager
      apt:
        update_cache: yes

    - name: Install Apache2
      apt:
        name: apache2
        state: present

    - name: Start Apache Service
      service:
        name: apache2
        state: started
        enabled: true

    - name: Create index.html
      copy:
        content: "<h1>Bienvenue sur votre serveur web dans un conteneur Docker !</h1>"
        dest: /var/www/html/index.html
```

### Explication de chaque section du playbook

#### 1. Déclaration du playbook
```yaml
- name: Configure Apache on Docker Containers
  hosts: node_containers
  become: yes
```

- **name** : Donne un titre au playbook, ici "Configure Apache on Docker Containers".
- **hosts** : Spécifie le groupe d'hôtes où le playbook sera exécuté, ici `node_containers`, ce qui implique qu'Ansible va exécuter les commandes sur tous les conteneurs Docker définis dans ce groupe d'hôtes de votre inventaire.
- **become** : Définit les privilèges d'administrateur (sudo) pour exécuter les tâches, ici avec `yes`.

#### 2. Mise à jour du gestionnaire de paquets APT
```yaml
- name: Update APT package manager
  apt:
    update_cache: yes
```

- **name** : Donne un nom à la tâche, ici "Update APT package manager".
- **apt** : Ce module Ansible gère les paquets sur les systèmes basés sur Debian. `update_cache: yes` force la mise à jour de la liste des paquets, équivalent à la commande `sudo apt update` en ligne de commande.

#### 3. Installation d'Apache2
```yaml
- name: Install Apache2
  apt:
    name: apache2
    state: present
```

- **name** : Nom de la tâche, ici "Install Apache2".
- **apt** : Le module installe le paquet `apache2` avec `name: apache2`, et `state: present` signifie que le paquet doit être installé (si déjà présent, il ne sera pas réinstallé).

#### 4. Démarrage et activation du service Apache
```yaml
- name: Start Apache Service
  service:
    name: apache2
    state: started
    enabled: true
```

- **name** : Nom de la tâche, "Start Apache Service".
- **service** : Module pour gérer les services. Ici, `name: apache2` cible le service Apache, `state: started` signifie qu'il doit être actif, et `enabled: true` s'assure qu'il sera démarré automatiquement au démarrage de la machine (équivalent à `sudo systemctl enable apache2`).

#### 5. Création d'un fichier `index.html` personnalisé
```yaml
- name: Create index.html
  copy:
    content: "<h1>Bienvenue sur votre serveur web dans un conteneur Docker !</h1>"
    dest: /var/www/html/index.html
```

- **name** : Nom de la tâche, ici "Create index.html".
- **copy** : Module qui copie un contenu ou un fichier. `content` contient le HTML que nous voulons afficher sur la page d'accueil du serveur Apache, et `dest` indique l’emplacement cible, `/var/www/html/index.html`, où ce contenu sera écrit (remplaçant tout fichier existant du même nom).

### Résumé
Ce playbook configure un serveur Apache dans des conteneurs Docker en quatre étapes simples : mise à jour du gestionnaire de paquets, installation d'Apache, démarrage et activation du service, et création d'une page d'accueil.



# Annexe 3 - commande tee vs >> 

## 3.1
La commande `tee` en Linux est utilisée pour lire l'entrée standard (souvent un texte que vous fournissez) et écrire ce contenu à la fois dans un fichier (ou plusieurs) et vers la sortie standard (habituellement affichée dans le terminal). Dans cet exemple, elle est utilisée pour :

1. Écrire le contenu HTML dans le fichier `/var/www/html/index.html`.
2. Afficher ce contenu dans le terminal en même temps.

La syntaxe exacte dans cet exemple est :

```bash
echo "<h1>Bienvenue sur votre serveur web dans un conteneur Docker !</h1>" | sudo tee /var/www/html/index.html
```

Cela permet d'envoyer le texte fourni par `echo` au fichier spécifié avec `tee` sans perdre le texte de vue dans le terminal.

## 3.2

Voici la différence entre `>>` et `tee` en Linux :

### 1. `>>` (Redirection vers un fichier en mode ajout)
La redirection `>>` est utilisée pour ajouter du contenu à un fichier. Elle ne produit aucune sortie à l'écran et ne nécessite pas de droits superutilisateur (sauf si vous écrivez dans un fichier système avec `sudo`). Exemple :

```bash
echo "Texte supplémentaire" >> /chemin/vers/fichier
```

- **Fonctionnement** : Le texte est ajouté à la fin du fichier sans être affiché dans le terminal.
- **Limite** : `>>` ne permet pas d'écrire simultanément dans un fichier et de voir le contenu dans le terminal.

### 2. `tee`
`tee` reçoit une entrée (pouvant être une chaîne de texte ou une sortie de commande) et écrit cette entrée à la fois dans le fichier spécifié et sur l’écran.

Exemple avec `tee` :

```bash
echo "Texte supplémentaire" | tee -a /chemin/vers/fichier
```

- **Fonctionnement** : Le texte est affiché dans le terminal et ajouté au fichier (grâce à l’option `-a`, qui ajoute le texte au lieu d’écraser le fichier).
- **Limite** : Nécessite `sudo tee` pour écrire dans des fichiers protégés (contrairement à `sudo echo` avec `>>`).

### Comparaison Résumée

| Fonction | `>>` (Append) | `tee` |
|----------|---------------|-------|
| Écrire dans un fichier uniquement | ✅ | ❌ (écrit et affiche) |
| Ajouter au fichier (append) | ✅ | ✅ (avec `-a`) |
| Afficher dans le terminal | ❌ | ✅ |
| Peut nécessiter `sudo` pour fichiers protégés | ❌ (direct `sudo`) | ✅ (ex: `echo ... | sudo tee`) |

`tee` est donc plus flexible quand on souhaite écrire dans des fichiers tout en visualisant l’écriture en direct.
