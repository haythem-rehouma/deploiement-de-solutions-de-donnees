# Examen formatif -- Kubernetes : Services, Kind et commandes avancées

## Modules couverts

Cet examen porte sur les notions des modules suivants :
- **05 - Kubernetes : déploiement de base** : Pods, Deployments, commandes kubectl
- **06 - Kubernetes suite** : Services (ClusterIP, NodePort, LoadBalancer), Kind, namespaces, labels, selectors

Avant de commencer, assurez-vous d'avoir lu les modules 05 et 06 et réalisé les pratiques associées.

## Consignes

- Répondez à chaque question, puis cliquez sur "Voir la réponse" pour vérifier.
- Comptez vos bonnes réponses.
- Score total : /30

---

## Section 1 -- Les Services (12 points)

---

**Q1.** Pourquoi a-t-on besoin de Services dans Kubernetes ?

<details>
<summary>Voir la réponse</summary>

Parce que les adresses IP des Pods changent quand ils sont redéployés ou recréés. Un Service fournit :
- une IP stable,
- un nom DNS,
- du load balancing entre les Pods.

</details>

---

**Q2.** Quels sont les 3 types de Services les plus courants ?

<details>
<summary>Voir la réponse</summary>

1. **ClusterIP** : accès interne au cluster uniquement (type par défaut).
2. **NodePort** : accès externe via un port sur chaque node (plage 30000-32767).
3. **LoadBalancer** : accès externe via un load balancer cloud (IP publique).

</details>

---

**Q3.** Quel type de Service est créé par défaut si aucun type n'est spécifié ?

<details>
<summary>Voir la réponse</summary>

**ClusterIP**. Il fournit une IP interne accessible uniquement depuis l'intérieur du cluster.

</details>

---

**Q4.** Dans quel cas utilise-t-on un Service ClusterIP ?

<details>
<summary>Voir la réponse</summary>

Pour la communication interne entre microservices dans le cluster. Exemples :
- un backend qui communique avec une base de données,
- un service de cache interne,
- la communication entre services d'une même application.

</details>

---

**Q5.** Quelle est la plage de ports valide pour un NodePort ?

<details>
<summary>Voir la réponse</summary>

**30000 à 32767**. C'est la plage par défaut pour les ports NodePort dans Kubernetes.

</details>

---

**Q6.** Quelle est la différence entre `targetPort` et `nodePort` dans un Service ?

<details>
<summary>Voir la réponse</summary>

