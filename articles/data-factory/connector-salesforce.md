---
title: Kopiuj dane z i do usługi Salesforce
description: Dowiedz się, jak skopiować dane z usługi Salesforce do obsługiwanych magazynów danych ujścia lub z obsługiwanych magazynów danych źródłowych do usługi Salesforce za pomocą działania kopiowania w potoku usługi Fabryka danych.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 5b49e62330c789d6d5cbe2af2edb28a2c3e1238f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583106"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Skopiuj dane z i do usługi Salesforce przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-salesforce-connector.md)
> * [Bieżąca wersja](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do usługi Salesforce. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Salesforce jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi Salesforce można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Możesz również skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do usługi Salesforce. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W ramach tego łącznika usługi Salesforce obsługiwane są następujące usługi:

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

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika usługi Salesforce.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Salesforce są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi być ustawiona na wartość **Salesforce**. |Tak |
| environmentUrl | Określ adres URL wystąpienia usługi Salesforce. <br> -Wartość domyślna to `"https://login.salesforce.com"` . <br> -Aby skopiować dane z piaskownicy, określ `"https://test.salesforce.com"` . <br> -Aby skopiować dane z domeny niestandardowej, określ, na przykład, `"https://[domain].my.salesforce.com"` . |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak |
| hasło |Określ hasło dla konta użytkownika.<br/><br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| Obiektu |Określ token zabezpieczający dla konta użytkownika. <br/><br/>Aby uzyskać ogólne informacje na temat tokenów zabezpieczających, zobacz [zabezpieczenia i interfejs API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). Token zabezpieczający można pominąć tylko wtedy, gdy dodasz adres IP Integration Runtime do [listy zaufanych adresów IP](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) w usłudze Salesforce. Korzystając z Azure IR, zapoznaj się z [Azure Integration Runtime adresami IP](azure-integration-runtime-ip-addresses.md).<br/><br/>Instrukcje dotyczące pobierania i resetowania tokenu zabezpieczającego znajdują się w temacie [Get a Security Token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Nie |
| apiVersion | Określ wersję interfejsu API REST/Bulk usługi Salesforce, która ma zostać użyta, np. `48.0` . Domyślnie łącznik używa [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) do kopiowania danych z usługi Salesforce i używa [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) do kopiowania danych do usługi Salesforce. | Nie |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie |

**Przykład: Przechowuj poświadczenia w Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Salesforce.

Aby skopiować dane z i do usługi Salesforce, ustaw właściwość Type zestawu danych na **salesforceobject**. Obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **salesforceobject**.  | Tak |
| objectApiName | Nazwa obiektu usług Salesforce, z którego mają zostać pobrane dane. | Nie dla źródła, tak dla ujścia |

> [!IMPORTANT]
> Część "__c" **nazwy interfejsu API** jest wymagana dla dowolnego obiektu niestandardowego.

![Data Factory nazwę interfejsu API połączenia usługi Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Przykład:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>W celu zapewnienia zgodności z poprzednimi wersjami: w przypadku kopiowania danych z usługi Salesforce, jeśli używasz poprzedniego zestawu danych typu "relacyjnego", będzie on nadal działać, gdy zobaczysz sugestię przełączenia do nowego typu "Salesforceobject".

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość **relacyjną**. | Tak |
| tableName | Nazwa tabeli w usłudze Salesforce. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia usługi Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce jako typ źródła

Aby skopiować dane z usługi Salesforce, ustaw typ źródła w działaniu Copy na **SalesforceSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość **SalesforceSource**. | Tak |
| query |Użyj zapytania niestandardowego do odczytywania danych. Można użyć zapytania [SOQL (Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lub zapytania SQL-92. Zobacz więcej porad w sekcji [porady dotyczące zapytań](#query-tips) . Jeśli nie określono zapytania, zostaną pobrane wszystkie dane obiektu usługi Salesforce określone w "objectApiName" w zestawie danych. | Nie (Jeśli określono wartość "objectApiName" w zestawie danych) |
| readBehavior | Wskazuje, czy mają być zbadane istniejące rekordy, czy też mają być poszukiwane wszystkie rekordy, w tym usunięte. Jeśli nie zostanie określony, domyślnym zachowaniem jest pierwsze. <br>Dozwolone wartości: **zapytanie** (wartość domyślna), **queryAll**.  | Nie |

> [!IMPORTANT]
> Część "__c" **nazwy interfejsu API** jest wymagana dla dowolnego obiektu niestandardowego.

![Data Factory lista nazw interfejsów API połączenia usługi Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>W celu zapewnienia zgodności z poprzednimi wersjami: w przypadku kopiowania danych z usługi Salesforce w przypadku używania poprzedniej kopii typu "RelationalSource" Źródło będzie działać w trakcie wyświetlania sugestii, aby przełączyć się do nowego typu "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce jako typ ujścia

Aby skopiować dane do usługi Salesforce, ustaw typ ujścia w działaniu Copy na **SalesforceSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **SalesforceSink**. | Tak |
| writeBehavior | Zachowanie zapisu dla operacji.<br/>Dozwolone wartości to **INSERT** i **upsert**. | Nie (wartość domyślna to Insert) |
| externalIdFieldName | Nazwa pola identyfikatora zewnętrznego dla operacji upsert. Określone pole musi być zdefiniowane jako "pole identyfikatora zewnętrznego" w obiekcie usługi Salesforce. Nie może mieć wartości NULL w odpowiednich danych wejściowych. | Tak dla "upsert" |
| writeBatchSize | Liczba wierszy danych zapisywana w usłudze Salesforce w każdej partii. | Nie (domyślnie 5 000) |
| ignoreNullValues | Wskazuje, czy ignorować wartości NULL z danych wejściowych podczas operacji zapisu.<br/>Dozwolone wartości to **true** i **false**.<br>- **True**: pozostawienie danych w obiekcie docelowym nie zmienia się po wykonaniu operacji upsert lub Update. Wstaw zdefiniowaną wartość domyślną podczas wykonywania operacji wstawiania.<br/>- **Fałsz**: zaktualizuj dane w obiekcie docelowym do wartości null po wykonaniu operacji upsert lub Update. Wstaw wartość NULL po wykonaniu operacji wstawiania. | Nie (wartość domyślna to false) |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie |

**Przykład: ujścia usługi Salesforce w działaniu kopiowania**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
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

### <a name="retrieve-data-from-a-salesforce-report"></a>Pobieranie danych z raportu usługi Salesforce

Dane można pobrać z raportów usługi Salesforce, określając zapytanie jako `{call "<report name>"}` . Może to być na przykład `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Pobieranie usuniętych rekordów z Kosza usługi Salesforce

Aby wykonać zapytanie o usunięte nietrwałe rekordy z Kosza usługi Salesforce, możesz określić `readBehavior` jako `queryAll` . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Różnica między SOQL i składnią zapytania SQL

Podczas kopiowania danych z usługi Salesforce można użyć zapytania SOQL lub zapytania SQL. Należy pamiętać, że te dwa mają różne składnie i funkcje, nie należy ich mieszać. Zalecane jest użycie zapytania SOQL, które jest natywnie obsługiwane przez usługę Salesforce. W poniższej tabeli wymieniono główne różnice:

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

## <a name="data-type-mapping-for-salesforce"></a>Mapowanie typu danych dla usługi Salesforce

Podczas kopiowania danych z usługi Salesforce następujące mapowania są używane w ramach typów danych usługi Salesforce do Data Factory danych pośrednich. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych usługi Salesforce | Data Factory typ danych pośrednich |
|:--- |:--- |
| Numer Autokorekty |Ciąg |
| Pole wyboru |Wartość logiczna |
| Waluta |Liczba dziesiętna |
| Date (Data) |DateTime |
| Data/godzina |DateTime |
| E-mail |Ciąg |
| ID (Identyfikator) |Ciąg |
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

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).