---
title: "Chapitre 12 - démonstration détaillée de NodePort (pratique9)"
description: "Projet final qui combine tous les concepts appris pour déployer automatiquement une infrastructure web complète avec base de données"
emoji: "🔧"
---



# Chapitre 12 - démonstration détaillée de NodePort (pratique9)



<a name="table-des-matières"></a>
## Table des matières
1. [Rappel de ce que c'est un Service Kubernete](#rappel-de-ce-que-c'est-un-service-kubernete)
2. [Réseau interne et externe dans Kubernetes](#réseau-interne-et-externe-dans-kubernetes)
3. [Implémentation d'un Service NodePort](#implementation-d'un-service-nodeport)
   - [3.1. Ports impliqués](#ports-impliques)
   - [3.2. Création du service](#creation-du-service)
   - [3.3. Définition des ports](#definition-des-ports)
   - [3.4. Labels et sélecteurs](#labeles-et-selecteurs)
   - [3.5. Accès au Service](#acces-au-service)
4. [Résumé de la démonstration](#resume-de-la-demonstration)



<a name="rappel-de-ce-que-c'est-un-service-kubernete"></a>
---
# 1 - Rappel de ce que c'est un Service Kubernete
---

- Dans cette partie, nous allons discuter des services Kubernetes.
> 1. Les services Kubernetes permettent la communication entre les différentes composantes de l'application, qu'elles soient internes ou externes.
> 2. Les services Kubernetes nous aident à connecter des applications entre elles ou avec des utilisateurs.
> 3. Par exemple, notre application pourrait avoir plusieurs groupes de composants exécutant différentes sections : un groupe pour servir une interface utilisateur, un autre groupe pour les processus backend, et un troisième groupe pour se connecter à une source de données externe.
> 4. Ce sont les services qui permettent la connectivité entre ces groupes de composants.
> 5. Les services rendent l'application frontale accessible aux utilisateurs finaux, facilitent la communication entre les composants backend et frontend, et permettent la connexion à une source de données externe.
> 6. Ainsi, les services permettent un couplage lâche entre les microservices de notre application.




<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service1.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "1200px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />

  <p style={{textAlign: "center"}}><em>Figure 1 : Les Services assurent la connexion entre les différents composants d'une application, notamment entre les utilisateurs et le frontend, entre le frontend et le backend, ainsi qu'entre le backend et la base de données</em></p>
</div>

Cette figure montre comment les services jouent un rôle central dans une architecture informatique. Les utilisateurs (en haut) accèdent aux fonctionnalités via des services qui agissent comme des "ponts" pour se connecter aux différentes ressources, comme des applications ou des bases de données.

> Chaque service est un **élément indépendant**, capable de fonctionner de manière autonome tout en échangeant des informations avec d'autres services si nécessaire. Cela signifie que chaque service peut regrouper et organiser des actions spécifiques, comme gérer des données ou exécuter des tâches précises, sans dépendre directement des autres. Cette indépendance permet de rendre le système plus structuré, évolutif et facile à gérer. Ainsi, même si un service tombe en panne ou est mis à jour, le reste de l'architecture continue de fonctionner normalement. Cela garantit une meilleure fiabilité et une réponse rapide aux besoins des utilisateurs.



> Les services constituent un élément fondamental et critique de toute architecture Kubernetes moderne. Ils agissent comme une couche d'abstraction essentielle qui :

- Fournit une interface stable et persistante pour accéder aux pods
- Gère automatiquement la découverte et le routage du trafic
- Assure l'équilibrage de charge entre les pods
- Permet une communication fiable entre les différents composants


> Les services Kubernetes remplissent plusieurs rôles stratégiques :

1. **Abstraction du Réseau** : Masquent la complexité des pods éphémères
2. **Load Balancing** : Distribuent le trafic de manière optimale
3. **Service Discovery** : Facilitent la découverte automatique des ressources
4. **Exposition Externe** : Permettent l'accès depuis l'extérieur du cluster


### 🔙 [Retour à la table des matires](#table-des-matières)

<a name="cas-concret-d'utilisation-des-services-nodeport-problème-de-communication-externe-et-solution"></a>
---
# 2 - Cas concret d'utilisation des Services (NodePort) : Problème de communication externe et solution
---
- Voyons un cas concret de l'utilisation des services.
- Jusqu'à présent, nous avons parlé de la communication entre les pods via le réseau interne.
- Examinons maintenant d'autres aspects du réseau dans cette leçon.

### *2.1 - Communication Externe et Configuration Existante*

> Imaginons que nous ayons déployé notre pod avec une application web qui y tourne. Comment, en tant qu'utilisateur externe, pouvons-nous accéder à cette page web ?



- Le nœud Kubernetes a une adresse IP, par exemple 192.168.1.2. Mon ordinateur portable est sur le même réseau avec l'adresse IP 192.168.1.10. Le réseau interne des pods est dans la plage 10.240.0.0, et le pod a une adresse IP 10.240.0.2.

- Il est clair que je ne peux pas pinger ou accéder directement au pod à l'adresse 10.240.0.2 car il se trouve sur un réseau séparé.



<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service2.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "1200px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />

  <p style={{
    animation: "slideIn 1s ease-in-out, fadeIn 2s ease-in",
    transform: "translateY(20px)"
  }}><em>Figure 2: Illustration d'un Service Kubernetes gérant la communication entre les Pods et le trafic externe</em></p>
</div>

<style>
{`
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }
  
  @keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.9; }
    100% { opacity: 1; }
  }
`}
</style>




### *2.2 - Solution*

*<u style={{color: "red"}}>2.2.1 - Options pour Voir la Page Web</u>*

- Premièrement, si nous nous connectons au nœud Kubernetes à l'adresse 192.168.1.2, depuis ce nœud, nous pourrions accéder à la page web du pod en utilisant une commande curl ou en ouvrant un navigateur et en accédant à l'adresse http://10.240.0.2.

- Cependant, cela se fait depuis l'intérieur du nœud Kubernetes, et ce n'est pas ce que nous voulons. Nous souhaitons accéder au serveur web depuis notre propre ordinateur sans avoir à nous connecter au nœud, simplement en accédant à l'IP du nœud Kubernetes.

- Nous avons donc besoin de quelque chose pour mapper les requêtes de notre ordinateur au nœud, puis du nœud au pod exécutant le conteneur web. C'est là qu'interviennent les services Kubernetes.



*<u style={{color: "red"}}>2.2.2 - Explication du fonctionnement du Service NodePort : Réseaux interne et externe</u>*


<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service16-17.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "1200px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />

  <p style={{
    animation: "slideIn 1s ease-in-out, fadeIn 2s ease-in",
    transform: "translateY(20px)"
  }}><em>Figure 3: Vues détaillées d'un Service Kubernete NodePort et ses interactions</em></p>
</div>

Cette illustration montre clairement que l'accès à une application Kubernetes via un **Service NodePort** implique l'utilisation de **deux réseaux distincts** : un réseau externe et un réseau interne


*<u style={{color: "#FF6B6B"}}>a. Réseau externe (côté utilisateur)</u>*

> - **Adresse IP externe du Node** : `192.168.1.2`
> - **Adresse IP de l'utilisateur** : `192.168.1.10`
> - L'utilisateur envoie une requête HTTP à l'adresse IP **externe** du Node (`192.168.1.2`) sur le **port exposé** (`30008`). Cette IP est visible sur le réseau auquel appartient le Node et permet aux clients de l'extérieur du cluster de joindre un service.



*<u style={{color: "#FF6B6B"}}>b. Réseau interne (côté cluster Kubernetes)</u>*

> - **Adresse IP interne du Pod** : `10.244.0.2`
> - **Adresse du sous-réseau Kubernetes** : `10.244.0.0/16` (exemple de plage IP interne assignée aux Pods)

>À l'intérieur du cluster, chaque Pod possède une adresse IP privée unique issue d'un réseau interne dédié (`10.x.x.x`). Cette adresse IP est **invisible depuis l'extérieur** du cluster. Le Pod communique avec d'autres ressources internes via ce réseau interne.



*<u style={{color: "#FF6B6B"}}>c. Comment fonctionne la communication entre les réseaux ?</u>*

> 1. L'utilisateur envoie une requête à l'IP **externe** du Node (`192.168.1.2`) sur le port `30008` :
>    ```bash
>    curl http://192.168.1.2:30008
>    ```
> 2. Le service **NodePort** reçoit cette requête sur le port et la redirige vers l'IP **interne** du Pod (`10.244.0.2`).
> 3. Le Pod traite la requête et répond à l'utilisateur via le réseau externe.



*<u style={{color: "#FF6B6B"}}>d. Pourquoi deux réseaux ?</u>*

> - **Réseau interne (10.x.x.x)** : Ce réseau est dédié aux communications internes des Pods et ne doit pas être accessible directement depuis l'extérieur pour des raisons de sécurité.
> - **Réseau externe (192.168.x.x)** : Ce réseau permet l'interconnexion entre les clients externes et les services du cluster.



*<u style={{color: "#FF6B6B"}}>e. Exemple pratique</u>*

> - Requête envoyée via le réseau externe :
>    ```bash
>    curl http://192.168.1.2:30008
>    ```
> - Redirection interne vers le Pod `10.244.0.2` qui renvoie :
>    ```
>    Hello World!
>    ```

Nous avons trouvé une solution pour accéder à notre application web depuis l'extérieur du cluster Kubernetes grâce au Service de type **NodePort**. Le Service **NodePort** agit comme un "pont" entre le réseau externe du Node et le réseau interne du cluster Kubernetes. Cette distinction des réseaux permet de protéger l'accès direct aux Pods tout en offrant un point d'entrée pour les utilisateurs extérieurs. Le service assure ainsi une communication fluide sans exposer directement les adresses IP internes des Pods.



### 🔙 [Retour à la table des matires](#table-des-matières)

<a name="implementation-d'un-service-nodeport"></a>
---
# 3 - Implémentation d'un Service NodePort
---

Revenons à NodePort, nous avons discuté de l'accès externe à l'application. Nous avons dit qu'un service peut nous aider en mappant un port sur le nœud à un port sur le pod.

<a name="ports-impliques"></a>
### *3.1. Ports impliqués :*

Regardons de plus près le service. Il y a trois ports impliqués :

> 1. Le port sur le pod où le serveur web s'exécute, appelé port cible (target port), généralement 80.
> 2. Le port sur le service lui-même, simplement appelé port.
> 3. Le port sur le nœud, appelé NodePort, par exemple 30008.


<a name="creation-du-service"></a>
### *3.2. Création du service :*

Nous créons un service en utilisant un fichier de définition, comme nous l'avons fait pour les déploiements, les réplicasets ou les pods. La structure du fichier reste la même : API version, kind, metadata et spec.

    > - La version de l'API est v1.
    > - Le kind est Service.
    > - Les metadata incluent le nom du service.
    > - La section spec est cruciale : on y définit les ports et le type de service (NodePort).


<a name="definition-des-ports"></a>
### *3.3. Définition des ports :*

Sous ports, nous définissons :
> - Le port cible (target port), 80.
    > - Le port du service, 80.
    > - Le NodePort, par exemple 30008.


<a name="labeles-et-selecteurs"></a>
### *3.4.Labeles et sélecteurs :*

> - Il manque encore une chose : connecter le service au pod. Nous utilisons des labels et des sélecteurs pour cela. Nous ajoutons les labels du pod dans la section selector du fichier de définition du service.

Une fois cela fait, nous créons le service avec la commande `kubectl create` et vérifions avec `kubectl get services`.


<a name="acces-au-service"></a>
### *3.5. Accès au Service :*

> - Nous pouvons maintenant accéder au service web en utilisant curl ou un navigateur avec l'adresse IP du nœud et le NodePort.

Pour résumer, que ce soit un pod unique, plusieurs pods sur un même nœud, ou plusieurs pods sur plusieurs nœuds, le service est créé de la même manière sans étapes supplémentaires.

### 🔙 [Retour à la table des matières](#table-des-matières)

<a name="resume-de-la-demonstration"></a>
---
# 4 - Résumé de la démonstration
---

1. Nous allons implémenter un service NodePort dans Kubernetes. Exminons d'abord la structure de notre projet.

```bash
.
├── Deployment/
│   └── deployment-definition.yaml
└── Service/
    └── service-definition.yaml
```

2. Nous allons créer quelques pods en créant un déploiement, alors vérifions d'abord le statut de ce déploiement. Nous avons un déploiement appelé "my-app-deployment" avec six réplicas, ce qui signifie essentiellement que six pods fonctionnent dans le cluster Kubernetes.

> Nous allons créer un déploiement dans le répertoire "Deployment".
> Créons le fichier de définition du déploiement : my-app-deployment.yaml


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 6
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

> Appliquons la commande `kubectl create -f Deployment/my-app-deployment.yaml` pour créer le déploiement.



```bash
kubectl get deployments
```
> Appliquons la commande `kubectl get deployments` pour vérifier le statut de ce déploiement.
> Vous remarquerez que le déploiement est créé et que les six pods sont en cours d'exécution.


3. Nous avons maintenant une application créée pour s'exécuter sur ce cluster. Cependant, pour que l'utilisateur final puisse y accéder via son navigateur web, nous devons créer un service.

4. Pour ce faire, retournons dans notre éditeur. Nous allons créer un nouveau répertoire nommé "Service". Dans ce répertoire "Service", nous créerons un nouveau fichier nommé "service-definition.yaml".


```bash
mkdir Service
cd Service
touch service-definition.yaml
```
> Créons le fichier de définition du service : service-definition.yaml au complet.
> Nous allons l'analyser partie par partie dans les prochaines étapes.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30004
  selector:
    app: my-app
```

5. Nous créons le fichier de définition du service. Comme précédemment, la première chose à ajouter est l'élément racine, la version de l'API. Pour un service, elle doit être définie à "v1". Pour le type (kind), nous allons spécifier "Service".

```yaml
apiVersion: v1
kind: Service
```
> Nous avons ajouté l'élément racine, la version de l'API. Pour un service, elle doit être définie à "v1". Pour le type (kind), nous allons spécifier "Service".

6. Ensuite, nous allons ajouter les métadonnées avec le nom du service que nous pouvons appeler "my-app-service".
/
```yaml
metadata:
  name: my-app-service
```
> Nous avons ajouté les métadonnées avec le nom du service que nous pouvons appeler "my-app-service".

7. Sous cela, nous allons ajouter la section des spécifications (spec). La première propriété que nous allons créer est le type de service, que nous allons définir comme "NodePort". 

```yaml
spec:
  type: NodePort
```
> Nous avons ajouté la section des spécifications (spec). La première propriété que nous allons créer est le type de service, que nous allons définir comme "NodePort".

8. Notre objectif est de pouvoir accéder à notre application sur un port du nœud, qui est le nœud Minikube dans notre cas.

9. Nous allons ensuite ajouter le port et le port par défaut sur lequel Nginx écoute, qui est 80. Nous allons également ajouter notre port cible (target port), qui est également le port 80. Il s'agit essentiellement du port sur le service lui-même.

```yaml
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
```
10. Nous avons ajouté le port et le port par défaut sur lequel Nginx écoute, qui est 80. Nous allons également ajouter notre port cible (target port), qui est également le port 80. Il s'agit essentiellement du port sur le service lui-même.



<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service5.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "1200px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />


  <p><em>Figure 4: Configuration des ports dans un service NodePort - port 80 pour le service, targetPort 80 pour le pod, et nodePort 30008 pour l'accès externe</em></p>
</div>

<style>
{`
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }
  
  @keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.9; }
    100% { opacity: 1; }
  }
`}
</style>


11. Ensuite, nous allons ajouter un NodePort que nous pouvons définir à une valeur telle que 30004 ou 30008. Cela pourrait être n'importe quelle valeur entre 30000 et 32767.

```yaml
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```


<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service6.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "1200px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />

  <p><em>Figure 5: Configuration détaillée des ports dans un service NodePort - Le port 80 est utilisé comme point d'entrée du service pour la communication interne au cluster, le targetPort 80 spécifie le port sur lequel le pod écoute les requêtes, et le nodePort 30008 expose le service à l'extérieur du cluster sur tous les nœuds workers. Le nodePort doit être compris entre 30000-32767 pour des raisons de sécurité et de convention Kubernetes.</em></p>
</div>

<style>
{`
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }
  
  @keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.9; }
    100% { opacity: 1; }
  }
`}
</style>

> NodePort est principalement utilisé pour le développement et les tests, comme avec Minikube, car il offre une façon simple d'exposer des services. Cependant, en production, il est rarement utilisé pour plusieurs raisons :
>
> 1. Limitations de sécurité : Il expose directement les nœuds workers sur Internet
> 2. Plage de ports restreinte (30000-32767)
> 3. Complexité de gestion dans un environnement multi-nœuds
>
> Avec Minikube, NodePort est très pratique car :
> - Il permet un accès rapide aux services depuis la machine locale
> - Parfait pour le développement et le débogage
> - Configuration simple avec une commande `minikube service`
>
> En production, on préfère généralement utiliser LoadBalancer ou Ingress Controller.




11. Ce NodePort est le port sur le nœud, le nœud de travail, qui est le nœud Minikube sur lequel l'application sera accessible.

```yaml
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
```



12. Nous vérifions le fichier de déploiement (ou le fichier de configuration de pod simplement comme illustré dans la figure ci-bas). Jetons rapidement un coup d'œil au fichier YAML de déploiement et vous remarquerez que l'étiquette pour le pod est "app" avec la valeur "my-app".


<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service7.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "1200px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />


  <p><em>Figure 6: Vue détaillée d'un Service Kubernetes et ses interactions</em></p>
</div>

<style>
{`
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }
  
  @keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.9; }
    100% { opacity: 1; }
  }
