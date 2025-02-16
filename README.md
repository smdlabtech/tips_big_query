# 🚀 BIG QUERY TIPS  
![BigQuery](https://img.shields.io/badge/Google_Cloud-BigQuery-blue?style=flat&logo=google-cloud)
![SQL](https://img.shields.io/badge/SQL-Data_Partitioning-orange?style=flat&logo=mysql)
![Data Engineering](https://img.shields.io/badge/Data_Engineering-Scalability-green?style=flat&logo=databricks)

---

## 📖 Table des matières  
1. [Partitionnement de table de données dans BigQuery](https://cloud.google.com/bigquery/docs/partitioned-tables?hl=fr)  

📄 **Supports documentaires**  
- [📌 Présentation des tables partitionnées](https://cloud.google.com/bigquery/docs/partitioned-tables?hl=fr)  

---

## 📊 1°) Partitionnement d'une table de données dans BigQuery  

Le **partitionnement de table** dans **BigQuery** est essentiel pour **gérer des volumes massifs de données** et optimiser les performances de requêtage.  

📌 **Pourquoi partitionner ?**  
- 🔹 Facilite l’exportation et le chargement des données  
- 🔹 Améliore les performances des requêtes  
- 🔹 Réduit le coût des analyses en limitant la lecture de données inutiles  

### 📌 Exemple d'utilisation  

💡 **Cas d'usage** : Supposons que vous ayez une table contenant **100 000 lignes**, et que vous souhaitez **exporter les données sans perte** dans Excel.  
➡️ **Solution** : Partitionner les données en **5 sous-ensembles** de **20 000 lignes**.  

---

### 📂 Étapes du Partitionnement  

✅ **1. Création d’une table partitionnée par un ID de partition**  
✅ **2. Export des données via les ID de partitions**  

---

### 🛠️ 1️⃣ Création d'une table partitionnée  
Nous utilisons **RANGE_BUCKET** pour diviser les données en **5 partitions** :  

```sql
CREATE OR REPLACE TABLE `votre_ensemble_de_donnees.votre_table_de_partitions_qui_sera_creer`
PARTITION BY RANGE_BUCKET(export_id, GENERATE_ARRAY(0, 5, 1))  --N=5, Le nombre de partitions est (N-1) <--> 5-1
CLUSTER BY export_id
AS (
  SELECT *, CAST(FLOOR(50*RAND()) AS INT64) AS export_id
  FROM `la_table_que_vous_souhaitez_partitionner`
);
```

📌 **Explication :**  
- `PARTITION BY RANGE_BUCKET(export_id, GENERATE_ARRAY(0, 5, 1))` → Création de **5 partitions**  
- `CLUSTER BY export_id` → Regroupement des données par **ID de partition**  
- `CAST(FLOOR(50*RAND()) AS INT64) AS export_id` → Génération aléatoire d'IDs de partition  

---

### 📂 2️⃣ Export des données par partition  

Vous pouvez ensuite **exporter les données** partitionnées via leurs **ID** respectifs :  

```sql
-- Sélection des données en excluant l'export_id
SELECT * EXCEPT(export_id)
FROM `votre_ensemble_de_donnees.votre_table_de_partitions_qui_sera_creer`
WHERE export_id = 0;  

--WHERE export_id = 1;  
--WHERE export_id = 2;  
--WHERE export_id = 3;  
--WHERE export_id = 4;  
--WHERE export_id = 5;    
```

📌 **Explication :**  
- L’ID `export_id` sert de clé de partition.  
- Vous pouvez exporter chaque partition **séparément** en changeant `export_id`.  

---

## 🎯 Avantages de cette approche  

✅ **Optimisation des requêtes** - Lecture rapide des partitions nécessaires uniquement  
✅ **Réduction des coûts** - Évite les scans complets inutiles  
✅ **Facilité d'exportation** - Téléchargement **séquentiel** sans surcharge mémoire  

---

✨ Développé par SMD Lab Tech  
📧 Contact : smdlabtech@gmail.com  
📜 **Licence** : Open-Source    
