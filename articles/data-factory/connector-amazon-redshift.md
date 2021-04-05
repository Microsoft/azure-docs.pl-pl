---
title: Kopiowanie danych z usługi Amazon RedShift
description: Informacje o sposobie kopiowania danych z usługi Amazon RedShift do obsługiwanych magazynów danych ujścia przy użyciu Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 9441885766dad97dfc237ab81a59710245bf13ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364260"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiowanie danych z usługi Amazon RedShift przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-amazon-redshift-connector.md)
> * [Bieżąca wersja](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z usługi Amazon RedShift. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Amazon RedShift jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi Amazon RedShift można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W każdym przypadku ten łącznik usługi Amazon RedShift obsługuje pobieranie danych z usługi RedShift przy użyciu zapytania lub wbudowanej obsługi ZWALNIAnia RedShift.

> [!TIP]
> Aby osiągnąć najlepszą wydajność podczas kopiowania dużych ilości danych z RedShift, należy rozważyć użycie wbudowanej usługi RedShift UNLOAD za pośrednictwem usługi Amazon S3. Aby uzyskać szczegółowe informacje, zobacz sekcję korzystanie z usługi [Unload w celu skopiowania danych z usługi Amazon RedShift](#use-unload-to-copy-data-from-amazon-redshift) .

## <a name="prerequisites"></a>Wymagania wstępne

* W przypadku kopiowania danych do lokalnego magazynu danych przy użyciu [Samoobsługowego Integration Runtime](create-self-hosted-integration-runtime.md)Udziel Integration Runtime (Użyj adresu IP maszyny) dostępu do klastra Amazon RedShift. Aby uzyskać instrukcje [, zobacz Autoryzuj dostęp do klastra](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) .
* Jeśli kopiujesz dane do magazynu danych platformy Azure, zobacz [zakres adresów IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) dla adresu IP obliczeń i zakresów SQL używanych przez centra danych platformy Azure.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Amazon RedShift.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Amazon RedShift są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **AmazonRedshift** | Tak |
| serwer |Adres IP lub nazwa hosta serwera Amazon RedShift. |Tak |
| port |Numer portu TCP używanego przez serwer Amazon RedShift do nasłuchiwania połączeń klientów. |Nie, wartość domyślna to 5439 |
| database |Nazwa bazy danych Amazon RedShift. |Tak |
| nazwa użytkownika |Nazwa użytkownika, który ma dostęp do bazy danych programu. |Tak |
| hasło |Hasło konta użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Amazon RedShift.

Aby skopiować dane z usługi Amazon RedShift, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **AmazonRedshiftTable** | Tak |
| schema | Nazwa schematu. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tabela | Nazwa tabeli. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. Użyj `schema` i `table` dla nowego obciążenia. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

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

Jeśli używasz `RelationalTable` określonego zestawu danych, jest on nadal obsługiwany w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Amazon RedShift.

### <a name="amazon-redshift-as-source"></a>Amazon RedShift jako źródło

Aby skopiować dane z Amazon RedShift, ustaw typ źródła w działaniu Copy na **AmazonRedshiftSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **AmazonRedshiftSource** | Tak |
| query |Użyj zapytania niestandardowego do odczytywania danych. Na przykład: select * from MyTable. |Nie (Jeśli określono "TableName" w zestawie danych) |
| redshiftUnloadSettings | Grupa właściwości podczas korzystania z narzędzia Amazon RedShift UNLOAD. | Nie |
| s3LinkedServiceName | Odwołuje się do usługi Amazon S3, która ma być używana jako magazyn tymczasowy, określając nazwę połączonej usług typu "AmazonS3". | Tak, jeśli używasz opcji UNLOAD |
| zasobnikname | Wskaż zasobnik S3, aby przechowywać dane pośrednie. Jeśli nie zostanie podany, Usługa Data Factory automatycznie wygeneruje ją.  | Tak, jeśli używasz opcji UNLOAD |

**Przykład: Źródło Amazon RedShift w działaniu kopiowania przy użyciu UNLOAD**

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

Dowiedz się więcej na temat sposobu korzystania z usługi UNLOAD w celu wydajnego kopiowania danych z usługi Amazon RedShift z kolejnej sekcji.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Korzystanie z usługi UNLOAD do kopiowania danych z usługi Amazon RedShift

[Unload](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) to mechanizm zapewniany przez usługę Amazon RedShift, który może zwolnić wyniki zapytania do jednego lub kilku plików w usłudze Amazon Simple Storage Service (Amazon S3). Jest to sposób zalecane przez Amazon do kopiowania dużych zestawów danych z RedShift.

**Przykład: kopiowanie danych z Amazon RedShift do usługi Azure Synapse Analytics przy użyciu narzędzia UNLOAD, kopii etapowej i bazy danych**

W przypadku tego przykładowego przypadku użycia działanie Copy powoduje odładowanie danych z Amazon RedShift do Amazon S3 zgodnie z konfiguracją w "redshiftUnloadSettings", a następnie skopiowanie danych z usługi Amazon S3 do obiektu blob platformy Azure zgodnie z opisem w "stagingSettings", a następnie użycie bazy danych na platformie Azure Synapse. Cały format tymczasowy jest obsługiwany przez działanie kopiowania prawidłowo.

![Przepływ pracy kopiowania RedShift do usługi Azure Synapse Analytics](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapowanie typu danych dla Amazon RedShift

Podczas kopiowania danych z usługi Amazon RedShift następujące mapowania są używane z typów danych Amazon RedShift do Azure Data Factory danych pośrednich. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Amazon RedShift — typ danych | Typ danych pośrednich fabryki danych |
|:--- |:--- |
| BIGINT |Int64 |
| TYPU |Ciąg |
| DELIKATN |Ciąg |
| DATE |DateTime |
| DOKŁADNOŚCI |Liczba dziesiętna |
| PODWÓJNA PRECYZJA |Double |
| LICZBA CAŁKOWITA |Int32 |
| LICZBA RZECZYWISTA |Pojedynczy |
| SMALLINT |Int16 |
| TEKST |Ciąg |
| TIMESTAMP |DateTime |
| VARCHAR |Ciąg |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
