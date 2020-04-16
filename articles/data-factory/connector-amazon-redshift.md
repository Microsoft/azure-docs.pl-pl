---
title: Kopiowanie danych z amazon redshift
description: Dowiedz się, jak kopiować dane z usługi Amazon Redshift do obsługiwanych magazynów danych ujścia przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: ce63da745fb84ebccd57b246fc934f595dd7cda1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418256"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiowanie danych z usługi Amazon Redshift przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-amazon-redshift-connector.md)
> * [Bieżąca wersja](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z usługi Amazon Redshift. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

To złącze Amazon Redshift jest obsługiwane dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z Amazon Redshift do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności ten łącznik Amazon Redshift obsługuje pobieranie danych z redshift przy użyciu kwerendy lub wbudowanej obsługi Redshift UNLOAD.

> [!TIP]
> Aby osiągnąć najlepszą wydajność podczas kopiowania dużych ilości danych z redshift, należy rozważyć użycie wbudowanego redshift UNLOAD przez Amazon S3. Zobacz [Użyj UNLOAD, aby skopiować dane z sekcji Amazon Redshift, aby](#use-unload-to-copy-data-from-amazon-redshift) uzyskać szczegółowe informacje.

## <a name="prerequisites"></a>Wymagania wstępne

* W przypadku kopiowania danych do lokalnego magazynu danych przy użyciu [środowiska uruchomieniowego integracji hostowanego przez użytkownika](create-self-hosted-integration-runtime.md)należy przyznać środowisko wykonawcze integracji (użyj adresu IP komputera) dostęp do klastra Amazon Redshift. Aby uzyskać [instrukcje, zobacz Autoryzowanie dostępu do klastra.](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)
* Jeśli kopiujesz dane do magazynu danych platformy Azure, zobacz [Zakresy adresów IP usługi Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653) dla obliczeń adres IP i zakresy SQL używane przez centra danych platformy Azure.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla łącznika Amazon Redshift.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Amazon Redshift:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **AmazonRedshift** | Tak |
| serwer |Adres IP lub nazwa hosta serwera Amazon Redshift. |Tak |
| port |Numer portu TCP używany przez serwer Amazon Redshift do nasłuchiwać połączeń klientów. |Nie, wartość domyślna to 5439 |
| database |Nazwa bazy danych Amazon Redshift. |Tak |
| nazwa użytkownika |Nazwa użytkownika, który ma dostęp do bazy danych. |Tak |
| hasło |Hasło do konta użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Amazon Redshift.

Aby skopiować dane z usługi Amazon Redshift, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **AmazonRedshiftTable** | Tak |
| Schematu | Nazwa schematu. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tabela | Nazwa tabeli. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. Użyj `schema` `table` i dla nowego obciążenia. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz `RelationalTable` wpisanego zestawu danych, jest on nadal obsługiwany w stanie gotowym do użycia, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift jako źródło

Aby skopiować dane z amazon redshift, ustaw typ źródła w działaniu kopiowania na **AmazonRedshiftSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **AmazonRedshiftSource** | Tak |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. Na przykład: wybierz * z MyTable. |Nie (jeśli określono "nazwa tabela" w zestawie danych) |
| redshiftUnloadSettings | Grupa właściwości podczas korzystania z Amazon Redshift UNLOAD. | Nie |
| s3LinkedServiceName | Odnosi się do Amazon S3 do wykorzystania jako sklep tymczasowy, określając nazwę usługi połączone typu "AmazonS3". | Tak w przypadku korzystania z funkcji UNLOAD |
| nazwa łyżki | Wskaż zasobnik S3 do przechowywania danych tymczasowych. Jeśli nie zostanie podana usługa Data Factory generuje ją automatycznie.  | Tak w przypadku korzystania z funkcji UNLOAD |

**Przykład: Źródło Amazon Redshift w aktywności kopiowania przy użyciu funkcji UNLOAD**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Dowiedz się więcej o tym, jak efektywnie kopiować dane z usługi Amazon Redshift za pomocą funkcji UNLOAD z następnej sekcji.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Kopiowanie danych z usługi Amazon Redshift za pomocą funkcji UNLOAD

[UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) to mechanizm dostarczony przez Amazon Redshift, który może zwolnić wyniki zapytania do jednego lub więcej plików na Amazon Simple Storage Service (Amazon S3). Jest to sposób zalecany przez Amazon do kopiowania dużego zestawu danych z Redshift.

**Przykład: kopiowanie danych z usługi Amazon Redshift do usługi Azure SQL Data Warehouse przy użyciu funkcji UNLOAD, kopia etapowa i PolyBase**

W tym przykładowym przypadku użycia sieka dane z amazon redshift do Amazon S3 skonfigurowane w "redshiftUnloadSettings", a następnie skopiować dane z Amazon S3 do azure blob, jak określono w "stagingSettings", wreszcie użyj PolyBase załadować dane do magazynu danych SQL. Cały format tymczasowy jest obsługiwany przez działanie kopiowania poprawnie.

![Przesunięcie zwięzłe do przepływu pracy kopiowania SQL DW](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapowanie typu danych dla amazon redshift

Podczas kopiowania danych z usługi Amazon Redshift są używane następujące mapowania z typów danych Amazon Redshift do tymczasowych typów danych usługi Azure Data Factory. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych Amazon Redshift | Tymczasowy typ danych fabryki danych |
|:--- |:--- |
| Bigint |Int64 |
| Boolean |Ciąg |
| Char |Ciąg |
| DATE |DateTime |
| Dziesiętnych |Wartość dziesiętna |
| PODWÓJNA PRECYZJA |Double |
| LICZBA CAŁKOWITA |Int32 |
| LICZBA RZECZYWISTA |Single |
| Smallint |Int16 |
| TEKST |Ciąg |
| Sygnatury czasowej |DateTime |
| Varchar |Ciąg |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
