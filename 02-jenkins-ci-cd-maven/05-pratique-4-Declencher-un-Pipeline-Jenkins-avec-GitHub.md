# Pratique 4 - Partie 1


1. Complétez le pipeline suivant en YAML en ajoutant l'étape manquante pour exécuter `python main.py` dans un environnement avec `requirements.txt`.

```groovy
pipeline {
  agent any

  stages {
    stage('Install') {
      steps {
        sh 'pip install -r requirements.txt'
      }
    }

    stage('Run') {
      steps {
        // compléter ici
      }
    }

    stage('Archive') {
      steps {
        archiveArtifacts artifacts: '**/*.csv', allowEmptyArchive: true
      }
    }
  }
}
```



2. Vous devez améliorer le pipeline Jenkins suivant pour qu’il soit robuste, modulaire et adapté à un environnement d’exécution isolé.

> Objectifs :
>
> * Utiliser un **environnement virtuel Python** (via `venv`)
> * Gérer les erreurs lors de l’exécution du script (`main.py`) avec un code de retour
> * Nettoyer l’environnement à la fin du pipeline (suppression du dossier `venv`)
> * Archiver tous les fichiers `.csv` générés
>
> Complétez les étapes manquantes dans le pipeline ci-dessous :

```groovy
pipeline {
  agent any

  environment {
    VENV_DIR = 'venv'
  }

  stages {
    stage('Prepare') {
      steps {
        // Créer l'environnement virtuel
        sh 'python3 -m venv $VENV_DIR'
      }
    }

    stage('Install') {
      steps {
        // Activer venv et installer les dépendances
        sh '''
          . $VENV_DIR/bin/activate
          pip install --upgrade pip
          pip install -r requirements.txt
        '''
      }
    }

    stage('Run') {
      steps {
        // Complétez ici : exécuter main.py et gérer les erreurs
      }
    }

    stage('Archive') {
      steps {
        archiveArtifacts artifacts: '**/*.csv', allowEmptyArchive: false
      }
    }
  }

  post {
    always {
      // Complétez ici : nettoyage de l’environnement virtuel
    }
    failure {
      echo 'Le pipeline a échoué. Consultez les logs.'
    }
  }
}
```



### Contraintes 

* L’étape **Run** doit :

  * Activer l’environnement virtuel
  * Exécuter `main.py`
  * Interrompre le pipeline manuellement si `main.py` échoue (`exit 1` si code de retour ≠ 0)
* Le bloc `post > always` doit :

  * Supprimer complètement le dossier `venv/`
  * S’assurer que la suppression ne provoque pas d’échec du pipeline (utiliser `|| true`)





















<br/>


# Pratique 4 - Partie 2

**Extraction, transformation et publication automatisées d’offres d’emploi**



## 1. Contexte

Vous appartenez à une cellule technologique chargée d’automatiser la veille des offres d’emploi dans le domaine du développement logiciel.
Votre mission : construire un **pipeline CI/CD complet** piloté par **Jenkins** qui

1. extrait les offres depuis plusieurs sources ;
2. stocke le résultat au format CSV ;
3. transforme ce CSV en une page HTML lisible ;
4. publie automatiquement cette page sur un espace public ;
5. se déclenche et s’arrête **sans intervention manuelle** en fonction de la présence (ou non) de nouvelles offres.



## 2. Objectifs 

| Compétence visée         | Description                                                          |
| ------------------------ | -------------------------------------------------------------------- |
| Automatisation           | Chaîne complète : *scraping ➝ transformation ➝ tests ➝ publication*. |
| CI/CD                    | Intégration et déploiement continus via Jenkins.                     |
| Détection de changements | Arrêt prématuré du pipeline si aucune nouveauté.                     |
| Déploiement Web          | Mise en ligne publique du rapport HTML.                              |
| Documentation            | Explications claires dans un `README.md`.                            |



## 3. Matériel fourni

| Fichier            | Rôle                                                                                                                  |
| ------------------ | --------------------------------------------------------------------------------------------------------------------- |
| `scraper.py`       | Script Python de scraping multi-sources (HackerNews, Python.org, Remotive, JSRemotely, WorkingNomads, AuthenticJobs). |
| `requirements.txt` | Dépendances : `requests`, `beautifulsoup4`, `lxml`, `pandas`.                                                         |

> **Remarque :** le script complet est annexé en fin d’énoncé ; il génère `jobs.csv`.



## 4. Arborescence cible

