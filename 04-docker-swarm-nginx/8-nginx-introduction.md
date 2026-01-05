# Module 8 : Nginx - Introduction et Configuration de Base

## Objectifs du module

- Comprendre le rôle et l'architecture de Nginx
- Maîtriser la structure du fichier de configuration
- Configurer des serveurs virtuels
- Servir du contenu statique

---

## 8.1 Qu'est-ce que Nginx ?

### Un peu d'histoire

**Nginx** (prononcé "engine-x") a été créé en 2004 par Igor Sysoev, un développeur russe. À l'époque, Apache dominait le marché des serveurs web mais avait du mal à gérer beaucoup de connexions simultanées (le problème "C10K" : 10 000 connexions).

Nginx a été conçu dès le départ pour gérer **des milliers de connexions simultanées** avec une faible consommation de mémoire.

### Définition simple

**Nginx** est un logiciel qui peut jouer plusieurs rôles :
- **Serveur web** : Sert des fichiers (HTML, images, CSS, JS) aux visiteurs
- **Reverse proxy** : Relaie les requêtes vers d'autres serveurs (comme vu au Module 9)
- **Load balancer** : Distribue le trafic entre plusieurs serveurs
- **Cache HTTP** : Stocke les réponses pour les servir plus vite

### Pourquoi Nginx est si populaire ?

| Caractéristique | Explication |
|-----------------|-------------|
| **Performance** | Gère facilement 10 000+ connexions simultanées |
| **Faible mémoire** | Consomme peu de RAM même sous forte charge |
| **Architecture événementielle** | N'utilise pas un processus par connexion (contrairement à Apache) |
| **Flexibilité** | Configuration simple mais puissante |
| **Stabilité** | Très fiable, utilisé par les plus grands sites (Netflix, Airbnb, etc.) |

### Nginx vs Apache

| Aspect | Nginx | Apache |
|--------|-------|--------|
| Architecture | Événementielle (async) | Processus/threads |
| Mémoire | Très faible | Plus élevée |
| Fichiers statiques | Excellent | Bon |
| .htaccess | Non supporté | Supporté |
| Configuration | Centralisée | Distribuée possible |

```mermaid
graph TB
    subgraph "Rôles de Nginx"
        WEB[Serveur Web]
        RP[Reverse Proxy]
        LB[Load Balancer]
        CACHE[Cache HTTP]
        SSL[Terminaison SSL]
    end
    
    CLIENT[Clients] --> NGINX[Nginx]
    NGINX --> WEB
    NGINX --> RP
    NGINX --> LB
    NGINX --> CACHE
    NGINX --> SSL
```

### Avantages

| Caractéristique | Description |
|-----------------|-------------|
| Performance | Architecture événementielle, non-bloquante |
| Mémoire | Faible empreinte mémoire |
| Concurrence | Milliers de connexions simultanées |
| Flexibilité | Nombreux modules et configurations |
| Stabilité | Très stable en production |

---

## 8.2 Architecture de Nginx

### Le modèle Master/Worker

Nginx utilise une architecture à deux niveaux :

1. **Un processus Master** (le chef)
2. **Plusieurs processus Workers** (les ouvriers)

```mermaid
graph TB
    subgraph "Architecture Nginx"
        MASTER[Master Process<br/>PID 1<br/>Lit la config<br/>Gère les workers]
        
        W1[Worker 1]
        W2[Worker 2]
        W3[Worker 3]
        W4[Worker N]
        
        MASTER --> W1
        MASTER --> W2
        MASTER --> W3
        MASTER --> W4
    end
    
    CLIENTS[Clients] --> W1
    CLIENTS --> W2
    CLIENTS --> W3
    CLIENTS --> W4
```

### Rôle de chaque processus

**Processus Master :**
- Lit et valide le fichier de configuration
- Crée et gère les processus workers
- Gère les signaux (reload, stop, etc.)
- Ne traite **jamais** de requêtes directement

**Processus Workers :**
- Traitent les requêtes des clients
- Chaque worker peut gérer des milliers de connexions simultanées
- Fonctionnent de manière indépendante (pas de communication entre eux)

### Pourquoi cette architecture ?

**Avantage 1 : Rechargement sans interruption**
Quand vous modifiez la configuration et faites `nginx -s reload` :
1. Le master lit la nouvelle config
2. Le master crée de nouveaux workers avec la nouvelle config
3. Les anciens workers terminent leurs requêtes en cours
4. Les anciens workers s'arrêtent
→ **Aucune requête n'est perdue !**

