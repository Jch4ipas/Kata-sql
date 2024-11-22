# HelloDojo Marketing

Ce fichier permet de consigner et documenter les étapes que vous avez suivies
pour répondre aux demandes du [README.md](README.md).

<!-- 
Note: de manière générale vous devez remplacer toutes les requêtes SQL,
les `XXX`, `NUMBER`, `TEXT` ou `NAME`.
-->

## Mise en place

<!-- 
Vous devez expliquer ici quelle solution technique vous avez choisie, comment
il faut procéder pour l'installer, quelles sont les commandes ou les étapes à
suivre pour importer les tables, quel outil vous avez utilisé pour créer le
schéma entité-relation de la base, et toutes autres informations qui pourraient
vous sembler utiles dans le but qu'une autre personne puisse **reproduire** 
votre démarche.
-->

Voici les étapes que j'ai suivies pour installer un docker MySql, créer une base de données
et y importer les tables :
  1. countries
  1. countries_people
  1. people
  1. ...

J'ai choisi d'utiliser MySql comme client de base de données.

J'ai généré le schéma avec XXX:

![Mon MLD](schema.jpg "Mon MLD généré avec XXX")

## Informations à récolter

### Générales

1. La table `people` contient `NUMBER` personnes, ma requête est :  
  ```sql
  SELECT count(*) FROM EXDB.people;
  ```
1. Cette requête permet de trouver l'email de la personne dont le nom de
   famille est "Warren" :
  ```sql
  SELECT email FROM EXDB.people WHERE lastname='Warren';
  ```
1. La table `people` est triée par nom de famille en ordre croissant, ma requête 
   est :  
  ```sql
  SELECT * FROM EXDB.people ORDER BY lastname ASC;
  ```
1. Les 5 premières entrées de la table `people` triée par nom de famille en 
   ordre croissant sont :  
  ```sql
  SELECT * FROM EXDB.people ORDER BY lastname ASC LIMIT 5;
  ```
1. Je trouve toutes les personnes dont le nom ou le prénom contient `ojo`, ma  
   requête est :  
  ```sql
  SELECT * FROM EXDB.people WHERE lastname LIKE '%ojo%' OR firstname LIKE '%ojo%';
  ```
1. Les 5 personnes les plus jeunes sont obtenues avec cette requête :  
  ```sql
  SELECT * FROM EXDB.people ORDER BY birthdate DESC LIMIT 5;
  ```
1. Les 5 personnes les plus agées sont obtenues avec cette requête :  
  ```sql
  SELECT * FROM EXDB.people ORDER BY birthdate ASC LIMIT 5;
  ```
1. La requête suivante permet de trouver l'age (en année) de chaque personne :  
  ```sql
  SELECT * FROM EXDB.people WHERE birthdate LIKE '%2009%';
  ```
1. La moyenne d'age (en année) est `NUMBER`, ma requête est :  
  ```sql
  SELECT AVG(DATEDIFF(CURDATE(), birthdate) / 365) FROM people;
  ```
1. Le prénom le plus long est `TEXT`, ma requête est :  
  ```sql
  SELECT * FROM people ORDER BY LENGTH(firstname) DESC LIMIT 1;
  ```
1. Le nom de famille le plus long est `TEXT`, ma requête est:  
  ```sql
  SELECT * FROM people ORDER BY LENGTH(lastname) DESC LIMIT 1;
  ```
1. La plus longue paire "nom + prénom" est `Wallace_Christensen`, ma requête est :  
  ```sql
  SELECT *, LENGTH(CONCAT(firstName, lastName)) AS total_length FROM EXDB.people ORDER BY total_length DESC LIMIT 1;
  ```
1. La table `people` contient `25` doublons, ma requête est :  
  ```sql
  SELECT firstname, COUNT(*) AS Nombre FROM EXDB.people GROUP BY firstname HAVING COUNT(*) > 1;
  ```

### Invitations

1. Pour lister tous les membres de plus de 18 ans :  
  ```sql
  SELECT * FROM EXDB.people WHERE birthdate < DATE_SUB(CURDATE(), INTERVAL 18 YEAR);
  ```
1. Pour lister tous les membres de plus de 18 ans et de moins de 60 ans :  
  ```sql
  SELECT * FROM EXDB.people WHERE birthdate < DATE_SUB(CURDATE(), INTERVAL 18 YEAR) AND birthdate > DATE_SUB(CURDATE(), INTERVAL 60 YEAR);
  ```
