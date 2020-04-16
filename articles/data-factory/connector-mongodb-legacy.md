---
title: Kopiowanie danych z mongodb przy użyciu starszej wersji
description: Dowiedz się, jak skopiować dane z mongo db do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 803e34a93e8019cfc2577bfaab3ba13c409c6b01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418171"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiowanie danych z mongodb przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktualna wersja](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych MongoDB. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

>[!IMPORTANT]
>ADF zwolnić nowy złącze MongoDB, który zapewnia lepszą obsługę natywną MongoDB w porównaniu do tej implementacji opartej na ODBC, odnoszą się do artykułu [mongodb łącznika](connector-mongodb.md) na szczegóły. Ten starszy łącznik MongoDB jest obsługiwany tak, jak jest dla kompowalności wstecz, podczas gdy w przypadku każdego nowego obciążenia użyj nowego łącznika.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Można skopiować dane z bazy danych MongoDB do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze MongoDB obsługuje:

- Wersje MongoDB **2.4, 2.6, 3.0, 3.2, 3.4 i 3.6**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **anonimowego.**

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime zapewnia wbudowany sterownik MongoDB, dlatego nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z MongoDB.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika MongoDB.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej MongoDB:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |Właściwość typu musi być ustawiona na: **MongoDb** |Tak |
| serwer |Adres IP lub nazwa hosta serwera MongoDB. |Tak |
| port |Port TCP używany przez serwer MongoDB do nasłuchiwać połączeń klientów. |Nie (wartość domyślna to 27017) |
| Databasename |Nazwa bazy danych MongoDB, do której chcesz uzyskać dostęp. |Tak |
| authenticationType | Typ uwierzytelniania używany do łączenia się z bazą danych MongoDB.<br/>Dozwolone wartości to: **Podstawowe**i **Anonimowe**. |Tak |
| nazwa użytkownika |Konto użytkownika, aby uzyskać dostęp do MongoDB. |Tak (jeśli używane jest uwierzytelnianie podstawowe). |
| hasło |Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak (jeśli używane jest uwierzytelnianie podstawowe). |
| źródło authSource |Nazwa bazy danych MongoDB, której chcesz użyć do sprawdzenia poświadczeń w celu uwierzytelnienia. |Nie. W przypadku uwierzytelniania podstawowego domyślnie należy użyć konta administratora i bazy danych określonej przy użyciu właściwości databaseName. |
| Enablessl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu TLS. Wartość domyślna to false.  | Nie |
| allowSelfSignedServerCert | Określa, czy zezwolić na certyfikaty z podpisem własnym z serwera. Wartość domyślna to false.  | Nie |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

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

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Następujące właściwości są obsługiwane dla zestawu danych MongoDB:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **MongoDbCollection** | Tak |
| Collectionname |Nazwa kolekcji w bazie danych MongoDB. |Tak |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako źródło

Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **MongoDbSource** | Tak |
| query |Użyj niestandardowej kwerendy SQL-92 do odczytu danych. Na przykład: wybierz * z MyTable. |Nie (jeśli określono "collectionName" w zestawie danych) |

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
> Podczas określania kwerendy SQL należy zwrócić uwagę na format DateTime. Na przykład: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` lub użyć parametru`SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schemat według fabryki danych

Usługa Azure Data Factory wywnioskować schemat z kolekcji MongoDB przy użyciu **najnowszych dokumentów 100** w kolekcji. Jeśli te 100 dokumentów nie zawiera pełnego schematu, niektóre kolumny mogą być ignorowane podczas operacji kopiowania.

## <a name="data-type-mapping-for-mongodb"></a>Mapowanie typu danych dla bazy danych MongoDB

Podczas kopiowania danych z mongodb, następujące mapowania są używane z typów danych MongoDB do usługi Azure Data Factory tymczasowych typów danych. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych MongoDB | Tymczasowy typ danych fabryki danych |
|:--- |:--- |
| plików binarnych |Bajt[] |
| Wartość logiczna |Wartość logiczna |
| Date |DateTime |
| NumberDouble (LiczbaDouble) |Double |
| LiczbaInt |Int32 |
| LiczbaLong |Int64 |
| ObjectID |Ciąg |
| Ciąg |Ciąg |
| Uuid |Guid (identyfikator GUID) |
| Obiekt |Przegrupowane na spłaszczone kolumny z "_" jako separatorem zagnieżdżanym |

> [!NOTE]
> Aby dowiedzieć się więcej na temat obsługi tablic przy użyciu tabel wirtualnych, zapoznaj się z [działem Obsługa typów złożonych przy użyciu sekcji tabel wirtualnych.](#support-for-complex-types-using-virtual-tables)
>
> Obecnie następujące typy danych MongoDB nie są obsługiwane: DBPointer, JavaScript, Max/Min, Wyrażenie regularne, Symbol, Sygnatura czasowa, Niezdefiniowany.

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługa typów złożonych przy użyciu tabel wirtualnych

Usługa Azure Data Factory używa wbudowanego sterownika ODBC do łączenia się z bazą danych mongodb i kopiowania ich. W przypadku typów złożonych, takich jak tablice lub obiekty o różnych typach w dokumentach, sterownik ponownie normalizuje dane do odpowiednich tabel wirtualnych. W szczególności jeśli tabela zawiera takie kolumny, sterownik generuje następujące tabele wirtualne:

* **Tabela bazowa**, która zawiera te same dane co tabela rzeczywista, z wyjątkiem kolumn typu złożonego. Tabela bazowa używa tej samej nazwy co rzeczywista tabela, którą reprezentuje.
* **Tabela wirtualna** dla każdej kolumny typu złożonego, która rozwija zagnieżdżone dane. Tabele wirtualne są nazywane przy użyciu nazwy tabeli rzeczywistej, separatora "_" i nazwy tablicy lub obiektu.

Tabele wirtualne odnoszą się do danych w rzeczywistej tabeli, umożliwiając kierowcy dostęp do danych denormalized. Dostęp do zawartości tablic MongoDB można uzyskać, odwołując zapytania i dołączając do tabel wirtualnych.

### <a name="example"></a>Przykład

Na przykład ExampleTable w tym miejscu jest tabela MongoDB, która ma jedną kolumnę z tablicą obiektów w każdej komórce — Faktury i jedną kolumnę z tablicą typów skalarnych — Oceny.

| _id | Nazwa klienta | Faktury | Poziom usług | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", poz invoice_id. |Srebrny |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", poz. |Złoty |[1,2] |

Sterownik wygeneruje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Pierwsza tabela wirtualna to tabela bazowa o nazwie "ExampleTable", pokazana w przykładzie. Tabela bazowa zawiera wszystkie dane oryginalnej tabeli, ale dane z tablic zostały pominięte i rozwinięte w tabelach wirtualnych.

| _id | Nazwa klienta | Poziom usług |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono tabele wirtualne reprezentujące oryginalne tablice w przykładzie. Tabele te zawierają następujące tabele:

* Odwołanie do oryginalnej kolumny klucza podstawowego odpowiadającej wierszowi oryginalnej tablicy (za pośrednictwem kolumny _id)
* Wskazanie położenia danych w oryginalnej tablicy
* Rozszerzone dane dla każdego elementu w tablicy

**Tabela "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toster |456 |0.2 |
| 1111 |1 |124 |Piekarnik |1235 |0.2 |
| 2222 |0 |135 |Lodówka |12543 |0.0 |

**Tabela "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