`}
</style>

13. Ensuite, nous allons ajouter un sélecteur (selector) qui nous aide à lier notre service au pod avec la même étiquette.

```yaml
spec:
  selector:
```

14. Ajoutons cette même valeur ici sous la section "selector".

```yaml
spec:
  selector:
    app: my-app
```



<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service11.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "1200px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />

  <p><em>Figure 7: Vue détaillée d'un Service Kubernetes illustrant le mécanisme de sélection - Le selector du Service permet de cibler les Pods ayant les labels correspondants, créant ainsi un lien dynamique entre le Service et les Pods qu'il dessert. Cette relation est essentielle car même si un Pod est terminé et remplacé, le Service continuera à router le trafic vers les nouveaux Pods tant qu'ils portent les mêmes labels.</em></p>
</div>

<style>
{`
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }
  
  @keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.9; }
    100% { opacity: 1; }
  }
`}
</style>


> Le sélecteur est essentiel pour le fonctionnement du service. Il permet de lier le service à un pod spécifique. Même si un pod est terminé et remplacé, le service continuera à router le trafic vers les nouveaux pods tant qu'ils portent les mêmes labels.
>
> Pour lier un service à un pod:
> 1. Dans la définition du service, sous `spec` > `selector`, on spécifie les labels à cibler
> 2. Dans la définition du pod, sous `metadata` > `labels`, on définit les labels correspondants
>
> Cela permet de garantir que le service est lié à un pod spécifique et que le trafic est routé vers le bon pod.

15. Une fois cela terminé, notre fichier de définition de service est complet et nous pouvons procéder à sa création sur nos clusters.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30004
  selector:
    app: my-app
```