1. Pour lister tous les membres de plus de 18 ans, de moins de 60 ans et qui 
   une addresse email valide :  
  ```sql
  SELECT * FROM EXDB.people WHERE birthdate < DATE_SUB(CURDATE(), INTERVAL 18 YEAR) AND birthdate > DATE_SUB(CURDATE(), INTERVAL 60 YEAR) AND email LIKE '%_@_%._%';
  ```
1. Pour ajoutez une colonne `age` dans le résultat de la requête :  
  ```sql
  SELECT *, TIMESTAMPDIFF(YEAR, birthdate, CURRENT_DATE) AS age FROM EXDB.people;
  ```
1. Pour générer un champs contenant `Prénom Nom <email@provider.com>;` :  
  ```sql
  SELECT *, CONCAT(firstname, ' ', lastname, ' ', email) AS full_info FROM EXDB.people;
  ```
1. Avec cette requête :  
  ```sql
  SELECT COUNT(*) AS people_swiss FROM EXDB.people WHERE email LIKE '%_@_%.ch';
  ```  
  je peux estimer que `` personnes habitent en Suisse.

### Countries

1. La requête qui permet d'obtenir la liste d'options sous la forme :  
   `<option value="XXX">XXX</option>` est :  
  ```sql
  SELECT CONCAT('<option value="', iso2, '">', name_fr, '</option>') AS select_option FROM EXDB.countries;
  ```
1. Pour avoir la liste d'options en plusieurs langues, je procède de la manière 
   suivante :  
  ```sql
  SELECT CONCAT('<option value="', iso2, '">', name_en, '</option>') AS select_option FROM EXDB.countries;
  ```

### Jointure

1. Avec cette requête :  
  ```sql
  SELECT count(*) FROM EXDB.people AS people RIGHT JOIN EXDB.countries_people AS cp ON people.id = cp.idperson RIGHT JOIN EXDB.countries AS countries ON cp.idcountry = countries.id WHERE countries.name_fr IN ('Suisse');
  ```    
   je sais que `NUMBER` personnes habitent en Suisse.
1. Avec cette requête :  
  ```sql
  SELECT count(*) FROM EXDB.people AS people RIGHT JOIN EXDB.countries_people AS cp ON people.id = cp.idperson RIGHT JOIN EXDB.countries AS countries ON cp.idcountry = countries.id WHERE countries.name_fr NOT LIKE 'Suisse';
  ```  
   je sais que `NUMBER` personnes n'habitent pas en Suisse.
1. Avec cette requête :  
  ```sql
  SELECT people.firstname, people.lastname FROM EXDB.people AS people INNER JOIN EXDB.countries_people AS cp ON people.id = cp.idperson INNER JOIN EXDB.countries AS countries ON cp.idcountry = countries.id WHERE countries.name_fr IN ('France', 'Allemagne', 'Italie', 'Autriche', 'Liechtenstein');
  ```  
  je liste (nom & prénom) les membres habitants de France, Allemagne, Italie, Autriche et Liechtenstein.
1. Cette requête :  
  ```sql
  SELECT countries.name_fr, count(*) AS number_people
  FROM EXDB.people AS people 
  RIGHT JOIN EXDB.countries_people AS cp 
  ON people.id = cp.idperson 
  RIGHT JOIN EXDB.countries AS countries 
  ON cp.idcountry = countries.id 
  GROUP BY countries.name_fr;
  ```  
   permet de compter combien il y a de personnes par pays.
1. Cette requête :  
  ```sql
  SELECT countries.name_fr, COUNT(people.id) AS number_people
  FROM EXDB.countries AS countries
  RIGHT JOIN EXDB.countries_people AS cp 
    ON countries.id = cp.idcountry
  RIGHT JOIN EXDB.people AS people
  ON cp.idperson = people.id
  WHERE people.id IS NULL
  GROUP BY countries.name_fr;
  ```  
  liste les pays qui ne possèdent pas de personnes.
1. En exécutant cette requête :  
  ```sql
  SELECT idperson, CONCAT(people.firstname, ' ', people.lastname) AS full_name, COUNT(*) AS nombrepays
  FROM EXDB.countries_people AS cp
  INNER JOIN EXDB.people AS people ON idperson = people.id
  INNER JOIN EXDB.countries AS countries ON idcountry = countries.id
  GROUP BY idperson, people.firstname, people.lastname
  HAVING nombrepays > 1;
  ```  
   je sais que `NAME`, `NAME` et `NAME` sont liés à plusieurs pays.
1. En exécutant cette requête :  
  ```sql
  SELECT somecolumns FROM sometable [...];
  ```  
  je sais que `TEXT` parce que `TEXT`.