- **targetPort** : le port sur lequel l'application écoute à l'intérieur du conteneur.
- **nodePort** : le port exposé sur chaque noeud du cluster (accessible de l'extérieur).

</details>

---

**Q7.** Comment un Service sait-il vers quels Pods rediriger le trafic ?

<details>
<summary>Voir la réponse</summary>

Via les **selectors** et les **labels**. Le Service utilise un selector qui correspond aux labels des Pods cibles.

```yaml
selector:
  app: mon-app
```

Tous les Pods ayant le label `app: mon-app` recevront le trafic.

</details>

---

**Q8.** Quelle commande expose rapidement un Deployment en tant que Service NodePort ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl expose deployment mon-app --type=NodePort --port=80
```

</details>

---

**Q9.** Comment un pod peut-il appeler un autre service par son nom ?

<details>
<summary>Voir la réponse</summary>

Grâce au DNS interne de Kubernetes :

```bash
curl http://nom-du-service
# ou avec le namespace
curl http://nom-du-service.mon-namespace
```

</details>

---

**Q10.** Quelle commande permet d'obtenir l'URL d'un service dans Minikube ?

<details>
<summary>Voir la réponse</summary>

```bash
minikube service nom-du-service --url
```

</details>

---

**Q11.** Quel est le rôle d'un Service de type LoadBalancer ?

<details>
<summary>Voir la réponse</summary>

Il expose le service via un load balancer externe fourni par le fournisseur cloud (AWS, GCP, Azure). Il distribue le trafic entrant vers les Pods et fournit une IP publique.

</details>

---

**Q12.** Qu'est-ce qu'un Service ExternalName ?

<details>
<summary>Voir la réponse</summary>

Un Service ExternalName mappe un service Kubernetes à un nom DNS externe. Il permet d'accéder à des services externes (ex. base de données cloud) via un alias DNS interne au cluster.

```yaml
spec:
  type: ExternalName
  externalName: db.example.com
```

</details>

---

## Section 2 -- Kind et gestion des clusters (8 points)

---

**Q13.** Quelle est la différence entre Minikube et Kind ?

<details>
<summary>Voir la réponse</summary>

| Aspect | Minikube | Kind |
|--------|----------|------|
| Backend | VM ou Docker | Docker uniquement |
| Multi-noeuds | Limité | Facile |
| Ressources | Plus lourd | Plus léger |
| Dashboard | Intégré | À configurer |

</details>

---

**Q14.** Quelle commande crée un cluster Kind ?

<details>
<summary>Voir la réponse</summary>

```bash
kind create cluster --name mon-cluster
```

Avec un fichier de configuration :

```bash
kind create cluster --name mon-cluster --config kind-config.yaml
```

</details>

---

**Q15.** Quelle commande supprime un cluster Kind ?

<details>
<summary>Voir la réponse</summary>

```bash
kind delete cluster --name mon-cluster
```

</details>

---

**Q16.** Quelle commande permet de changer de contexte Kubernetes (passer d'un cluster à un autre) ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl config use-context <nom-du-contexte>
```

Pour lister les contextes disponibles :

```bash
kubectl config get-contexts
```

</details>

---

**Q17.** Qu'est-ce qu'un namespace dans Kubernetes ?

<details>
<summary>Voir la réponse</summary>

Un namespace est un mécanisme d'isolation logique qui partitionne les ressources d'un cluster. Il permet de séparer les environnements (dev, staging, prod) ou les équipes.

</details>

---

**Q18.** Quelle commande crée un namespace ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl create namespace production
```

</details>

---

**Q19.** Comment spécifier un namespace dans une commande kubectl ?

<details>
<summary>Voir la réponse</summary>

Avec l'option `-n` :

```bash
kubectl get pods -n production
```

</details>

---

**Q20.** Quel est le rôle des labels dans Kubernetes ?

<details>
<summary>Voir la réponse</summary>

Les labels sont des paires clé/valeur attachées aux objets Kubernetes. Ils permettent :
- d'organiser les ressources,
- de les filtrer (`kubectl get pods -l app=mon-app`),
- de lier un Service à des Pods via les selectors.

</details>

---

## Section 3 -- QCM (10 points)

Cochez **une seule** bonne réponse par question.

---

**Q21.** Quelle commande affiche les pods de tous les namespaces ?

- [ ] A. `kubectl get pods`
- [ ] B. `kubectl get pods -A`
- [ ] C. `kubectl get pods --namespace`
- [ ] D. `kubectl list pods`

<details>
<summary>Voir la réponse</summary>

**B.** `kubectl get pods -A` (ou `kubectl get pods --all-namespaces`).

</details>

---

**Q22.** Si un Deployment a `replicas: 3` et qu'un Pod est supprimé, que se passe-t-il ?

- [ ] A. Le Deployment s'arrête
- [ ] B. Kubernetes recrée automatiquement un Pod pour maintenir 3 replicas
- [ ] C. Il faut relancer manuellement le Pod
- [ ] D. Le cluster est en erreur

<details>
<summary>Voir la réponse</summary>

**B.** Le ReplicaSet (géré par le Deployment) détecte qu'il n'y a plus que 2 Pods et en recrée un automatiquement pour revenir à l'état désiré (3 replicas).

</details>

---

**Q23.** Quel fichier décrit une ressource Kubernetes de manière déclarative ?

- [ ] A. Un fichier .groovy
- [ ] B. Un fichier .yaml ou .yml
- [ ] C. Un fichier .dockerfile
- [ ] D. Un fichier .json uniquement

<details>
<summary>Voir la réponse</summary>

**B.** Les fichiers YAML (.yaml ou .yml) sont le format standard pour décrire les ressources Kubernetes de manière déclarative. Le JSON est aussi supporté mais rarement utilisé en pratique.

</details>

---

**Q24.** Que fait la commande `kubectl rollout status deployment/mon-app` ?

- [ ] A. Supprime le Deployment
- [ ] B. Affiche la progression du déploiement en cours
- [ ] C. Revient à la version précédente
- [ ] D. Met à jour l'image du Deployment

<details>
<summary>Voir la réponse</summary>

**B.** Cette commande affiche en temps réel la progression d'un déploiement (rolling update) jusqu'à ce qu'il soit terminé.

</details>

---

**Q25.** Quelle section du YAML d'un Service permet de cibler les bons Pods ?

- [ ] A. `metadata`
- [ ] B. `spec.ports`
- [ ] C. `spec.selector`
- [ ] D. `spec.replicas`

<details>
<summary>Voir la réponse</summary>

**C.** `spec.selector` contient les labels qui correspondent aux Pods cibles.

</details>

---

**Q26.** Quelle commande ouvre le dashboard Kubernetes dans Minikube ?

- [ ] A. `kubectl dashboard`
- [ ] B. `minikube dashboard`
- [ ] C. `kubectl get dashboard`
- [ ] D. `minikube start --dashboard`

<details>
<summary>Voir la réponse</summary>

**B.** `minikube dashboard` ouvre le dashboard web dans le navigateur.

</details>

---

**Q27.** Comment rendre un Deployment accessible depuis l'extérieur du cluster avec Minikube ?

- [ ] A. Créer un Service ClusterIP
- [ ] B. Créer un Service NodePort puis utiliser `minikube service <nom> --url`
- [ ] C. Modifier directement l'IP du Pod
- [ ] D. Ajouter un label "external: true" au Pod

<details>
<summary>Voir la réponse</summary>

**B.** On crée un Service de type NodePort, puis on utilise `minikube service <nom> --url` pour obtenir l'URL accessible depuis la machine hôte.

</details>

---

**Q28.** Vrai ou Faux : un Pod peut contenir plusieurs conteneurs.

<details>
<summary>Voir la réponse</summary>

**Vrai.** Un Pod peut contenir plusieurs conteneurs qui partagent le même réseau et le même stockage. Cependant, dans la majorité des cas (90%), un Pod contient un seul conteneur.

</details>

---

**Q29.** Vrai ou Faux : `kubectl apply` peut créer une ressource ET la mettre à jour si elle existe déjà.

<details>
<summary>Voir la réponse</summary>

**Vrai.** C'est la méthode déclarative recommandée. Contrairement à `kubectl create` qui échoue si la ressource existe déjà, `apply` est idempotent.

</details>

---

**Q30.** Vrai ou Faux : un Service ClusterIP est accessible depuis l'extérieur du cluster.

<details>
<summary>Voir la réponse</summary>

**Faux.** Un ClusterIP est accessible uniquement depuis l'intérieur du cluster. Pour un accès externe, il faut utiliser un NodePort ou un LoadBalancer.

</details>

---

## Barème

| Score | Niveau |
|-------|--------|
| 27-30 | Excellent |
| 22-26 | Très bien |
| 17-21 | Bien |
| 12-16 | À réviser |
| 0-11 | À reprendre depuis le début |

---

## Aide-mémoire -- Services

```bash
# Créer un service rapidement
kubectl expose deployment <nom> --type=NodePort --port=80

# Voir les services
kubectl get services

# Détails d'un service
kubectl describe service <nom>

# URL d'un service (Minikube)
minikube service <nom> --url

# Supprimer un service
kubectl delete service <nom>
```

## Aide-mémoire -- Kind

```bash
# Créer un cluster
kind create cluster --name <nom>

# Supprimer un cluster
kind delete cluster --name <nom>

# Lister les clusters
kind get clusters

# Changer de contexte
kubectl config use-context kind-<nom>
kubectl config get-contexts
```