16. Résumons ce que nous avons fait :

> 1️⃣ Nous avons créé un fichier de définition de pod qui contient un label `app: my-app` dans la section `metadata` > `labels` . On peut ajouter le label `type: font end` dans la section `metadata` pour identifier notre application. Observez le fichier pod-defintion.yaml (section labels). 🔍

    <div align="center">
      <img 
        src="/img/kubernetes/chapitre11/service7.png"
        alt="Diagramme d'un service Kubernetes"
        width="100%"
        height="auto" 
        style={{
          maxWidth: "800px", 
          boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
          borderRadius: "8px",
          margin: "2rem auto",
          animation: "bounce 2s infinite, pulse 3s infinite"
        }}
      />


      <p><em>Figure 8: Les labels dans Kubernetes - Comme des étiquettes sur vos valises! Ils permettent d'identifier et de regrouper facilement vos pods, un peu comme quand vous mettez une étiquette avec votre nom sur votre valise à l'aéroport pour la retrouver facilement.</em></p>
    </div>

    <style>
    {`
      @keyframes bounce {
        0%, 100% { transform: translateY(0); }
        50% { transform: translateY(-5px); }
      }
      
      @keyframes slideIn {
        from { transform: translateX(-100%); }
        to { transform: translateX(0); }
      }

      @keyframes pulse {
        0% { opacity: 1; }
        50% { opacity: 0.9; }
        100% { opacity: 1; }
      }
    `}
    </style>

