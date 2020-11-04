---
title: SQL Server dane do SQL Database za pomocą procesu nauki o danych w zespole Azure Data Factory
description: Skonfiguruj potok ADF składający się z dwóch działań związanych z migracją danych, które razem regularnie przenoś dane między bazami danych i w chmurze.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02fd6c1d4cbd1c2db287a38e086045042b5f220a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309541"
---
# <a name="move-data-from-a-sql-server-database-to-sql-database-with-azure-data-factory"></a>Przenoszenie danych z bazy danych SQL Server do SQL Database za pomocą Azure Data Factory

W tym artykule pokazano, jak przenieść dane z bazy danych SQL Server do Azure SQL Database za pośrednictwem usługi Azure Blob Storage przy użyciu Azure Data Factory (ADF): Ta metoda jest obsługiwanym starszym podejściem, które ma zalety zreplikowanej kopii przemieszczania, ale [sugerujemy zapoznaj się z naszą stroną migracji danych, aby uzyskać najnowsze opcje](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

W przypadku tabeli podsumowującej różne opcje przenoszenia danych do Azure SQL Database, zobacz [przenoszenie danych do Azure SQL Database Azure Machine Learning](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Wprowadzenie: co to jest ADF i kiedy należy go używać do migrowania danych?
Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze, która organizuje i automatyzuje przenoszenie i transformację danych. Kluczową koncepcją w modelu ADF jest potok. Potok jest logicznym grupą działań, z których każda definiuje akcje do wykonania na danych zawartych w zestawach DataSet. Połączone usługi służą do definiowania informacji wymaganych do Data Factory łączenia się z zasobami danych.

W przypadku usługi ADF Usługa Data Processing istniejąca może składać się z potoków danych, które są wysoce dostępne i zarządzane w chmurze. Te potoki danych mogą być zaplanowane do pozyskiwania, przygotowywania, przekształcania, analizowania i publikowania danych, a także do zarządzania i organizowania złożonych zależności danych i przetwarzania. Rozwiązania można szybko kompilować i wdrażać w chmurze, łącząc rosnącą liczbę źródeł danych lokalnych i w chmurze.

Rozważ użycie funkcji ADF:

* gdy dane wymagają ciągłego migrowania w scenariuszu hybrydowym, który uzyskuje dostęp do zasobów lokalnych i w chmurze
* gdy dane wymagają przekształceń lub podczas migrowania są do niej dodawane logiki biznesowej.

ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które regularnie zarządzają przenoszeniem danych. Funkcja ADF oferuje również inne funkcje, takie jak obsługa złożonych operacji. Aby uzyskać więcej informacji na temat ADF, zapoznaj się z dokumentacją w [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>Scenariusz
Skonfigurujemy potok ADF składający się z dwóch działań związanych z migracją danych. Jednocześnie przenosimy dane codziennie między SQL Server bazą danych i Azure SQL Database. Te dwie działania są następujące:

* Kopiowanie danych z bazy danych SQL Server na konto usługi Azure Blob Storage
* Skopiuj dane z konta usługi Azure Blob Storage, aby Azure SQL Database.

> [!NOTE]
> Przedstawione tutaj kroki zostały dostosowane z bardziej szczegółowego samouczka dostarczonego przez zespół usługi ADF: [Kopiowanie danych z bazy danych SQL Server do usługi Azure Blob Storage](../../data-factory/tutorial-hybrid-copy-portal.md) odwołania do odpowiednich sekcji tego tematu są udostępniane w razie potrzeby.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Wymagania wstępne
W tym samouczku założono, że masz:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage**. Używasz konta usługi Azure Storage do przechowywania danych w tym samouczku. Jeśli nie masz konta usługi Azure Storage, zobacz artykuł [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) . Po utworzeniu konta magazynu należy uzyskać klucz konta używany do uzyskiwania dostępu do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Dostęp do **Azure SQL Database**. Jeśli konieczne jest skonfigurowanie Azure SQL Database, temat [wprowadzenie z Microsoft Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) zawiera informacje na temat sposobu aprowizacji nowego wystąpienia Azure SQL Database.
* Zainstalowano i skonfigurowano **Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [How to Install and configure Azure PowerShell](/powershell/azure/).

> [!NOTE]
> Ta procedura używa [Azure Portal](https://portal.azure.com/).
>
>

## <a name="upload-the-data-to-your-sql-server-instance"></a><a name="upload-data"></a> Przekaż dane do wystąpienia SQL Server
Używamy [zestawu danych NYC taksówki](https://chriswhong.com/open-data/foil_nyc_taxi/) w celu zademonstrowania procesu migracji. Zestaw danych NYC taksówki jest dostępny w tym wpisie w przypadku [danych o taksówkach](https://www.andresmh.com/nyctaxitrips/)w usłudze Azure Blob Storage NYC. Dane mają dwa pliki, plik trip_data.csv, zawierający szczegóły dotyczące wyjazdu i plik trip_far.csv, który zawiera szczegółowe informacje o opłatach za każdą podróż. Przykład i opis tych plików znajdują się w [opisie zestawu danych NYC taksówki](sql-walkthrough.md#dataset).

Można dostosować procedurę podaną tutaj do zestawu własnych danych lub postępować zgodnie z instrukcjami w sposób opisany przy użyciu zestawu danych NYC taksówki. Aby przekazać zestaw danych NYC taksówki do bazy danych SQL Server, wykonaj procedurę opisaną w temacie [zbiorcze Importowanie danych do SQL Server Database](sql-walkthrough.md#dbload).

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a> Tworzenie Azure Data Factory
Poniżej [Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory)przedstawiono instrukcje dotyczące tworzenia nowych Azure Data Factory i grupy zasobów w [Azure Portal](https://portal.azure.com/) . Nazwij nowe wystąpienie ADF *adfdsp* i Nazwij grupę zasobów utworzoną *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Zainstaluj i skonfiguruj Integration Runtime Azure Data Factory
Integration Runtime to infrastruktura integracji danych zarządzana przez klienta używana przez program Azure Data Factory do zapewniania możliwości integracji danych w różnych środowiskach sieciowych. To środowisko uruchomieniowe wcześniej nosiła nazwę "Zarządzanie danymi Gateway".

Aby skonfigurować, [postępuj zgodnie z instrukcjami dotyczącymi tworzenia potoku](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Tworzenie połączonych usług w celu łączenia się z zasobami danych
Połączona usługa definiuje informacje, które są konieczne w celu nawiązania połączenia z zasobem danych Azure Data Factory. W tym scenariuszu istnieją trzy zasoby, dla których są konieczne usługi połączone:

1. SQL Server lokalne
2. Azure Blob Storage
3. Azure SQL Database

Procedury krok po kroku dotyczące tworzenia połączonych usług są dostępne w temacie [Tworzenie połączonych usług](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Definiowanie i tworzenie tabel w celu określenia sposobu uzyskiwania dostępu do zestawów danych
Utwórz tabele określające strukturę, lokalizację i dostępność zestawów danych za pomocą poniższych procedur opartych na skryptach. Pliki JSON są używane do definiowania tabel. Aby uzyskać więcej informacji na temat struktury tych plików, zobacz [zestawy danych](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Przed uruchomieniem polecenia `Add-AzureAccount` cmdlet [New-AzureDataFactoryTable](/previous-versions/azure/dn835096(v=azure.100)) należy wykonać polecenie cmdlet, aby upewnić się, że dla wykonania polecenia wybrano odpowiednią subskrypcję platformy Azure. Aby uzyskać dokumentację tego polecenia cmdlet, zobacz [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-3.7.0).
>
>

W definicjach opartych na notacji JSON w tabelach są używane następujące nazwy:

* **Nazwa tabeli** w SQL Server jest *nyctaxi_data*
* **nazwa kontenera** na koncie usługi Azure Blob Storage to *ContainerName*

Dla tego potoku APD jest wymaganych trzy definicje tabel:

1. [Tabela lokalna SQL](#adf-table-onprem-sql)
2. [Tabela obiektów BLOB](#adf-table-blob-store)
3. [Tabela SQL Azure](#adf-table-azure-sql)

> [!NOTE]
> Te procedury służą do definiowania i tworzenia działań ADF przy użyciu Azure PowerShell. Te zadania można także wykonać przy użyciu Azure Portal. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zestawów danych](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>Tabela lokalna SQL
Definicja tabeli dla SQL Server jest określona w następującym pliku JSON:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Nazwy kolumn nie zostały uwzględnione w tym miejscu. W tym miejscu możesz wybrać nazwy kolumn, dołączając je tutaj (Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Dokumentacja usługi ADF](../../data-factory/copy-activity-overview.md) .

Skopiuj definicję JSON tabeli do pliku o nazwie *onpremtabledef.jsw* pliku i Zapisz ją w znanej lokalizacji (przyjęto, że *C:\temp\onpremtabledef.jsna* ). Utwórz tabelę w usłudze ADF przy użyciu następującego polecenia cmdlet Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json
```


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Tabela obiektów BLOB
Definicja tabeli dla wyjściowej lokalizacji obiektu BLOB znajduje się w następujących tematach (mapuje dane odebrane z lokalnego na obiekt blob platformy Azure):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Skopiuj definicję JSON tabeli do pliku o nazwie *bloboutputtabledef.jsw* pliku i Zapisz ją w znanej lokalizacji (przyjęto, że *C:\temp\bloboutputtabledef.jsna* ). Utwórz tabelę w usłudze ADF przy użyciu następującego polecenia cmdlet Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json
```

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>Tabela SQL Azure
Definicja tabeli dla danych wyjściowych usługi SQL Azure jest następująca (ten schemat mapuje dane pochodzące z obiektu BLOB):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Skopiuj definicję JSON tabeli do pliku o nazwie *AzureSqlTable.jsw* pliku i Zapisz ją w znanej lokalizacji (przyjęto, że *C:\temp\AzureSqlTable.jsna* ). Utwórz tabelę w usłudze ADF przy użyciu następującego polecenia cmdlet Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json
```


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Definiowanie i tworzenie potoku
Określ działania należące do potoku i Utwórz potok z poniższymi procedurami opartymi na skrypcie. Plik JSON jest używany do definiowania właściwości potoku.

* Skrypt zakłada, że **Nazwa potoku** to *AMLDSProcessPipeline*.
* Należy również pamiętać, że ustawimy okresowość potoku, która ma być wykonywana codziennie, i Użyj domyślnego czasu wykonania zadania (12 godzin UTC).

> [!NOTE]
> Poniższe procedury służą do definiowania i tworzenia potoku ADF przy użyciu Azure PowerShell. To zadanie można także wykonać przy użyciu Azure Portal. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie potoku](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Korzystając z podanych wcześniej definicji tabeli, definicja potoku dla ADF jest określana w następujący sposób:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has two activities: the first one copies data from SQL Server to Azure Blob, and the second one copies from Azure Blob to Azure Database Table",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from SQL Server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Skopiuj tę definicję JSON potoku do pliku o nazwie *pipelinedef.jsw* pliku i Zapisz ją w znanej lokalizacji (przyjęto, że *C:\temp\pipelinedef.jsna* ). Utwórz potok w podajniku APD przy użyciu następującego polecenia cmdlet Azure PowerShell:

```azurepowershell
New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json
```


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Uruchamianie potoku
Potok można teraz uruchomić przy użyciu następującego polecenia:

```azurepowershell
Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline
```

Wartości parametrów *StartDate* i *EndDate* muszą zostać zastąpione rzeczywistymi datami, między którymi ma zostać uruchomiony potok.

Po wykonaniu potoku powinno być możliwe wyświetlenie danych w kontenerze wybranym dla obiektu BLOB, jednego pliku dziennie.

Nie wykorzystano funkcji zapewnianych przez funkcję ADF w przyrostowym potoku danych. Aby uzyskać więcej informacji na temat tego, jak to zrobić, i innych możliwości zapewnianych przez funkcję ADF, zobacz [dokumentację ADF](https://azure.microsoft.com/services/data-factory/).