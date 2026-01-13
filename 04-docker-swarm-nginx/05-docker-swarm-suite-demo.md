# docker-swarm-demo

### Prérequis:
- 3 instances AWS EC2 : Pour le master (2 cœurs CPU) , et pour chacun des workers (1CPU).
- Docker installé sur chaque instance.
- Les instances doivent être accessibles via SSH.

### Configuration de l'Environnement:
1. Connectez-vous à votre instance master via SSH.
2. Passez en superutilisateur avec `sudo -s` pour exécuter des commandes avec les privilèges root.

### Installation de docker sur les 3 machines
```bash
git clone https://github.com/hrhouma/install-docker.git
cd /install-docker/
chmod +x install-docker.sh
./install-docker.sh
docker version
docker compose version
```

#### Sur le Nœud Manager (Master):
```bash
sudo hostnamectl set-hostname master
exec bash
```

#### Sur le Worker 1:
```bash
sudo hostnamectl set-hostname worker1
exec bash
```

#### Sur le Worker 2:
```bash
sudo hostnamectl set-hostname worker2
exec bash
```

Après avoir exécuté `exec bash`, le nouvel invite de commande devrait refléter le nom d'hôte mis à jour.


### Initialisation de Docker Swarm:
1. Sur le nœud master, initialisez le swarm avec la commande suivante:
   ```
   docker swarm init --advertise-addr <IP_DU_MANAGER> --listen-addr <IP_DU_MANAGER>:2377
   ```
   Remplacez `<IP_DU_MANAGER>` par l'adresse IP privée de votre instance master.

2. Après l'initialisation, un token sera généré. Copiez la commande `docker swarm join` fournie par l'initialisation.

### Configuration des Worker Nodes:
1. Connectez-vous à chaque worker node (2ème et 3ème instances).
2. Passez en superutilisateur avec `sudo -s`.
3. Exécutez la commande `docker swarm join` que vous avez copiée précédemment sur chaque worker node.

### Installation de l'Outil de Visualisation:
1. Revenez au nœud master.
2. Exécutez la commande suivante pour déployer l'outil de visualisation `viz`:
   ```
   docker service create --name=viz --publish=8080:8080/tcp --constraint=node.role==manager --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock dockersamples/visualizer
   ```

### Vérification de l'État du Swarm:
1. Utilisez la commande suivante pour vérifier l'état du swarm et voir tous les nœuds connectés:
   ```
   docker node ls
   ```

### Déploiement de Services:
1. Pour déployer un service nginx et le visualiser, exécutez:
   ```
   docker service create --name nginx --replicas 4 --publish 80:80 -d nginx
   ```

2. Vérifiez le service avec `docker service ls` et `docker service ps nginx` pour voir où les répliques sont exécutées.

### Visualisation du Swarm:
1. Ouvrez un navigateur et accédez à `http://<IP_PUBLIQUE_DU_MANAGER>:8080` pour voir l'outil de visualisation.

### Mise à l'Échelle des Services:
1. Pour mettre à l'échelle le service nginx, utilisez:
   ```
   docker service scale nginx=<NOUVEAU_NOMBRE_DE_RÉPLIQUES>
   ```

2. Vérifiez les changements dans le visualiseur à l'adresse `http://<IP_PUBLIQUE_DU_MANAGER>:8080`.


### Visualisation

L'outil de visualisation, souvent appelé `viz`, est un outil graphique qui permet de visualiser les nœuds d'un Docker Swarm et les conteneurs en cours d'exécution sur chacun d'eux. Pour déployer cet outil sur le nœud manager dans votre cluster Docker Swarm sur AWS, suivez les étapes ci-dessous :

### Déploiement de l'Outil de Visualisation sur le Nœud Manager:

1. **Connexion au Nœud Manager:**
   Connectez-vous à votre instance master (le nœud manager du swarm) via SSH.

2. **Déploiement de Viz:**
   Sur le nœud manager, exécutez la commande suivante pour lancer l'outil de visualisation :

   ```sh
   docker service create \
     --name=viz \
     --publish=8080:8080/tcp \
     --constraint=node.role==manager \
     --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
     dockersamples/visualizer
   ```

   Cette commande va créer un service dans le swarm appelé `viz` qui :
   - Est publié sur le port 8080 de l'hôte.
   - Est contraint de s'exécuter uniquement sur les nœuds ayant le rôle de manager.
   - Montre le socket Docker pour permettre à `viz` de communiquer avec le daemon Docker.

3. **Vérification:**
   Vérifiez que le service est correctement lancé en exécutant :

   ```sh
   docker service ls
   ```

   Vous devriez voir le service `viz` dans la liste avec le nombre de réplicas souhaités.

4. **Accès à l'Outil de Visualisation:**
   Ouvrez un navigateur web et accédez à l'adresse IP publique de votre instance manager sur AWS suivie de `:8080`. Par exemple, si votre IP publique est `65.2.150.126`, vous accéderiez à :

   ```
   http://65.2.150.126:8080
   ```

   Vous devriez voir l'interface graphique de l'outil de visualisation montrant la disposition de votre swarm.

### Notes:

- Assurez-vous que le port 8080 est ouvert dans les règles de groupe de sécurité de votre instance EC2 pour pouvoir accéder à l'outil de visualisation depuis votre navigateur.
- Si vous rencontrez des problèmes pour visualiser le Swarm, vérifiez que le daemon Docker est bien en fonctionnement et que le nœud est en mode manager (`docker node ls` pour vérifier le rôle du nœud).
- L'outil de visualisation est un conteneur qui doit être en cours d'exécution pour que vous puissiez voir l'état actuel de votre Swarm. Si vous le supprimez, vous ne pourrez plus accéder à l'interface de visualisation.

Avec ces étapes, vous devriez pouvoir installer et accéder à l'outil de visualisation sur votre nœud manager Docker Swarm sur AWS.

### Nettoyage:
1. Pour supprimer un service, exécutez:
   ```
   docker service rm <NOM_DU_SERVICE>
   ```
2. Pour quitter un swarm sur un nœud worker, utilisez:
   ```
   docker swarm leave
   ```
3. Sur le nœud master, utilisez `docker swarm leave --force` pour forcer le master à quitter le swarm.

4. Vérifiez que tous les services sont supprimés et que le visualiseur n'est plus accessible.

### Remarques:
- Assurez-vous que les instances EC2 ont des règles de groupe de sécurité appropriées pour permettre la communication entre les nœuds du swarm.
- Utilisez `exit` ou `logout` pour quitter les sessions SSH.

