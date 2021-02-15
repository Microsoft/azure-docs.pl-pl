---
title: Przenoszenie danych z MongoDB
description: Dowiedz się więcej na temat przenoszenia danych z bazy danych MongoDB przy użyciu Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: cedb0b99f04df00763a3ee83287eec90bd5fb45d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387516"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Przenoszenie danych z MongoDB za pomocą Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-on-premises-mongodb-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-mongodb.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik MongoDB w wersji 2](../connector-mongodb.md).


W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z lokalnej bazy danych MongoDB. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Dane można kopiować z lokalnego magazynu danych MongoDB do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Fabryka danych obsługuje obecnie tylko przeniesienie danych z magazynu danych MongoDB do innych magazynów danych, ale nie do przeniesienia danych z innych magazynów danych do magazynu dataMongoDB.

## <a name="prerequisites"></a>Wymagania wstępne
Aby usługa Azure Data Factory mogła nawiązać połączenie z lokalną bazą danych MongoDB, należy zainstalować następujące składniki:

- Obsługiwane wersje MongoDB to: 2,4, 2,6, 3,0, 3,2, 3,4 i 3,6.
- Zarządzanie danymi bramę na tym samym komputerze, na którym znajduje się baza danych programu, lub na oddzielnym komputerze, aby uniknąć konkurowania zasobów z bazą danych. Zarządzanie danymi Gateway to oprogramowanie, które łączy lokalne źródła danych z usługami w chmurze w bezpieczny i zarządzany sposób. Szczegółowe informacje o bramie Zarządzanie danymi można znaleźć w artykule [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) . Instrukcje krok po kroku dotyczące konfigurowania bramy do przenoszenia danych można znaleźć w artykule [przenoszenie danych z lokalizacji lokalnej do chmury](data-factory-move-data-between-onprem-and-cloud.md) .

    Podczas instalacji bramy automatycznie instalowany jest sterownik Microsoft MongoDB ODBC używany do nawiązywania połączenia z MongoDB.

    > [!NOTE]
    > Musisz użyć bramy, aby nawiązać połączenie z usługą MongoDB, nawet jeśli jest ona hostowana na maszynach wirtualnych usługi Azure IaaS. Jeśli próbujesz nawiązać połączenie z wystąpieniem MongoDB hostowanym w chmurze, możesz również zainstalować wystąpienie bramy na maszynie wirtualnej IaaS.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane z lokalnego magazynu danych MongoDB przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z lokalnego magazynu danych MongoDB, zobacz [przykład JSON: Kopiuj dane z MongoDB do usługi Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla źródła MongoDB:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla **OnPremisesMongoDB** połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **OnPremisesMongoDb** |Tak |
| serwer |Adres IP lub nazwa hosta serwera MongoDB. |Tak |
| port |Port TCP, którego serwer MongoDB używa do nasłuchiwania połączeń klientów. |Opcjonalna, wartość domyślna: 27017 |
| authenticationType |Podstawowa lub anonimowa. |Tak |
| nazwa użytkownika |Konto użytkownika do uzyskiwania dostępu do MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| hasło |Hasło użytkownika. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, która ma zostać użyta do sprawdzenia poświadczeń w celu uwierzytelnienia. |Opcjonalne (jeśli jest używane uwierzytelnianie podstawowe). Domyślnie: używa konta administratora i bazy danych określonej przy użyciu właściwości databaseName. |
| Bazy |Nazwa bazy danych MongoDB, do której chcesz uzyskać dostęp. |Tak |
| gatewayName |Nazwa bramy, która uzyskuje dostęp do magazynu danych. |Tak |
| encryptedCredential |Poświadczenie zaszyfrowane przez bramę. |Opcjonalne |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja typeProperties zestawu danych typu **MongoDbCollection** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| CollectionName |Nazwa kolekcji w bazie danych MongoDB. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji **typeProperties** działania z drugiej strony różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

Gdy źródło jest typu **MongoDbSource** , w sekcji typeProperties są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL-92. Na przykład: select * from MyTable. |Nie (Jeśli określono **CollectionName** **zestawu danych** ) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Przykład JSON: kopiowanie danych z MongoDB do obiektu blob platformy Azure
Ten przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazuje sposób kopiowania danych z MongoDB lokalnego do Blob Storage platformy Azure. Dane można jednak kopiować do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

Przykład zawiera następujące jednostki fabryki danych:

1. Połączona usługa typu [OnPremisesMongoDb](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [MongoDbCollection](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [MongoDbSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku zapytania w bazie danych MongoDB do obiektu BLOB co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

Najpierw należy skonfigurować bramę zarządzania danymi zgodnie z instrukcjami w artykule [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) .

**MongoDB połączona usługa:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**MongoDB wejściowy zestaw danych:** Ustawienie "External": "true" informuje usługę Data Factory, że tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Działanie kopiowania w potoku ze źródłem MongoDB i obiektem BLOB:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania powyższych wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **MongoDbSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **zapytania** wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schemat według Data Factory
Azure Data Factory schemat wniosku o usługę z kolekcji MongoDB przy użyciu najnowszych dokumentów 100 w kolekcji. Jeśli następujące dokumenty 100 nie zawierają pełnego schematu, niektóre kolumny mogą zostać zignorowane podczas operacji kopiowania.

## <a name="type-mapping-for-mongodb"></a>Mapowanie typu dla MongoDB
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia o następujących dwuetapowych podejściach:

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przesuwania danych do MongoDB następujące mapowania są używane z typów MongoDB do typów .NET.

| Typ MongoDB | Typ programu .NET Framework |
| --- | --- |
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
> Aby dowiedzieć się więcej o obsłudze tablic przy użyciu tabel wirtualnych, zapoznaj się z sekcją [Obsługa typów złożonych przy użyciu poniższych tabel wirtualnych](#support-for-complex-types-using-virtual-tables) .

Obecnie następujące typy danych MongoDB nie są obsługiwane: dbpointer, JavaScript, max/min Key, wyrażenie regularne, symbol, znacznik czasu, niezdefiniowany

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługa typów złożonych przy użyciu tabel wirtualnych
Azure Data Factory używa wbudowanego sterownika ODBC do nawiązywania połączenia i kopiowania danych z bazy danych MongoDB. W przypadku typów złożonych, takich jak tablice lub obiekty o różnych typach w dokumentach, sterownik renormalizuje dane do odpowiednich tabel wirtualnych. W przypadku, gdy tabela zawiera takie kolumny, sterownik generuje następujące tabele wirtualne:

* **Tabela podstawowa**, która zawiera te same dane, co rzeczywista tabela z wyjątkiem kolumn typu złożonego. Tabela podstawowa używa takiej samej nazwy jak rzeczywista tabela, która reprezentuje.
* **Tabela wirtualna** dla każdej kolumny typu złożonego, która rozszerza zagnieżdżone dane. Tabele wirtualne są nazwane przy użyciu nazwy rzeczywistej tabeli, separatora "_" oraz nazwy tablicy lub obiektu.

Tabele wirtualne odwołują się do danych w rzeczywistej tabeli, umożliwiając sterownikowi dostęp do nieznormalizowanych danych. Zobacz sekcję przykład poniżej. Możesz uzyskać dostęp do zawartości tablic MongoDB, wykonując zapytania i dołączając do tabel wirtualnych.

[Kreatora kopiowania](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) można użyć, aby intuicyjnie wyświetlić listę tabel w bazie danych MongoDB, w tym tabele wirtualne, i wyświetlić podgląd danych wewnątrz. Możesz również utworzyć zapytanie w Kreatorze kopiowania i sprawdzić poprawność, aby zobaczyć wynik.

### <a name="example"></a>Przykład
Na przykład "przykład" poniżej jest tabelą MongoDB, która zawiera jedną kolumnę z tablicą obiektów w każdej komórce — faktury i jedną kolumnę z tablicą typów skalarnych — klasyfikacje.

| _id | Nazwa klienta | Faktury | Poziom usługi | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", Item: "wyskakujący", Cena: "456", Rabat: "0,2"}, {invoice_id: "124", Item: "piekarnik", Cena: "1235", Rabat: "0,2"}] |Srebrny |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", element: "lodówki", Cena: "12543", Rabat: "0,0"}] |Złoty |[1, 2] |

Sterownik generuje wiele tabel wirtualnych do reprezentowania tej pojedynczej tabeli. Pierwsza tabela wirtualna jest tabelą podstawową o nazwie "Przykładowe", pokazana poniżej. Tabela podstawowa zawiera wszystkie dane oryginalnej tabeli, ale dane z tablic zostały pominięte i rozwinięte w tabelach wirtualnych.

| _id | Nazwa klienta | Poziom usługi |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono tabele wirtualne, które reprezentują oryginalne tablice w przykładzie. Te tabele zawierają następujące elementy:

* Odwołanie z powrotem do oryginalnej kolumny klucza podstawowego odpowiadającej wierszowi oryginalnej tablicy (za pośrednictwem kolumny _id)
* Wskazanie pozycji danych w oryginalnej tablicy
* Rozwinięte dane dla każdego elementu w tablicy

Tabela "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |wyskakujący |456 |0,2 |
| 1111 |1 |124 |laboratoryjn |1235 |0,2 |
| 2222 |0 |135 |lodówki |12543 |0,0 |

Tabela "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej na temat mapowania kolumn w źródłowym zestawie danych na kolumny w datadataset, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzanie odczytu ze źródeł relacyjnych
Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Zobacz [powtarzanie odczytu ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.

## <a name="next-steps"></a>Następne kroki
Instrukcje krok po kroku dotyczące tworzenia potoku danych, który przenosi dane z lokalnego magazynu danych do magazynu danych platformy Azure, można znaleźć w artykule [przenoszenie danych między środowiskiem lokalnym i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .
