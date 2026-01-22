# README - Projet R3.10 : Gestion de Stock SQL

## 1. Création de la Structure et Initialisation
CREATE DATABASE GestionStock CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE GestionStock;

CREATE TABLE typeProduit (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nom VARCHAR(50)
);

CREATE TABLE produit (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nom VARCHAR(50),
    stock INT,
    prix DECIMAL(10,2),
    type_id INT,
    FOREIGN KEY (type_id) REFERENCES typeProduit(id)
);

CREATE TABLE User (
    id INT PRIMARY KEY AUTO_INCREMENT,
    pseudo VARCHAR(50),
    role VARCHAR(50)
);

CREATE TABLE Facture (
    id INT PRIMARY KEY AUTO_INCREMENT,
    date_facture DATE,
    montant DECIMAL(10,2)
);

## 2. Privilèges et Sécurité
CREATE USER 'admin_app'@'%' IDENTIFIED BY 'Admin#2025';
GRANT ALL PRIVILEGES ON GestionStock.* TO 'admin_app'@'%';

CREATE USER 'lecteur'@'%' IDENTIFIED BY 'Read#2025';
GRANT SELECT ON GestionStock.* TO 'lecteur'@'%';

CREATE USER 'api_user'@'127.0.0.1' IDENTIFIED BY 'ApiUser#2025';
GRANT SELECT, INSERT, UPDATE ON GestionStock.* TO 'api_user'@'127.0.0.1';

FLUSH PRIVILEGES;

## 3. Requêtes Métier (Analyses)
-- Stock total par catégorie
SELECT tp.nom, SUM(p.stock) AS total_stock FROM produit p JOIN typeProduit tp ON p.type_id = tp.id GROUP BY tp.nom;

-- Valeur marchande totale
SELECT SUM(stock * prix) AS valeur_totale FROM produit;

-- Produit le plus cher par catégorie
SELECT nom, prix, type_id FROM produit p1 WHERE prix = (SELECT MAX(prix) FROM produit p2 WHERE p1.type_id = p2.type_id);

## 4. Exploitation et Sauvegarde
-- Sauvegarde : mysqldump -u admin_app -p GestionStock > gestionstock_backup.sql
-- Restauration : mysql -u admin_app -p GestionStock < gestionstock_backup.sql

## 5. Démonstration et Évolutions
CREATE TABLE historique (id INT PRIMARY KEY AUTO_INCREMENT, action VARCHAR(50), date_action DATETIME);
ALTER TABLE typeProduit ADD description TEXT;
CREATE VIEW vue_stock AS SELECT p.nom, tp.nom AS categorie FROM produit p JOIN typeProduit tp ON p.type_id = tp.id;

---
Rendu par : Maxence Boyer - BUT RT 2 - 2026
