# SQL-Project
# Database Project for "E-commerce clothing shop"

The scope of this project is to use all the SQL knowledge gained throught the Software Testing course and apply them in practice.

**Application under test:** "E-commerce clothing shop"

**Tools used:** MySQL Workbench

**Database description:** This project represents a database model for an online clothing store, designed to efficiently manage customers, products, orders, and the relationships between them.
The database includes tables for customers (with information such as name, email, phone), products (name, price, stock, description), orders placed by customers, and an intermediary table that establishes a many-to-many relationship between orders and products, recording the quantity of each product in an order.
Through this model, the store aims to support order tracking, stock management, and analysis of customer and product data, ensuring a robust and scalable structure for the online application.
Regarding the DDL aspect, we started by creating the tables "clienti" (customers), "produse" (products), "comenzi" (orders), and "comenzi_produse" (the latter representing a many-to-many relationship between orders and products).

## Database Schema 
  
You can find below the database schema that was generated through Reverse Engineer and which contains all the tables and the relationships between them.

The tables are connected in the following way:

- **comenzi**  is connected with **clienti** through a **one-to-many** relationship which was implemented through **clienti.id_client** as a primary key and **comenzi.id_client** as a foreign key
- **produse_comandate**  is connected with **comenzi** through a **many-to-one** relationship which was implemented through **comenzi.id_comanda** as a primary key and **produse_comandate.id_comanda** as a foreign key
- **produse_comandate**  is connected with **produse_v2** through a **many-to-one** relationship which was implemented through **produse_v2.id_produs** as a primary key and **produse_comandate.id_produs** as a foreign key
- this structure implements a **many-to-many** relationship between **comenzi** and **produse_v2** via the intermediate table **produse_comandate**
## Database Queries

### DDL (Data Definition Language)

  The following instructions were written in the scope of CREATING the structure of the database (CREATE INSTRUCTIONS)

  CREATE DATABASE  magazinonlineproiect1;
USE magazinonlineproiect1;

-- creearea tabelelor --
CREATE TABLE clienti (
    id_client INT PRIMARY KEY AUTO_INCREMENT,
    nume VARCHAR(50),
    email VARCHAR(100),
    telefon CHAR(10)
);

CREATE TABLE produse (
    id_produs INT PRIMARY KEY AUTO_INCREMENT,
    denumire VARCHAR(100),
    pret DECIMAL(10,2),
    cantitate_stoc INT,
    descriere TEXT,
    culoare VARCHAR(30)
);

CREATE TABLE comenzi (
    id_comanda INT PRIMARY KEY AUTO_INCREMENT,
    id_client INT,
    data_comanda DATETIME,
    FOREIGN KEY (id_client) REFERENCES clienti(id_client)
);

-- Tabel many-to-many între comenzi și produse --
CREATE TABLE produse_comandate (
    id_comanda INT,
    id_produs INT,
    cantitate INT,
    PRIMARY KEY (id_comanda, id_produs),
    FOREIGN KEY (id_comanda) REFERENCES comenzi(id_comanda),
    FOREIGN KEY (id_produs) REFERENCES produse(id_produs)
);


 
  After the database and the tables have been created, a few ALTER instructions were written in order to update the structure of the database, as described below:

--  Schimbare nume tabelă
RENAME TABLE produse TO produse_v2;

-- Adăugare coloană
ALTER TABLE produse_v2
ADD descriere TEXT;

--  Ștergere coloană
ALTER TABLE produse_v2
DROP COLUMN descriere;

--  Redenumire coloană
ALTER TABLE produse_v2
CHANGE pret pret_nou DECIMAL(12,2);

--  Adăugare proprietăți coloane (auto-increment pentru id_client)
ALTER TABLE clienti
MODIFY COLUMN id_client INT NOT NULL AUTO_INCREMENT;

-- Modificare tip de date și poziție coloană
ALTER TABLE clienti
MODIFY COLUMN telefon VARCHAR(15) AFTER email;

  
### DML (Data Manipulation Language)

  In order to be able to use the database I populated the tables with various data necessary in order to perform queries and manipulate the data. 
  In the testing process, this necessary data is identified in the Test Design phase and created in the Test Implementation phase. 

  Below you can find all the insert instructions that were created in the scope of this project:

-- Insert cu coloane explicite: specificarea coloanelor pe care sa se faca insert --
INSERT INTO clienti (nume, email, telefon) VALUES 
('Ion Popescu', 'ionp@mail.com', '0722334455'),
('Maria Ionescu', 'maria@mail.com', '0733445566'),
('Andrei Georgescu', 'andrei@mail.com', '0722111222');

-- Insert pe toate coloanele --
INSERT INTO produse
VALUES 
(NULL, 'Hanorac', 199.99, 15, 'Hanorac bumbac 100%', 'albastru'),
(NULL, 'Pantaloni', 149.99, 20, 'Pantaloni din bumbac', 'negru'),
(NULL, 'Tricou', 49.99, 50, 'Tricou casual', 'alb');

