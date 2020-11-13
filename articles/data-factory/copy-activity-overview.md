---
title: Działanie kopiowania w Azure Data Factory
description: Dowiedz się więcej o działaniu kopiowania w Azure Data Factory. Za jego pomocą można skopiować dane z obsługiwanego źródłowego magazynu danych do obsługiwanego magazynu danych ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: ef7f7ba659d874d4514c45e9391912f3027e2265
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592060"
---
# <a name="copy-activity-in-azure-data-factory"></a>Działanie kopiowania w Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję Data Factory:"]
> * [Wersja 1](v1/data-factory-data-movement-activities.md)
> * [Bieżąca wersja](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W Azure Data Factory można użyć działania kopiowania do kopiowania danych między magazynami danych znajdującymi się w środowisku lokalnym i w chmurze. Po skopiowaniu danych można użyć innych działań do dalszej transformacji i przeanalizowania. Możesz również użyć działania kopiowania, aby opublikować wyniki transformacji i analizy dla analizy biznesowej i użycia aplikacji.

![Rola działania kopiowania](media/copy-activity-overview/copy-activity.png)

Działanie kopiowania jest wykonywane w [środowisku Integration Runtime](concepts-integration-runtime.md). Możesz użyć różnych typów środowiska Integration Runtime dla różnych scenariuszy kopiowania danych:

* W przypadku kopiowania danych między dwoma magazynami danych, które są publicznie dostępne za pośrednictwem Internetu z dowolnego adresu IP, możesz użyć środowiska Azure Integration Runtime dla działania kopiowania. To środowisko Integration Runtime jest bezpieczne, niezawodne, skalowalne i [dostępne globalnie](concepts-integration-runtime.md#integration-runtime-location).
* Podczas kopiowania danych do i z magazynów danych, które znajdują się lokalnie lub w sieci z kontrolą dostępu (na przykład w sieci wirtualnej platformy Azure), należy skonfigurować własne środowisko Integration Runtime.

Środowisko Integration Runtime musi być skojarzone z poszczególnymi magazynami danych źródła i ujścia. Aby uzyskać informacje o tym, jak działanie kopiowania określa, które środowisko Integration Runtime ma być używane, zobacz [Określanie, który z nich ma być używany](concepts-integration-runtime.md#determining-which-ir-to-use).

Aby skopiować dane ze źródła do ujścia, usługa, która uruchamia działanie kopiowania, wykonuje następujące czynności:

1. Odczytuje dane z magazynu danych źródłowych.
2. Wykonuje serializacji/deserializacji, kompresję/dekompresowanie, Mapowanie kolumn i tak dalej. Wykonuje te operacje na podstawie konfiguracji wejściowego zestawu danych, wyjściowego zestawu danych i działania kopiowania.
3. Zapisuje dane w magazynie danych ujścia/docelowy.

![Omówienie działania kopiowania](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Obsługiwane magazyny i formaty danych

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Obsługiwane formaty plików

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Możesz użyć działania kopiowania, aby skopiować pliki między dwoma magazynami danych opartymi na plikach. w takim przypadku dane są kopiowane efektywnie bez serializacji ani deserializacji. Ponadto można również analizować lub generować pliki danego formatu, na przykład, można wykonać następujące czynności:

* Skopiuj dane z bazy danych SQL Server i Zapisz do Azure Data Lake Storage Gen2 w formacie Parquet.
* Skopiuj pliki w formacie tekstu (CSV) z lokalnego systemu plików i Zapisz w usłudze Azure Blob Storage w formacie Avro.
* Skopiuj pliki spakowane z lokalnego systemu plików, Dekompresuj je na bieżąco i napisz wyodrębnione pliki do Azure Data Lake Storage Gen2.
* Skopiuj dane w formacie skompresowanego tekstu (CSV) w usłudze Azure Blob Storage i Zapisz je w Azure SQL Database.
* Wiele innych działań, które wymagają serializacji/deserializacji lub kompresji/dekompresji.

## <a name="supported-regions"></a>Obsługiwane regiony

Usługa, która umożliwia działanie kopiowania, jest dostępna globalnie w regionach i lokalizacje geograficzne na liście [lokalizacji w usłudze Azure Integration Runtime](concepts-integration-runtime.md#integration-runtime-location). Topologia dostępna globalnie zapewnia wydajne przenoszenie danych, które zwykle pozwala uniknąć przeskoków między regionami. Zobacz [produkty według regionów](https://azure.microsoft.com/regions/#services) , aby sprawdzić dostępność Data Factory i przenoszenia danych w określonym regionie.

## <a name="configuration"></a>Konfiguracja

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Ogólnie rzecz biorąc, aby użyć działania kopiowania w Azure Data Factory, należy wykonać następujące czynności:

1. **Utwórz połączone usługi dla źródłowego magazynu danych i magazynu danych ujścia.** Listę obsługiwanych łączników można znaleźć w sekcji [obsługiwane magazyny i formaty danych](#supported-data-stores-and-formats) w tym artykule. Informacje o konfiguracji i obsługiwane właściwości można znaleźć w sekcji "właściwości połączonej usługi" w artykule. 
2. **Utwórz zestawy danych dla źródła i ujścia.** Zapoznaj się z sekcjami "właściwości zestawu danych" w artykule dotyczącym łącznika źródła i ujścia, aby uzyskać informacje o konfiguracji i obsługiwane właściwości.
3. **Utwórz potok za pomocą działania kopiowania.** Następna sekcja zawiera przykład.

### <a name="syntax"></a>Składnia

Następujący szablon działania kopiowania zawiera pełną listę obsługiwanych właściwości. Określ te, które pasują do Twojego scenariusza.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Szczegóły składni

| Właściwość | Opis | Wymagane? |
|:--- |:--- |:--- |
| typ | Dla działania kopiowania ustaw wartość na `Copy` | Tak |
| danych wejściowych | Określ utworzony zestaw danych, który wskazuje na dane źródłowe. Działanie kopiowania obsługuje tylko pojedyncze dane wejściowe. | Tak |
| wydajności | Określ utworzony zestaw danych, który wskazuje na dane ujścia. Działanie kopiowania obsługuje tylko pojedyncze dane wyjściowe. | Tak |
| typeProperties | Określ właściwości, aby skonfigurować działanie kopiowania. | Tak |
| source | Określ typ źródła kopiowania i odpowiednie właściwości do pobierania danych.<br/>Aby uzyskać więcej informacji, zobacz sekcję "właściwości działania kopiowania" w artykule łącznika wymienionym w temacie [obsługiwane magazyny i formaty danych](#supported-data-stores-and-formats). | Tak |
| fotografii | Określ typ ujścia kopiowania i odpowiadające im właściwości zapisywania danych.<br/>Aby uzyskać więcej informacji, zobacz sekcję "właściwości działania kopiowania" w artykule łącznika wymienionym w temacie [obsługiwane magazyny i formaty danych](#supported-data-stores-and-formats). | Tak |
| tłumacz | Określ jawne mapowania kolumn ze źródła do ujścia. Ta właściwość ma zastosowanie, gdy domyślne zachowanie kopiowania nie spełnia Twoich potrzeb.<br/>Aby uzyskać więcej informacji, zobacz [Mapowanie schematu w działaniu kopiowania](copy-activity-schema-and-type-mapping.md). | Nie |
| dataIntegrationUnits | Określ miarę, która przedstawia ilość mocy używanej przez [środowisko Azure Integration Runtime](concepts-integration-runtime.md) do kopiowania danych. Te jednostki były wcześniej znane jako jednostki przenoszenia danych w chmurze (DMU). <br/>Aby uzyskać więcej informacji, zobacz [jednostki integracji danych](copy-activity-performance-features.md#data-integration-units). | Nie |
| parallelCopies | Określ równoległość, która ma być używana przez działanie kopiowania podczas odczytywania danych ze źródła i zapisywania danych do ujścia.<br/>Aby uzyskać więcej informacji, zobacz [Kopiowanie równoległe](copy-activity-performance-features.md#parallel-copy). | Nie |
| zachowywał | Określ, czy podczas kopiowania danych mają być zachowywane metadane/listy ACL. <br/>Aby uzyskać więcej informacji, zobacz [zachowywanie metadanych](copy-activity-preserve-metadata.md). |Nie |
| enableStaging<br/>stagingSettings | Określ, czy przemieścić dane tymczasowe w magazynie obiektów blob, zamiast bezpośrednio kopiować dane ze źródła do ujścia.<br/>Aby uzyskać informacje na temat przydatnych scenariuszy i szczegółów konfiguracji, zobacz [przygotowane kopie](copy-activity-performance-features.md#staged-copy). | Nie |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Wybierz sposób obsługi niezgodnych wierszy podczas kopiowania danych ze źródła do ujścia.<br/>Aby uzyskać więcej informacji, zobacz [odporność na uszkodzenia](copy-activity-fault-tolerance.md). | Nie |

## <a name="monitoring"></a>Monitorowanie

Można monitorować przebieg działania kopiowania w Azure Data Factory wizualnie i programowo. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie działania kopiowania](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Kopia przyrostowa

Data Factory umożliwia przyrostowe kopiowanie danych różnicowych z magazynu danych źródłowych do magazynu danych ujścia. Aby uzyskać szczegółowe informacje, zobacz [Samouczek: przyrostowo Kopiuj dane](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Środowisko [monitorowania działania kopiowania](copy-activity-monitoring.md) przedstawia statystyki wydajności kopiowania dla każdego uruchomienia działania. [Przewodnik dotyczący wydajności i skalowalności działania kopiowania](copy-activity-performance.md) opisuje kluczowe czynniki wpływające na wydajność przenoszenia danych za pośrednictwem działania kopiowania w Azure Data Factory. Wyświetla również wartości wydajności zaobserwowane podczas testowania i omawia sposób optymalizowania wydajności działania kopiowania.

## <a name="resume-from-last-failed-run"></a>Wznowienie od ostatniego nieudanego uruchomienia

Działanie kopiowania obsługuje wznowienie od ostatniego nieudanego uruchomienia, gdy kopiujesz duży rozmiar plików jako-jest w formacie binarnym między magazynami opartymi na plikach i chcesz zachować hierarchię folderów/plików ze źródła do ujścia, np. w celu migrowania danych z usługi Amazon S3 do Azure Data Lake Storage Gen2. Dotyczy to następujących łączników opartych na plikach: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)i [SFTP](connector-sftp.md).

Możesz użyć wznowienia działania kopiowania na dwa sposoby:

- **Ponowna próba poziomu działania:** Możesz ustawić liczbę ponownych prób dla działania kopiowania. W trakcie wykonywania potoku, Jeśli uruchomienie tego działania kopiowania nie powiedzie się, następne automatyczne ponawianie prób rozpocznie się od punktu awarii z ostatniej wersji próbnej.
- **Uruchom ponownie z działania zakończonego niepowodzeniem:** Po zakończeniu wykonywania potoku można także wyzwolić ponowne uruchomienie z działania zakończonego niepowodzeniem w widoku monitorowania interfejsu użytkownika usługi ADF lub programowo. Jeśli działanie zakończone niepowodzeniem jest działaniem kopiowania, potok nie zostanie ponownie uruchomiony z tego działania, ale również zostanie wznowiony od punktu awarii poprzedniego przebiegu.

    ![Wznowienie kopiowania](media/copy-activity-overview/resume-copy.png)

Kilka punktów do uwagi:

- Wznowienie odbywa się na poziomie pliku. Jeśli działanie kopiowania zakończy się niepowodzeniem podczas kopiowania pliku, w następnym uruchomieniu ten określony plik zostanie ponownie skopiowany.
- Aby wznowienie działało prawidłowo, nie należy zmieniać ustawień działania kopiowania między ponownie uruchomionym programem.
- Podczas kopiowania danych z usługi Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 i Google Cloud Storage, działanie Copy można wznowić z dowolnej liczby skopiowanych plików. W przypadku pozostałych łączników opartych na plikach jako źródło, obecnie działanie kopiowania obsługuje wznowienie od ograniczonej liczby plików, zwykle z zakresu liczby tysięcy i różni się w zależności od długości ścieżek plików; Pliki wykraczające poza tę liczbę zostaną ponownie skopiowane podczas ponownego uruchomienia.

W przypadku innych scenariuszy niż kopiowanie plików binarnych ponowne uruchomienie działania kopiowania rozpoczyna się od początku.

## <a name="preserve-metadata-along-with-data"></a>Zachowywanie metadanych wraz z danymi

Podczas kopiowania danych ze źródła do ujścia, w scenariuszach takich jak Data Lake Migration, można również zachować metadane i listy kontroli dostępu oraz dane za pomocą działania kopiowania. Aby uzyskać szczegółowe informacje, zobacz temat [zachowywanie metadanych](copy-activity-preserve-metadata.md) .

## <a name="schema-and-data-type-mapping"></a>Mapowanie schematu i typu danych

Zobacz [Mapowanie schematu i typu danych,](copy-activity-schema-and-type-mapping.md) Aby uzyskać informacje o tym, jak działanie kopiowania mapuje dane źródłowe do ujścia.

## <a name="add-additional-columns-during-copy"></a>Dodaj dodatkowe kolumny podczas kopiowania

Oprócz kopiowania danych ze źródłowego magazynu danych do ujścia, można również skonfigurować program, aby dodać do niego dodatkowe kolumny danych. Przykład:

- Podczas kopiowania z lokalizacji źródłowej plików należy przechowywać względną ścieżkę pliku jako dodatkową kolumnę do śledzenia, z którego pliku pochodzą dane.
- Duplikuj określoną kolumnę źródłową jako inną kolumnę. 
- Dodaj kolumnę z wyrażeniem ADF, aby dołączyć zmienne systemowe ADF, takie jak nazwa potoku/identyfikator potoku, lub przechowywać inną wartość dynamiczną z danych wyjściowych działania nadrzędnego.
- Dodaj kolumnę z wartością statyczną, aby spełnić wymagania dotyczące użycia.

Poniższą konfigurację można znaleźć na karcie Źródło działania kopiowania. Możesz również zmapować te dodatkowe kolumny w [mapowaniu schematu](copy-activity-schema-and-type-mapping.md#schema-mapping) działania kopiowania w zwykły sposób, używając zdefiniowanych nazw kolumn. 

![Dodawanie dodatkowych kolumn w działaniu kopiowania](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Ta funkcja działa z najnowszym modelem zestawu danych. Jeśli ta opcja nie jest widoczna z poziomu interfejsu użytkownika, spróbuj utworzyć nowy zestaw danych.

Aby programowo skonfigurować go, Dodaj `additionalColumns` Właściwość w źródle działania kopiowania:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| additionalColumns | Dodaj dodatkowe kolumny danych do skopiowania do ujścia.<br><br>Każdy obiekt w `additionalColumns` tablicy reprezentuje dodatkową kolumnę. `name`Definiuje nazwę kolumny i `value` wskazuje wartość danych tej kolumny.<br><br>Dozwolone wartości danych to:<br>- **`$$FILEPATH`** -Zmienna zastrzeżona wskazuje na przechowywanie ścieżki względnej plików źródłowych do ścieżki folderu określonej w zestawie danych. Zastosuj do źródła opartego na plikach.<br>- **`$$COLUMN:<source_column_name>`** -wzór zmiennej zastrzeżonej wskazuje na duplikowanie określonej kolumny źródłowej jako innej kolumny<br>- **Wyrażenia**<br>- **Wartość statyczna** | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>Utwórz ponownie tabele ujścia

W przypadku kopiowania danych do usługi SQL Database/Azure Synapse Analytics, jeśli tabela docelowa nie istnieje, działanie kopiowania obsługuje automatyczne tworzenie go na podstawie danych źródłowych. Ma ona na celu ułatwienie szybkiego rozpoczęcia ładowania danych oraz ocenę usługi SQL Database/Azure Synapse Analytics. Po pozyskaniu danych można przejrzeć i dostosować schemat tabeli ujścia zgodnie z potrzebami.

Ta funkcja jest obsługiwana podczas kopiowania danych z dowolnego źródła do następujących magazynów danych ujścia. Możesz znaleźć opcję w *interfejsie użytkownika tworzenia ADF* *— >-* > *opcji tabeli* — > *automatycznego tworzenia tabeli* lub za pośrednictwem `tableOption` właściwości w ładunku ujścia działania kopiowania.

- [Azure SQL Database](connector-azure-sql-database.md)
- [Wystąpienie zarządzane usługi Azure SQL Database](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics (dawniej Azure SQL Data Warehouse)](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Tworzenie tabel ujścia](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Odporność na uszkodzenia

Domyślnie działanie kopiowania kończy kopiowanie danych i zwraca błąd, gdy wiersze danych źródłowych są niezgodne z wierszami danych ujścia. Aby pomyślnie wykonać kopię, można skonfigurować działanie kopiowania, aby pominąć i zarejestrować niezgodne wiersze i skopiować tylko zgodne dane. Aby uzyskać szczegółowe informacje, zobacz [odporność na błędy działania kopiowania](copy-activity-fault-tolerance.md) .

## <a name="data-consistency-verification"></a>Weryfikacja spójności danych

Po przeniesieniu danych ze źródła do magazynu docelowego, Azure Data Factory działanie kopiowania oferuje dodatkową weryfikację spójności danych, aby upewnić się, że dane nie zostały pomyślnie skopiowane do magazynu docelowego, ale również zweryfikowane jako zgodne z magazynem źródłowym i docelowym. Po znalezieniu niespójnych plików podczas przenoszenia danych można przerwać działanie kopiowania lub kontynuować kopiowanie reszty przez włączenie ustawienia odporności na uszkodzenia, aby pominąć niespójne pliki. Pominięte nazwy plików można uzyskać, włączając ustawienie dziennika sesji w działaniu kopiowania. Aby uzyskać szczegółowe informacje, zobacz [Weryfikacja spójności danych w działaniu kopiowania](copy-activity-data-consistency.md) .

## <a name="session-log"></a>Dziennik sesji
Można rejestrować skopiowane nazwy plików, co może pomóc w dokładniejszym zapewnieniu, że dane nie zostaną pomyślnie skopiowane z lokalizacji źródłowej do magazynu docelowego, ale również spójne między magazynem źródłowym i docelowym, przeglądając dzienniki sesji działania kopiowania. Aby uzyskać szczegółowe informacje, zobacz [dziennik sesji w działaniu kopiowania](copy-activity-log.md) .

## <a name="next-steps"></a>Następne kroki
Zobacz następujące Przewodniki Szybki Start, samouczki i przykłady:

- [Skopiuj dane z jednej lokalizacji do innej lokalizacji na tym samym koncie usługi Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Kopiowanie danych z usługi Azure Blob Storage do Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiowanie danych z bazy danych SQL Server na platformę Azure](tutorial-hybrid-copy-powershell.md)
