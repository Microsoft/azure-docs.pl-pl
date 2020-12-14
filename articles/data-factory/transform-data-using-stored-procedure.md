---
title: Przekształcanie danych za pomocą działania procedury składowanej
description: Wyjaśnia, jak za pomocą działania procedury składowanej SQL Server wywołać procedurę składowaną w magazynie Azure SQL Database/danych z potoku Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: f20af5ea9628dd6c8aa732ac1d09625156eed0c4
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387545"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Przekształcanie danych za pomocą działania procedury składowanej SQL Server w Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-stored-proc-activity.md)
> * [Bieżąca wersja](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Korzystając z działań przekształcania danych w [potoku](concepts-pipelines-activities.md) Data Factory, można przekształcać i przetwarzać dane pierwotne w przewidywania i szczegółowe informacje. Działanie procedury składowanej jest jedną z działań transformacji obsługiwanych przez Data Factory. Ten artykuł jest oparty na artykule [Przekształcanie danych](transform-data.md) , który przedstawia ogólne omówienie transformacji danych i obsługiwanych działań transformacji w Data Factory.

> [!NOTE]
> Jeśli jesteś nowym do Azure Data Factory, przeczytaj artykuł [wprowadzenie do Azure Data Factory](introduction.md) i wykonaj samouczek: [Samouczek: Przekształcanie danych](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

Możesz użyć działania procedury składowanej, aby wywołać procedurę składowaną w jednym z następujących magazynów danych w przedsiębiorstwie lub na maszynie wirtualnej platformy Azure: 

- Azure SQL Database
- Azure Synapse Analytics
- Baza danych SQL Server.  Jeśli używasz SQL Server, zainstaluj własne środowisko Integration Runtime na tym samym komputerze, na którym znajduje się baza danych programu, lub na oddzielnym komputerze, który ma dostęp do bazy danych programu. Self-Hosted Integration Runtime to składnik, który łączy źródła danych lokalnie/na maszynie wirtualnej platformy Azure z usługami w chmurze w bezpieczny i zarządzany sposób. Szczegółowe informacje można znaleźć w artykule [samodzielne środowisko Integration Runtime](create-self-hosted-integration-runtime.md) .

> [!IMPORTANT]
> Podczas kopiowania danych do Azure SQL Database lub SQL Server, można skonfigurować działanie **sqlsink** w działaniu Copy, aby wywołać procedurę składowaną za pomocą właściwości **sqlWriterStoredProcedureName** . Aby uzyskać szczegółowe informacje na temat właściwości, zobacz następujące artykuły dotyczące łącznika: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Wywoływanie procedury składowanej podczas kopiowania danych do usługi Azure Synapse Analytics przy użyciu działania kopiowania nie jest obsługiwane. Można jednak użyć działania procedury składowanej, aby wywołać procedurę składowaną w usłudze Azure Synapse Analytics. 
>
> Podczas kopiowania danych z Azure SQL Database lub SQL Server lub z usługi Azure Synapse Analytics można skonfigurować element **sqlsource** w działaniu Copy, aby wywołać procedurę składowaną w celu odczytania danych ze źródłowej bazy danych przy użyciu właściwości **sqlReaderStoredProcedureName** . Aby uzyskać więcej informacji, zobacz następujące artykuły dotyczące łącznika: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szczegóły składni
Oto format JSON służący do definiowania działania procedury składowanej:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

W poniższej tabeli opisano te właściwości JSON:

| Właściwość                  | Opis                              | Wymagane |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nazwa działania                     | Tak      |
| description               | Tekst opisujący działanie używanego działania | Nie       |
| typ                      | W przypadku działania procedury składowanej typem działania jest **SqlServerStoredProcedure** | Tak      |
| linkedServiceName         | Odnosi się do **Azure SQL Database** lub do usługi **Azure Synapse Analytics** lub **SQL Server** zarejestrowanych jako usługa połączona w Data Factory. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) . | Tak      |
| storedProcedureName       | Określ nazwę procedury składowanej do wywołania. | Tak      |
| storedProcedureParameters | Określ wartości parametrów procedury składowanej. Służy `"param1": { "value": "param1Value","type":"param1Type" }` do przekazywania wartości parametrów i ich typów obsługiwanych przez źródło danych. Jeśli chcesz przekazać wartość null dla parametru, użyj (w `"param1": { "value": null }` przypadku małych liter). | Nie       |

## <a name="parameter-data-type-mapping"></a>Mapowanie typu danych parametru
Typ danych określony dla parametru to typ Azure Data Factory, który jest mapowany na typ danych w używanym źródle danych. Mapowania typu danych dla źródła danych można znaleźć w obszarze łączniki. Przykłady są

| Źródło danych          | Mapowanie typu danych |
| ---------------------|-------------------|
| Azure Synapse Analytics | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |




## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego w usłudze Azure Machine Learning Studio (wersja klasyczna)](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