-- Insert comenzi --
INSERT INTO comenzi (id_client, data_comanda) VALUES 
(1, '2025-05-26 10:30:00'),
(2, '2025-05-27 11:00:00');

-- Insert many-to-many (produse comandate) --
INSERT INTO produse_comandate (id_comanda, id_produs, cantitate) VALUES
(1, 1, 2),
(1, 3, 1),
(2, 2, 1);


  After the insert, in order to prepare the data to be better suited for the testing process, I updated some data in the following way:

  -- Modificare pret doar pentru produsul cu id_produs=1 --
UPDATE produse
SET pret = 249.99
WHERE id_produs = 1;

-- Modificare cantitate stoc pentru produsul cu id_produs=3 --
UPDATE produse
SET cantitate_stoc = 45
WHERE id_produs = 3;

After the testing process, I deleted the data that was no longer relevant in order to preserve the database clean: 

-- Stergerea produselor comandate pentru clientul 1 --
DELETE FROM produse_comandate
WHERE id_comanda IN (SELECT id_comanda FROM comenzi WHERE id_client = 1);

-- Stergerea comenzilor clientului 1 --
DELETE FROM comenzi
WHERE id_client = 1;

-- Stergerea clientului cu id_client=1 --
DELETE FROM clienti
WHERE id_client = 1;


### DQL (Data Query Language)


In order to simulate various scenarios that might happen in real life I created the following queries that would cover multiple potential real-life situations:

**Inserati aici toate instructiunile de SELECT pe care le-ati scris folosind filtrarile necesare astfel incat sa extrageti doar datele de care aveti nevoie**
**Incercati sa acoperiti urmatoarele:**<br>
-- Select all din produse --
SELECT * FROM produse;

-- Select nume si email clienti --
SELECT nume, email FROM clienti;

-- Filtrare produse cu pret sub 200 --
SELECT * FROM produse
WHERE pret < 200;

-- Filtrare clienti cu email pe mail.com --
SELECT * FROM clienti
WHERE email LIKE '%@mail.com';

-- Filtrare produse cu pret peste 100 si stoc disponibil --
SELECT * FROM produse
WHERE pret > 100 AND cantitate_stoc > 0;

-- Filtrare clienti fara comenzi luna anterioara (subquery) --
SELECT nume FROM clienti
WHERE id_client NOT IN (
    SELECT DISTINCT id_client FROM comenzi
    WHERE MONTH(data_comanda) = MONTH(CURDATE()) - 1
);

-- Inner Join: clienti care au comenzi --
SELECT clienti.nume, comenzi.data_comanda
FROM clienti
INNER JOIN comenzi ON clienti.id_client = comenzi.id_client;

-- Left Join: toti clientii, chiar si cei fara comenzi --
SELECT clienti.nume, comenzi.data_comanda
FROM clienti
LEFT JOIN comenzi ON clienti.id_client = comenzi.id_client;

-- Right Join: produse cu cantitate comandata --
SELECT produse.denumire, produse_comandate.cantitate
FROM produse
RIGHT JOIN produse_comandate ON produse.id_produs = produse_comandate.id_produs;

-- Cross Join: fiecare client cu fiecare produs --
SELECT clienti.nume, produse.denumire
FROM clienti
CROSS JOIN produse;

-- Top 5 produse cele mai scumpe --
SELECT * FROM produse
ORDER BY pret DESC
LIMIT 5;

-- Functii agregate si GROUP BY + HAVING  --
SELECT id_produs, SUM(cantitate) AS total_vandute
FROM produse_comandate
GROUP BY id_produs
HAVING total_vandute > 1;

-- BONUS SUBQUERY : clienții care nu au făcut comenzi în luna anterioară --
SELECT nume FROM clienti
WHERE id_client NOT IN (
    SELECT DISTINCT id_client FROM comenzi
    WHERE MONTH(data_comanda) = MONTH(CURDATE()) - 1
);

## Conclusions

Throughout this project, I designed and implemented a relational database schema for an online clothing store, focusing on efficiently managing customers, products, orders, and their interrelations. I learned how to create tables with primary and foreign keys to enforce data integrity and model real-world relationships, including many-to-many associations via intermediate tables. The process of writing DDL statements such as CREATE TABLE and ALTER TABLE helped me deepen my understanding of database structure design and schema evolution.

Working with DML commands—INSERT, UPDATE, and DELETE—allowed me to practice manipulating data accurately, respecting constraints and dependencies. The exploration of complex queries using SELECT with various filters, joins, aggregate functions, and subqueries enhanced my ability to retrieve meaningful insights from relational data.

I also gained experience in troubleshooting common database errors related to foreign key constraints, which taught me the importance of order when inserting or deleting dependent data. Additionally, practicing reverse engineering techniques and generating textual reports of database schemas improved my skills in documenting and analyzing existing databases.

Overall, this project was a comprehensive introduction to designing, populating, maintaining, and querying a relational database system, equipping me with practical skills that are essential for real-world database management and development.

