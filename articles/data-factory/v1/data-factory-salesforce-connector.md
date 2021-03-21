---
title: Przenoszenie danych z usługi Salesforce przy użyciu Data Factory
description: Dowiedz się więcej na temat przenoszenia danych z usługi Salesforce przy użyciu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ccc20f415d13356de755af5d1d3afc5b29de72f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387057"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Przenoszenie danych z usługi Salesforce przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-salesforce-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-salesforce.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik usług Salesforce w wersji 2](../connector-salesforce.md).

W tym artykule opisano, jak można użyć działania kopiowania w usłudze Azure Data Factory w celu skopiowania danych z usługi Salesforce do dowolnego magazynu danych wymienionego w kolumnie ujścia w tabeli [obsługiwane źródła i ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . W tym artykule opisano informacje o [działaniach związanych z przenoszeniem danych](data-factory-data-movement-activities.md) , które przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania i obsługiwanych kombinacji magazynu danych.

Azure Data Factory obecnie obsługuje tylko przeniesienie danych z usługi Salesforce do [obsługiwanych magazynów danych ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats), ale nie obsługuje przemieszczania danych z innych magazynów danych do usługi Salesforce.

## <a name="supported-versions"></a>Obsługiwane wersje
Ten łącznik obsługuje następujące wersje usług Salesforce: Developer Edition, Professional Edition, Enterprise Edition i Unlimited Edition. Obsługuje natomiast kopiowanie z usług Salesforce, piaskownicy i domeny niestandardowej.

## <a name="prerequisites"></a>Wymagania wstępne
* Uprawnienie interfejsu API musi być włączone. Zobacz [Jak mogę włączyć dostęp do interfejsu API w usłudze Salesforce według zestawu uprawnień?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Aby skopiować dane z usługi Salesforce do lokalnych magazynów danych, w środowisku lokalnym musi być zainstalowana co najmniej Zarządzanie danymi Brama 2,0.

## <a name="salesforce-request-limits"></a>Limity żądań usługi Salesforce
Usługi Salesforce mają limity dla obu żądań interfejsu API i współbieżnych żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczba współbieżnych żądań przekracza limit, ograniczanie odbywa się i zobaczysz błędy losowe.
- Jeśli łączna liczba żądań przekracza limit, konto usługi Salesforce zostanie zablokowane przez 24 godziny.

W obu scenariuszach może być również wyświetlany błąd "REQUEST_LIMIT_EXCEEDED". Aby uzyskać szczegółowe informacje, zobacz sekcję "limity żądań interfejsu API" w artykule [limity deweloperów usługi Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) .

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane z usługi Salesforce przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z usług Salesforce, zobacz [przykład JSON: kopiowanie danych z usług Salesforce do usługi Azure BLOB w](#json-example-copy-data-from-salesforce-to-azure-blob) tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla usługi Salesforce:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla połączonej usługi Salesforce.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi być ustawiona na wartość: **Salesforce**. |Tak |
| environmentUrl | Określ adres URL wystąpienia usługi Salesforce. <br><br> -Wartość domyślna to "https: \/ /login.Salesforce.com". <br> -Aby skopiować dane z piaskownicy, określ " https://test.salesforce.com ". <br> -Aby skopiować dane z domeny niestandardowej, określ, na przykład "https://[domena]. my. Salesforce. com". |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak |
| hasło |Określ hasło dla konta użytkownika. |Tak |
| Obiektu |Określ token zabezpieczający dla konta użytkownika. Zobacz [pobieranie tokenu zabezpieczającego](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) , aby uzyskać instrukcje dotyczące resetowania/pobierania tokenu zabezpieczającego. Aby uzyskać ogólne informacje na temat tokenów zabezpieczających, zobacz [zabezpieczenia i interfejs API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych, są podobne do wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itd.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja typeProperties zestawu danych typu **relacyjnego** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w usłudze Salesforce. |Nie (Jeśli określono **zapytanie** o **RelationalSource** ) |

> [!IMPORTANT]
> Część "__c" nazwy interfejsu API jest wymagana dla dowolnego obiektu niestandardowego.

![Zrzut ekranu przedstawia szczegóły niestandardowej definicji obiektu, gdzie można zobaczyć nazwy P I obiektów niestandardowych.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania, z drugiej strony, różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

W działaniu kopiowania, gdy źródłem jest typ **RelationalSource** (w tym Salesforce), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Zapytanie dotyczące języka SQL-92 lub zapytania o [obiekt SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Na przykład: `select * from MyTable__c`. |Nie (Jeśli określono wartość **TableName** **zestawu danych** ) |

> [!IMPORTANT]
> Część "__c" nazwy interfejsu API jest wymagana dla dowolnego obiektu niestandardowego.

![Zrzut ekranu przedstawia pola niestandardowe & relacje, w których można zobaczyć nazwy P I obiektów niestandardowych.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Porady dotyczące zapytań
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Pobieranie danych przy użyciu klauzuli WHERE w kolumnie DateTime
W przypadku określenia zapytania SOQL lub SQL należy zwrócić uwagę na różnice w formacie daty/godziny. Na przykład:

* **Przykład SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Przykład SQL**:
    * **Używanie Kreatora kopiowania do określenia zapytania:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Używanie edycji JSON w celu określenia zapytania (znak ucieczki):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Pobieranie danych z raportu usługi Salesforce
Dane można pobrać z raportów usługi Salesforce, określając zapytanie jako `{call "<report name>"}` , na przykład. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Pobieranie usuniętych rekordów z Kosza usługi Salesforce
Aby wykonać zapytanie dotyczące usuniętych nietrwałych rekordów z Kosza usługi Salesforce, można określić w zapytaniu **wartość "IsDeleted = 1"** . Na przykład

* Aby wykonać zapytanie dotyczące tylko usuniętych rekordów, określ wartość "select * from MyTable__c, **gdzie IsDeleted = 1**"
* Aby zbadać wszystkie rekordy, w tym istniejące i usunięte, określ wartość "select * from MyTable__c, w **której IsDeleted = 0 lub IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Przykład JSON: kopiowanie danych z usług Salesforce do obiektu blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu programu [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych z usług Salesforce do usługi Azure Blob Storage. Dane można jednak kopiować do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

Poniżej przedstawiono Data Factory artefakty, które należy utworzyć, aby zaimplementować scenariusz. Poniższe sekcje zawierają szczegółowe informacje o tych krokach.

* Połączona usługa typu [Salesforce](#linked-service-properties)
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [relacyjnego](#dataset-properties)
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [RelationalSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Połączona usługa Salesforce**

Ten przykład używa połączonej usługi **Salesforce** . Zapoznaj się z sekcją [połączonej usługi Salesforce](#linked-service-properties) , aby poznać właściwości, które są obsługiwane przez tę połączoną usługę. Aby uzyskać instrukcje dotyczące resetowania/pobierania tokenu zabezpieczającego, zobacz [pobieranie tokenu zabezpieczającego](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) .

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Połączona usługa Azure Storage**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**Wejściowy zestaw danych usługi Salesforce**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Ustawienie od **zewnątrz** do **true** informuje usługę Data Factory, że zestaw danych znajduje się poza fabryką danych i nie jest wytwarzany przez działanie w fabryce danych.

> [!IMPORTANT]
> Część "__c" nazwy interfejsu API jest wymagana dla dowolnego obiektu niestandardowego.

![Zrzut ekranu przedstawia szczegóły definicji obiektu niestandardowego, w którym można zobaczyć pojedyncze etykiety, etykiety w liczbie mnogiej, nazwę obiektu i nazwę P.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Potok z działaniem kopiowania**

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **RelationalSource**, a typ **ujścia** to **wartość blobsink**.

Zobacz [właściwości typu RelationalSource](#copy-activity-properties) , aby uzyskać listę właściwości, które są obsługiwane przez RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> Część "__c" nazwy interfejsu API jest wymagana dla dowolnego obiektu niestandardowego.

![Zrzut ekranu przedstawia pola niestandardowe, & relacje z nazwami P I o nazwie A.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapowanie typu dla usługi Salesforce

| Typ usługi Salesforce | . Typ oparty na sieci |
| --- | --- |
| Numer Autokorekty |Ciąg |
| Pole wyboru |Wartość logiczna |
| Waluta |Liczba dziesiętna |
| Date (Data) |DateTime |
| Data/godzina |DateTime |
| E-mail |Ciąg |
| Id |Ciąg |
| Relacja odnośnika |Ciąg |
| Lista wyboru z wybórem |Ciąg |
| Liczba |Liczba dziesiętna |
| Procent |Liczba dziesiętna |
| Telefon |Ciąg |
| Lista wyboru |Ciąg |
| Tekst |Ciąg |
| Obszar tekstu |Ciąg |
| Obszar tekstowy (Long) |Ciąg |
| Obszar tekstowy (rozbudowany) |Ciąg |
| Tekst (zaszyfrowany) |Ciąg |
| Adres URL |Ciąg |

> [!NOTE]
> Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z obiektu ujścia danych, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zapoznaj się z [przewodnikiem dotyczącym wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