> 2️⃣ Nous avons ajouté un sélecteur dans le fichier de définition de service  `service-definition.yaml` pour lier le service à un pod.
  
  ```yaml
    selector:
  ```




> 3️⃣ Nous déplaçons de *gauche à droite* dans le fichier de définition de service et ajoutons un sélecteur pour lier le service à un pod. On déplace la valeur de la clé  `labels` (app: my-app et type: font end) du fichier pod-definition.yaml (section labels) dans la valeur de la clé `selector` de service-defintion.yaml.  

La partie suivante du fichier pod-definition.yaml
 ```yaml
    labels:
      app: my-app
      type: font-end
  ```
  ➡️ devient dans le fichier service-definition.yaml
  ```yaml
    selector:
      app: my-app
      type: font-end
  ```


<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service8.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "800px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />


  <p><em>Figure 9: Déplacement des labels du fichier pod-definition.yaml vers le sélecteur dans service-definition.yaml pour établir la liaison entre le service et le pod</em></p>
</div>

<style>
{`
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }
  
  @keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.9; }
    100% { opacity: 1; }
  }
`}
</style>


> 🎯 Nous avons finalisé la configuration en déplaçant les labels du fichier pod-definition.yaml vers le sélecteur dans service-definition.yaml, établissant ainsi la liaison entre le service et le pod.


