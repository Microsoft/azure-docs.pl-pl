---
title: Kopiuj dane z usługi Salesforce do chmury
description: Informacje o kopiowaniu danych z chmury usługi Salesforce do obsługiwanych magazynów danych ujścia lub z obsługiwanych magazynów danych źródłowych do chmury usług Salesforce za pomocą działania kopiowania w potoku usługi Fabryka danych.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/11/2021
ms.openlocfilehash: 755346c1da38f66c0c0fef6144d34eea62735273
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072087"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Skopiuj dane z i do chmury usługi Salesforce przy użyciu Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do chmury usługi Salesforce. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik chmury usługi Salesforce jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z chmury usługi Salesforce można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Możesz również skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do chmury usługi Salesforce. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W ramach tego łącznika usługi Salesforce w chmurze obsługuje:

- Wersje Developer, Professional, Enterprise i Unlimited usługi Salesforce.
- Kopiowanie danych z i do środowiska produkcyjnego, piaskownicy i niestandardowej domeny usługi Salesforce.

Łącznik usługi Salesforce jest oparty na interfejsie API REST/Bulk usługi Salesforce. Domyślnie podczas kopiowania danych z usługi Salesforce łącznik używa [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) i automatycznie wybiera interfejsy API REST i bulk na podstawie rozmiaru danych — gdy zestaw wyników jest duży, MASOWY interfejs API jest używany w celu zapewnienia lepszej wydajności. podczas zapisywania danych w usłudze Salesforce łącznik używa usługi [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) w ramach interfejsu API BULK. Można również jawnie ustawić wersję interfejsu API używaną do odczytu/zapisu danych za pośrednictwem [ `apiVersion` Właściwości](#linked-service-properties) w połączonej usłudze.

## <a name="prerequisites"></a>Wymagania wstępne

Uprawnienie API musi być włączone w usłudze Salesforce. Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do interfejsu API w usłudze Salesforce według zestawu uprawnień](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limity żądań usługi Salesforce

Usługi Salesforce mają limity dla obu żądań interfejsu API i współbieżnych żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczba współbieżnych żądań przekracza limit, nastąpi ograniczenie i zobaczysz błędy losowe.
- Jeśli łączna liczba żądań przekracza limit, konto usługi Salesforce jest blokowane przez 24 godziny.

W obu scenariuszach może być również wyświetlany komunikat o błędzie "REQUEST_LIMIT_EXCEEDED". Aby uzyskać więcej informacji, zobacz sekcję "limity żądań interfejsu API" w obszarze [limity deweloperów usługi Salesforce](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika usługi Salesforce w chmurze.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Salesforce są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi być ustawiona na wartość **SalesforceServiceCloud**. |Tak |
| environmentUrl | Określ adres URL wystąpienia chmury usługi Salesforce. <br> -Wartość domyślna to `"https://login.salesforce.com"` . <br> -Aby skopiować dane z piaskownicy, określ `"https://test.salesforce.com"` . <br> -Aby skopiować dane z domeny niestandardowej, określ, na przykład, `"https://[domain].my.salesforce.com"` . |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak |
| hasło |Określ hasło dla konta użytkownika.<br/><br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| Obiektu |Określ token zabezpieczający dla konta użytkownika. <br/><br/>Aby uzyskać ogólne informacje na temat tokenów zabezpieczających, zobacz [zabezpieczenia i interfejs API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). Token zabezpieczający można pominąć tylko wtedy, gdy dodasz adres IP Integration Runtime do [listy zaufanych adresów IP](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) w usłudze Salesforce. Korzystając z Azure IR, zapoznaj się z [Azure Integration Runtime adresami IP](azure-integration-runtime-ip-addresses.md).<br/><br/>Instrukcje dotyczące pobierania i resetowania tokenu zabezpieczającego znajdują się w temacie [Get a Security Token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Nie |
| apiVersion | Określ wersję interfejsu API REST/Bulk usługi Salesforce, która ma zostać użyta, np. `48.0` . Domyślnie łącznik używa [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) do kopiowania danych z usługi Salesforce i używa [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) do kopiowania danych do usługi Salesforce. | Nie |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie dla źródła, tak dla ujścia, jeśli źródłowa usługa nie ma środowiska Integration Runtime |

>[!IMPORTANT]
>Podczas kopiowania danych do chmury usługi Salesforce nie można użyć domyślnego Azure Integration Runtime do wykonania kopiowania. Innymi słowy, jeśli źródłowa usługa połączona nie ma określonego środowiska Integration Runtime, jawnie [utwórz Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) przy użyciu lokalizacji zbliżonej do wystąpienia chmury usługi Salesforce. Skojarz połączoną usługę w chmurze usługi Salesforce, jak w poniższym przykładzie.

**Przykład: Przechowuj poświadczenia w Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: Przechowuj poświadczenia w Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych w chmurze usługi Salesforce.

Aby skopiować dane z i do chmury usługi Salesforce, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **SalesforceServiceCloudObject**.  | Tak |
| objectApiName | Nazwa obiektu usług Salesforce, z którego mają zostać pobrane dane. | Nie dla źródła, tak dla ujścia |

> [!IMPORTANT]
> Część "__c" **nazwy interfejsu API** jest wymagana dla dowolnego obiektu niestandardowego.

![Data Factory nazwę interfejsu API połączenia usługi Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Przykład:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość **relacyjną**. | Tak |
| tableName | Nazwa tabeli w chmurze usługi Salesforce. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia w chmurze usługi Salesforce.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Chmura usługi Salesforce jako typ źródła

Aby skopiować dane z chmury usługi Salesforce, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość **SalesforceServiceCloudSource**. | Tak |
| query |Użyj zapytania niestandardowego do odczytywania danych. Można użyć zapytania [SOQL (Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lub zapytania SQL-92. Zobacz więcej porad w sekcji [porady dotyczące zapytań](#query-tips) . Jeśli nie określono zapytania, zostaną pobrane wszystkie dane obiektu chmury usługi Salesforce określonego w "objectApiName" w zestawie danych. | Nie (Jeśli określono wartość "objectApiName" w zestawie danych) |
| readBehavior | Wskazuje, czy mają być zbadane istniejące rekordy, czy też mają być poszukiwane wszystkie rekordy, w tym usunięte. Jeśli nie zostanie określony, domyślnym zachowaniem jest pierwsze. <br>Dozwolone wartości: **zapytanie** (wartość domyślna), **queryAll**.  | Nie |

> [!IMPORTANT]
> Część "__c" **nazwy interfejsu API** jest wymagana dla dowolnego obiektu niestandardowego.

![Data Factory lista nazw interfejsów API połączenia usługi Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Chmura usługi Salesforce jako typ ujścia

Aby skopiować dane do chmury usługi Salesforce, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **SalesforceServiceCloudSink**. | Tak |
| writeBehavior | Zachowanie zapisu dla operacji.<br/>Dozwolone wartości to **INSERT** i **upsert**. | Nie (wartość domyślna to Insert) |
| externalIdFieldName | Nazwa pola identyfikatora zewnętrznego dla operacji upsert. Określone pole musi być zdefiniowane jako "pole identyfikatora zewnętrznego" w obiekcie chmury usługi Salesforce. Nie może mieć wartości NULL w odpowiednich danych wejściowych. | Tak dla "upsert" |
| writeBatchSize | Liczba wierszy danych zapisywana w chmurze usługi Salesforce w każdej partii. | Nie (domyślnie 5 000) |
| ignoreNullValues | Wskazuje, czy ignorować wartości NULL z danych wejściowych podczas operacji zapisu.<br/>Dozwolone wartości to **true** i **false**.<br>- **True**: pozostawienie danych w obiekcie docelowym nie zmienia się po wykonaniu operacji upsert lub Update. Wstaw zdefiniowaną wartość domyślną podczas wykonywania operacji wstawiania.<br/>- **Fałsz**: zaktualizuj dane w obiekcie docelowym do wartości null po wykonaniu operacji upsert lub Update. Wstaw wartość NULL po wykonaniu operacji wstawiania. | Nie (wartość domyślna to false) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Porady dotyczące zapytań

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Pobieranie danych z raportu w chmurze usługi Salesforce

Dane można pobrać z raportów w chmurze usługi Salesforce, określając zapytanie jako `{call "<report name>"}` . Może to być na przykład `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Pobieranie usuniętych rekordów z kosza chmury usługi Salesforce

Aby wykonać zapytanie dotyczące usuniętych nietrwałych rekordów z Kosza usługi Salesforce w chmurze, możesz określić `readBehavior` jako `queryAll` . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Różnica między SOQL i składnią zapytania SQL

Podczas kopiowania danych z chmury usługi Salesforce można użyć zapytania SOQL lub zapytania SQL. Należy pamiętać, że te dwa mają różne składnie i funkcje, nie należy ich mieszać. Zalecane jest użycie zapytania SOQL, które jest natywnie obsługiwane przez chmurę usługi Salesforce. W poniższej tabeli wymieniono główne różnice:

| Składnia | Tryb SOQL | Tryb SQL |
|:--- |:--- |:--- |
| Wybór kolumny | Należy wyliczyć pola, które mają być skopiowane do zapytania, np. `SELECT field1, filed2 FROM objectname` | `SELECT *` jest obsługiwana oprócz zaznaczenia kolumny. |
| Cudzysłowy | Nazwy zgłoszonych/obiektów nie mogą być ujęte w cudzysłów. | Nazwy pól/obiektów mogą być ujęte w cudzysłów, np. `SELECT "id" FROM "Account"` |
| Format daty i godziny |  Zapoznaj się z informacjami [tutaj](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) i przykładami w następnej sekcji. | Zapoznaj się z informacjami [tutaj](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals) i przykładami w następnej sekcji. |
| Wartości logiczne | Reprezentowane jako `False` i `True` , np. `SELECT … WHERE IsDeleted=True` . | Reprezentowane jako 0 lub 1, np. `SELECT … WHERE IsDeleted=1` . |
| Zmiana nazwy kolumny | Nieobsługiwane. | Obsługiwane, np.: `SELECT a AS b FROM …` . |
| Relacja | Obsługiwane, np. `Account_vod__r.nvs_Country__c` . | Nieobsługiwane. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Pobieranie danych przy użyciu klauzuli WHERE w kolumnie DateTime

Po określeniu zapytania SOQL lub SQL należy zwrócić uwagę na różnice w formacie daty/godziny. Na przykład:

* **Przykład SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Przykład SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Błąd MALFORMED_QUERY: obcięty

Jeśli wystąpi błąd "MALFORMED_QUERY: obcięty", zazwyczaj jest to spowodowane tym, że masz kolumnę typu JunctionIdList w danych, a Salesforce ma ograniczenia dotyczące obsługi takich danych o dużej liczbie wierszy. Aby wyeliminować problem, spróbuj wykluczyć kolumnę JunctionIdList lub ograniczyć liczbę wierszy do skopiowania (można podzielić na wiele uruchomień działania kopiowania).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapowanie typu danych dla chmury usługi Salesforce

Podczas kopiowania danych z chmury usługi Salesforce następujące mapowania są używane w ramach typów danych w chmurze usługi Salesforce do Data Factory danych pośrednich. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych w chmurze usługi Salesforce | Data Factory typ danych pośrednich |
|:--- |:--- |
| Numer Autokorekty |String |
| Pole wyboru |Boolean |
| Waluta |Liczba dziesiętna |
| Data |DateTime |
| Data/godzina |DateTime |
| E-mail |String |
| ID (Identyfikator) |String |
| Relacja odnośnika |String |
| Lista wyboru z wybórem |String |
| Liczba |Liczba dziesiętna |
| Procent |Liczba dziesiętna |
| Telefon |String |
| Lista wyboru |String |
| Tekst |Ciąg |
| Obszar tekstu |String |
| Obszar tekstowy (Long) |String |
| Obszar tekstowy (rozbudowany) |String |
| Tekst (zaszyfrowany) |String |
| Adres URL |String |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).