```
projet-offres/
├── Jenkinsfile
├── scraper.py
├── html_generator.py        # À créer
├── requirements.txt
├── data/
│   ├── jobs.csv             # Dernière extraction
│   └── jobs_previous.csv    # Extraction précédente
├── public/
│   └── index.html           # Rapport HTML généré
├── logs/
│   └── log.txt              # Historique / erreurs
└── README.md
```



## 5. Travail à réaliser

### 5.1 Préparation (Install)

* Étape Jenkins `Install`

  * Installation propre des dépendances : `pip install -r requirements.txt`.
  * Utilisation d’un `venv` recommandée.

### 5.2 Scraping

* Étape `Scraping`

  * Exécution de `scraper.py`
  * Génération automatique de `data/jobs.csv`.

### 5.3 Transformation HTML

* Créer `html_generator.py` puis étape `Conversion` :

  * Lire `data/jobs.csv`.
  * Générer `public/index.html` avec un tableau HTML : colonnes **Titre**, **Entreprise**, **Source**, **Lien** (lien cliquable).
  * Mise en page minimaliste : table responsive, tri, recherche optionnelle.

### 5.4 Validation / Tests

* Étape `Tests` incluant au minimum :

  * Vérification que `jobs.csv` ≥ 10 lignes.
  * Vérification que `index.html` contient bien une balise `<table>` et au moins 10 lignes de données.
  * Arrêt du pipeline (`exit 1`) si l’une de ces conditions échoue.

### 5.5 Détection de changements

* Étape `DetectChanges` (obligatoire) :

  * Comparer `jobs.csv` à `jobs_previous.csv` (s’il existe) via `cmp`, `diff` ou hash MD5/SHA-256.
  * Si **aucune modification**, journaliser « Aucune nouvelle offre » puis **terminer prématurément** le pipeline (`currentBuild.result = 'SUCCESS'; return`).
  * Si changements détectés :

    * Copier `jobs.csv` → `jobs_previous.csv`.
    * Poursuivre les étapes `Conversion`, `Archive`, `Deploy`.

### 5.6 Archivage

* Étape `Archive`

  * Archivage Jenkins de `jobs.csv`, `index.html` et `logs/log.txt`.

### 5.7 Déploiement

* Étape `Deploy` (choisir **une** option, la documenter dans le `README.md`) :

| Option          | Détail technique minimal                                         |
| --------------- | ---------------------------------------------------------------- |
| A. NGINX local  | `scp public/index.html user@192.168.X.X:/var/www/html/`          |
| B. Bucket S3    | `aws s3 cp public/index.html s3://mon-bucket/ --acl public-read` |
| C. GitHub Pages | Push automatique sur la branche `gh-pages`                       |
| D. VPS perso    | Copie via `scp` ou `rsync` vers `/var/www/html`                  |

### 5.8 Déclenchement automatique

Le pipeline doit démarrer sans action manuelle :

* **WebHook Git** déclenché par `push` **ou**
* **Planification cron Jenkins** (exemple : `H */6 * * *`) **et**
* Bloc `DetectChanges` pour éviter les exécutions inutiles.



## 6. Livrables obligatoires

1. `Jenkinsfile` complet, commenté.
2. `html_generator.py` fonctionnel.
3. `README.md` détaillant :

   * Architecture du pipeline.
   * Choix du moyen de déploiement.
   * Mécanisme exact de détection de changements.
4. Captures d’écran Jenkins : exécution réussie, artefacts visibles, étape de déploiement.
5. Artefacts générés (`jobs.csv`, `index.html`, `log.txt`).



## 7. Barème (30 points)

| Critère                                         | Pts    |
| ----------------------------------------------- | ------ |
| Pipeline Jenkins (Install → Deploy) fonctionnel | 5      |
| Exécution réussie de `scraper.py`               | 3      |
| Génération valide de `jobs.csv`                 | 3      |
| Génération correcte de `index.html`             | 5      |
| Détection et gestion des changements            | 4      |
| Archivage des artefacts dans Jenkins            | 2      |
| Déploiement public opérationnel                 | 5      |
| Documentation (`README.md`) claire              | 3      |
| **Total**                                       | **30** |

### Bonus (+3 pts)

* Pipeline s’arrêtant intelligemment si aucune nouveauté (hash ou diff propre, journalisation claire).



## 8. Règles et conseils

* Aucune variable sensible (clé AWS, mot de passe) ne doit apparaître en clair dans le dépôt Git.
* Le code doit être commenté et respecter les standards PEP 8.
* Tout échec d’étape doit faire échouer le build (exit code ≠ 0).
* Utilisez `logging` plutôt que `print()` dans vos scripts.



### Annexe : contenu abrégé de `scraper.py`


