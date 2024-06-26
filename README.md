# BIG QUERY TIPS

## Table des matières
[1- Partitionnement de table de données dans Big Query](https://cloud.google.com/bigquery/docs/partitioned-tables?hl=fr)    

### Supports docs : 
[1- Présentation des tables partitionnées](https://cloud.google.com/bigquery/docs/partitioned-tables?hl=fr)  


### 1°) Partitionnement d'une table de données dans Big Query  
Pour réaliser, une partition de données très facilement sur big query quand vous avez un grand volume de données, il suffit de vous assurer que le nombre de partitions et facilement chargeable ou téléchargeable dans un système de données.
Pour être plus précis, supposons que vous avez 100 000 lignes et que vous voulez les télécharger en locale vos données. Dans ce cas ci, vous pouvez partitionné vos données par 5, ce qui fera, 20 000 lignes par partition en moyenne.
Ce qui fait, lors du téléchargement sous Excel, nous sommes sûrs de ne pas perdre des données.  

Voici donc, comment procéder :   
*1- Partition de données par le nombre (N) de partions souhaitées*  
*2- Export des données par les ID de partitions.*

**1- Partition de données par le nombre (N) de partions souhaitées :**  
```sql
CREATE OR REPLACE TABLE `votre_ensemble_de_donnees.votre_table_de_partitions_qui_sera_creer`
PARTITION BY RANGE_BUCKET(export_id, GENERATE_ARRAY(0, 5, 1))  --N=5, Le nombre de partitions est (N-1) <--> 5-1
CLUSTER BY export_id
AS (
  SELECT *, CAST(FLOOR(50*RAND()) AS INT64) AS export_id
FROM `la_table_que_vous_souhaitez_partitionner`
);
```  

**2- Export des données par les ID (N) de partions :**
```sql
--Partition des données ;
SELECT * EXCEPT(export_id)
FROM `votre_ensemble_de_donnees.votre_table_de_partitions_qui_sera_creer`

WHERE export_id = 0;  
--WHERE export_id = 1;  
--WHERE export_id = 2;  
--WHERE export_id = 3;  
--WHERE export_id = 4;  
--WHERE export_id = 5;    
```