**Avantage 2 : Stabilité**
Si un worker plante, le master en crée un nouveau. Les autres workers continuent de fonctionner.

### Combien de workers ?

Règle simple : **1 worker par coeur CPU**

```nginx
# Automatique (recommandé)
worker_processes auto;

# Manuel
worker_processes 4;  # Pour un serveur 4 coeurs
```

---

## 8.3 Structure de configuration

### Organisation des fichiers

Nginx utilise des fichiers texte pour sa configuration. Comprendre leur organisation est essentiel.

### Fichiers principaux

```
/etc/nginx/
    nginx.conf              # Configuration principale
    conf.d/                 # Configurations additionnelles
        default.conf
        mysite.conf
    sites-available/        # Sites disponibles (Debian/Ubuntu)
    sites-enabled/          # Sites activés (liens symboliques)
    mime.types              # Types MIME
```

### Les contextes (blocs) de configuration

Nginx organise sa configuration en **contextes** imbriqués (comme des poupées russes). Chaque contexte a un rôle spécifique.

### Structure de nginx.conf

```nginx
# Contexte global (main)
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

# Contexte events
events {
    worker_connections 1024;
    multi_accept on;
}

# Contexte http
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    
    # Logging
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent"';
    access_log /var/log/nginx/access.log main;
    
    # Performance
    sendfile on;
    tcp_nopush on;
    keepalive_timeout 65;
    gzip on;
    
    # Inclusion des configurations de sites
    include /etc/nginx/conf.d/*.conf;
}
```

```mermaid
graph TB
    subgraph "Hiérarchie des contextes"
        MAIN[main<br/>Configuration globale]
        EVENTS[events<br/>Connexions]
        HTTP[http<br/>Configuration HTTP]
        SERVER[server<br/>Serveur virtuel]
        LOCATION[location<br/>Chemin URL]
        
        MAIN --> EVENTS
        MAIN --> HTTP
        HTTP --> SERVER
        SERVER --> LOCATION
    end
```

### Explication de chaque contexte

| Contexte | Rôle | Exemple de directives |
|----------|------|----------------------|
| **main** | Configuration globale du processus | `worker_processes`, `user`, `pid` |
| **events** | Gestion des connexions | `worker_connections`, `multi_accept` |
| **http** | Configuration HTTP globale | `include`, `log_format`, `gzip` |
| **server** | Un site/domaine virtuel | `listen`, `server_name`, `root` |
| **location** | Un chemin URL spécifique | `proxy_pass`, `try_files`, `alias` |

### Règle d'héritage

Les directives définies dans un contexte parent sont héritées par les contextes enfants, sauf si elles sont redéfinies.

---

## Exercice 1 (15 minutes)

### Explorer la configuration par défaut

1. Lancez un container Nginx :
   ```bash
   docker run -d --name nginx-explore -p 8080:80 nginx:alpine
   ```

2. Explorez la configuration :
   ```bash
   # Configuration principale
   docker exec nginx-explore cat /etc/nginx/nginx.conf
   
   # Configuration du site par défaut
   docker exec nginx-explore cat /etc/nginx/conf.d/default.conf
   
   # Types MIME
   docker exec nginx-explore head -50 /etc/nginx/mime.types
   ```

3. Testez le serveur :
   ```bash
   curl http://localhost:8080
   ```

4. Consultez les logs :
   ```bash
   docker logs nginx-explore
   ```

5. Nettoyez :
   ```bash
   docker rm -f nginx-explore
   ```

---

## 8.4 Configuration d'un serveur virtuel

### Qu'est-ce qu'un serveur virtuel ?

Un **serveur virtuel** (virtual host) permet d'héberger plusieurs sites web sur une seule instance Nginx. Chaque site a son propre bloc `server`.

**Exemple :** Une seule machine peut héberger :
- www.site1.com (bloc server 1)
- www.site2.com (bloc server 2)
- api.site1.com (bloc server 3)

Nginx détermine quel bloc utiliser grâce au nom de domaine demandé (`Host` header).

### Server block basique

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    
    root /var/www/html;
    index index.html index.htm;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

### Directives importantes

```nginx
server {
    # Port d'écoute
    listen 80;
    listen 443 ssl;
    listen [::]:80;  # IPv6
    
    # Noms de domaine
    server_name example.com *.example.com;
    
    # Racine du site
    root /var/www/html;
    
    # Fichiers index
    index index.html index.php;
    
    # Logs spécifiques
    access_log /var/log/nginx/example.access.log;
    error_log /var/log/nginx/example.error.log;
}
```

