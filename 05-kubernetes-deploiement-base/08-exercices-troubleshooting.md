
- Je vous propose **3 fichiers YAML complets (exhaustifs)**, chacun avec **UNE erreur volontaire**.
- Chaque fichier contient **Namespace + Secrets + DB + Service + Wiki.js + Service NodePort**.

> Objectif étudiant : **diagnostiquer** (pas corriger) en s’appuyant sur `kubectl get/describe/logs/events`, tests DNS/port in-cluster, etc.

---

# 01 — Wiki.js ne démarre pas (SecretKeyRef cassé)

**Erreur voulue :** la clé `passwd` n’existe pas dans le Secret (devrait être `password`).
**Symptôme attendu :** `CreateContainerConfigError` / events “secret key not found”.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: cka-wikijs-01

---
apiVersion: v1
kind: Secret
metadata:
  name: pg-secret
  namespace: cka-wikijs-01
type: Opaque
data:
  username: d2lraQ==      # wiki
  password: c2VjcmV0MTIz  # secret123

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: cka-wikijs-01
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15
        env:
        - name: POSTGRES_DB
          value: wikijs
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: password
        ports:
        - containerPort: 5432

---
apiVersion: v1
kind: Service
metadata:
  name: postgres-svc
  namespace: cka-wikijs-01
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wikijs
  namespace: cka-wikijs-01
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wikijs
  template:
    metadata:
      labels:
        app: wikijs
    spec:
      containers:
      - name: wikijs
        image: requarks/wiki:2
        env:
        - name: DB_TYPE
          value: postgres
        - name: DB_HOST
          value: postgres-svc
        - name: DB_PORT
          value: "5432"
        - name: DB_NAME
          value: wikijs
        - name: DB_USER
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: username
        - name: DB_PASS
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: passwd   # ERREUR VOLONTAIRE (clé inexistante)
        ports:
        - containerPort: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: wikijs-svc
  namespace: cka-wikijs-01
spec:
  type: NodePort
  selector:
    app: wikijs
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 30090
```

---

# 02 — Wiki.js démarre mais ne se connecte pas à la DB (mauvais DB_HOST)

**Erreur voulue :** `DB_HOST` pointe vers un service inexistant `postgres-service`.
**Symptôme attendu :** Pod Wiki.js `Running` (ou `CrashLoopBackOff` selon timing) + logs `ENOTFOUND` / “could not translate host name”.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: cka-wikijs-02

---
apiVersion: v1
kind: Secret
metadata:
  name: pg-secret
  namespace: cka-wikijs-02
type: Opaque
data:
  username: d2lraQ==      # wiki
  password: c2VjcmV0MTIz  # secret123

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: cka-wikijs-02
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15
        env:
        - name: POSTGRES_DB
          value: wikijs
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: password
        ports:
        - containerPort: 5432

---
apiVersion: v1
kind: Service
metadata:
  name: postgres-svc
  namespace: cka-wikijs-02
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wikijs
  namespace: cka-wikijs-02
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wikijs
  template:
    metadata:
      labels:
        app: wikijs
    spec:
      containers:
      - name: wikijs
        image: requarks/wiki:2
        env:
        - name: DB_TYPE
          value: postgres
        - name: DB_HOST
          value: postgres-service   # ERREUR VOLONTAIRE (mauvais nom)
        - name: DB_PORT
          value: "5432"
        - name: DB_NAME
          value: wikijs
        - name: DB_USER
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: username
        - name: DB_PASS
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: password
        ports:
        - containerPort: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: wikijs-svc
  namespace: cka-wikijs-02
spec:
  type: NodePort
  selector:
    app: wikijs
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 30091
```

---

# 03 — Pod Wiki.js reste Pending (requests trop élevées)

**Erreur voulue :** requests CPU trop élevées (`8`).
**Symptôme attendu :** Pod Wiki.js `Pending` + event `Insufficient cpu`.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: cka-wikijs-03

---
apiVersion: v1
kind: Secret
metadata:
  name: pg-secret
  namespace: cka-wikijs-03
type: Opaque
data:
  username: d2lraQ==      # wiki
  password: c2VjcmV0MTIz  # secret123

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: cka-wikijs-03
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15
        env:
        - name: POSTGRES_DB
          value: wikijs
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: password
        ports:
        - containerPort: 5432

---
apiVersion: v1
kind: Service
metadata:
  name: postgres-svc
  namespace: cka-wikijs-03
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wikijs
  namespace: cka-wikijs-03
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wikijs
  template:
    metadata:
      labels:
        app: wikijs
    spec:
      containers:
      - name: wikijs
        image: requarks/wiki:2
        resources:
          requests:
            cpu: "8"        # ERREUR VOLONTAIRE (trop élevé)
            memory: "512Mi"
        env:
        - name: DB_TYPE
          value: postgres
        - name: DB_HOST
          value: postgres-svc
        - name: DB_PORT
          value: "5432"
        - name: DB_NAME
          value: wikijs
        - name: DB_USER
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: username
        - name: DB_PASS
          valueFrom:
            secretKeyRef:
              name: pg-secret
              key: password
        ports:
        - containerPort: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: wikijs-svc
  namespace: cka-wikijs-03
spec:
  type: NodePort
  selector:
    app: wikijs
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 30092
```

---

## Démarche (checklist commandes)

Tu peux donner cette liste “autorisée” dans l’énoncé :

```bash
kubectl get all -n <ns>
kubectl get pods -n <ns> -o wide
kubectl describe pod <pod> -n <ns> | sed -n '/Events:/,$p'
kubectl get events -n <ns> --sort-by=.lastTimestamp
kubectl logs deploy/wikijs -n <ns> --tail=100
kubectl describe deploy wikijs -n <ns> | egrep -i "env|DB_|secret|requests|limits"
kubectl get secret -n <ns> -o yaml
kubectl get svc -n <ns>
kubectl get endpoints -n <ns>
kubectl exec -it <wikijs-pod> -n <ns> -- sh -c 'getent hosts postgres-svc; nc -vz postgres-svc 5432'
kubectl get nodes -o wide
kubectl describe node <node> | egrep -i "Taints|Ready|Allocatable|Capacity|cpu|memory"
```

