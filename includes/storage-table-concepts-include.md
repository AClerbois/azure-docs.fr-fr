## <a name="what-is-table-storage"></a>Qu’est-ce qu’un stockage de table ?
Le stockage de table Azure permet de stocker de grandes quantités de données structurées. Il s’agit d’une banque de données NoSQL qui accepte les appels authentifiés provenant de l’intérieur et de l’extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles. Voici quelques utilisations courantes du stockage de table :

* Stockage des téraoctets de données structurées capables de servir des applications Web
* Stockage des jeux de données ne nécessitant pas de jonctions complexes, de clés étrangères ou de procédures stockées, et pouvant être dénormalisés pour un accès rapide
* Interrogation rapide des données par requête à l’aide d’un index cluster
* Accès aux données avec le protocole OData et les quêtes LINQ avec les bibliothèques WCF Data Service .NET

Vous pouvez utiliser le stockage de table pour stocker et interroger de grands ensembles de données non relationnelles structurées. Vos tables évoluent en même temps que la demande.

## <a name="table-storage-concepts"></a>Concepts de stockage de table
Le stockage de table est composé des éléments suivants :

![Diagramme des composants du stockage de table][Table1]

* **Format d'URL :** le code traite les tables dans un compte à l'aide de ce format d'adresse :   
  http://`<storage account>`.table.core.windows.net/`<table>`  
  
  Vous pouvez traiter les tables Azure directement à l’aide de cette adresse avec le protocole OData. Pour plus d’informations, consultez [OData.org][OData.org].
* **Compte de stockage :** tout accès au stockage Azure s'effectue via un compte de stockage. Pour plus d’informations sur la capacité du compte de stockage, consultez la page [Objectifs de performance et évolutivité du stockage Azure](../articles/storage/common/storage-scalability-targets.md) .
* **Table**: une table est une collection d’entités. Les tables n’appliquent pas de schéma sur les entités, ce qui signifie qu’une seule table peut contenir des entités ayant différents ensembles de propriétés. Le nombre de tables qu’un compte de stockage peut contenir est uniquement limité par la limite de capacité du compte de stockage.
* **Entité**: une entité est un ensemble de propriétés similaire à une ligne de base de données. Une entité peut avoir une taille maximale de 1 Mo.
* **Propriété**: une propriété est une paire nom-valeur. Chaque entité peut inclure jusqu’à 252 propriétés pour stocker des données. Chaque entité dispose également de trois propriétés système qui spécifient une clé de partition, une clé de ligne et un horodatage. Les entités ayant la même clé de partition peuvent être interrogées plus rapidement et insérées ou mises à jour dans des opérations atomiques. La clé de ligne d'une entité est son identificateur unique à l'intérieur d'une partition.

Pour plus de détails sur l’affectation de noms à des tables et les propriétés, consultez la rubrique [Présentation du modèle de données du service de Table (en anglais)](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
