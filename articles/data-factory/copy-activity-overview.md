---
title: Kopiowanie aktywności w fabryce danych platformy Azure
description: Dowiedz się więcej o działaniu kopiowania w usłudze Azure Data Factory. Służy do kopiowania danych z obsługiwanego magazynu danych źródłowych do obsługiwanego magazynu danych ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 2557ce7be44f0505b96df06cd2b44a2fa3ce3fdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414216"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopiowanie aktywności w fabryce danych platformy Azure

> [!div class="op_single_selector" title1="Wybierz używana wersja fabryki danych:"]
> * [Wersja 1](v1/data-factory-data-movement-activities.md)
> * [Bieżąca wersja](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W usłudze Azure Data Factory można użyć copy activity do kopiowania danych między magazynami danych znajdującymi się lokalnie i w chmurze. Po skopiowaniu danych można użyć innych działań, aby je dalej przekształcać i analizować. Można również użyć kopiuj działania do publikowania wyników transformacji i analizy dla analizy biznesowej (BI) i zużycia aplikacji.

![Rola działania Kopiowanie](media/copy-activity-overview/copy-activity.png)

Działanie Kopiowanie jest wykonywane w [czasie wykonywania integracji](concepts-integration-runtime.md). Można użyć różnych typów środowiska wykonawczego integracji dla różnych scenariuszy kopiowania danych:

* Podczas kopiowania danych między dwoma magazynami danych, które są publicznie dostępne za pośrednictwem Internetu z dowolnego adresu IP, można użyć środowiska uruchomieniowego integracji platformy Azure dla działania kopiowania. To środowisko wykonawcze integracji jest bezpieczne, niezawodne, skalowalne i [dostępne globalnie.](concepts-integration-runtime.md#integration-runtime-location)
* Podczas kopiowania danych do i z magazynów danych, które znajdują się lokalnie lub w sieci z kontrolą dostępu (na przykład sieci wirtualnej platformy Azure), należy skonfigurować środowisko uruchomieniowe integracji hostowanego samodzielnie.

Środowisko uruchomieniowe integracji musi być skojarzone z każdym źródłem i magazynem danych ujścia. Aby uzyskać informacje o tym, jak działanie kopiowanie określa, którego środowiska uruchomieniowego integracji użyć, zobacz [Określanie, które środowisko IR ma być używane.](concepts-integration-runtime.md#determining-which-ir-to-use)

Aby skopiować dane ze źródła do ujścia, usługa uruchamiana przez działanie Kopiowanie wykonuje następujące kroki:

1. Odczytuje dane ze źródłowego magazynu danych.
2. Wykonuje serializacji/deserializacji, kompresji/dekompresji, mapowanie kolumn i tak dalej. Wykonuje te operacje na podstawie konfiguracji wejściowego zestawu danych, wyjściowego zestawu danych i działania kopiowania.
3. Zapisuje dane do magazynu danych ujścia/miejsca docelowego.

![Omówienie działania kopiowania](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Obsługiwane magazyny i formaty danych

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Obsługiwane formaty plików

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Za pomocą działania Kopiuj można kopiować pliki w stanie takim, w jakim znajdują się dwa magazyny danych opartych na plikach, w którym to przypadku dane są kopiowane wydajnie bez serializacji lub deserializacji. Ponadto można również analizować lub generować pliki o danym formacie, na przykład można wykonać następujące czynności:

* Kopiowanie danych z lokalnej bazy danych programu SQL Server i zapisywanie w usłudze Azure Data Lake Storage Gen2 w formacie Parquet.
* Kopiowanie plików w formacie CSV (CSV) z lokalnego systemu plików i zapisywanie w magazynie obiektów Blob platformy Azure w formacie Avro.
* Skopiuj spakowane pliki z lokalnego systemu plików, zdekompresuj je w locie i zapisuj wyodrębnione pliki w usłudze Azure Data Lake Storage Gen2.
* Skopiuj dane w formacie CSV (Gzip skompresowanego tekstu) z magazynu obiektów blob platformy Azure i zapisz je w bazie danych SQL Azure.
* Wiele innych działań, które wymagają serializacji/deserializacji lub kompresji/dekompresji.

## <a name="supported-regions"></a>Obsługiwane regiony

Usługa, która włącza działanie kopiowania jest dostępna globalnie w regionach i regionach wymienionych w [lokalizacjach środowiska uruchomieniowego integracji platformy Azure.](concepts-integration-runtime.md#integration-runtime-location) Globalnie dostępna topologia zapewnia efektywne przenoszenie danych, które zwykle pozwala uniknąć przeskoków między regionami. Zobacz [Produkty według regionów,](https://azure.microsoft.com/regions/#services) aby sprawdzić dostępność fabryki danych i przenoszenia danych w określonym regionie.

## <a name="configuration"></a>Konfigurowanie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Ogólnie rzecz biorąc, aby użyć działania kopiowania w usłudze Azure Data Factory, należy:

1. **Tworzenie połączonych usług dla magazynu danych źródłowych i magazynu danych ujścia.** Listę obsługiwanych łączników można znaleźć w sekcji [Obsługiwane magazyny danych i formaty](#supported-data-stores-and-formats) tego artykułu. Informacje o konfiguracji i obsługiwane właściwości można znaleźć w sekcji "Połączone właściwości usługi" w artykule łącznika. 
2. **Tworzenie zestawów danych dla źródła i ujścia.** Informacje o konfiguracji i obsługiwane właściwości można znaleźć w sekcjach "Właściwości zestawu danych" artykułów łącznika źródłowego i ujścia.
3. **Utwórz potok z działaniem Kopiuj.** Następna sekcja zawiera przykład.

### <a name="syntax"></a>Składnia

Poniższy szablon działania kopiowania zawiera pełną listę obsługiwanych właściwości. Określ te, które pasują do twojego scenariusza.

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

| Właściwość | Opis | Wymagana? |
|:--- |:--- |:--- |
| type | W przypadku działania kopiowania ustaw`Copy` | Tak |
| Wejścia | Określ utworzony zestaw danych, który wskazuje dane źródłowe. Działanie Kopiowanie obsługuje tylko jedno dane wejściowe. | Tak |
| Wyjść | Określ utworzony zestaw danych, który wskazuje dane ujścia. Działanie Kopiowanie obsługuje tylko jedno wyjście. | Tak |
| typeProperties | Określ właściwości, aby skonfigurować działanie Kopiowanie. | Tak |
| source | Określ typ źródła kopiowania i odpowiednie właściwości pobierania danych.<br/>Aby uzyskać więcej informacji, zobacz sekcję "Kopiowanie właściwości działania" w artykule łącznika wymienionym w [obsługiwanych magazynach danych i formatach](#supported-data-stores-and-formats). | Tak |
| Zlew | Określ typ ujścia kopii i odpowiednie właściwości do zapisywania danych.<br/>Aby uzyskać więcej informacji, zobacz sekcję "Kopiowanie właściwości działania" w artykule łącznika wymienionym w [obsługiwanych magazynach danych i formatach](#supported-data-stores-and-formats). | Tak |
| tłumacz | Określ jawne mapowania kolumn od źródła do ujścia. Ta właściwość ma zastosowanie, gdy domyślne zachowanie kopiowania nie spełnia twoich potrzeb.<br/>Aby uzyskać więcej informacji, zobacz [Mapowanie schematu w działaniu kopiowania](copy-activity-schema-and-type-mapping.md). | Nie |
| dataIntegrationJednostki | Określ miarę, która reprezentuje ilość energii używanej przez [środowisko wykonawcze integracji platformy Azure](concepts-integration-runtime.md) do kopiowania danych. Jednostki te były wcześniej znane jako jednostki przenoszenia danych w chmurze (DMU). <br/>Aby uzyskać więcej informacji, zobacz [Jednostki integracji danych](copy-activity-performance-features.md#data-integration-units). | Nie |
| parallelCopies (Kopia równoległa) | Określ równoległość, które mają być używane podczas odczytywania danych ze źródła i zapisywania danych do ujścia.<br/>Aby uzyskać więcej informacji, zobacz [Kopiowanie równoległe](copy-activity-performance-features.md#parallel-copy). | Nie |
| Zachować | Określ, czy metadane/listy ACL mają być zachowywane podczas kopiowania danych. <br/>Aby uzyskać więcej informacji, zobacz [Zachowywanie metadanych](copy-activity-preserve-metadata.md). |Nie |
| enableStaging (włączaniestaging)<br/>stagingSettings | Określ, czy dane tymczasowe w magazynie obiektów Blob zamiast bezpośrednio kopiować dane ze źródła do ujścia.<br/>Aby uzyskać informacje o przydatnych scenariuszach i szczegółach konfiguracji, zobacz [Kopia etapowa](copy-activity-performance-features.md#staged-copy). | Nie |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Wybierz sposób obsługi niezgodnych wierszy podczas kopiowania danych ze źródła do ujścia.<br/>Aby uzyskać więcej informacji, zobacz [Odporność na uszkodzenia](copy-activity-fault-tolerance.md). | Nie |

## <a name="monitoring"></a>Monitorowanie

Można monitorować działanie kopiowania uruchamiane w fabryce danych platformy Azure zarówno wizualnie, jak i programowo. Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie aktywności kopiowania](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Kopia przyrostowa

Usługa Data Factory umożliwia przyrostowe kopiowanie danych różnicowych z magazynu danych źródłowych do magazynu danych ujścia. Aby uzyskać szczegółowe informacje, zobacz [Samouczek: Przyrostowo kopiuj dane](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Środowisko [monitorowania aktywności kopiowania](copy-activity-monitoring.md) pokazuje statystyki wydajności kopiowania dla każdego uruchomienia aktywności. [W przewodniku Wydajność i skalowalność działania kopiowania](copy-activity-performance.md) opisano kluczowe czynniki, które wpływają na wydajność przenoszenia danych za pośrednictwem działania Kopiowanie w usłudze Azure Data Factory. Zawiera również listę wartości wydajności obserwowanych podczas testowania i omówiono sposób optymalizacji wydajności działania kopiowania.

## <a name="resume-from-last-failed-run"></a>Wznawianie z ostatniego nieudanego uruchomienia

Działanie kopiowania obsługuje wznowienie od ostatniego nieudanego uruchomienia podczas kopiowania dużych rozmiarów plików w formacie binarnym między magazynami opartymi na plikach i wybierania opcji zachowania hierarchii folderów/plików ze źródła do ujścia, na przykład w celu migracji danych z amazon S3 do usługi Azure Data Lake Storage Gen2. Dotyczy to następujących łączników opartych na plikach: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [System plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)i [SFTP](connector-sftp.md).

Wznowienie działania kopiowania można wykorzystać na dwa sposoby:

- **Ponów próbę poziomu aktywności:** Możesz ustawić liczbę ponownych prób na działanie kopiowania. Podczas wykonywania potoku, jeśli to uruchomienie działania kopiowania zakończy się niepowodzeniem, następna automatyczna ponowna próba rozpocznie się od punktu awarii ostatniej wersji próbnej.
- **Uruchom ponownie z działania po awarii:** Po zakończeniu wykonywania potoku można również wyzwolić ponowne uruchomienie z nieudanego działania w widoku monitorowania interfejsu użytkownika usługi ADF lub programowo. Jeśli działanie nie powiodło się jest działanie kopiowania, potoku nie tylko ponownie z tego działania, ale także wznowić z poprzedniego uruchomienia punktu awarii.

    ![Kopiuj życiory](media/copy-activity-overview/resume-copy.png)

Kilka punktów do uwagi:

- Wznów na poziomie pliku. Jeśli działanie kopiowania nie powiedzie się podczas kopiowania pliku, w następnym uruchomieniu ten konkretny plik zostanie ponownie skopiowany.
- Aby wznowienie działało poprawnie, nie należy zmieniać ustawień działania kopiowania między powtórzeniami.
- Podczas kopiowania danych z amazon S3, Azure Blob, Azure Data Lake Storage Gen2 i Google Cloud Storage, aktywność kopiowania może zostać wznowiona z dowolnej liczby skopiowanych plików. Podczas gdy w przypadku pozostałych łączników opartych na plikach jako źródło, aktualnie działanie kopiowania obsługuje wznowienie z ograniczonej liczby plików, zwykle w zakresie dziesiątek tysięcy i różni się w zależności od długości ścieżek plików; pliki wykraczające poza ten numer zostaną ponownie skopiowane podczas powtórek.

W przypadku innych scenariuszy niż kopia pliku binarnego ponowne uruchamianie aktywności kopiowania rozpoczyna się od początku.

## <a name="preserve-metadata-along-with-data"></a>Zachowywanie metadanych wraz z danymi

Podczas kopiowania danych ze źródła do ujścia, w scenariuszach, takich jak migracja usługi data lake, można również zachować metadane i listy ACL wraz z danymi przy użyciu działania kopiowania. Zobacz [Zachowywanie metadanych,](copy-activity-preserve-metadata.md) aby uzyskać szczegółowe informacje.

## <a name="schema-and-data-type-mapping"></a>Mapowanie schematu i typu danych

Zobacz [Mapowanie schematu i typów danych,](copy-activity-schema-and-type-mapping.md) aby uzyskać informacje o tym, jak działanie kopiowania mapuje dane źródłowe do ujścia.

## <a name="add-additional-columns-during-copy"></a>Dodawanie dodatkowych kolumn podczas kopiowania

Oprócz kopiowania danych z magazynu danych źródłowych do ujścia, można również skonfigurować, aby dodać dodatkowe kolumny danych do kopiowania wraz z ujścia. Przykład:

- Podczas kopiowania ze źródła opartego na plikach należy przechowywać względną ścieżkę pliku jako dodatkową kolumnę do śledzenia, z którego pliku pochodzą dane.
- Dodaj kolumnę z wyrażeniem ADF, aby dołączyć zmienne systemowe ADF, takie jak nazwa potoku/identyfikator potoku, lub zapisz inną wartość dynamiczną z danych wyjściowych działania nadrzędnego.
- Dodaj kolumnę o wartości statycznej, aby spełnić swoje potrzeby związane z zużyciem.

Następującą konfigurację można znaleźć na karcie źródło działania kopiowania: 

![Dodawanie dodatkowych kolumn w działaniu kopiowania](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Ta funkcja działa z najnowszym modelem zestawu danych. Jeśli nie widzisz tej opcji w interfejsie użytkownika, spróbuj utworzyć nowy zestaw danych.

Aby skonfigurować go programowo, `additionalColumns` dodaj właściwość w źródle aktywności kopiowania:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| dodatkoweKolumny | Dodaj dodatkowe kolumny danych do skopiowania do ujścia.<br><br>Każdy obiekt `additionalColumns` w tablicy reprezentuje dodatkową kolumnę. Definiuje `name` nazwę kolumny, a `value` wskazuje wartość danych tej kolumny.<br><br>Dozwolone wartości danych to:<br>- **`$$FILEPATH`**- zmienna zastrzeżona wskazuje, że do przechowywania względnej ścieżki plików źródłowych do ścieżki folderu określonej w zestawie danych. Zastosuj do źródła opartego na plikach.<br>- **Wyrażenie**<br>- **Wartość statyczna** | Nie |

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

## <a name="fault-tolerance"></a>Odporność na uszkodzenia

Domyślnie działanie Kopiowanie zatrzymuje kopiowanie danych i zwraca błąd, gdy wiersze danych źródłowych są niezgodne z wierszami danych ujścia. Aby kopia powiodła się, można skonfigurować działanie Kopiowanie, aby pominąć i zarejestrować niezgodne wiersze i skopiować tylko zgodne dane. Szczegółowe informacje można znaleźć w części Odporność na [uszkodzenia działania kopiowania.](copy-activity-fault-tolerance.md)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące przewodniki Szybki start, samouczki i przykłady:

- [Kopiowanie danych z jednej lokalizacji do innej lokalizacji na tym samym koncie magazynu obiektów Blob platformy Azure](quickstart-create-data-factory-dot-net.md)
- [Kopiowanie danych z magazynu obiektów blob platformy Azure do bazy danych SQL usługi Azure](tutorial-copy-data-dot-net.md)
- [Kopiowanie danych z lokalnej bazy danych programu SQL Server na platformę Azure](tutorial-hybrid-copy-powershell.md)
