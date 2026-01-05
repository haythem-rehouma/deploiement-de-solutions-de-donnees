# Docker, Swarm et Nginx - Guide de Formation

## Structure du cours

Ce cours est divisé en 10 modules progressifs. Chaque module contient des exercices pratiques toutes les 15 minutes pour permettre des pauses pédagogiques.

---

## Plan de formation

### Partie 1 : Fondamentaux Docker (Modules 1-4)

| Module | Fichier | Durée | Contenu |
|--------|---------|-------|---------|
| 1 | `1-docker-images-containers.md` | 45 min | Images, containers, cycle de vie |
| 2 | `2-dockerfile.md` | 45 min | Dockerfile, multi-stage build |
| 3 | `3-volumes.md` | 45 min | Volumes, bind mounts, persistance |
| 4 | `4-reseaux.md` | 45 min | Réseaux Docker, isolation |

### Partie 2 : Docker Swarm (Modules 5-7)

| Module | Fichier | Durée | Contenu |
|--------|---------|-------|---------|
| 5 | `5-docker-swarm-introduction.md` | 45 min | Architecture Swarm, noeuds |
| 6 | `6-services-stacks.md` | 45 min | Services, stacks, secrets |
| 7 | `7-scaling.md` | 45 min | Scaling, haute disponibilité |

### Partie 3 : Nginx (Modules 8-10)

| Module | Fichier | Durée | Contenu |
|--------|---------|-------|---------|
| 8 | `8-nginx-introduction.md` | 45 min | Configuration de base |
| 9 | `9-nginx-reverse-proxy.md` | 45 min | Reverse proxy, routage |
| 10 | `10-nginx-load-balancer-securite.md` | 45 min | Load balancing, sécurité |

---

## Structure de chaque module

```
Module X
    |-- Objectifs du module
    |-- Section 1 (théorie + diagramme Mermaid)
    |-- Section 2 (théorie + diagramme Mermaid)
    |-- EXERCICE 1 (15 minutes) <-- Pause enseignant
    |-- Section 3
    |-- Section 4
    |-- EXERCICE 2 (15 minutes) <-- Pause enseignant
    |-- Section 5
    |-- Section 6
    |-- EXERCICE 3 (15 minutes) <-- Pause enseignant
    |-- Résumé (mindmap Mermaid)
    |-- Quiz de validation
```

---

## Diagrammes Mermaid

Tous les modules contiennent des diagrammes Mermaid pour illustrer les concepts :

- Architecture système
- Flux de données
- Cycles de vie
- Mindmaps de résumé

Pour visualiser les diagrammes :
- VS Code avec extension Markdown Preview Mermaid
- GitHub (support natif)
- Mermaid Live Editor : https://mermaid.live

---

## Prérequis

### Logiciels requis

```bash
# Docker Desktop ou Docker Engine
docker --version

# Docker Compose (inclus dans Docker Desktop)
docker compose version
```

### Vérification de l'environnement

```bash
# Vérifier Docker
docker run hello-world

# Vérifier les ressources
docker info
```

---

## Conseils pour l'enseignant

### Gestion du temps

- Chaque module = ~45 minutes
- 3 exercices par module = 3 pauses de 15 minutes
- Prévoir du temps supplémentaire pour les questions

### Utilisation des exercices

Les exercices sont conçus pour :
1. Permettre à l'enseignant de se reposer
2. Valider la compréhension des concepts
3. Préparer les questions des étudiants

### Nettoyage entre les modules

```bash
# Supprimer tous les containers
docker rm -f $(docker ps -aq)

# Supprimer les réseaux non utilisés
docker network prune -f

# Supprimer les volumes non utilisés
docker volume prune -f

# Nettoyage complet
docker system prune -af
```

---

## Ressources supplémentaires

### Documentation officielle

- Docker : https://docs.docker.com
- Docker Swarm : https://docs.docker.com/engine/swarm/
- Nginx : https://nginx.org/en/docs/

### Outils utiles

- Portainer : Interface web pour Docker
- Lazydocker : TUI pour Docker
- ctop : Monitoring containers

---

## Licence

Ce matériel de cours est destiné à un usage éducatif.
