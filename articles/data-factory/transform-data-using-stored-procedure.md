---
title: Przekształcanie danych przy użyciu działania Procedura składowana
description: W tym artykule wyjaśniono, jak używać działania procedury składowanej programu SQL Server do wywoływania procedury składowanej w magazynie danych sql sql z potoku fabryki danych.
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
ms.openlocfilehash: 57bf653aa3f421ae8897c4be661ceef589fcdc06
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418817"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania procedury przechowywanej programu SQL Server w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-stored-proc-activity.md)
> * [Bieżąca wersja](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działania przekształcania danych w [potoku](concepts-pipelines-activities.md) fabryki danych służy do przekształcania i przetwarzania nieprzetworzonych danych do prognoz i szczegółowych informacji. Działanie procedury składowanej jest jednym z działań transformacji, które obsługuje usługa Data Factory. W tym artykule opiera się na artykule [transform danych,](transform-data.md) który przedstawia ogólne omówienie transformacji danych i obsługiwanych działań transformacji w fabryce danych.

> [!NOTE]
> Jeśli jesteś nowy w usłudze Azure Data Factory, przeczytaj [wprowadzenie do usługi Azure Data Factory](introduction.md) i wykonaj samouczek: [Samouczek: przekształć dane](tutorial-transform-data-spark-powershell.md) przed przeczytaniem tego artykułu. 

Działanie procedury składowanej służy do wywoływania procedury składowanej w jednym z następujących magazynów danych w przedsiębiorstwie lub na maszynie wirtualnej platformy Azure(VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Baza danych programu SQL Server.  Jeśli używasz programu SQL Server, zainstaluj środowisko uruchomieniowe integracji hostowanego samodzielnie na tym samym komputerze, na którym znajduje się baza danych, lub na oddzielnym komputerze, który ma dostęp do bazy danych. Środowisko uruchomieniowe integracji hostowanego samodzielnie jest składnikiem łączącym źródła danych lokalnie/na maszynie Wirtualnej platformy Azure z usługami w chmurze w bezpieczny i zarządzany sposób. Zobacz [artykuł środowiska uruchomieniowego integracji hostowanego samodzielnie,](create-self-hosted-integration-runtime.md) aby uzyskać szczegółowe informacje.

> [!IMPORTANT]
> Podczas kopiowania danych do bazy danych SQL Sql Database lub programu SQL Server można skonfigurować **sqlsink** w copy activity do wywołania procedury składowanej przy użyciu właściwości **sqlWriterStoredProcedureName.** Aby uzyskać szczegółowe informacje na temat właściwości, zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md). Wywoływanie procedury składowanej podczas kopiowania danych do usługi Azure SQL Data Warehouse przy użyciu działania kopiowania nie jest obsługiwane. Ale można użyć działania procedury składowanej do wywołania procedury składowanej w magazynie danych SQL. 
>
> Podczas kopiowania danych z bazy danych SQL Azure lub programu SQL Server lub usługi Azure SQL Data Warehouse można skonfigurować **sqlsource** w działaniu kopiowania, aby wywołać procedurę składowaną w celu odczytu danych ze źródłowej bazy danych przy użyciu właściwości **sqlReaderStoredProcedureName.** Aby uzyskać więcej informacji, zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md), usługa Azure SQL [Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szczegóły składni
Oto format JSON do definiowania działania procedury składowanej:

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

| Właściwość                  | Opis                              | Wymagany |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nazwa działania                     | Tak      |
| description               | Tekst opisujący, do czego jest używane działanie | Nie       |
| type                      | W przypadku działania procedury składowanej typem działania jest **SqlServerStoredProcedure** | Tak      |
| linkedServiceName         | Odwołanie do **usługi Azure SQL Database** lub usługi Azure SQL Data **Warehouse** lub SQL **Server** zarejestrowanej jako usługa połączona w fabryce danych. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md) | Tak      |
| przechowywaneProcedureName       | Określ nazwę procedury składowanej do wywołania. | Tak      |
| przechowywaneParametryprocedure | Określ wartości parametrów procedury składowanej. Służy `"param1": { "value": "param1Value","type":"param1Type" }` do przekazywania wartości parametrów i ich typu obsługiwanych przez źródło danych. Jeśli chcesz przekazać null dla parametru, użyj `"param1": { "value": null }` (wszystkie małe litery). | Nie       |

## <a name="parameter-data-type-mapping"></a>Mapowanie typów danych parametrów
Typ danych określony dla parametru to typ usługi Azure Data Factory, który jest mapowyny na typ danych w używanym źródle danych. Mapowania typów danych dla źródła danych można znaleźć w obszarze łączników. Niektóre przykłady są

| Źródło danych          | Mapowanie typu danych |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Informacje o błędzie

Gdy procedura składowana nie powiedzie się i zwraca szczegóły błędu, nie można przechwycić informacje o błędzie bezpośrednio w danych wyjściowych działania. Jednak usługa Data Factory pompuje wszystkie zdarzenia uruchamiania działania do usługi Azure Monitor. Wśród zdarzeń, które usługa Data Factory pompuje do usługi Azure Monitor, wypycha tam szczegóły błędu. Można na przykład skonfigurować alerty e-mail z tych zdarzeń. Aby uzyskać więcej informacji, zobacz [Fabryki danych alertów i monitorów przy użyciu usługi Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność strumieniowania Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktywność iskra](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonawcze Bacha w uczeniu maszynowym](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