<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service9.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "800px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />

  <p><em>Figure 10: Code final du fichier `service-definition.yaml` avec la configuration complète du service</em></p>
</div>

<style>
{`
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }
  
  @keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.9; }
    100% { opacity: 1; }
  }
`}
</style>




> 4️⃣ Nous créons le service en utilisant la commande `kubectl create`. 


16. Je vais le sauvegarder ici et revenir à mon terminal. Je vais naviguer vers le nouveau répertoire que nous avons créé, où se trouve notre fichier de définition de service.

17. Je vais créer le service en utilisant la commande `kubectl create` avec l'option `-f` et spécifier le fichier de définition du service en entrée.


```bash
kubectl create -f Service/service-definition.yaml
kubectl get services
curl http://localhost:30008
curl http://192.168.1.2:30008/index.html
```


18. Je lance cette commande et le service a été créé.

<div align="center">
  <img 
    src="/img/kubernetes/chapitre11/service10.png"
    alt="Diagramme d'un service Kubernetes"
    width="100%"
    height="auto" 
    style={{
      maxWidth: "1200px", 
      boxShadow: "0 4px 8px rgba(0,0,0,0.1)",
      borderRadius: "8px",
      margin: "2rem auto",
      animation: "bounce 2s infinite, pulse 3s infinite"
    }}
  />


  <p><em>Figure 11: Commandes kubectl pour créer et vérifier un service, et tester l'accès via curl</em></p>
