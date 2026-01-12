# Quiz Final : 30 Questions sur Kubernetes

## Instructions

- Répondez à chaque question avant de cliquer sur "Voir la réponse"
- Comptez vos bonnes réponses
- Score : /30

---

## Section 1 : Concepts de base (Questions 1-10)

**1. En une phrase simple, c'est quoi Kubernetes ?**

<details>
<summary>Voir la réponse</summary>

Kubernetes est un système qui automatise le déploiement, la mise à l'échelle et la gestion des applications dans des containers.

</details>

---

**2. Que signifie "K8s" ?**

<details>
<summary>Voir la réponse</summary>

K8s est l'abréviation de Kubernetes : K + 8 lettres + s

</details>

---

**3. Qui a créé Kubernetes ?**

<details>
<summary>Voir la réponse</summary>

Google, basé sur leur système interne appelé "Borg". Kubernetes est maintenant open source et géré par la CNCF.

</details>

---

**4. Quelle est la différence principale entre Docker Swarm et Kubernetes ?**

<details>
<summary>Voir la réponse</summary>

- **Docker Swarm** : Plus simple, moins de fonctionnalités, intégré à Docker
- **Kubernetes** : Plus complexe, très riche en fonctionnalités, standard de l'industrie

</details>

---

**5. C'est quoi un "cluster" Kubernetes ?**

<details>
<summary>Voir la réponse</summary>

Un cluster est un ensemble de machines (nodes) qui travaillent ensemble et sont gérées par Kubernetes comme une seule unité.

</details>

---

**6. Quelle est la différence entre le Control Plane et les Workers ?**

<details>
<summary>Voir la réponse</summary>

- **Control Plane** : Le cerveau - prend les décisions, gère le cluster
- **Workers** : Les bras - exécutent les containers

</details>

---

**7. C'est quoi un Node ?**

<details>
<summary>Voir la réponse</summary>

Un Node est une machine (physique ou virtuelle) dans le cluster Kubernetes. Il peut être Master (Control Plane) ou Worker.

</details>

---

**8. Qu'est-ce que kubectl ?**

<details>
<summary>Voir la réponse</summary>

kubectl est l'outil en ligne de commande pour communiquer avec un cluster Kubernetes. C'est votre "télécommande" pour Kubernetes.

</details>

---

**9. Comment Kubernetes sait ce que vous voulez ?**

<details>
<summary>Voir la réponse</summary>

Vous décrivez l'**état désiré** dans des fichiers YAML. Kubernetes s'occupe ensuite de maintenir cet état (créer les pods, les redémarrer si besoin, etc.)

</details>

---

**10. C'est quoi Minikube ?**

<details>
<summary>Voir la réponse</summary>

Minikube est un outil qui crée un cluster Kubernetes local sur votre ordinateur. Parfait pour apprendre et tester.

</details>

---

## Section 2 : Installation et commandes (Questions 11-15)

**11. Quelle commande pour démarrer Minikube ?**

<details>
<summary>Voir la réponse</summary>

```bash
minikube start
```

</details>

---

**12. Quelle commande pour vérifier l'état de Minikube ?**

<details>
<summary>Voir la réponse</summary>

```bash
minikube status
```

</details>

---

**13. Quelle commande pour ouvrir le dashboard Kubernetes ?**

<details>
<summary>Voir la réponse</summary>

```bash
minikube dashboard
```

</details>

---

**14. Quelle commande pour voir les nodes du cluster ?**

<details>
<summary>Voir la réponse</summary>

```bash
kubectl get nodes
```

</details>

---

**15. Quelle commande pour arrêter Minikube sans perdre les données ?**

<details>
<summary>Voir la réponse</summary>

```bash
minikube stop
```

</details>

---

## Section 3 : Les Pods (Questions 16-20)

**16. C'est quoi un Pod ?**

<details>
<summary>Voir la réponse</summary>

Un Pod est la plus petite unité dans Kubernetes. C'est un groupe d'un ou plusieurs containers qui partagent le même réseau et stockage.

</details>

---

**17. Dans la majorité des cas, combien de containers y a-t-il dans un Pod ?**

<details>
<summary>Voir la réponse</summary>

**Un seul container** (dans 90% des cas). La configuration multi-containers est pour des cas avancés.

</details>

---

**18. Quelle commande pour créer rapidement un pod nginx ?**

<details>
<summary>Voir la réponse</summary>

