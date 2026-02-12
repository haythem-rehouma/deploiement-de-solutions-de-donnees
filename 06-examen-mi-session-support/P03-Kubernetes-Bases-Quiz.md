# Examen formatif -- Kubernetes : concepts de base

## Modules couverts

Cet examen porte sur les notions du module suivant :
- **05 - Kubernetes : déploiement de base** : concepts fondamentaux, architecture, kubectl, Pods, Deployments, Minikube

Avant de commencer, assurez-vous d'avoir lu le module 05 et réalisé les pratiques associées.

## Consignes

- Répondez à chaque question, puis cliquez sur "Voir la réponse" pour vérifier.
- Comptez vos bonnes réponses.
- Score total : /30

---

## Section 1 -- Concepts fondamentaux (10 points)

---

**Q1.** En une phrase, qu'est-ce que Kubernetes ?

<details>
<summary>Voir la réponse</summary>

Kubernetes est une plateforme d'orchestration de conteneurs qui automatise le déploiement, la mise à l'échelle et la gestion d'applications conteneurisées.

</details>

---

**Q2.** Que signifie l'abréviation "K8s" ?

<details>
<summary>Voir la réponse</summary>

K8s est l'abréviation de Kubernetes : la lettre K, suivie de 8 lettres (ubernete), suivie de la lettre s.

</details>

---

**Q3.** Quelle est la différence principale entre Docker Swarm et Kubernetes ?

<details>
<summary>Voir la réponse</summary>

- **Docker Swarm** : plus simple à configurer, moins de fonctionnalités, intégré directement à Docker.
- **Kubernetes** : plus complexe, beaucoup plus riche en fonctionnalités, standard de l'industrie en 2025.

</details>

---

**Q4.** Qu'est-ce qu'un cluster Kubernetes ?

<details>
<summary>Voir la réponse</summary>

Un cluster est un ensemble de machines (nodes) qui travaillent ensemble et sont gérées par Kubernetes comme une seule unité. Il comprend un Control Plane et un ou plusieurs Workers.

</details>

---

**Q5.** Quelle est la différence entre le Control Plane et les Workers ?

<details>
<summary>Voir la réponse</summary>