</div>

<style>
{`
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }
  
  @keyframes slideIn {
    from { transform: translateX(-100%); }
    to { transform: translateX(0); }
  }

  @keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.9; }
    100% { opacity: 1; }
  }
`}
</style>





19. Nous pouvons maintenant exécuter la commande `kubectl get services` et vous remarquerez que le nouveau service est visible. Le type du service est "NodePort", car nous voulons qu'il soit accessible sur le port du nœud de travail.

```bash
kubectl get services
```
20. Pour accéder à notre application, nous avons besoin de l'adresse IP et du port du service. Comme nous utilisons Minikube, nous pouvons facilement obtenir l'URL complète avec la commande suivante :

```bash
minikube service my-app-service --url
```

21. Voici l'adresse IP du cluster qui est également créée pour le service. Il s'agit d'une adresse créée pour le service dans le réseau interne du cluster.

```bash
minikube ip
```

22. Voici le port sur le nœud de travail que nous pouvons utiliser pour accéder à notre application. Si vous connaissez l'adresse IP du nœud de travail, vous pouvez simplement aller sur un navigateur, taper l'adresse IP du nœud de travail suivie du numéro de port, et vous devriez pouvoir accéder à cette application.

```bash
minikube service my-app-service --url
```

23. Essayons d'accéder à cela sur un navigateur. Vous pouvez simplement copier cette URL, aller sur un navigateur et coller cette URL dans le navigateur. Nous voyons alors la page web par défaut de Nginx, ce qui confirme que l'application Nginx est opérationnelle et accessible via un navigateur web.

24. C'est tout pour cette démonstration. 


### 🔙 [Retour à la table des matières](#table-des-matières)
