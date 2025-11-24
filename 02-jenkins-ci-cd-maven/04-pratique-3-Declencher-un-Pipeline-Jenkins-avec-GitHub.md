**PRATIQUE – Pipeline Jenkins + GitHub : énoncé, corrections et ressources**

Bonjour à toutes et à tous,

Dans le cadre de notre module DevOps / CI-CD, vous trouverez ci-dessous l’ensemble des ressources pour la **01-PRATIQUE – Déclencher un Pipeline Jenkins avec GitHub**.
L’objectif est de mettre en place un pipeline réellement exploitable en entreprise : déclenchement automatique à chaque modification du fichier `README.md` sur GitHub.

<br/>

### 1. Énoncé officiel de la pratique

**01-PRATIQUE – Déclencher un Pipeline Jenkins avec GitHub**
Lien :
[https://docs.google.com/document/d/1XGoJfMBTSElPrzYUwePjkyHpjlFVbExP/edit?usp=sharing&ouid=114388549516551190899&rtpof=true&sd=true](https://docs.google.com/document/d/1XGoJfMBTSElPrzYUwePjkyHpjlFVbExP/edit?usp=sharing&ouid=114388549516551190899&rtpof=true&sd=true)

Veuillez lire attentivement toutes les étapes avant de commencer.

<br/>

### 2. Corrections détaillées

Ces corrections sont fournies comme **références de qualité professionnelle**. Elles ne remplacent pas votre propre travail, mais doivent vous servir à comparer, valider et améliorer votre solution.

* **Correction 1 – Implémentation complète du pipeline Jenkins**
  [https://drive.google.com/file/d/1YOmdvqE-2pEXH_fBTkzBIPcHgyzV2bZW/view?usp=sharing](https://drive.google.com/file/d/1YOmdvqE-2pEXH_fBTkzBIPcHgyzV2bZW/view?usp=sharing)

* **Correction 2 – Variante / autre approche de pipeline**
  [https://drive.google.com/file/d/1OcBz5OKRWyeg4eiJTbyNTR28RGK20Jgf/view?usp=sharing](https://drive.google.com/file/d/1OcBz5OKRWyeg4eiJTbyNTR28RGK20Jgf/view?usp=sharing)

Je vous recommande de :

1. Tenter d’abord la pratique **sans** regarder les corrections.
2. Ensuite, comparer votre travail aux deux versions proposées.
3. Noter les différences (structure du Jenkinsfile, webhooks GitHub, triggers, logs, etc.).

<br/>

### 3. Dossier complet – Ressources de la pratique

Vous pouvez également accéder à l’ensemble des fichiers liés à cette pratique (documents, exemples, matériaux complémentaires) via le dossier suivant :

**Dossier Drive – 01-PRATIQUE Jenkins + GitHub**
[https://drive.google.com/drive/folders/1bREC1A7cg7uSPxDAW_QoNxtCS9N9AxW5?usp=sharing](https://drive.google.com/drive/folders/1bREC1A7cg7uSPxDAW_QoNxtCS9N9AxW5?usp=sharing)

<br/>

### 4. Attendus pédagogiques

À l’issue de cette pratique, vous devez être capable de :

* Créer et configurer un dépôt GitHub dédié au pipeline.
* Mettre en place un **Jenkinsfile** minimal mais robuste (script “Hello World” exécuté via pipeline).
* Configurer l’intégration GitHub ↔ Jenkins (webhook / polling) pour déclencher le pipeline à chaque modification du `README.md`.
* Lire et interpréter les **logs Jenkins** pour diagnostiquer un échec de build.

Cette pratique se rapproche fortement de ce que l’on trouve dans des pipelines CI-CD **en production** (déclenchement sur commit, traçabilité, logs, corrections itératives).


Si vous avez des questions précises (erreur dans Jenkins, problème de webhook, build qui ne se déclenche pas, etc.), merci de me communiquer :

* une capture d’écran de l’erreur,
* un extrait de votre Jenkinsfile,
* et l’URL de votre dépôt GitHub.

Bonne pratique et prenez le temps de soigner votre pipeline.