- **Control Plane** : le cerveau du cluster, il prend les décisions (planification des pods, gestion de l'état désiré).
- **Workers** : les machines qui exécutent les conteneurs (les pods y tournent réellement).

</details>

---

**Q6.** Qu'est-ce qu'un Node ?

<details>
<summary>Voir la réponse</summary>

Un Node est une machine (physique ou virtuelle) dans le cluster. Il peut être un node Master (Control Plane) ou un node Worker.

</details>

---

**Q7.** Qu'est-ce que kubectl ?

<details>
<summary>Voir la réponse</summary>

kubectl est l'outil en ligne de commande pour communiquer avec un cluster Kubernetes. C'est la commande principale pour créer, modifier, supprimer et observer les ressources.

</details>

---

**Q8.** Comment Kubernetes sait-il ce que l'on souhaite déployer ?

<details>
<summary>Voir la réponse</summary>

On décrit l'état désiré dans des fichiers YAML (ou JSON). Kubernetes compare en permanence l'état actuel du cluster à l'état désiré et agit pour les faire correspondre (créer des pods, les redémarrer, etc.).

</details>

---

**Q9.** Qu'est-ce que Minikube ?

<details>
<summary>Voir la réponse</summary>

Minikube est un outil qui crée un cluster Kubernetes local sur un seul ordinateur. Il est utilisé pour apprendre, tester et développer.

</details>

---

**Q10.** Qu'est-ce que Kind (Kubernetes in Docker) ?

<details>
<summary>Voir la réponse</summary>

Kind est un outil qui exécute des clusters Kubernetes locaux en utilisant des conteneurs Docker comme "noeuds". Il est léger et permet facilement de créer des clusters multi-noeuds pour les tests.

</details>

---

## Section 2 -- Commandes de base (10 points)

---

**Q11.** Quelle commande démarre Minikube ?

<details>
<summary>Voir la réponse</summary>

```bash
minikube start
```

</details>

---

**Q12.** Quelle commande vérifie l'état de Minikube ?

<details>
<summary>Voir la réponse</summary>

```bash
minikube status
```

</details>

---

**Q13.** Quelle commande affiche les nodes du cluster ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl get nodes
```

</details>

---

**Q14.** Quelle commande crée un pod nginx rapidement ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl run mon-pod --image=nginx
```

</details>

---

**Q15.** Quelle commande affiche tous les pods en cours ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl get pods
```

Pour tous les namespaces : `kubectl get pods -A`

</details>

---

**Q16.** Quelle commande affiche les détails d'un pod nommé "mon-pod" ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl describe pod mon-pod
```

</details>

---

**Q17.** Quelle commande affiche les logs d'un pod ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl logs mon-pod
```

En temps réel : `kubectl logs -f mon-pod`

</details>

---

**Q18.** Quelle commande permet d'entrer dans un pod avec un shell ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl exec -it mon-pod -- /bin/sh
```

</details>

---

**Q19.** Quelle commande applique un fichier YAML pour créer ou mettre à jour une ressource ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl apply -f mon-fichier.yaml
```

</details>

---

**Q20.** Quelle commande supprime un pod nommé "mon-pod" ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl delete pod mon-pod
```

</details>

---

## Section 3 -- Pods et Deployments (10 points)

---

**Q21.** Qu'est-ce qu'un Pod dans Kubernetes ?

<details>
<summary>Voir la réponse</summary>

Un Pod est la plus petite unité déployable dans Kubernetes. C'est un groupe d'un ou plusieurs conteneurs qui partagent le même réseau et le même stockage. Dans la majorité des cas, un pod contient un seul conteneur.

</details>

---

**Q22.** Pourquoi utiliser un Deployment plutôt qu'un Pod seul ?

<details>
<summary>Voir la réponse</summary>

Un Deployment :
- gère plusieurs replicas (copies) du pod,
- recrée automatiquement les pods qui plantent,
- permet les mises à jour progressives (rolling updates),
- garde l'historique des versions pour faire un rollback.

Un pod seul n'a aucune de ces fonctionnalités.

</details>

---

**Q23.** Comment définir le nombre de replicas dans un fichier YAML de Deployment ?

<details>
<summary>Voir la réponse</summary>

```yaml
spec:
  replicas: 3
```

</details>

---

**Q24.** Quelle commande met à l'échelle un Deployment à 5 replicas ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl scale deployment mon-app --replicas=5
```

</details>

---

**Q25.** Quelle commande met à jour l'image d'un Deployment ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl set image deployment/mon-app conteneur=nouvelle-image:tag
```

</details>

---

**Q26.** Quelle commande revient à la version précédente d'un Deployment ?

<details>
<summary>Voir la réponse</summary>

```bash
kubectl rollout undo deployment/mon-app
```

</details>

---

**Q27.** Qu'est-ce qu'un "rolling update" dans un Deployment ?

<details>
<summary>Voir la réponse</summary>

Un rolling update est une mise à jour progressive : Kubernetes remplace les anciens Pods par les nouveaux un par un (ou par petits groupes), sans interruption de service. Si un problème survient, on peut faire un rollback avec `kubectl rollout undo`.

</details>

---

**Q28.** Quel composant maintient un nombre spécifié de replicas de Pods en cours d'exécution ?

<details>
<summary>Voir la réponse</summary>

Le **ReplicaSet**. Il s'assure qu'un nombre défini de Pods identiques sont toujours actifs. En pratique, on ne crée pas de ReplicaSet directement : c'est le Deployment qui le gère automatiquement.

</details>

---

**Q29.** Quelle est la hiérarchie des objets Kubernetes du plus simple au plus complexe ?

<details>
<summary>Voir la réponse</summary>

```
Pod (unité de base)
  |
  v
ReplicaSet (gestion des replicas)
  |
  v
Deployment (gestion des mises à jour et rollbacks)
```

En pratique, on travaille avec des Deployments qui gèrent automatiquement les ReplicaSets et les Pods.

</details>

---

**Q30.** Quelle commande arrête Minikube sans perdre les données ?

<details>
<summary>Voir la réponse</summary>

```bash
minikube stop
```

Pour supprimer complètement le cluster : `minikube delete`

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

## Aide-mémoire rapide

```bash
# Minikube
minikube start / stop / status / delete / dashboard

# Voir les ressources
kubectl get pods / deployments / services / nodes

# Détails d'une ressource
kubectl describe <type> <nom>

# Logs d'un pod
kubectl logs <nom-du-pod>

# Entrer dans un pod
kubectl exec -it <nom-du-pod> -- /bin/sh

# Créer ou mettre à jour
kubectl apply -f <fichier.yaml>

# Supprimer
kubectl delete <type> <nom>

# Scaler
kubectl scale deployment <nom> --replicas=<n>

# Mise à jour d'image
kubectl set image deployment/<nom> <conteneur>=<image:tag>

# Rollback
kubectl rollout undo deployment/<nom>
```
