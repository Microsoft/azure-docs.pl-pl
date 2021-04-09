---
title: Przenoszenie danych z Cassandra za pomocą Data Factory
description: Dowiedz się, jak przenieść dane z lokalnej bazy danych Cassandra przy użyciu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 005fd85a152ee2765facda0d961bd9119d1598e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387414"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Przenoszenie danych z lokalnej bazy danych Cassandra przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-cassandra-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-cassandra.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik Cassandra w wersji 2](../connector-cassandra.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z lokalnej bazy danych Cassandra. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Dane można kopiować z lokalnego magazynu danych Cassandra do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Fabryka danych obsługuje obecnie tylko przeniesienie danych z magazynu danych Cassandra do innych magazynów danych, ale nie do przeniesienia danych z innych magazynów danych do magazynu danych Cassandra.

## <a name="supported-versions"></a>Obsługiwane wersje
Łącznik Cassandra obsługuje następujące wersje Cassandra: 2. x i 3. x. W przypadku działania uruchomionego na samoobsługowym Integration Runtime Cassandra 3. x jest obsługiwany przez środowisko IR w wersji 3,7 lub nowszej.

## <a name="prerequisites"></a>Wymagania wstępne
Aby usługa Azure Data Factory mogła nawiązać połączenie z lokalną bazą danych Cassandra, należy zainstalować bramę Zarządzanie danymi na tym samym komputerze, na którym znajduje się baza danych programu, lub na oddzielnym komputerze, aby uniknąć konkurowania zasobów z bazą danych. Zarządzanie danymi Gateway to składnik, który łączy lokalne źródła danych z usługami w chmurze w bezpieczny i zarządzany sposób. Szczegółowe informacje o bramie Zarządzanie danymi można znaleźć w artykule [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) . Instrukcje krok po kroku dotyczące konfigurowania bramy do przenoszenia danych można znaleźć w artykule [przenoszenie danych z lokalizacji lokalnej do chmury](data-factory-move-data-between-onprem-and-cloud.md) .

Musisz użyć bramy, aby nawiązać połączenie z bazą danych Cassandra, nawet jeśli baza danych jest hostowana w chmurze, na przykład na maszynie wirtualnej platformy Azure IaaS. T możesz mieć bramę na tej samej maszynie wirtualnej, która hostuje bazę danych lub na oddzielnej maszynie wirtualnej, o ile Brama może połączyć się z bazą danych.

Podczas instalacji bramy automatycznie instalowany jest sterownik Microsoft Cassandra ODBC używany do łączenia się z bazą danych Cassandra. W związku z tym nie trzeba ręcznie instalować żadnego sterownika na maszynie bramy podczas kopiowania danych z bazy danych Cassandra.