---

## 8.5 Directives location

### À quoi sert location ?

La directive `location` permet de définir des comportements différents selon l'URL demandée. C'est comme un aiguillage : selon le chemin, Nginx fait des choses différentes.

**Exemples d'utilisation :**
- `/` → Servir le site principal
- `/api/` → Rediriger vers le backend
- `/images/` → Servir des fichiers statiques avec cache
- `/admin/` → Ajouter une authentification

### Types de correspondance

Nginx propose plusieurs façons de matcher une URL :

```nginx
# Correspondance exacte (= )
location = /favicon.ico {
    log_not_found off;
}
# Match: /favicon.ico
# No match: /favicon.ico?v=1, /path/favicon.ico

# PRÉFIXE simple - URL commence par...
location /images/ {
    root /var/www/static;
}
# Match: /images/, /images/photo.jpg, /images/2024/vacation.png

# REGEX case sensitive (~ ) - Expression régulière
location ~ \.php$ {
    fastcgi_pass php:9000;
}
# Match: /index.php, /admin/config.php
# No match: /index.PHP (majuscules)

# REGEX case insensitive (~* ) - Ignore majuscules/minuscules
location ~* \.(jpg|jpeg|png|gif)$ {
    expires 30d;
}
# Match: /photo.jpg, /photo.JPG, /photo.Jpg

# PRÉFIXE PRIORITAIRE (^~ ) - Préfixe qui bat les regex
location ^~ /static/ {
    root /var/www;
}
# Utilisé même si une regex pourrait matcher
```

### Ordre de priorité (IMPORTANT !)

Nginx évalue les locations dans cet ordre précis :

1. **`=`** (exacte) - Si match, utilise immédiatement
2. **`^~`** (préfixe prioritaire) - Si match, utilise immédiatement
3. **`~` ou `~*`** (regex) - Première regex qui match
4. **Préfixe normal** - Le plus long préfixe qui match

```mermaid
graph TB
    REQ[Requête URL]
    
    REQ --> EXACT["1. = exacte ?"]
    EXACT --> |Match| STOP1[Utiliser]
    EXACT --> |No match| PREFIX["2. ^~ préfixe prioritaire ?"]
    
    PREFIX --> |Match| STOP2[Utiliser]
    PREFIX --> |No match| REGEX["3. ~ ou ~* regex ?"]
    
    REGEX --> |Match| STOP3[Utiliser]
    REGEX --> |No match| NORMAL["4. Préfixe normal le plus long"]
    
    NORMAL --> STOP4[Utiliser]
```

### Conseil pratique

Pour éviter les confusions, utilisez principalement :
- `location /chemin/` pour les préfixes
- `location = /chemin` pour les correspondances exactes
- `location ~* \.(ext)$` pour les extensions de fichiers

---

## Exercice 2 (15 minutes)

### Configurer un site personnalisé

1. Créez un dossier de travail :
   ```bash
   mkdir -p ~/nginx-demo/html
   cd ~/nginx-demo
   ```

2. Créez du contenu HTML :
   ```bash
   cat > html/index.html << 'EOF'
   <!DOCTYPE html>
   <html>
   <head><title>Mon Site</title></head>
   <body>
   <h1>Bienvenue sur mon site Nginx</h1>
   <p>Configuration personnalisée</p>
   </body>
   </html>
   EOF
   
   mkdir html/images
   echo "Image placeholder" > html/images/test.txt
   ```

3. Créez la configuration :
   ```bash
   cat > nginx.conf << 'EOF'
   events {
       worker_connections 1024;
   }
   
   http {
       include /etc/nginx/mime.types;
       
       server {
           listen 80;
           server_name localhost;
           
           root /usr/share/nginx/html;
           index index.html;
           
           location / {
               try_files $uri $uri/ =404;
           }
           
           location /images/ {
               autoindex on;
           }
       }
   }
   EOF
   ```

4. Lancez le container :
   ```bash
   docker run -d --name custom-nginx \
       -p 8080:80 \
       -v $(pwd)/nginx.conf:/etc/nginx/nginx.conf:ro \
       -v $(pwd)/html:/usr/share/nginx/html:ro \
       nginx:alpine
   ```

5. Testez :
   ```bash
   curl http://localhost:8080
   curl http://localhost:8080/images/
   ```

6. Nettoyez :
   ```bash
   docker rm -f custom-nginx
   cd ~
   rm -rf ~/nginx-demo
   ```

---

## 8.6 Variables Nginx

### Qu'est-ce qu'une variable Nginx ?