```bash
kubectl run mon-pod --image=nginx
```

</details>

---

**19. Quelle commande pour voir les logs d'un pod ?**

<details>
<summary>Voir la réponse</summary>

```bash
kubectl logs <nom-du-pod>

# En temps réel
kubectl logs -f <nom-du-pod>
```

</details>

---

**20. Quelle commande pour entrer dans un pod avec un shell ?**

<details>
<summary>Voir la réponse</summary>

```bash
kubectl exec -it <nom-du-pod> -- /bin/sh
```

</details>

---

## Section 4 : Les Deployments (Questions 21-25)

**21. Pourquoi utiliser un Deployment plutôt qu'un Pod seul ?**

<details>
<summary>Voir la réponse</summary>

Un Deployment :
- Gère plusieurs copies (replicas) du pod
- Recrée automatiquement les pods qui plantent
- Permet les mises à jour progressives (rolling updates)
- Garde l'historique des versions pour rollback

</details>

---

**22. Comment définir le nombre de copies dans un Deployment YAML ?**

<details>
<summary>Voir la réponse</summary>

```yaml
spec:
  replicas: 3
```

</details>

---

**23. Quelle commande pour scaler un Deployment à 5 replicas ?**

<details>
<summary>Voir la réponse</summary>

```bash
kubectl scale deployment mon-app --replicas=5
```

</details>

---

**24. Quelle commande pour mettre à jour l'image d'un Deployment ?**

<details>
<summary>Voir la réponse</summary>

```bash
kubectl set image deployment/mon-app container=nouvelle-image:tag
```

</details>

---

**25. Quelle commande pour revenir à la version précédente d'un Deployment ?**

<details>
<summary>Voir la réponse</summary>

```bash
kubectl rollout undo deployment/mon-app
```

</details>

---

## Section 5 : Les Services (Questions 26-30)

**26. Pourquoi a-t-on besoin de Services ?**

<details>
<summary>Voir la réponse</summary>

Parce que les IPs des Pods changent quand ils sont recréés. Un Service fournit :
- Une IP stable
- Un nom DNS
- Du load balancing entre les pods

</details>

---

**27. Quels sont les 3 types de Services les plus courants ?**

<details>
<summary>Voir la réponse</summary>

1. **ClusterIP** : Accès interne au cluster seulement (défaut)
2. **NodePort** : Accès externe via un port sur chaque node (30000-32767)
3. **LoadBalancer** : Accès externe via un load balancer cloud (IP publique)

</details>

---

**28. Quel type de Service utiliser pour la communication entre deux applications dans le cluster ?**

<details>
<summary>Voir la réponse</summary>

**ClusterIP** - C'est le type par défaut, accessible uniquement depuis l'intérieur du cluster.

</details>

---

**29. Comment un pod peut-il appeler un service par son nom ?**

<details>
<summary>Voir la réponse</summary>

Grâce au DNS interne de Kubernetes. Exemple :
```bash
curl http://nom-du-service
# ou
curl http://nom-du-service.namespace
```

</details>

---

**30. Quelle commande pour accéder à un service NodePort avec Minikube ?**

<details>
<summary>Voir la réponse</summary>

```bash
minikube service nom-du-service --url
```
Cela affiche l'URL complète pour accéder au service.

</details>

---

## Résultats

### Calculez votre score :

| Score | Niveau |
|-------|--------|
| 27-30 | Excellent ! Vous maîtrisez les bases |
| 22-26 | Très bien ! Quelques révisions à faire |
| 17-21 | Bien ! Relisez les cours |
| 12-16 | Moyen - Refaites les exercices pratiques |
| 0-11 | À revoir - Reprenez le module depuis le début |

---

## Aide-mémoire rapide

```bash
# Minikube
minikube start / stop / status / dashboard

# Voir les ressources
kubectl get pods / deployments / services / nodes

# Détails
kubectl describe <ressource> <nom>

# Logs
kubectl logs <pod>

# Entrer dans un pod
kubectl exec -it <pod> -- /bin/sh

# Créer depuis un fichier
kubectl apply -f <fichier.yaml>

# Supprimer
kubectl delete <ressource> <nom>

# Scaler
kubectl scale deployment <nom> --replicas=<n>

# Mise à jour
kubectl set image deployment/<nom> <container>=<image>

# Rollback
kubectl rollout undo deployment/<nom>
```

---

## Félicitations !

Vous avez terminé le quiz final du module Kubernetes de base !