1. De la manière suivante :  
  ```sql
  SELECT countries.name_fr, count(*)/410*100 AS number_people
  FROM EXDB.people AS people 
  RIGHT JOIN EXDB.countries_people AS cp 
  ON people.id = cp.idperson 
  RIGHT JOIN EXDB.countries AS countries 
  ON cp.idcountry = countries.id 
  GROUP BY countries.name_fr
  ORDER BY number_people DESC;
  ```  
  nous pouvons afficher le pourcentage de personnes par pays.


### Procédures

1. Cette requête permet d'extraire le `tld` de l'adresse email et de le lier à la table `countries` :  
  ```sql
  SELECT email, SUBSTRING_INDEX(SUBSTRING_INDEX(email, '@', -1), '.', -1) AS tld, countries.name_fr, countries.iso2 FROM EXDB.people LEFT JOIN EXDB.countries AS countries ON SUBSTRING_INDEX(SUBSTRING_INDEX(email, '@', -1), '.', -1) = countries.iso2;
  ```  
1. Pour ajouter une chaine si la jointure ne retourne rien, j'ai procédé de la manière suivante :  
  SELECT 
  p.email, 
  SUBSTRING_INDEX(SUBSTRING_INDEX(p.email, '@', -1), '.', -1) AS tld,
  COALESCE(c.name_fr, 'Unknown Country') AS country_name
  FROM EXDB.people AS p
  LEFT JOIN EXDB.countries AS c
  ON SUBSTRING_INDEX(SUBSTRING_INDEX(p.email, '@', -1), '.', -1) = c.iso2;
1. Avec `STRING`, nous pouvons partager le mécanisme qui extrait le `tld`.
  ```sql
  SELECT somecolumns FROM sometable [...];
  ```

### Vue SQL

1. J'ai créé une vue bien pratique contenant toutes les informations utiles à un humain. Ma requête est:  
  ```sql
  CREATE VIEW view_people_details AS
  SELECT 
  p.id AS person_id,
  CONCAT(p.firstname, ' ', p.lastname) AS full_name,
  p.email,
  TIMESTAMPDIFF(YEAR, p.birthdate, CURDATE()) AS age,
  COALESCE(c.name_fr, 'Unknown Country') AS country_name
  FROM EXDB.people AS p
  LEFT JOIN EXDB.countries_people AS cp
  ON p.id = cp.idperson
  LEFT JOIN EXDB.countries AS c
  ON cp.idcountry = c.id;

  ```  
1. Je peux exporter ma vue au format CSV avec la requête :
  ```sql
  SELECT * 
  FROM view_people_details
  INTO OUTFILE '/path/where/you/want'
  ```

### Finances

1. J'ai créé une table pour les finances. Ma requête est:  
  ```sql
  CREATE TABLE finances (
    id INT AUTO_INCREMENT PRIMARY KEY,
    date DATE ,
    description VARCHAR(255) ,
    category VARCHAR(100),
    amount DECIMAL(10, 2),
    transaction_type ENUM('income', 'expense')
    );
  ```
1. J'ai ajouté des données de test avec la requêtes SQL suivante :  
   ```sql
  INSERT INTO finances (date, description, category, amount, transaction_type) 
  VALUES 
  ('2024-11-22', 'Salaire mensuel', 'Salaire', 2500.00, 'income'),
  ('2024-11-15', 'Achat supermarché', 'Nourriture', 150.75, 'expense'),
  ('2024-11-20', 'Paiement loyer', 'Loyer', 900.00, 'expense');
   ```
1. J'ai modifié la vue en y ajoutant les finances. Ma requête est:  
  ```sql
  CREATE OR REPLACE VIEW view_people_details AS
  SELECT
      p.id AS person_id,
      CONCAT(p.firstname, ' ', p.lastname) AS full_name,
      p.email,
      TIMESTAMPDIFF(YEAR, p.birthdate, CURDATE()) AS age,
      COALESCE(c.name_fr, 'Unknown Country') AS country_name
      SUM(expenses.amount) AS total_member_expenses
  FROM
      people
  LEFT JOIN countries_people ON people.id = countries_people.idperson
  LEFT JOIN countries ON countries_people.idcountry = countries.id
  LEFT JOIN expenses ON expenses.member_id = people.id
  GROUP BY people.id
  ```

### Intégrité référentielle
(WIP)
1. Pour ajouter les clés étrangères, j'ai utilisé les requêtes suivantes :  
  ```sql
  ALTER sometable [...];
  ALTER sometable [...];
  ```
1. J'ai du modifier les données de la table `NAME` parce que XXX.