Les variables Nginx sont des valeurs dynamiques que vous pouvez utiliser dans la configuration. Elles commencent par `$`.

**Utilité :**
- Accéder aux informations de la requête (IP, URL, headers)
- Créer des configurations dynamiques
- Faire du logging personnalisé

### Variables intégrées

```nginx
server {
    # Variables de requête
    # $request_uri    - URI complète avec query string
    # $uri            - URI normalisé
    # $args           - Query string
    # $request_method - GET, POST, etc.
    
    # Variables client
    # $remote_addr    - IP du client
    # $http_user_agent - User-Agent
    # $http_referer   - Referer
    
    # Variables serveur
    # $host           - Nom d'hôte demandé
    # $server_name    - Nom du serveur
    # $server_port    - Port du serveur
    
    location /debug {
        return 200 "IP: $remote_addr\nHost: $host\nURI: $uri\n";
        add_header Content-Type text/plain;
    }
}
```

### Variables personnalisées

```nginx
http {
    # Définition de variable
    map $http_user_agent $is_mobile {
        default 0;
        ~*mobile 1;
    }
    
    server {
        location / {
            if ($is_mobile) {
                return 302 /mobile/;
            }
        }
    }
}
```

---

## 8.7 Gestion des erreurs

### Pourquoi personnaliser les pages d'erreur ?

Les pages d'erreur par défaut de Nginx sont minimalistes et techniques. Des pages personnalisées permettent de :
- Garder votre identité visuelle
- Aider l'utilisateur (liens vers l'accueil, recherche)
- Éviter de révéler des informations techniques (version Nginx)

### Configuration des pages d'erreur

```nginx
server {
    listen 80;
    server_name example.com;
    
    # Pages d'erreur personnalisées
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    
    location = /404.html {
        root /var/www/errors;
        internal;
    }
    
    location = /50x.html {
        root /var/www/errors;
        internal;
    }
}
```

---

## Exercice 3 (15 minutes)

### Pages d'erreur personnalisées

1. Créez la structure :
   ```bash
   mkdir -p ~/nginx-errors/{html,errors}
   cd ~/nginx-errors
   ```

2. Créez les pages :
   ```bash
   cat > html/index.html << 'EOF'
   <!DOCTYPE html>
   <html><body><h1>Page principale</h1></body></html>
   EOF
   
   cat > errors/404.html << 'EOF'
   <!DOCTYPE html>
   <html><body><h1>404 - Page non trouvée</h1><p>Désolé!</p></body></html>
   EOF
   
   cat > errors/50x.html << 'EOF'
   <!DOCTYPE html>
   <html><body><h1>Erreur serveur</h1><p>Veuillez réessayer.</p></body></html>
   EOF
   ```

3. Configuration :
   ```bash
   cat > nginx.conf << 'EOF'
   events { worker_connections 1024; }
   
   http {
       server {
           listen 80;
           root /usr/share/nginx/html;
           
           error_page 404 /404.html;
           error_page 500 502 503 504 /50x.html;
           
           location = /404.html {
               root /var/www/errors;
               internal;
           }
           
           location = /50x.html {
               root /var/www/errors;
               internal;
           }
       }
   }
   EOF
   ```

4. Lancez et testez :
   ```bash
   docker run -d --name error-nginx \
       -p 8080:80 \
       -v $(pwd)/nginx.conf:/etc/nginx/nginx.conf:ro \
       -v $(pwd)/html:/usr/share/nginx/html:ro \
       -v $(pwd)/errors:/var/www/errors:ro \
       nginx:alpine
   
   curl http://localhost:8080
   curl http://localhost:8080/page-inexistante
   ```

5. Nettoyez :
   ```bash
   docker rm -f error-nginx
   cd ~ && rm -rf ~/nginx-errors
   ```

---

## Résumé du module

```mermaid
mindmap
  root((Nginx Basics))
    Architecture
      Master process
      Worker processes
      Event-driven
    Configuration
      nginx.conf
      Contextes
        main
        events
        http
        server
        location
    Directives
      listen
      server_name
      root
      location
      try_files
    Fonctionnalités
      Serveur web
      Contenu statique
      Pages d'erreur
      Variables
```

---

## Quiz de validation

1. Quel est le rôle du processus master de Nginx ?
2. Dans quel ordre sont évaluées les directives location ?
3. Quelle est la différence entre `root` et `alias` ?
4. Comment servir une page d'erreur 404 personnalisée ?
5. Quelle variable contient l'adresse IP du client ?
