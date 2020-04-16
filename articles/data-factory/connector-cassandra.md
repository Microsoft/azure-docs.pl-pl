---
title: Kopiowanie danych z firmy Cassandra przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane z Cassandra do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 4b7fd2de0762de147ad3ceae0d562a1c78b33dc2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417472"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopiowanie danych z firmy Cassandra przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Bieżąca wersja](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych Cassandra. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Cassandra jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z bazy danych Cassandra do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze Cassandra obsługuje:

- Wersje Cassandra **2.x i 3.x**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **anonimowego.**

>[!NOTE]
>W przypadku działań uruchomionych w czasie wykonywania integracji hostowanego samodzielnie cassandra 3.x jest obsługiwana od czasu podczerwonych wersji 3.7 lub nowszych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime zapewnia wbudowany sterownik Cassandra, dlatego nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z/do Cassandra.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika Cassandra.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Cassandra:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |Właściwość typu musi być ustawiona na: **Cassandra** |Tak |
| host |Co najmniej jeden adres IP lub nazwy hostów serwerów Cassandra.<br/>Określ oddzieloną przecinkami listę adresów IP lub nazw hostów, aby połączyć się ze wszystkimi serwerami jednocześnie. |Tak |
| port |Port TCP używany przez serwer Cassandra do nasłuchiwać połączeń klientów. |Nie (wartość domyślna to 9042) |
| authenticationType | Typ uwierzytelniania używany do łączenia się z bazą danych Cassandra.<br/>Dozwolone wartości to: **Podstawowe**i **Anonimowe**. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli authenticationType jest ustawiony na Podstawowy. |
| hasło |Określ hasło dla konta użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak, jeśli authenticationType jest ustawiony na Podstawowy. |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

>[!NOTE]
>Obecnie połączenie z cassandra przy użyciu protokołu TLS nie jest obsługiwane.

**Przykład:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Cassandra.

Aby skopiować dane z cassandra, ustaw właściwość typu zestawu danych na **CassandraTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **CassandraTable** | Tak |
| przestrzeń kluczy |Nazwa przestrzeni kluczy lub schematu w bazie danych Cassandra. |Nie (jeśli określono "zapytanie" dla "CassandraSource") |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Nie (jeśli określono "zapytanie" dla "CassandraSource") |

**Przykład:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania


Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Cassandra.

### <a name="cassandra-as-source"></a>Cassandra jako źródło

Aby skopiować dane z kasandry, ustaw typ źródła w działaniu kopiowania na **CassandraSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **CassandraSource** | Tak |
| query |Użyj kwerendy niestandardowej, aby odczytać dane. Kwerenda SQL-92 lub kwerenda CQL. Zobacz [odwołanie CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Podczas korzystania z kwerendy SQL określ **nazwę name.table obszaru kluczowego,** aby reprezentować tabelę, którą chcesz zbadać. |Nie (jeśli określono "tableName" i "keyspace" w zestawie danych). |
| spójnośćPoziom |Poziom spójności określa, ile replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określoną liczbę replik danych w celu spełnienia żądania odczytu. Zobacz [Konfigurowanie spójności danych,](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) aby uzyskać szczegółowe informacje.<br/><br/>Dozwolone wartości to: **JEDEN,** **DWA,** **TRZY,** **KWORUM,** **ALL,** **LOCAL_QUORUM,** **EACH_QUORUM**i **LOCAL_ONE.** |Nie (domyślnie jest) `ONE` |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Mapowanie typu danych dla cassandra

Podczas kopiowania danych z Cassandra, następujące mapowania są używane z typów danych Cassandra do usługi Azure Data Factory tymczasowych typów danych. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych Cassandra | Tymczasowy typ danych fabryki danych |
|:--- |:--- |
| ASCII |Ciąg |
| Bigint |Int64 |
| Blob |Bajt[] |
| Boolean |Wartość logiczna |
| Dziesiętnych |Wartość dziesiętna |
| Podwójne |Double |
| Float |Single |
| Inet |Ciąg |
| INT |Int32 |
| TEKST |Ciąg |
| Sygnatury czasowej |DateTime |
| TIMEUUID |Guid (identyfikator GUID) |
| Uuid |Guid (identyfikator GUID) |
| Varchar |Ciąg |
| Funkcja VARINT |Wartość dziesiętna |

> [!NOTE]
> W przypadku typów kolekcji (mapa, zestaw, lista itp.) zobacz [Praca z typami kolekcji Cassandra przy użyciu sekcji tabeli wirtualnej.](#work-with-collections-using-virtual-table)
>
> Typy zdefiniowane przez użytkownika nie są obsługiwane.
>
> Długość kolumn binarnych i długości kolumny ciąg nie może być większa niż 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Praca z kolekcjami przy użyciu tabeli wirtualnej

Usługa Azure Data Factory używa wbudowanego sterownika ODBC do łączenia się z bazą danych Cassandra i kopiowania ich z bazy danych Cassandra. W przypadku typów kolekcji, w tym mapy, zestawu i listy, sterownik renormalizuje dane do odpowiednich tabel wirtualnych. W szczególności jeśli tabela zawiera kolumny kolekcji, sterownik generuje następujące tabele wirtualne:

* **Tabela bazowa**, która zawiera te same dane co tabela rzeczywista, z wyjątkiem kolumn kolekcji. Tabela bazowa używa tej samej nazwy co rzeczywista tabela, którą reprezentuje.
* **Tabela wirtualna** dla każdej kolumny kolekcji, która rozwija zagnieżdżone dane. Tabele wirtualne reprezentujące kolekcje są nazywane przy użyciu nazwy tabeli rzeczywistej, separatora "*vt*" i nazwy kolumny.

Tabele wirtualne odnoszą się do danych w rzeczywistej tabeli, umożliwiając kierowcy dostęp do danych denormalized. Zobacz przykład sekcji, aby uzyskać szczegółowe informacje. Dostęp do zawartości kolekcji Cassandra można uzyskać, odpytując i dołączając do tabel wirtualnych.

### <a name="example"></a>Przykład

Na przykład następująca "ExampleTable" to tabela bazy danych Cassandra zawierająca kolumnę klucza podstawowego o nazwie "pk_int", kolumnę tekstową o nazwie value, kolumnę listy, kolumnę mapy i kolumnę zestawu (o nazwie "StringSet").

| pk_int | Wartość | List | Mapa | Zestaw ciągów |
| --- | --- | --- | --- | --- |
| 1 |"wartość próbki 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"wartość próbki 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Sterownik wygeneruje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Kolumny klucza obcego w tabelach wirtualnych odwołują się do kolumn klucza podstawowego w tabeli rzeczywistej i wskazują, który wiersz tabeli rzeczywistej odpowiada wierszowi tabeli wirtualnej.

Pierwsza tabela wirtualna to tabela bazowa o nazwie "ExampleTable" jest wyświetlana w poniższej tabeli: 

| pk_int | Wartość |
| --- | --- |
| 1 |"wartość próbki 1" |
| 3 |"wartość próbki 3" |

Tabela bazowa zawiera te same dane co oryginalna tabela bazy danych, z wyjątkiem kolekcji, które są pomijane w tej tabeli i rozwijane w innych tabelach wirtualnych.

W poniższych tabelach przedstawiono tabele wirtualne, które ponownie znormalizują dane z kolumn Lista, Mapa i StringSet. Kolumny z nazwami, które kończą się na "_index" lub "_key" wskazują położenie danych na oryginalnej liście lub mapie. Kolumny z nazwami, które kończą się na "_value" zawierają rozszerzone dane z kolekcji.

**Tabela "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabela "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabela "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
