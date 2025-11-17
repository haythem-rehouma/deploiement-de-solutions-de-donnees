#  ** MAVEN + JENKINS (Projet Calculatrice Java) - Partie 1**

Ce tutoriel couvre **toute la partie Maven** avant l’intégration dans Jenkins.
Il suit une progression claire :

1. Comprendre les **commandes Maven essentielles**
2. Créer un **projet Maven calculatrice** (manuellement ou automatiquement)
3. Écrire le **code + tests**
4. Exécuter les principales commandes Maven
5. Préparer l’intégration dans **Jenkins (partie 3)**



# **1. Introduction à Maven**

Maven est un outil d’automatisation de build pour Java.
Il permet de gérer :

* les dépendances,
* la compilation,
* les tests,
* le packaging (JAR/WAR),
* le cycle du build,
* la génération de rapports.

Tout projet Maven repose sur un fichier **pom.xml**, qui décrit la configuration du projet.



# **2. Commandes Maven essentielles (à mémoriser)**

Voici les commandes les plus utiles, exécutées **à la racine du projet** (où se trouve `pom.xml`) :

```bash
mvn archetype:generate        # Génère un projet complet
mvn compile                   # Compile le code
mvn test                      # Exécute les tests unitaires
mvn package                   # Build complet (compile + tests + jar)
mvn verify                    # Valide la qualité du build
mvn install                   # Installe le jar dans ~/.m2 (local repo)
mvn deploy                    # Déploie dans un repo distant
mvn clean                     # Supprime target/
mvn site                      # Génère un site de documentation
mvn dependency:analyze        # Analyse les dépendances
mvn dependency:update-snapshots
```

Ce sont exactement les commandes que Jenkins exécutera automatiquement dans la suite.


# **3. Créer le projet Maven Calculatrice**

Maven offre deux méthodes :
✔ **Méthode rapide (recommandée)** : Archetype
✔ **Méthode longue** : création manuelle

## **Méthode A – Création automatique (recommandée)**

Dans un terminal :

```bash
mvn archetype:generate \
  -DgroupId=com.example \
  -DartifactId=calculator \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DinteractiveMode=false
```

Résultat : un dossier complet nommé **calculator/** est généré avec :

```
src/main/java
src/test/java
pom.xml
```

Vous n’avez plus qu’à remplacer les fichiers Java par ceux du projet calculatrice.



## **Méthode B – Création manuelle**

Pour comprendre la structure Maven, vous pouvez créer les dossiers vous-même :

Linux/macOS :

```bash
mkdir -p CalculatorProject/src/main/java/com/example
mkdir -p CalculatorProject/src/test/java/com/example
```

Windows :

```cmd
mkdir CalculatorProject\src\main\java\com\example
mkdir CalculatorProject\src\test\java\com\example
```

Ensuite, créez :

* `Calculator.java`
* `CalculatorTest.java`
* `pom.xml`



# **4. Structure finale du projet**

Voici la structure standard Maven :

```
CalculatorProject/
│-- pom.xml
└── src/
    ├── main/
    │   └── java/com/example/Calculator.java
    └── test/
        └── java/com/example/CalculatorTest.java
```



# **5. Code source – Calculator.java**

Créez :

`src/main/java/com/example/Calculator.java`

```java
package com.example;

public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public double divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Division by zero.");
        }
        return (double) a / b;
    }
}
```


# **6. Tests unitaires – CalculatorTest.java**

Créez :

`src/test/java/com/example/CalculatorTest.java`

```java
package com.example;

import org.junit.Assert;
import org.junit.Test;

public class CalculatorTest {

    private Calculator calculator = new Calculator();

    @Test
    public void testAdd() {
        Assert.assertEquals(5, calculator.add(2, 3));
    }

    @Test
    public void testSubtract() {
        Assert.assertEquals(1, calculator.subtract(3, 2));
    }

    @Test
    public void testMultiply() {
        Assert.assertEquals(6, calculator.multiply(2, 3));
    }

    @Test
    public void testDivide() {
        Assert.assertEquals(2.0, calculator.divide(4, 2), 0);
    }

    @Test(expected = IllegalArgumentException.class)
    public void testDivideByZero() {
        calculator.divide(1, 0);
    }
}
```



# **7. Fichier pom.xml**

Créez un fichier `pom.xml` avec JUnit :

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>calculator</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```



# **8. Exécution des commandes Maven**

Placez-vous dans le dossier du projet :

```bash
cd calculator
```

Puis lancez les commandes :

| Action                | Commande      | Résultat                       |
| --------------------- | ------------- | ------------------------------ |
| Compiler              | `mvn compile` | Génère les `.class`            |
| Exécuter les tests    | `mvn test`    | Exécute les tests JUnit        |
| Construire le projet  | `mvn package` | Crée un fichier `.jar`         |
| Nettoyer              | `mvn clean`   | Supprime `target/`             |
| Installer localement  | `mvn install` | Ajoute le JAR au dépôt `~/.m2` |
| Générer documentation | `mvn site`    | Génère un site HTML            |



# **Conclusion — Pourquoi ce tutoriel est important ?**

Après avoir :

✔ créé un projet Maven propre
✔ écrit la classe + tests
✔ configuré `pom.xml`
✔ exécuté les commandes essentielles

… vous êtes maintenant prêt pour la suite :


