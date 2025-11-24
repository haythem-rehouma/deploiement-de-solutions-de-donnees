# Préparation du Dépôt GitHub

1. **Créer un Nouveau Dépôt GitHub :**
    - Connectez-vous à GitHub et créez un nouveau dépôt nommé `jenkins-data-pipeline`.
    - Clonez le dépôt sur votre machine locale.

2. **Ajouter un Script Python Avancé :**
    - Créez un fichier `data_analysis.py` avec le contenu suivant :
        ```python
        import pandas as pd

        def analyze_sales_data(file_path):
            # Lire les données à partir d'un fichier CSV
            data = pd.read_csv(file_path)
            
            # Calculer les statistiques
            total_sales = data['Sales'].sum()
            average_sales = data['Sales'].mean()
            max_sales = data['Sales'].max()
            min_sales = data['Sales'].min()

            # Afficher les statistiques
            print(f"Total des ventes: {total_sales}")
            print(f"Vente moyenne: {average_sales}")
            print(f"Ventes maximales: {max_sales}")
            print(f"Ventes minimales: {min_sales}")

        if __name__ == "__main__":
            file_path = 'sales_data.csv'  # Chemin vers le fichier CSV
            analyze_sales_data(file_path)
        ```
    - Ajoutez un fichier `sales_data.csv` avec le contenu suivant pour tester le script :
        ```csv
        Date,Product,Sales
        2024-01-01,Product A,100
        2024-01-02,Product B,150
        2024-01-03,Product C,200
        2024-01-04,Product A,130
        2024-01-05,Product B,170
        ```
    - Ajoutez un fichier `README.md` avec une description simple de votre projet.

3. **Poussez vos Modifications :**
    - Utilisez Git pour ajouter, commettre et pousser vos fichiers sur GitHub :
        ```sh
        git add .
        git commit -m "Initial commit with advanced data analysis script"
        git push origin main
        ```

# Partie 2 : Configuration du Pipeline Jenkins
1. **Créer un Nouveau Job Pipeline dans Jenkins :**
    - Dans Jenkins, sélectionnez "Nouveau Job".
    - Nommez votre job (par exemple, `DataAnalysisPipeline`), sélectionnez "Pipeline" et créez-le.

2. **Configurer le Pipeline pour Utiliser Votre Dépôt GitHub :**
    - Dans la configuration du job, dans la section "Pipeline", choisissez "Pipeline script from SCM".
    - Sélectionnez "Git" comme SCM.
    - Entrez l'URL de votre dépôt GitHub et spécifiez la branche (par exemple, `main`).
    - Dans le champ "Script Path", entrez le nom de votre Jenkinsfile (que vous allez créer dans l'étape suivante).

3. **Créer un Jenkinsfile dans Votre Dépôt :**
    - Créez un fichier nommé `Jenkinsfile` à la racine de votre dépôt avec le contenu suivant :
        ```groovy
        pipeline {
            agent any
            stages {
                stage('Build') {
                    steps {
                        script {
                            // Choisissez la commande en fonction de votre script
                            sh 'pip install pandas' // Installer les dépendances
                            sh 'python data_analysis.py' // Exécuter le script Python
                        }
                    }
                }
            }
        }
        ```
    - Poussez le Jenkinsfile dans votre dépôt GitHub :
        ```sh
        git add Jenkinsfile
        git commit -m "Add Jenkinsfile for pipeline"
        git push origin main
        ```

4. **Configurer le Pipeline pour Utiliser Votre Dépôt GitHub avec Vérification Périodique :**
    - Dans la configuration du job, dans la section "Pipeline", choisissez "Pipeline script from SCM".
    - Sélectionnez "Git" comme SCM.
    - Entrez l'URL de votre dépôt GitHub et spécifiez la branche (par exemple, `main`).
    - Dans le champ "Script Path", entrez le nom de votre `Jenkinsfile`.
    - Pour activer la vérification périodique, allez dans la section "Build Triggers" de la configuration du job et sélectionnez "Poll SCM". Dans le champ de programmation, vous pouvez entrer un horaire selon la syntaxe cron. Par exemple, `H/5 * * * *` pour vérifier le dépôt toutes les cinq minutes.

# Partie 3 : Testez Votre Configuration
- Modifiez le fichier `README.md` dans votre dépôt GitHub, puis commettez et poussez la modification.
- Cette action devrait déclencher automatiquement votre pipeline Jenkins.
- Vérifiez que le pipeline s'exécute correctement et affiche les statistiques des ventes dans la console de sortie Jenkins.

# (BONUS) Configuration du Webhook GitHub pour Déclencher le Pipeline :
1. Dans votre dépôt GitHub, allez dans "Settings" > "Webhooks" et ajoutez un nouveau webhook.
2. L'URL du webhook sera l'URL de votre serveur Jenkins suivi de `/github-webhook/` (par exemple, `http://your-jenkins-server/github-webhook/`).
3. Choisissez "Just the push event".
4. Activez le webhook.

# Livrable :
Soumettez un rapport word sur l'exercice "Déclencher un Pipeline Jenkins avec GitHub", incluant des captures d'écran comme preuves :

1. **Dépôt GitHub :** Montrez le dépôt avec `data_analysis.py`, `sales_data.csv`, `README.md`, et `Jenkinsfile`.
2. **Configuration Jenkins :** Capture de la configuration du pipeline Jenkins, montrant "Pipeline script from SCM" avec l'URL du dépôt et le chemin du `Jenkinsfile`.
3. **Vérification SCM :** (Si applicable) Montrez la programmation cron dans "Build Triggers" pour la vérification périodique.
4. **Pipeline en Action :** Capture du pipeline Jenkins exécutant suite à une modification du `README.md`.
5. **Résultat du Pipeline :** Montrez la sortie de la console Jenkins, avec l'exécution réussie du script.
6. **(Optionnel) Webhook GitHub :** Si un webhook a été configuré, incluez sa configuration.

### Consignes :
- Soyez clair et organisé.
- Chaque capture doit être décrite brièvement.
- Assurez-vous de la lisibilité des captures.
- Soumettez en PDF ou Word.

