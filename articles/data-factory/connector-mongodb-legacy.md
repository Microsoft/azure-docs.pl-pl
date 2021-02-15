---
title: Kopiuj dane z MongoDB przy użyciu starszej wersji
description: Informacje o kopiowaniu danych z usługi Mongo DB do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w starszym potoku Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: e13a1a5a939d314bdf4500c0827fa13201505016
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368850"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory-legacy"></a>Kopiowanie danych z MongoDB przy użyciu Azure Data Factory (starsza wersja)

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Bieżąca wersja](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych MongoDB. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

>[!IMPORTANT]
>Moduł ADF zwolni nowy łącznik MongoDB, który zapewnia lepszą obsługę natywnych MongoDB w porównaniu do tej implementacji opartej na ODBC, zobacz artykuł dotyczący [łącznika MongoDB](connector-mongodb.md) , aby uzyskać szczegółowe informacje. Ten starszy łącznik MongoDB jest obsługiwany jako gotowy do zapewnienia zgodności z poprzednimi wersjami, a w przypadku każdego nowego obciążenia Użyj nowego łącznika.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Dane z bazy danych MongoDB można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik MongoDB obsługuje:

- MongoDB **wersje 2,4, 2,6, 3,0, 3,2, 3,4 i 3,6**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **anonimowego** .

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime udostępnia wbudowany sterownik MongoDB, dlatego nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z MongoDB.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika MongoDB.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi MongoDB są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi mieć wartość: **MongoDB** |Tak |
| serwer |Adres IP lub nazwa hosta serwera MongoDB. |Tak |
| port |Port TCP, którego serwer MongoDB używa do nasłuchiwania połączeń klientów. |Nie (domyślnie 27017) |
| Bazy |Nazwa bazy danych MongoDB, do której chcesz uzyskać dostęp. |Tak |
| authenticationType | Typ uwierzytelniania używany do łączenia się z bazą danych MongoDB.<br/>Dozwolone wartości to: **Basic** i **Anonymous**. |Tak |
| nazwa użytkownika |Konto użytkownika do uzyskiwania dostępu do MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| hasło |Hasło użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, która ma zostać użyta do sprawdzenia poświadczeń w celu uwierzytelnienia. |Nie. W przypadku uwierzytelniania podstawowego wartość domyślna to użycie konta administratora i bazy danych określonej przy użyciu właściwości databaseName. |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu TLS. Wartość domyślna to false.  | Nie |
| allowSelfSignedServerCert | Określa, czy zezwalać na certyfikaty z podpisem własnym z serwera. Wartość domyślna to false.  | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
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

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Dla zestawu danych MongoDB są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **MongoDbCollection** | Tak |
| CollectionName |Nazwa kolekcji w bazie danych MongoDB. |Tak |

**Przykład:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako źródło

W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **MongoDbSource** | Tak |
| query |Użyj niestandardowej kwerendy SQL-92 do odczytu danych. Na przykład: select * from MyTable. |Nie (Jeśli określono "CollectionName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> W przypadku określenia zapytania SQL należy zwrócić uwagę na format daty i godziny. Na przykład: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` lub, aby użyć parametru `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schemat według Data Factory

Azure Data Factory schemat wniosku o usługę z kolekcji MongoDB przy użyciu **najnowszych dokumentów 100** w kolekcji. Jeśli następujące dokumenty 100 nie zawierają pełnego schematu, niektóre kolumny mogą zostać zignorowane podczas operacji kopiowania.

## <a name="data-type-mapping-for-mongodb"></a>Mapowanie typu danych dla MongoDB

Podczas kopiowania danych z MongoDB następujące mapowania są używane z typów danych MongoDB do Azure Data Factory danych pośrednich. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| MongoDB — typ danych | Typ danych pośrednich fabryki danych |
|:--- |:--- |
| Binarne |Byte [] |
| Boolean (wartość logiczna) |Wartość logiczna |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String (ciąg) |
| String (ciąg) |String (ciąg) |
| INTERFEJSU |Guid (identyfikator GUID) |
| Obiekt |Reznormalizowany do spłaszczonych kolumn z "_" jako separatorem zagnieżdżonym |

> [!NOTE]
> Aby dowiedzieć się więcej o obsłudze tablic przy użyciu tabel wirtualnych, zobacz sekcję [Obsługa typów złożonych przy użyciu tabel wirtualnych](#support-for-complex-types-using-virtual-tables) .
>
> Obecnie następujące typy danych MongoDB nie są obsługiwane: dbpointer, JavaScript, max/min Key, wyrażenie regularne, symbol, sygnatura czasowa, niezdefiniowane.

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługa typów złożonych przy użyciu tabel wirtualnych

Azure Data Factory używa wbudowanego sterownika ODBC do nawiązywania połączenia i kopiowania danych z bazy danych MongoDB. W przypadku typów złożonych, takich jak tablice lub obiekty o różnych typach w dokumentach, sterownik renormalizuje dane do odpowiednich tabel wirtualnych. W przypadku, gdy tabela zawiera takie kolumny, sterownik generuje następujące tabele wirtualne:

* **Tabela podstawowa**, która zawiera te same dane, co rzeczywista tabela z wyjątkiem kolumn typu złożonego. Tabela podstawowa używa takiej samej nazwy jak rzeczywista tabela, która reprezentuje.
* **Tabela wirtualna** dla każdej kolumny typu złożonego, która rozszerza zagnieżdżone dane. Tabele wirtualne są nazwane przy użyciu nazwy rzeczywistej tabeli, separatora "_" oraz nazwy tablicy lub obiektu.

Tabele wirtualne odwołują się do danych w rzeczywistej tabeli, umożliwiając sterownikowi dostęp do nieznormalizowanych danych. Możesz uzyskać dostęp do zawartości tablic MongoDB, wykonując zapytania i dołączając do tabel wirtualnych.

### <a name="example"></a>Przykład

Na przykład przykładem jest tabela MongoDB, która zawiera jedną kolumnę z tablicą obiektów w każdej komórce — faktury i jedną kolumnę z tablicą typów skalarnych — klasyfikacje.

| _id | Nazwa klienta | Faktury | Poziom usługi | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", Item: "wyskakujący", Cena: "456", Rabat: "0,2"}, {invoice_id: "124", Item: "piekarnik", Cena: "1235", Rabat: "0,2"}] |Srebrny |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", element: "lodówki", Cena: "12543", Rabat: "0,0"}] |Złoty |[1, 2] |

Sterownik generuje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Pierwsza tabela wirtualna jest tabelą podstawową o nazwie "Przykładowe", pokazana w przykładzie. Tabela podstawowa zawiera wszystkie dane oryginalnej tabeli, ale dane z tablic zostały pominięte i rozwinięte w tabelach wirtualnych.

| _id | Nazwa klienta | Poziom usługi |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono tabele wirtualne, które reprezentują oryginalne tablice w przykładzie. Te tabele zawierają następujące elementy:

* Odwołanie z powrotem do oryginalnej kolumny klucza podstawowego odpowiadającej wierszowi oryginalnej tablicy (za pośrednictwem kolumny _id)
* Wskazanie pozycji danych w oryginalnej tablicy
* Rozwinięte dane dla każdego elementu w tablicy

**Tabela "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |wyskakujący |456 |0,2 |
| 1111 |1 |124 |laboratoryjn |1235 |0,2 |
| 2222 |0 |135 |lodówki |12543 |0,0 |

**Tabela "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