```
import requests
from bs4 import BeautifulSoup
import pandas as pd

headers = {
    "User-Agent": (
        "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
        "AppleWebKit/537.36 (KHTML, like Gecko) "
        "Chrome/114.0.0.0 Safari/537.36"
    )
}


def scrape_hackernews():
    url = "https://news.ycombinator.com/jobs"
    offers = []
    try:
        res = requests.get(url, headers=headers, timeout=10)
        soup = BeautifulSoup(res.text, "lxml")
        for row in soup.find_all("tr", class_="athing"):
            a = row.find("a")
            if a and "item?id=" in a.get("href", ""):
                offers.append({
                    "Source": "HackerNews",
                    "Titre": a.text.strip(),
                    "Entreprise": "N/A",
                    "Lien": "https://news.ycombinator.com/" + a["href"]
                })
    except Exception as e:
        print("Erreur HackerNews:", e)
    return offers


def scrape_python_jobs():
    url = "https://www.python.org/jobs/"
    offers = []
    try:
        res = requests.get(url, headers=headers, timeout=10)
        soup = BeautifulSoup(res.text, "lxml")
        for job in soup.select(".list-recent-jobs li"):
            title = job.h2.text.strip()
            company = job.find("span", class_="listing-company-name").text.strip()
            link = "https://www.python.org" + job.h2.a["href"]
            offers.append({
                "Source": "Python.org",
                "Titre": title,
                "Entreprise": company,
                "Lien": link
            })
    except Exception as e:
        print("Erreur Python.org:", e)
    return offers


def scrape_jsremotely():
    url = "https://jsremotely.com/"
    offers = []
    try:
        res = requests.get(url, headers=headers, timeout=10)
        soup = BeautifulSoup(res.text, "lxml")
        for div in soup.find_all("div", class_="job"):
            a = div.find("a")
            if a:
                title = a.text.strip()
                link = "https://jsremotely.com" + a["href"]
                offers.append({
                    "Source": "JSRemotely",
                    "Titre": title,
                    "Entreprise": "N/A",
                    "Lien": link
                })
    except Exception as e:
        print("Erreur JSRemotely:", e)
    return offers


def scrape_remotive():
    url = "https://remotive.io/api/remote-jobs?category=software-dev"
    offers = []
    try:
        res = requests.get(url, headers=headers, timeout=10)
        jobs = res.json()["jobs"]
        for job in jobs:
            offers.append({
                "Source": "Remotive",
                "Titre": job["title"],
                "Entreprise": job["company_name"],
                "Lien": job["url"]
            })
    except Exception as e:
        print("Erreur Remotive:", e)
    return offers


def scrape_workingnomads():
    url = "https://www.workingnomads.com/jobs"
    offers = []
    try:
        res = requests.get(url, headers=headers, timeout=10)
        soup = BeautifulSoup(res.text, "lxml")
        for li in soup.select("div#jobsboard > a"):
            title = li.find("h3")
            company = li.find("h4")
            if title and company:
                offers.append({
                    "Source": "WorkingNomads",
                    "Titre": title.text.strip(),
                    "Entreprise": company.text.strip(),
                    "Lien": "https://www.workingnomads.com" + li["href"]
                })
    except Exception as e:
        print("Erreur WorkingNomads:", e)
    return offers


def scrape_authenticjobs():
    url = "https://authenticjobs.com/"
    offers = []
    try:
        res = requests.get(url, headers=headers, timeout=10)
        soup = BeautifulSoup(res.text, "lxml")
        for job in soup.select(".job-listing"):
            title_tag = job.find("h4")
            company_tag = job.find("h5")
            a_tag = job.find("a", href=True)
            if title_tag and a_tag:
                offers.append({
                    "Source": "AuthenticJobs",
                    "Titre": title_tag.text.strip(),
                    "Entreprise": company_tag.text.strip() if company_tag else "N/A",
                    "Lien": "https://authenticjobs.com" + a_tag["href"]
                })
    except Exception as e:
        print("Erreur AuthenticJobs:", e)
    return offers


def main():
    print("Scraping multi-sources en cours...\n")

    all_offers = []
    all_offers += scrape_hackernews()
    all_offers += scrape_python_jobs()
    all_offers += scrape_jsremotely()
    all_offers += scrape_remotive()
    all_offers += scrape_workingnomads()
    all_offers += scrape_authenticjobs()

    df = pd.DataFrame(all_offers)
    df.to_csv("jobs.csv", index=False, encoding="utf-8")

    print(f"\nFichier jobs.csv généré avec {len(df)} offres d'emploi.")
    if not df.empty:
        display(df.head(10))


main()
```


## requirements.txt

```
txt
requests
beautifulsoup4
lxml
pandas
```