> [!NOTE]
> Zobacz [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) , aby uzyskać porady dotyczące rozwiązywania problemów związanych z połączeniem/bramą.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.
- Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z lokalnego magazynu danych Cassandra, zobacz [przykład JSON: Kopiuj dane z Cassandra do usługi Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla magazynu danych Cassandra:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla Cassandra połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **OnPremisesCassandra** |Tak |
| host |Co najmniej jeden adres IP lub nazwa hosta serwerów Cassandra.<br/><br/>Określ rozdzieloną przecinkami listę adresów IP lub nazw hostów, które mają być połączone jednocześnie ze wszystkimi serwerami. |Tak |
| port |Port TCP, którego serwer Cassandra używa do nasłuchiwania połączeń klientów. |Nie, wartość domyślna: 9042 |
| authenticationType |Podstawowa lub anonimowa |Tak |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, Jeśli AuthenticationType ma wartość Basic. |
| hasło |Określ hasło dla konta użytkownika. |Tak, Jeśli AuthenticationType ma wartość Basic. |
| gatewayName |Nazwa bramy, która jest używana do nawiązywania połączenia z lokalną bazą danych Cassandra. |Tak |
| encryptedCredential |Poświadczenie zaszyfrowane przez bramę. |Nie |

>[!NOTE]
>Obecnie połączenie z usługą Cassandra przy użyciu protokołu TLS nie jest obsługiwane.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja typeProperties zestawu danych typu **CassandraTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| przestrzeń kluczy |Nazwa przestrzeni kluczy lub schematu w bazie danych Cassandra. |Tak (jeśli nie zdefiniowano **kwerendy** dla **CassandraSource** ). |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Tak (jeśli nie zdefiniowano **kwerendy** dla **CassandraSource** ). |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

Jeśli źródło jest typu **CassandraSource**, w sekcji typeProperties są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Zapytanie SQL-92 zapytania lub CQL. Zobacz [CQL Reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>W przypadku korzystania z zapytania SQL określ **nazwę przestrzeni kluczy. nazwa tabeli** do reprezentowania tabeli, którą chcesz zbadać. |Nie (Jeśli określono element TableName i przestrzeń kluczy w zestawie danych). |
| consistencyLevel |Poziom spójności określa, ile replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określoną liczbę replik dla danych, aby spełnić żądanie odczytu. |JEDEN, DWA, TRZY, KWORUM, WSZYSTKIE, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie spójności danych](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Nie. Wartość domyślna to 1. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Przykład JSON: kopiowanie danych z Cassandra do obiektu blob platformy Azure
Ten przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazuje, jak skopiować dane z lokalnej bazy danych Cassandra do Blob Storage platformy Azure. Dane można jednak kopiować do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Instrukcje krok po kroku znajdują się [w sekcji przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

Przykład zawiera następujące jednostki fabryki danych:

* Połączona usługa typu [OnPremisesCassandra](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [CassandraTable](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [CassandraSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra połączona usługa:**

Ten przykład używa połączonej usługi **Cassandra** . Zobacz sekcję [Cassandra połączonej usługi](#linked-service-properties) , aby uzyskać właściwości obsługiwane przez tę połączoną usługę.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Połączona usługa Azure Storage:**

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

**Cassandra wejściowy zestaw danych:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
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

**Wyjściowy zestaw danych obiektów blob platformy Azure:**

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
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Działanie kopiowania w potoku ze źródłem Cassandra i obiektem BLOB:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **CassandraSource** , a typ **ujścia** to **wartość blobsink**.

Zobacz [właściwości typu RelationalSource](#copy-activity-properties) , aby uzyskać listę właściwości obsługiwanych przez RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

### <a name="type-mapping-for-cassandra"></a>Mapowanie typu dla Cassandra
| Typ Cassandra | Typ oparty na platformie .NET |
| --- | --- |
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
>

## <a name="work-with-collections-using-virtual-table"></a>Pracuj z kolekcjami przy użyciu tabeli wirtualnej
Azure Data Factory używa wbudowanego sterownika ODBC do nawiązywania połączenia i kopiowania danych z bazy danych Cassandra. W przypadku typów kolekcji, w tym map, Set i list, sterownik ponownie normalizuje dane do odpowiednich tabel wirtualnych. W przypadku, gdy tabela zawiera wszystkie kolumny kolekcji, sterownik generuje następujące tabele wirtualne:

* **Tabela podstawowa**, która zawiera te same dane, co rzeczywista tabela z wyjątkiem kolumn kolekcji. Tabela podstawowa używa takiej samej nazwy jak rzeczywista tabela, która reprezentuje.
* **Tabela wirtualna** dla każdej kolumny kolekcji, która rozszerza zagnieżdżone dane. Tabele wirtualne reprezentujące kolekcje są nazwane przy użyciu nazwy rzeczywistej tabeli, separatora "*VT*" i nazwy kolumny.

Tabele wirtualne odwołują się do danych w rzeczywistej tabeli, umożliwiając sterownikowi dostęp do nieznormalizowanych danych. Aby uzyskać szczegółowe informacje, zobacz sekcję dotyczącą przykładu. Możesz uzyskać dostęp do zawartości kolekcji Cassandra, wykonując zapytania i dołączając do tabel wirtualnych.

[Kreatora kopiowania](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) można użyć, aby intuicyjnie wyświetlić listę tabel w bazie danych Cassandra, w tym tabele wirtualne, i wyświetlić podgląd danych wewnątrz. Możesz również utworzyć zapytanie w Kreatorze kopiowania i sprawdzić poprawność, aby zobaczyć wynik.

### <a name="example"></a>Przykład
Na przykład następująca "przykładowy" jest tabelą bazy danych Cassandra, która zawiera kolumnę klucza podstawowego Integer o nazwie "pk_int", kolumna tekstowa o nazwie Value, kolumnie listy, kolumnie mapy i kolumnie zestawu (o nazwie "StringSet").

| pk_int | Wartość | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"przykładowa wartość 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"przykładowa wartość 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Sterownik generuje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Kolumny klucza obcego w tabelach wirtualnych odwołują się do kolumn klucza podstawowego w rzeczywistej tabeli i wskazują, który wiersz tabeli w rzeczywistości odpowiada wiersz tabeli wirtualnej.

Pierwsza tabela wirtualna jest tabelą podstawową o nazwie "Przykładowe" przedstawiono w poniższej tabeli. Tabela podstawowa zawiera te same dane, co oryginalna tabela bazy danych, z wyjątkiem kolekcji, które zostały pominięte w tej tabeli i rozwinięte w innych tabelach wirtualnych.

| pk_int | Wartość |
| --- | --- |
| 1 |"przykładowa wartość 1" |
| 3 |"przykładowa wartość 3" |

W poniższych tabelach przedstawiono tabelę wirtualną, która ponownie normalizuje dane z kolumn list, map i StringSet. Kolumny z nazwami kończącymi się znakiem "_index" lub "_key" wskazują pozycję danych w oryginalnej liście lub mapie. Kolumny z nazwami kończącymi się znakiem "_value" zawierają rozwinięte dane z kolekcji.

#### <a name="table-exampletable_vt_list"></a>Tabela "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabela "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabela "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej na temat mapowania kolumn w źródłowym zestawie danych na kolumny w datadataset, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzanie odczytu ze źródeł relacyjnych
Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Zobacz [powtarzanie odczytu ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
