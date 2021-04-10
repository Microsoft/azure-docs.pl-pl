---
title: Kopiowanie danych z Cassandra za pomocą Azure Data Factory
description: Informacje o kopiowaniu danych z programu Cassandra do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383708"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopiowanie danych z Cassandra za pomocą Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Bieżąca wersja](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych Cassandra. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Cassandra jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z bazy danych Cassandra można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik Cassandra obsługuje:

- Cassandra **w wersji 2. x i 3. x**.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **anonimowego** .

>[!NOTE]
>W przypadku działania uruchomionego na samoobsługowym Integration Runtime Cassandra 3. x jest obsługiwany przez środowisko IR w wersji 3,7 lub nowszej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime udostępnia wbudowany sterownik Cassandra, dlatego nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z/do Cassandra.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Cassandra.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Cassandra są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi mieć wartość: **Cassandra** |Tak |
| host |Co najmniej jeden adres IP lub nazwa hosta serwerów Cassandra.<br/>Określ rozdzieloną przecinkami listę adresów IP lub nazw hostów, które mają być połączone jednocześnie ze wszystkimi serwerami. |Tak |
| port |Port TCP, którego serwer Cassandra używa do nasłuchiwania połączeń klientów. |Nie (domyślnie 9042) |
| authenticationType | Typ uwierzytelniania używany do łączenia się z bazą danych Cassandra.<br/>Dozwolone wartości to: **Basic** i **Anonymous**. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, Jeśli AuthenticationType ma wartość Basic. |
| hasło |Określ hasło dla konta użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak, Jeśli AuthenticationType ma wartość Basic. |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Obecnie połączenie z usługą Cassandra przy użyciu protokołu TLS nie jest obsługiwane.

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Cassandra.

Aby skopiować dane z Cassandra, ustaw właściwość Type zestawu danych na **CassandraTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **CassandraTable** | Tak |
| przestrzeń kluczy |Nazwa przestrzeni kluczy lub schematu w bazie danych Cassandra. |Nie (Jeśli określono "Query" dla "CassandraSource") |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Nie (Jeśli określono "Query" dla "CassandraSource") |

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


Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Cassandra.

### <a name="cassandra-as-source"></a>Cassandra jako źródło

Aby skopiować dane z Cassandra, ustaw typ źródła w działaniu Copy na **CassandraSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **CassandraSource** | Tak |
| query |Użyj zapytania niestandardowego do odczytywania danych. Zapytanie SQL-92 zapytania lub CQL. Zobacz [CQL Reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>W przypadku korzystania z zapytania SQL określ **nazwę przestrzeni kluczy. nazwa tabeli** do reprezentowania tabeli, którą chcesz zbadać. |Nie (Jeśli określono "TableName" i "przestrzeń kluczy" w zestawie danych). |
| consistencyLevel |Poziom spójności określa, ile replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określoną liczbę replik dla danych, aby spełnić żądanie odczytu. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie spójności danych](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) .<br/><br/>Dozwolone wartości to: **jeden**, **dwa**, **trzy**, **kworum**, **wszystkie**, **LOCAL_QUORUM**, **EACH_QUORUM** i **LOCAL_ONE**. |Nie (domyślnie `ONE` ) |

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

## <a name="data-type-mapping-for-cassandra"></a>Mapowanie typu danych dla Cassandra

Podczas kopiowania danych z Cassandra następujące mapowania są używane z typów danych Cassandra do Azure Data Factory danych pośrednich. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Cassandra — typ danych | Typ danych pośrednich fabryki danych |
|:--- |:--- |
| ASCII |Ciąg |
| BIGINT |Int64 |
| TWORZENIA |Byte [] |
| TYPU |Wartość logiczna |
| DOKŁADNOŚCI |Liczba dziesiętna |
| DOUBLE |Double |
| FLOAT |Pojedynczy |
| INET |Ciąg |
| INT |Int32 |
| TEKST |Ciąg |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid (identyfikator GUID) |
| INTERFEJSU |Guid (identyfikator GUID) |
| VARCHAR |Ciąg |
| VARINT |Liczba dziesiętna |

> [!NOTE]
> W przypadku typów kolekcji (map, Set, list itp.) zapoznaj się z sekcją [Work with Cassandra Types using przy użyciu tabeli wirtualnej](#work-with-collections-using-virtual-table) .
>
> Typy zdefiniowane przez użytkownika nie są obsługiwane.
>
> Długość kolumny binarnej i długości kolumny ciągu nie może być większa niż 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Pracuj z kolekcjami przy użyciu tabeli wirtualnej

Azure Data Factory używa wbudowanego sterownika ODBC do nawiązywania połączenia i kopiowania danych z bazy danych Cassandra. W przypadku typów kolekcji, w tym map, Set i list, sterownik ponownie normalizuje dane do odpowiednich tabel wirtualnych. W przypadku, gdy tabela zawiera wszystkie kolumny kolekcji, sterownik generuje następujące tabele wirtualne:

* **Tabela podstawowa**, która zawiera te same dane, co rzeczywista tabela z wyjątkiem kolumn kolekcji. Tabela podstawowa używa takiej samej nazwy jak rzeczywista tabela, która reprezentuje.
* **Tabela wirtualna** dla każdej kolumny kolekcji, która rozszerza zagnieżdżone dane. Tabele wirtualne reprezentujące kolekcje są nazwane przy użyciu nazwy rzeczywistej tabeli, separatora "*VT*" i nazwy kolumny.

Tabele wirtualne odwołują się do danych w rzeczywistej tabeli, umożliwiając sterownikowi dostęp do nieznormalizowanych danych. Aby uzyskać szczegółowe informacje, zobacz sekcję dotyczącą przykładu. Możesz uzyskać dostęp do zawartości kolekcji Cassandra, wykonując zapytania i dołączając do tabel wirtualnych.

### <a name="example"></a>Przykład

Na przykład następująca "przykładowy" jest tabelą bazy danych Cassandra, która zawiera kolumnę klucza podstawowego Integer o nazwie "pk_int", kolumna tekstowa o nazwie Value, kolumnie listy, kolumnie mapy i kolumnie zestawu (o nazwie "StringSet").

| pk_int | Wartość | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"przykładowa wartość 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"przykładowa wartość 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Sterownik generuje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Kolumny klucza obcego w tabelach wirtualnych odwołują się do kolumn klucza podstawowego w rzeczywistej tabeli i wskazują, który wiersz tabeli w rzeczywistości odpowiada wiersz tabeli wirtualnej.

Pierwsza tabela wirtualna jest tabelą podstawową o nazwie "Przykładowe" przedstawiono w poniższej tabeli: 

| pk_int | Wartość |
| --- | --- |
| 1 |"przykładowa wartość 1" |
| 3 |"przykładowa wartość 3" |

Tabela podstawowa zawiera te same dane, co oryginalna tabela bazy danych, z wyjątkiem kolekcji, które zostały pominięte w tej tabeli i rozwinięte w innych tabelach wirtualnych.

W poniższych tabelach przedstawiono tabelę wirtualną, która ponownie normalizuje dane z kolumn list, map i StringSet. Kolumny z nazwami kończącymi się znakiem "_index" lub "_key" wskazują pozycję danych w oryginalnej liście lub mapie. Kolumny z nazwami kończącymi się znakiem "_value" zawierają rozwinięte dane z kolekcji.

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

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
