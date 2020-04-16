---
title: Kopiowanie danych z i do usługi Salesforce Service Cloud
description: Dowiedz się, jak kopiować dane z usługi Salesforce Service Cloud do obsługiwanych magazynów danych ujścia lub z obsługiwanych magazynów danych źródłowych do usługi Salesforce Service Cloud przy użyciu działania kopiowania w potoku fabryki danych.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: ec2aa5b1492534908adb55544623110242717609
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416671"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Kopiowanie danych z i do usługi Salesforce Service Cloud przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Salesforce Service Cloud. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Salesforce w chmurze jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z usługi Salesforce Service Cloud do dowolnego obsługiwanego magazynu danych ujścia. Można również skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do usługi Salesforce Service Cloud. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła lub pochłaniacze przez działanie Kopiowanie, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to łącznik salesforce service cloud obsługuje:

- Wersje Salesforce Developer, Professional, Enterprise lub Unlimited.
- Kopiowanie danych z i do produkcji Salesforce, piaskownicy i domeny niestandardowej.

Łącznik Salesforce jest zbudowany na interfejsie API REST/zbiorczy Salesforce. Domyślnie łącznik używa [wersji 45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) do kopiowania danych z salesforce i używa [wersji 40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) do kopiowania danych do salesforce. Można również jawnie ustawić wersję interfejsu API używanej do odczytu/zapisu danych za pośrednictwem [ `apiVersion` właściwości](#linked-service-properties) w połączonej usłudze.

## <a name="prerequisites"></a>Wymagania wstępne

Uprawnienie interfejsu API musi być włączone w Salesforce. Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do interfejsu API w Salesforce według zestawu uprawnień](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limity żądań Salesforce

Salesforce ma limity zarówno dla wszystkich żądań interfejsu API, jak i równoczesnych żądań interfejsu API. Pamiętaj o następujących kwestiach:

- Jeśli liczba równoczesnych żądań przekracza limit, występuje ograniczanie przepustowości i widoczne są losowe błędy.
- Jeśli łączna liczba żądań przekroczy limit, konto Salesforce zostanie zablokowane na 24 godziny.

W obu scenariuszach może również pojawić się komunikat o błędzie "REQUEST_LIMIT_EXCEEDED". Aby uzyskać więcej informacji, zobacz sekcję "Limity żądań interfejsu API" w [limitach deweloperów Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika Salesforce Service Cloud.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Salesforce.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |Właściwość typu musi być ustawiona na **SalesforceServiceCloud**. |Tak |
| środowiskoUrl | Określ adres URL wystąpienia usługi Salesforce Service Cloud. <br> - Domyślnie jest `"https://login.salesforce.com"`to . <br> - Aby skopiować dane `"https://test.salesforce.com"`z piaskownicy, należy określić . <br> - Aby skopiować dane z domeny `"https://[domain].my.salesforce.com"`niestandardowej, należy określić na przykład . |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak |
| hasło |Określ hasło dla konta użytkownika.<br/><br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| Securitytoken |Określ token zabezpieczający dla konta użytkownika. <br/><br/>Aby dowiedzieć się więcej o tokenach zabezpieczających, zobacz [Zabezpieczenia i interfejs API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). Token zabezpieczający można pominąć tylko po dodaniu adresu IP środowiska wykonawczego integracji do [listy zaufanych adresów IP](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) w salesforce. Korzystając z usługi Azure IR, zapoznaj się z [adresami IP środowiska wykonawczego integracji platformy Azure.](azure-integration-runtime-ip-addresses.md)<br/><br/>Aby uzyskać instrukcje dotyczące sposobu uzyskania i zresetowania tokenu [zabezpieczającego,](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)zobacz Pobierz token zabezpieczający . Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Nie |
| apiVersion | Określ wersję interfejsu API REST/zbiorczego salesforce, która ma być używana, np. `48.0` Domyślnie łącznik używa [wersji 45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) do kopiowania danych z salesforce i używa [wersji 40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) do kopiowania danych do salesforce. | Nie |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie dla źródła, Tak dla ujścia, jeśli usługa połączona ze źródłem nie ma środowiska uruchomieniowego integracji |

>[!IMPORTANT]
>Podczas kopiowania danych do usługi Salesforce Service Cloud domyślnego środowiska wykonawczego integracji platformy Azure nie można użyć do wykonania kopii. Innymi słowy, jeśli źródło połączone usługi nie ma określonego środowiska uruchomieniowego integracji, jawnie [utworzyć środowisko uruchomieniowe integracji platformy Azure](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w pobliżu wystąpienia salesforce service cloud. Skojarz usługę połączony Salesforce Service Cloud, jak w poniższym przykładzie.

**Przykład: Przechowywanie poświadczeń w fabryce danych**

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

**Przykład: Przechowywanie poświadczeń w magazynie kluczy**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Salesforce Service Cloud.

Aby skopiować dane z i do salesforce service cloud, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **SalesforceServiceCloudObject**.  | Tak |
| objectApiName | Nazwa obiektu Salesforce do pobierania danych. | Nie dla źródła, tak dla zlewu |

> [!IMPORTANT]
> "__c" część **nazwy interfejsu API** jest potrzebna dla każdego obiektu niestandardowego.

![Nazwa interfejsu API połączenia usługi Data Factory Salesforce](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

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

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na **RelationalTable**. | Tak |
| tableName | Nazwa tabeli w chmurze usług Salesforce. | Nie (jeśli określono "zapytanie" w źródle działania) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujście usługi Salesforce Service Cloud.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce Service Cloud jako typ źródła

Aby skopiować dane z usługi Salesforce Service Cloud, następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **SalesforceServiceCloudSource**. | Tak |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. Można użyć [kwerendy języka zapytania obiektów Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lub kwerendy SQL-92. Zobacz więcej wskazówek w sekcji [Porady dotyczące zapytań.](#query-tips) Jeśli kwerenda nie jest określona, zostaną pobrane wszystkie dane obiektu Salesforce Service Cloud określonego w "objectApiName" w zestawie danych. | Nie (jeśli określono "objectApiName" w zestawie danych) |
| readBehavior (Zachowanie) | Wskazuje, czy kwerenda istniejących rekordów lub kwerendy wszystkie rekordy, w tym te usunięte. Jeśli nie określono, domyślne zachowanie jest pierwszym. <br>Dozwolone wartości: **kwerenda** (domyślna), **queryAll**.  | Nie |

> [!IMPORTANT]
> "__c" część **nazwy interfejsu API** jest potrzebna dla każdego obiektu niestandardowego.

![Lista nazwa interfejsu API połączenia Usługi Salesforce danych](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud jako typ ujścia

Aby skopiować dane do usługi Salesforce Service Cloud, następujące właściwości są obsługiwane w sekcji ujście działania **kopiowania.**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na **SalesforceServiceCloudSink**. | Tak |
| writeZachody | Zachowanie zapisu dla operacji.<br/>Dozwolone wartości to **Insert** i **Upsert**. | Nie (domyślnie jest Wstaw) |
| externalIdFieldName | Nazwa zewnętrznego pola identyfikatora operacji upsert. Określone pole musi być zdefiniowane jako "Pole identyfikatora zewnętrznego" w obiekcie Salesforce Service Cloud. Nie może mieć wartości NULL w odpowiednich danych wejściowych. | Tak dla "Upsert" |
| writeBatchSize | Liczba wierszy danych zapisanych w chmurze usług Salesforce w każdej partii. | Nie (wartość domyślna to 5000) |
| ignoreNullValues | Wskazuje, czy podczas operacji zapisu należy ignorować wartości NULL z danych wejściowych.<br/>Dozwolone wartości są **prawdziwe** i **fałszywe**.<br>- **Prawda:** Pozostaw dane w obiekcie docelowym bez zmian podczas wykonywania operacji upsert lub aktualizacji. Wstaw zdefiniowaną wartość domyślną podczas wykonywania operacji wstawiania.<br/>- **False**: Zaktualizuj dane w obiekcie docelowym do wartości NULL podczas wykonywania operacji aktualizacji lub aktualizacji. Wstaw wartość NULL podczas wykonywania operacji wstawiania. | Nie (wartość domyślna jest false) |

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

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Pobieranie danych z raportu Salesforce Service Cloud

Dane można pobierać z raportów Salesforce Service Cloud, określając kwerendę jako `{call "<report name>"}`. Może to być na przykład `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Pobieranie usuniętych rekordów z Kosza w chmurze usługi Salesforce

Aby zbadać nietrwale usunięte rekordy z Kosza w `readBehavior` `queryAll`chmurze usługi Salesforce, można określić jako . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Różnica między składnią kwerendy SOQL i SQL

Podczas kopiowania danych z usługi Salesforce Service Cloud można użyć kwerendy SOQL lub kwerendy SQL. Należy zauważyć, że te dwa ma różne składni i obsługi funkcji, nie mieszać go. Zaleca się użycie kwerendy SOQL, która jest natywnie obsługiwana przez Salesforce Service Cloud. W poniższej tabeli wymieniono główne różnice:

| Składnia | Tryb SOQL | Tryb SQL |
|:--- |:--- |:--- |
| Wybór kolumny | Trzeba wyliczyć pola, które mają zostać skopiowane w zapytaniu, np.`SELECT field1, filed2 FROM objectname` | `SELECT *`jest obsługiwana oprócz wyboru kolumny. |
| Cudzysłowu | Nie można podać nazw obiektów filed/object. | Nazwy pól/obiektów mogą być cytowane, np.`SELECT "id" FROM "Account"` |
| Format Datetime |  Zapoznaj się ze szczegółami [tutaj](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) i przykładami w następnej sekcji. | Zapoznaj się ze szczegółami [tutaj](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) i przykładami w następnej sekcji. |
| Wartości logiczne | Reprezentowane `False` jako `True`i , `SELECT … WHERE IsDeleted=True`np. | Reprezentowane jako 0 lub 1, `SELECT … WHERE IsDeleted=1`np. |
| Zmiana nazwy kolumny | Bez pomocy technicznej. | Obsługiwane, np.: `SELECT a AS b FROM …`. |
| Relacja | Obsługiwane np. `Account_vod__r.nvs_Country__c` | Bez pomocy technicznej. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Pobieranie danych przy użyciu klauzuli where w kolumnie DateTime

Po określeniu zapytania SOQL lub SQL należy zwrócić uwagę na różnicę formatu DateTime. Przykład:

* **Próbka SOKL:**`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Przykład SQL:**`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Błąd MALFORMED_QUERY:Obcięty

Jeśli trafisz błąd "MALFORMED_QUERY: Obcięty", zwykle jest to spowodowane tym, że masz kolumnę typu JunctionIdList w danych, a Salesforce ma ograniczenie obsługi takich danych z dużą liczbą wierszy. Aby ograniczyć, spróbuj wykluczyć Kolumnę JunctionIdList lub ograniczyć liczbę wierszy do skopiowania (można podzielić na wiele uruchomień działania kopiowania).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapowanie typów danych dla usługi Salesforce Service Cloud

Podczas kopiowania danych z usługi Salesforce Service Cloud używane są następujące mapowania z typów danych Salesforce Service Cloud do tymczasowych typów danych data factory. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ danych usługi Salesforce Service Cloud | Tymczasowy typ danych data factory |
|:--- |:--- |
| Automatyczny numer |Ciąg |
| Pole wyboru |Wartość logiczna |
| Waluta |Wartość dziesiętna |
| Date |DateTime |
| Data/godzina |DateTime |
| Adres e-mail |Ciąg |
| Identyfikator |Ciąg |
| Relacja odnośnika |Ciąg |
| Lista wyboru wielokrotnego wyboru |Ciąg |
| Liczba |Wartość dziesiętna |
| Wartość procentowa |Wartość dziesiętna |
| Telefon |Ciąg |
| Lista wyboru |Ciąg |
| Tekst |Ciąg |
| Obszar tekstu |Ciąg |
| Obszar tekstu (długi) |Ciąg |
| Obszar tekstu (bogaty) |Ciąg |
| Tekst (zaszyfrowany) |Ciąg |
| Adres URL |Ciąg |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
