---
title: Poznanie danych wyjściowych z Azure Stream Analytics
description: W tym artykule opisano opcje danych wyjściowych dostępne w Azure Stream Analytics, w tym Power BI wyników analizy.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/8/2020
ms.openlocfilehash: c4790585d089ab287260f74001a8aa3f1cb7e5f7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647503"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Poznanie danych wyjściowych z Azure Stream Analytics

W tym artykule opisano typy danych wyjściowych dostępnych dla zadania Azure Stream Analytics. Dane wyjściowe umożliwiają przechowywanie i zapisywanie wyników zadania Stream Analytics. Korzystając z danych wyjściowych, można wykonywać dalsze analizy biznesowe i magazynowanie danych.

Podczas projektowania zapytania o Stream Analytics należy odwołać się do nazwy danych wyjściowych za pomocą [klauzuli into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Możesz użyć jednego danych wyjściowych na zadanie lub wiele wyjść na zadanie przesyłania strumieniowego (jeśli są potrzebne), dostarczając wiele klauzul INTO do zapytania.

Aby tworzyć, edytować i testować Stream Analytics dane wyjściowe zadań, możesz użyć [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), interfejsu API [rest](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)i [programu Visual Studio](stream-analytics-quick-create-vs.md).

Niektóre typy danych wyjściowych obsługują [partycjonowanie](#partitioning). [Rozmiary partii danych wyjściowych](#output-batch-size) różnią się w zależności od wydajności.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics obsługuje [Azure Data Lake Storage generacji 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage to całe repozytorium w całym przedsiębiorstwie na potrzeby obciążeń analitycznych danych Big Data. Za pomocą Data Lake Storage można przechowywać dane dowolnego rozmiaru, typu i szybkość pozyskiwania dla analiz operacyjnych i poznawczych. Stream Analytics musi mieć autoryzację, aby uzyskać dostęp do Data Lake Storage.

Azure Data Lake Storage dane wyjściowe z Stream Analytics nie są obecnie dostępne w regionach platformy Azure w Chinach (Chiny) i Azure (Niemcy).

W poniższej tabeli wymieniono nazwy właściwości i ich opisy w celu skonfigurowania danych wyjściowych Data Lake Storage generacji 1.   

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do Data Lake Store. |
| Subskrypcja | Subskrypcja, która zawiera konto Azure Data Lake Storage. |
| Nazwa konta | Nazwa konta Data Lake Store, do którego wysyłane są dane wyjściowe. Zostanie wyświetlona lista rozwijana kont Data Lake Store, które są dostępne w ramach subskrypcji. |
| Wzorzec prefiksu ścieżki | Ścieżka pliku, która jest używana do zapisywania plików w ramach określonego konta Data Lake Store. Można określić jedno lub więcej wystąpień zmiennych {date} i {Time}:<br /><ul><li>Przykład 1: Folder1/dzienniki/{Date}/{Time}</li><li>Przykład 2: Folder1/dzienniki/{Date}</li></ul><br />Sygnatura czasowa utworzonej struktury folderów następuje po UTC, a nie na czas lokalny.<br /><br />Jeśli wzorzec ścieżki pliku nie zawiera końcowego ukośnika (/), ostatni wzorzec w ścieżce pliku jest traktowany jako prefiks nazwy pliku. <br /><br />Nowe pliki są tworzone w następujących okolicznościach:<ul><li>Zmień w schemacie wyjściowym</li><li>Zewnętrzne lub wewnętrzne ponowne uruchomienie zadania</li></ul> |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w którym są zorganizowane pliki. Przykład: RRRR/MM/DD |
|Format czasu | Opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, określ format czasu, w którym są zorganizowane pliki. Obecnie jedyną obsługiwaną wartością jest HH. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV i Avro.|
| Encoding | Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. W tym momencie jedynym obsługiwanym formatem kodowania jest UTF-8.|
| Ogranicznik | Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników na potrzeby serializowania danych woluminu CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek.|
| Format | Dotyczy tylko serializacji JSON. **Linia rozdzielona** określa, że dane wyjściowe są formatowane przy użyciu każdego obiektu JSON oddzielonego przez nowy wiersz. W przypadku wybrania opcji **rozdzielone linią**kod JSON jest odczytywany po jednym obiekcie naraz. Cała zawartość nie będzie prawidłowym kodem JSON.  **Tablica** określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamykana tylko wtedy, gdy zadanie zostało zatrzymane lub Stream Analytics zostało przeniesione do następnego przedziału czasu. Ogólnie rzecz biorąc, zalecane jest użycie kodu JSON rozdzielonego wierszem, ponieważ nie wymaga żadnej specjalnej obsługi, gdy plik wyjściowy jest nadal w trakcie zapisywania.|
| Tryb uwierzytelniania | Możesz autoryzować dostęp do konta Data Lake Storage przy użyciu [tożsamości zarządzanej](stream-analytics-managed-identities-adls.md) lub tokenu użytkownika. Po udzieleniu dostępu można odwołać dostęp poprzez zmianę hasła konta użytkownika, usunięcie Data Lake Storage danych wyjściowych dla tego zadania lub usunięcie zadania Stream Analytics. |

## <a name="sql-database"></a>SQL Database

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) jako dane wyjściowe można użyć jako danych wyjściowych, które są relacyjne, lub dla aplikacji, które są zależne od zawartości hostowanej w relacyjnej bazie danych. Stream Analytics zadania zapisu do istniejącej tabeli w SQL Database. Schemat tabeli musi dokładnie pasować do pól i ich typów w danych wyjściowych zadania. Możesz również określić [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) jako dane wyjściowe za pośrednictwem opcji SQL Database Output. Aby dowiedzieć się więcej na temat sposobów zwiększania przepływności zapisu, zobacz artykuł [Stream Analytics z Azure SQL Database jako dane wyjściowe](stream-analytics-sql-output-perf.md) .

Możesz również użyć [Azure SQL Database wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) jako danych wyjściowych. Należy [skonfigurować publiczny punkt końcowy w Azure SQL Database wystąpieniu zarządzanym](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) , a następnie ręcznie skonfigurować poniższe ustawienia w programie Azure Stream Analytics. Maszyna wirtualna platformy Azure z systemem SQL Server z dołączoną bazą danych jest również obsługiwana przez ręczne skonfigurowanie ustawień poniżej.

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia SQL Database danych wyjściowych.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tej bazy danych. |
| baza danych | Nazwa bazy danych, w której wysyłane są dane wyjściowe. |
| Nazwa serwera | Nazwa serwera SQL Database. Dla Azure SQL Database wystąpienia zarządzanego wymagane jest określenie portu 3342. Na przykład *sampleserver. Public. Database. Windows. NET, 3342* |
| Nazwa użytkownika | Nazwa użytkownika, która ma dostęp do zapisu w bazie danych. Stream Analytics obsługuje tylko uwierzytelnianie SQL. |
| Hasło | Hasło, aby połączyć się z bazą danych. |
| Tabela | Nazwa tabeli, w której zapisano dane wyjściowe. W nazwie tabeli jest rozróżniana wielkość liter. Schemat tej tabeli powinien dokładnie pasować do liczby pól i ich typów generowanych przez dane wyjściowe zadania. |
|Dziedzicz schemat partycji| Opcja dziedziczenia schematu partycjonowania poprzedniego kroku zapytania, w celu włączenia w pełni równoległej topologii z wieloma składnikami zapisywania do tabeli. Aby uzyskać więcej informacji, zobacz [Azure Stream Analytics danych wyjściowych Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maksymalna liczba partii| Zalecany górny limit liczby rekordów wysyłanych z każdą zbiorczą transakcją wstawiania.|

Istnieją dwie karty, które umożliwiają wyjście z Azure Stream Analytics usługi Azure Synapse Analytics (dawniej SQL Data Warehouse): SQL Database i Azure Synapse. Zalecamy wybranie karty usługi Azure Synapse Analytics zamiast karty SQL Database, jeśli spełnione są następujące warunki:

* **Przepływność**: jeśli oczekiwana przepływność teraz lub w przyszłości jest większa niż 10 MB/s, użyj opcji danych wyjściowych usługi Azure Synapse, aby uzyskać lepszą wydajność.

* **Partycje wejściowe**: Jeśli masz osiem lub więcej partycji wejściowych, użyj opcji danych wyjściowych usługi Azure Synapse w celu lepszego skalowania w poziomie.

## <a name="azure-synapse-analytics-preview"></a>Azure Synapse Analytics (wersja zapoznawcza)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (dawniej SQL Data Warehouse) to nieograniczona Usługa analityczna, która łączy magazyn danych w przedsiębiorstwie i analizę danych Big Data. 

Azure Stream Analytics zadania mogą wynikać z tabeli puli SQL w usłudze Azure Synapse Analytics i mogą przetwarzać stawki przepływności do 200 MB/s. Obsługuje to najbardziej wymagające analiz w czasie rzeczywistym i wymaga przetwarzania danych na gorąco w przypadku obciążeń, takich jak raportowanie i pulpit nawigacyjny.  

Tabela puli SQL musi istnieć, aby można było dodać ją jako dane wyjściowe do zadania Stream Analytics. Schemat tabeli musi pasować do pól i ich typów w danych wyjściowych zadania. 

Aby korzystać z usługi Azure Synapse jako danych wyjściowych, należy upewnić się, że konto magazynu zostało skonfigurowane. Przejdź do ustawień konta magazynu, aby skonfigurować konto magazynu. Dozwolone są tylko typy kont magazynu obsługujące tabele: ogólnego przeznaczenia w wersji 2 i ogólnego przeznaczenia w wersji 1. Wybierz tylko warstwę Standardowa. Warstwa Premium nie jest obsługiwana.   

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia danych wyjściowych usługi Azure Synapse Analytics.

|Nazwa właściwości|Opis|
|-|-|
|Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tej bazy danych. |
|baza danych |Nazwa puli SQL, do której wysyłane są dane wyjściowe. |
|Nazwa serwera |Nazwa serwera usługi Azure Synapse.  |
|Nazwa użytkownika |Nazwa użytkownika, która ma dostęp do zapisu w bazie danych. Stream Analytics obsługuje tylko uwierzytelnianie SQL. |
|Hasło |Hasło, aby połączyć się z bazą danych. |
|Tabela  | Nazwa tabeli, w której zapisano dane wyjściowe. W nazwie tabeli jest rozróżniana wielkość liter. Schemat tej tabeli powinien dokładnie pasować do liczby pól i ich typów generowanych przez dane wyjściowe zadania.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>BLOB Storage i Azure Data Lake Gen2

Data Lake Storage Gen2 sprawia, że usługa Azure Storage jest podstawą do tworzenia jezior danych przedsiębiorstwa na platformie Azure. Zaprojektowana od początku do obsługi wielu petabajtów informacji przy zachowaniu setek gigabitowej przepływności, Data Lake Storage Gen2 umożliwia łatwe zarządzanie ogromnymi ilościami danych. Podstawową częścią Data Lake Storage Gen2 jest dodanie hierarchicznej przestrzeni nazw do magazynu obiektów BLOB.

Usługa Azure Blob Storage oferuje ekonomiczne i skalowalne rozwiązanie służące do przechowywania dużych ilości danych bez struktury w chmurze. Aby zapoznać się z wprowadzeniem do usługi BLOB Storage i jej użycia, zobacz [przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia obiektów blob lub ADLS Gen2 danych wyjściowych.

| Nazwa właściwości       | Opis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias danych wyjściowych        | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego magazynu obiektów BLOB. |
| Konto magazynu     | Nazwa konta magazynu, do którego wysyłane są dane wyjściowe.               |
| Klucz konta magazynu | Klucz tajny skojarzony z kontem magazynu.                              |
| Kontener magazynu   | Grupowanie logiczne dla obiektów BLOB przechowywanych w usłudze Azure Blob service. Po przekazaniu obiektu BLOB do Blob service należy określić kontener dla tego obiektu BLOB. |
| Wzorzec ścieżki | Opcjonalny. Wzorzec ścieżki pliku używany do pisania obiektów BLOB w określonym kontenerze. <br /><br /> We wzorcu ścieżki można wybrać, aby użyć jednego lub większej liczby wystąpień zmiennych daty i godziny, aby określić częstotliwość zapisywania obiektów blob: <br /> {Date}, {Time} <br /><br />Możesz użyć niestandardowego partycjonowania obiektów blob, aby określić jedną niestandardową nazwę {Field} z danych zdarzenia do partycjonowania obiektów BLOB. Nazwa pola jest alfanumeryczna i może zawierać spacje, łączniki i podkreślenia. Ograniczenia dotyczące pól niestandardowych są następujące: <ul><li>W nazwach pól nie jest rozróżniana wielkość liter. Na przykład usługa nie może rozróżnić między kolumnami "ID" i kolumną "ID".</li><li>Zagnieżdżone pola są niedozwolone. Zamiast tego należy użyć aliasu w zapytaniu zadania do "Spłaszcz" pola.</li><li>Wyrażenia nie mogą być używane jako nazwa pola.</li></ul> <br />Ta funkcja umożliwia użycie niestandardowych konfiguracji specyfikatora formatu daty i godziny w ścieżce. Niestandardowe formaty daty i godziny muszą być określone pojedynczo, ujęte w słowo kluczowe {DateTime: \< specyfikator>}. Dozwolone dane wejściowe dla \< specyfikatora> to rrrr, mm, m, DD, d, hh, H, mm, m, SS lub s. Słowo kluczowe {DateTime: \< specyfikator>} może być używane wiele razy w ścieżce, aby utworzyć niestandardową konfigurację daty/czasu. <br /><br />Przykłady: <ul><li>Przykład 1: Klaster1/dzienniki/{Date}/{Time}</li><li>Przykład 2: Klaster1/dzienniki/{Date}</li><li>Przykład 3: Klaster1/{client_id}/{Date}/{Time}</li><li>Przykład 4: Klaster1/{DateTime: SS}/{myField}, gdzie zapytanie jest: Wybierz dane. moje pole z danych wejściowych;</li><li>Przykład 5: Klaster1/Year = {DateTime: RRRR}/miesiąc = {DateTime: MM}/Day = {DateTime: DD}</ul><br />Sygnatura czasowa utworzonej struktury folderów następuje po UTC, a nie na czas lokalny.<br /><br />Nazwa pliku używa następującej konwencji: <br /><br />{Wzorzec prefiksu ścieżki}/schemaHashcode_Guid_Number. rozszerzenie<br /><br />Przykładowe pliki wyjściowe:<ul><li>Dane wyjściowe/20170901/00/45434_gguid_1. csv</li>  <li>Dane wyjściowe/20170901/01/45434_gguid_1. csv</li></ul> <br />Aby uzyskać więcej informacji na temat tej funkcji, zobacz [niestandardowe Partycjonowanie danych wyjściowych obiektu blob Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w którym są zorganizowane pliki. Przykład: RRRR/MM/DD |
| Format czasu | Opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, określ format czasu, w którym są zorganizowane pliki. Obecnie jedyną obsługiwaną wartością jest HH. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV, Avro i Parquet. |
|Minimalna ilość wierszy (tylko parquet)|Liczba minimalnych wierszy na partię. Dla Parquet każda partia utworzy nowy plik. Bieżąca wartość domyślna to 2 000 wierszy, a maksymalna dozwolona liczba wierszy to 10 000.|
|Maksymalny czas (tylko parquet)|Maksymalny czas oczekiwania na partię. Po upływie tego czasu partia zostanie zapisywana w danych wyjściowych, nawet jeśli nie spełniono wymagań dotyczących minimalnych wierszy. Bieżąca wartość domyślna to 1 minuta, a maksymalna dozwolona liczba to 2 godziny. Jeśli dane wyjściowe obiektu BLOB mają częstotliwość tworzenia wzorców ścieżki, czas oczekiwania nie może być wyższy niż zakres czasu partycji.|
| Encoding    | Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. W tym momencie jedynym obsługiwanym formatem kodowania jest UTF-8. |
| Ogranicznik   | Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników na potrzeby serializowania danych woluminu CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Format      | Dotyczy tylko serializacji JSON. **Linia rozdzielona** określa, że dane wyjściowe są formatowane przy użyciu każdego obiektu JSON oddzielonego przez nowy wiersz. W przypadku wybrania opcji **rozdzielone linią**kod JSON jest odczytywany po jednym obiekcie naraz. Cała zawartość nie będzie prawidłowym kodem JSON. **Tablica** określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamykana tylko wtedy, gdy zadanie zostało zatrzymane lub Stream Analytics zostało przeniesione do następnego przedziału czasu. Ogólnie rzecz biorąc, zalecane jest użycie kodu JSON rozdzielonego wierszem, ponieważ nie wymaga żadnej specjalnej obsługi, gdy plik wyjściowy jest nadal w trakcie zapisywania. |

W przypadku korzystania z usługi BLOB Storage jako danych wyjściowych w następujących przypadkach tworzony jest nowy plik:

* Jeśli rozmiar pliku przekracza maksymalną dozwoloną liczbę bloków (obecnie 50 000). Może nawiązać połączenie z maksymalną dozwoloną liczbą bloków bez osiągnięcia maksymalnego dozwolonego rozmiaru obiektu BLOB. Na przykład jeśli szybkość danych wyjściowych jest wysoka, można zobaczyć więcej bajtów na blok, a rozmiar pliku jest większy. Jeśli szybkość danych wyjściowych jest niska, każdy blok ma mniejszą ilość danych, a rozmiar pliku jest mniejszy.
* Jeśli istnieje zmiana schematu w danych wyjściowych, a format danych wyjściowych wymaga stałego schematu (CSV i Avro).
* Jeśli zadanie zostanie ponownie uruchomione zewnętrznie przez użytkownika, zatrzymując je i uruchamiając, lub wewnętrznie do konserwacji systemu lub odzyskiwania błędów.
* Jeśli zapytanie jest w pełni partycjonowane i tworzony jest nowy plik dla każdej partycji wyjściowej.
* Jeśli użytkownik usunie plik lub kontener konta magazynu.
* Jeśli dane wyjściowe są podzielone na partycje przy użyciu wzorca prefiksu ścieżki, a nowy obiekt BLOB jest używany podczas przenoszenia zapytania do kolejnej godziny.
* Jeśli dane wyjściowe są podzielone na partycje za pomocą pola niestandardowego, a dla klucza partycji zostanie utworzony nowy obiekt BLOB, jeśli nie istnieje.
* Jeśli dane wyjściowe są podzielone na partycje za pomocą pola niestandardowego, w którym Kardynalność klucza partycji przekracza 8 000, a dla klucza partycji tworzony jest nowy obiekt BLOB.

## <a name="event-hubs"></a>Event Hubs

Usługa [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) jest wysoce skalowalnym pozyskiwaniem zdarzeń publikowania/subskrybowania. Może zbierać miliony zdarzeń na sekundę. Jednym z nich jest użycie centrum zdarzeń jako danych wyjściowych, gdy dane wyjściowe zadania Stream Analytics staną się danymi wejściowymi innego zadania przesyłania strumieniowego. Aby uzyskać informacje o maksymalnym rozmiarze wiadomości i optymalizacji rozmiaru partii, zobacz sekcję [wyjściowy rozmiar wsadu](#output-batch-size) .

Potrzebujesz kilku parametrów, aby skonfigurować strumienie danych z centrów zdarzeń jako dane wyjściowe.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego centrum zdarzeń. |
| Przestrzeń nazw centrum zdarzeń | Kontener dla zestawu jednostek obsługi komunikatów. Po utworzeniu nowego centrum zdarzeń jest również tworzona przestrzeń nazw centrum zdarzeń. |
| Nazwa centrum zdarzeń | Nazwa danych wyjściowych centrum zdarzeń. |
| Nazwa zasad centrum zdarzeń | Zasady dostępu współdzielonego, które można utworzyć na karcie **Konfiguracja** centrum zdarzeń. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. |
| Klucz zasad centrum zdarzeń | Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw centrum zdarzeń. |
| Kolumna klucza partycji | Opcjonalny. Kolumna zawierająca klucz partycji dla danych wyjściowych centrum zdarzeń. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV i Avro. |
| Encoding | W przypadku plików CSV i JSON jedynym obsługiwanym formatem kodowania jest UTF-8. |
| Ogranicznik | Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników do serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Format | Dotyczy tylko serializacji JSON. **Linia rozdzielona** określa, że dane wyjściowe są formatowane przy użyciu każdego obiektu JSON oddzielonego przez nowy wiersz. W przypadku wybrania opcji **rozdzielone linią**kod JSON jest odczytywany po jednym obiekcie naraz. Cała zawartość nie będzie prawidłowym kodem JSON. **Tablica** określa, że dane wyjściowe są formatowane jako tablica obiektów JSON.  |
| Kolumny właściwości | Opcjonalny. Kolumny oddzielone przecinkami, które muszą być dołączone jako właściwości użytkownika wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [właściwości metadanych niestandardowych dla danych wyjściowych](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) można użyć jako danych wyjściowych dla zadania Stream Analytics, aby zapewnić bogate środowisko wizualizacji wyników analizy. Tej funkcji można użyć w przypadku operacyjnych pulpitów nawigacyjnych, generowania raportów i raportowania opartego na metrykach.

Power BI dane wyjściowe z Stream Analytics nie są obecnie dostępne w regionach platformy Azure w Chinach (Chiny) i Azure (Niemcy).

W poniższej tabeli wymieniono nazwy właściwości i ich opisy w celu skonfigurowania danych wyjściowych Power BI.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Podaj przyjazną nazwę używaną w zapytaniach, aby skierować dane wyjściowe zapytania do tego Power BI danych wyjściowych. |
| Obszar roboczy grupy |Aby włączyć udostępnianie danych innym użytkownikom Power BI, możesz wybrać grupy wewnątrz konta Power BI lub wybrać pozycję **Mój obszar roboczy** , jeśli nie chcesz pisać do grupy. Aktualizacja istniejącej grupy wymaga odnowienia Power BI uwierzytelniania. |
| Nazwa zestawu danych |Podaj nazwę zestawu danych, który ma być używany przez dane wyjściowe Power BI. |
| Nazwa tabeli |Podaj nazwę tabeli w zestawie danych danych wyjściowych Power BI. Obecnie Power BI dane wyjściowe z zadań Stream Analytics mogą mieć tylko jedną tabelę w zestawie danych. |
| Autoryzowanie połączenia | Musisz autoryzować się przy użyciu Power BI, aby skonfigurować ustawienia danych wyjściowych. Po udzieleniu tego danych wyjściowych do pulpitu nawigacyjnego Power BI można odwołać dostęp poprzez zmianę hasła konta użytkownika, usunięcie danych wyjściowych zadania lub usunięcie zadania Stream Analytics. | 

Przewodnik konfigurowania Power BI danych wyjściowych i pulpitu nawigacyjnego znajduje się w samouczku [Azure Stream Analytics i Power BI](stream-analytics-power-bi-dashboard.md) .

> [!NOTE]
> Nie twórz jawnie zestawu danych i tabeli na pulpicie nawigacyjnym Power BI. Zestaw danych i tabela są wypełniane automatycznie po rozpoczęciu zadania, a zadanie rozpoczyna pompowanie danych wyjściowych do Power BI. Jeśli zapytanie zadania nie generuje żadnych wyników, zestaw danych i tabela nie zostaną utworzone. Jeśli Power BI już istnieje zestaw danych i tabela o takiej samej nazwie jak nazwa podana w tym zadaniu Stream Analytics, istniejące dane zostaną zastąpione.
>

### <a name="create-a-schema"></a>Utwórz schemat
Azure Stream Analytics tworzy Power BI zestaw danych i schemat tabeli dla użytkownika, jeśli jeszcze nie istnieją. We wszystkich innych przypadkach tabela jest aktualizowana o nowe wartości. Obecnie w zestawie danych może istnieć tylko jedna tabela. 

Power BI używa zasad przechowywania w pierwszej kolejności (FIFO). Dane będą zbierane w tabeli, dopóki nie trafią 200 000 wierszy.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konwertuj typ danych z Stream Analytics na Power BI
Azure Stream Analytics automatycznie aktualizuje model danych w czasie wykonywania, jeśli zmieni się schemat danych wyjściowych. Zmiany nazwy kolumny, zmiany typu kolumny i Dodawanie lub usuwanie kolumn są śledzone.

W tej tabeli opisano konwersje typów danych z [Stream Analytics typów danych](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) do Power BI [Entity Data Model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), jeśli Power BI zestaw danych i tabela nie istnieją.

Z Stream Analytics | Aby Power BI
-----|-----
bigint | Int64
nvarchar (max) | String
datetime | Datetime (data/godzina)
float | Double
Rejestruj tablicę | Typ ciągu, stała wartość "IRecord" lub "IArray"

### <a name="update-the-schema"></a>Aktualizowanie schematu
Stream Analytics wnioskuje schemat modelu danych na podstawie pierwszego zestawu zdarzeń w danych wyjściowych. Później, w razie potrzeby, schemat modelu danych zostanie zaktualizowany w celu uwzględnienia zdarzeń przychodzących, które mogą nie pasować do oryginalnego schematu.

Należy unikać `SELECT *` zapytania, aby zapobiec dynamicznej aktualizacji schematu w wierszach. Oprócz potencjalnego wpływu na wydajność może to spowodować niepewność czasu trwania dla wyników. Wybierz dokładne pola, które mają być wyświetlane na pulpicie nawigacyjnym Power BI. Ponadto wartości danych powinny być zgodne z wybranym typem danych.


Poprzedni/bieżący | Int64 | String | Datetime (data/godzina) | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime (data/godzina) | String | String |  Datetime (data/godzina) | String

## <a name="table-storage"></a>Table Storage

[Usługa Azure Table Storage](../storage/common/storage-introduction.md) oferuje wysoką dostępność i skalowalność magazynu, dzięki czemu aplikacja może automatycznie skalować się w celu spełnienia wymagań użytkownika. Magazyn tabel to Magazyn NoSQL Key/Attribute firmy Microsoft, którego można użyć dla danych ze strukturą z mniejszą liczbą ograniczeń w schemacie. Usługa Azure Table Storage może służyć do przechowywania danych w celu zapewnienia trwałości i wydajnego pobierania.

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia danych wyjściowych tabeli.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego magazynu tabel. |
| Konto magazynu |Nazwa konta magazynu, do którego wysyłane są dane wyjściowe. |
| Klucz konta magazynu |Klucz dostępu skojarzony z kontem magazynu. |
| Nazwa tabeli |Nazwa tabeli. Tabela zostanie utworzona, jeśli nie istnieje. |
| Klucz partycji |Nazwa kolumny wyjściowej zawierającej klucz partycji. Klucz partycji jest unikatowym identyfikatorem partycji w tabeli, która stanowi pierwszą część klucza podstawowego jednostki. Jest to wartość ciągu, która może mieć długość do 1 KB. |
| Klucz wiersza |Nazwa kolumny wyjściowej zawierającej klucz wiersza. Klucz wiersza jest unikatowym identyfikatorem dla jednostki w ramach partycji. Stanowi on drugą część klucza podstawowego jednostki. Klucz wiersza jest wartością ciągu, która może mieć długość do 1 KB. |
| Rozmiar partii |Liczba rekordów dla operacji wsadowych. Wartość domyślna (100) jest wystarczająca dla większości zadań. Aby uzyskać więcej informacji na temat modyfikowania tego ustawienia, zobacz [specyfikację operacji wsadowej tabeli](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) . |

## <a name="service-bus-queues"></a>Kolejki usługi Service Bus

[Kolejki Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) oferują dostarczanie komunikatów FIFO do co najmniej jednego konkurującego odbiorcy. Zazwyczaj komunikaty są odbierane i przetwarzane przez odbiorniki w kolejności czasowej, w której zostały dodane do kolejki. Każdy komunikat jest odbierany i przetwarzany przez tylko jednego odbiorcę wiadomości.

W przypadku [poziomu zgodności 1,2](stream-analytics-compatibility-level.md)Azure Stream Analytics używa protokołu obsługi komunikatów [protokołu Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) do zapisu w Service Bus kolejkach i tematach. AMQP umożliwia tworzenie aplikacji hybrydowych dla wielu platform przy użyciu protokołu Open Standard.

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia danych wyjściowych kolejki.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tej kolejki Service Bus. |
| Service Bus przestrzeń nazw |Kontener dla zestawu jednostek obsługi komunikatów. |
| Nazwa kolejki |Nazwa kolejki Service Bus. |
| Nazwa zasad kolejki |Podczas tworzenia kolejki można także utworzyć zasady dostępu współdzielonego na karcie **Konfiguracja** kolejki. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. |
| Klucz zasad kolejki |Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw Service Bus. |
| Format serializacji zdarzeń |Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV i Avro. |
| Encoding |W przypadku plików CSV i JSON jedynym obsługiwanym formatem kodowania jest UTF-8. |
| Ogranicznik |Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników do serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Format |Dotyczy tylko typu JSON. **Linia rozdzielona** określa, że dane wyjściowe są formatowane przy użyciu każdego obiektu JSON oddzielonego przez nowy wiersz. W przypadku wybrania opcji **rozdzielone linią**kod JSON jest odczytywany po jednym obiekcie naraz. Cała zawartość nie będzie prawidłowym kodem JSON. **Tablica** określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. |
| Kolumny właściwości | Opcjonalny. Kolumny oddzielone przecinkami, które muszą być dołączone jako właściwości użytkownika wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [właściwości metadanych niestandardowych dla danych wyjściowych](#custom-metadata-properties-for-output). |
| Kolumny właściwości systemu | Opcjonalny. Pary klucz-wartość właściwości systemu i odpowiednich nazw kolumn, które muszą być dołączone do wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [Właściwości systemu dla Service Bus kolejki i danych wyjściowych tematu](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Liczba partycji jest [określana na podstawie Service Bus jednostki SKU i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="service-bus-topics"></a>Tematy dotyczące usługi Service Bus
Kolejki Service Bus zapewniają metodę komunikacji jeden-do-jednego od nadawcy do odbiorcy. [Tematy Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) zapewniają formę komunikacji typu "jeden do wielu".

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia danych wyjściowych tematu Service Bus.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego tematu Service Bus. |
| Service Bus przestrzeń nazw |Kontener dla zestawu jednostek obsługi komunikatów. Po utworzeniu nowego centrum zdarzeń utworzono również przestrzeń nazw Service Bus. |
| Nazwa tematu |Tematy to jednostki obsługi komunikatów, podobne do centrów zdarzeń i kolejek. Są one przeznaczone do zbierania strumieni zdarzeń z urządzeń i usług. Po utworzeniu tematu ma również określoną nazwę. Komunikaty wysyłane do tematu nie są dostępne, jeśli subskrypcja nie zostanie utworzona, dlatego upewnij się, że w temacie istnieje co najmniej jedna subskrypcja. |
| Nazwa zasad tematu |Podczas tworzenia tematu Service Bus można także utworzyć zasady dostępu współdzielonego na karcie **Konfiguracja** tematu. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. |
| Klucz zasad tematu |Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw Service Bus. |
| Format serializacji zdarzeń |Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV i Avro. |
| Encoding |Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. W tym momencie jedynym obsługiwanym formatem kodowania jest UTF-8. |
| Ogranicznik |Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników do serializacji danych w formacie CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Kolumny właściwości | Opcjonalny. Kolumny oddzielone przecinkami, które muszą być dołączone jako właściwości użytkownika wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [właściwości metadanych niestandardowych dla danych wyjściowych](#custom-metadata-properties-for-output). |
| Kolumny właściwości systemu | Opcjonalny. Pary klucz-wartość właściwości systemu i odpowiednich nazw kolumn, które muszą być dołączone do wiadomości wychodzącej zamiast ładunku. Więcej informacji na temat tej funkcji znajduje się w sekcji [Właściwości systemu dla Service Bus kolejki i danych wyjściowych tematu](#system-properties-for-service-bus-queue-and-topic-outputs) |

Liczba partycji jest [określana na podstawie Service Bus jednostki SKU i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) to globalnie dystrybuowana usługa bazy danych, która oferuje nieograniczoną skalę elastyczną wokół świata, zaawansowanego zapytania i automatycznego indeksowania dla modeli danych niezależny od schematu. Aby dowiedzieć się więcej o Azure Cosmos DB opcjach kontenera Stream Analytics, zobacz artykuł [Stream Analytics with Azure Cosmos dB jako dane wyjściowe](stream-analytics-documentdb-output.md) .

Azure Cosmos DB dane wyjściowe z Stream Analytics nie są obecnie dostępne w regionach platformy Azure w Chinach (Chiny) i Azure (Niemcy).

> [!Note]
> W tej chwili Azure Stream Analytics obsługuje tylko połączenia z Azure Cosmos DB przy użyciu interfejsu API SQL.
> Inne interfejsy API Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli użytkownik wskaże Azure Stream Analytics kont Azure Cosmos DB utworzonych z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane.

W poniższej tabeli opisano właściwości tworzenia Azure Cosmos DB danych wyjściowych.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Alias do odwoływania danych wyjściowych w kwerendzie Stream Analytics. |
| Ujście | Azure Cosmos DB. |
| Opcja importu | Wybierz opcję **wybierz Cosmos DB z subskrypcji** lub **podaj ręcznie ustawienia Cosmos DB**.
| Identyfikator konta | Nazwa lub identyfikator URI punktu końcowego konta Azure Cosmos DB. |
| Klucz konta | Współużytkowany klucz dostępu dla konta Azure Cosmos DB. |
| baza danych | Nazwa bazy danych Azure Cosmos DB. |
| Nazwa kontenera | Nazwa kontenera do użycia, która musi istnieć w Cosmos DB. Przykład:  <br /><ul><li> Obiekt _Webcontainerer_: kontener o nazwie "The containerer" musi istnieć.</li>|
| Identyfikator dokumentu |Opcjonalny. Nazwa pola w zdarzeniach wyjściowych, które służy do określania klucza podstawowego, na którym bazują operacje wstawiania lub aktualizacji.

## <a name="azure-functions"></a>Azure Functions
Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie kodu na żądanie bez konieczności jawnego udostępniania infrastruktury ani zarządzania nią. Umożliwia zaimplementowanie kodu wyzwalanego przez zdarzenia występujące na platformie Azure lub w usługach partnerskich. Azure Functions odpowiedzi na wyzwalacze są naturalnymi danymi wyjściowymi Azure Stream Analytics. Ta karta wyjściowa umożliwia użytkownikom łączenie Stream Analytics z Azure Functions i uruchamianie skryptu lub fragmentu kodu w odpowiedzi na różne zdarzenia.

Azure Functions dane wyjściowe z Stream Analytics nie są obecnie dostępne w regionach platformy Azure w Chinach (Chiny) i Azure (Niemcy).

Azure Stream Analytics wywołuje Azure Functions za pośrednictwem wyzwalacza HTTP. Karta wyjściowa Azure Functions jest dostępna z następującymi konfigurowalnymi właściwościami:

| Nazwa właściwości | Opis |
| --- | --- |
| Aplikacja funkcji |Nazwa aplikacji Azure Functions. |
| Funkcja |Nazwa funkcji w aplikacji Azure Functions. |
| Klucz |Jeśli chcesz użyć funkcji platformy Azure z innej subskrypcji, możesz to zrobić, podając klucz, aby uzyskać dostęp do funkcji. |
| Maksymalny rozmiar wsadu |Właściwość, która umożliwia ustawienie maksymalnego rozmiaru dla każdej partii wyjściowej, która jest wysyłana do funkcji platformy Azure. Jednostka wejściowa jest w bajtach. Wartość domyślna to 262 144 bajtów (256 KB). |
| Maksymalna liczba partii  |Właściwość, która umożliwia określenie maksymalnej liczby zdarzeń w każdej partii, która jest wysyłana do Azure Functions. Wartość domyślna to 100. |

Azure Stream Analytics oczekuje stanu HTTP 200 z aplikacji Functions dla partii, które zostały przetworzone pomyślnie.

Gdy Azure Stream Analytics otrzymuje wyjątek 413 ("jednostka żądania HTTP zbyt duża") z funkcji platformy Azure, zmniejsza rozmiar partii wysyłanych do Azure Functions. W kodzie funkcji platformy Azure Użyj tego wyjątku, aby upewnić się, że Azure Stream Analytics nie wysyła zbyt dużych partii. Należy również upewnić się, że maksymalna liczba partii i wartości rozmiaru używane w funkcji są zgodne z wartościami wprowadzonymi w portalu Stream Analytics.

> [!NOTE]
> Podczas połączenia testowego Stream Analytics wysyła pustą partię do Azure Functions, aby przetestować, czy połączenie między nimi działa. Upewnij się, że aplikacja Functions obsługuje puste żądania usługi Batch, aby upewnić się, że połączenie testowe przebiega pomyślnie.

Ponadto, w sytuacji, gdy w przedziale czasowym nie ma wypełniania zdarzeń, nie jest generowane żadne wyjście. W związku z tym funkcja **computeResult** nie jest wywoływana. To zachowanie jest spójne z wbudowaną funkcją agregującą w oknie.

## <a name="custom-metadata-properties-for-output"></a>Niestandardowe właściwości metadanych dla danych wyjściowych 

Kolumny zapytań można dołączać jako właściwości użytkownika do wiadomości wychodzących. Te kolumny nie przechodzą do ładunku. Właściwości są obecne w formie słownika w wiadomości wyjściowej. *Klucz* to nazwa kolumny i *wartość* jest wartością kolumny w słowniku właściwości. Wszystkie typy danych Stream Analytics są obsługiwane z wyjątkiem rekordów i tablic.  

Obsługiwane dane wyjściowe: 
* Kolejka usługi Service Bus 
* Temat usługi Service Bus 
* Centrum zdarzeń 

W poniższym przykładzie dodamy dwa pola `DeviceId` i `DeviceStatus` do metadanych. 
* Dotyczących`select *, DeviceId, DeviceStatus from iotHubInput`
* Konfiguracja wyjściowa:`DeviceId,DeviceStatus`

![Kolumny właściwości](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Poniższy zrzut ekranu przedstawia właściwości komunikatu wyjściowego inspekcji w centrum EventHub za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

![Właściwości niestandardowe zdarzenia](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Właściwości systemu dla Service Bus kolejki i danych wyjściowych tematu 
Kolumny zapytania można dołączać jako [Właściwości systemu](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) do kolejki lub komunikatów tematu usługi wychodzącej magistrali usług. Te kolumny nie znajdują się w ładunku, natomiast odpowiednia [Właściwość systemu](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage jest wypełniana wartościami kolumn zapytania.
Te właściwości systemu są obsługiwane — `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .
Wartości ciągu tych kolumn są analizowane jako odpowiadające im typy wartości właściwości systemu, a wszystkie błędy analizy są traktowane jako błędy danych.
To pole jest dostępne jako format obiektu JSON. Szczegółowe informacje o tym formacie są następujące:
* Ujęte w nawiasy klamrowe {} .
* Zapisywane w parach klucz/wartość.
* Klucze i wartości muszą być ciągami.
* Klucz jest nazwą i wartością właściwości systemowej jest nazwa kolumny zapytania.
* Klucze i wartości są oddzielone dwukropkiem.
* Każda para klucz/wartość jest oddzielona przecinkiem.

Pokazuje, jak używać tej właściwości —

* Dotyczących`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolumny właściwości systemu:`{ "MessageId": "column1", "PartitionKey": "column2"}`

To ustawienie powoduje, że `MessageId` komunikaty w kolejce usługi Service Bus z `column1` wartościami i PartitionKey są ustawione na `column2` wartości.

## <a name="partitioning"></a>Partycjonowanie

Poniższa tabela zawiera podsumowanie obsługi partycji oraz liczbę modułów zapisywania danych wyjściowych dla każdego typu danych wyjściowych:

| Typ danych wyjściowych | Obsługa partycjonowania | Klucz partycji  | Liczba modułów zapisywania danych wyjściowych |
| --- | --- | --- | --- |
| Azure Data Lake Store | Yes | Użyj tokenów {date} i {Time} w wzorcu prefiksu ścieżki. Wybierz format daty, na przykład RRRR/MM/DD, DD/MM/RRRR lub MM-DD-RRRR. HH jest używany w formacie czasu. | Następuje Partycjonowanie danych wejściowych dla w [pełni działania równoległegoych zapytań](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Tak, musi być włączona. | Na podstawie klauzuli PARTITION BY w zapytaniu. | Gdy opcja dziedziczenia jest włączona, następuje Partycjonowanie danych wejściowych dla w [pełni działania równoległegoych zapytań](stream-analytics-scale-jobs.md). Aby dowiedzieć się więcej o osiągnięciu lepszej wydajności zapisu podczas ładowania danych do Azure SQL Database, zobacz [Azure Stream Analytics dane wyjściowe do Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Yes | Użyj tokenów {date} i {Time} z pól zdarzeń we wzorcu ścieżki. Wybierz format daty, na przykład RRRR/MM/DD, DD/MM/RRRR lub MM-DD-RRRR. HH jest używany w formacie czasu. Dane wyjściowe obiektu BLOB mogą być partycjonowane za pomocą pojedynczego niestandardowego atrybutu zdarzenia {FieldName} lub {DateTime: \< specyfikator>}. | Następuje Partycjonowanie danych wejściowych dla w [pełni działania równoległegoych zapytań](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Yes | Yes | Różni się w zależności od wyrównania partycji.<br /> Gdy klucz partycji dla danych wyjściowych centrum zdarzeń jest równomiernie wyrównany przy użyciu nadrzędnego (poprzedni) kroku zapytania, liczba modułów zapisujących jest taka sama jak liczba partycji w danych wyjściowych centrum zdarzeń. Każdy moduł zapisujący używa [klasy EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) do wysyłania zdarzeń do konkretnej partycji. <br /> Gdy klucz partycji dla danych wyjściowych centrum zdarzeń nie jest wyrównany przy użyciu nadrzędnego (poprzedni) kroku zapytania, liczba modułów zapisujących jest taka sama jak liczba partycji w tym poprzednim kroku. Każdy moduł zapisujący używa [klasy SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) w **EventHubClient** do wysyłania zdarzeń do wszystkich partycji wyjściowych. |
| Power BI | Nie | Brak | Nie dotyczy. |
| Azure Table Storage | Yes | Dowolna kolumna wyjściowa.  | Następuje Partycjonowanie danych wejściowych w przypadku w [pełni równoległych zapytań](stream-analytics-scale-jobs.md). |
| Temat usługi Azure Service Bus | Yes | Wybrana automatycznie. Liczba partycji jest określana na podstawie [Service Bus jednostki SKU i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.| Wartość taka sama jak liczba partycji w temacie danych wyjściowych.  |
| Kolejka usługi Azure Service Bus | Yes | Wybrana automatycznie. Liczba partycji jest określana na podstawie [Service Bus jednostki SKU i rozmiaru](../service-bus-messaging/service-bus-partitioning.md). Klucz partycji jest unikatową wartością całkowitą dla każdej partycji.| Wartość taka sama jak liczba partycji w kolejce wyjściowej. |
| Azure Cosmos DB | Yes | Na podstawie klauzuli PARTITION BY w zapytaniu. | Następuje Partycjonowanie danych wejściowych w przypadku w [pełni równoległych zapytań](stream-analytics-scale-jobs.md). |
| Azure Functions | Yes | Na podstawie klauzuli PARTITION BY w zapytaniu. | Następuje Partycjonowanie danych wejściowych w przypadku w [pełni równoległych zapytań](stream-analytics-scale-jobs.md). |

Liczba modułów zapisywania danych wyjściowych może być również kontrolowana za pomocą `INTO <partition count>` klauzuli (patrz [do](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) w zapytaniu, co może być przydatne w osiągnięciu odpowiedniej topologii zadań. Jeśli karta wyjściowa nie zostanie podzielona na partycje, Brak danych w jednej partycji wejściowej spowoduje opóźnienie do późnego przybycia. W takich przypadkach dane wyjściowe są scalane z pojedynczym składnikiem zapisywania, co może powodować wąskie gardła w potoku. Aby dowiedzieć się więcej na temat zasad późnego przybycia, zobacz temat [Azure Stream Analytics uwagi dotyczące kolejności zdarzeń](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej
Azure Stream Analytics używa partii o zmiennym rozmiarze do przetwarzania zdarzeń i zapisywania do danych wyjściowych. Zwykle aparat Stream Analytics nie zapisuje jednej wiadomości w danym momencie i używa ich do zwiększenia wydajności. Gdy częstotliwość dla zdarzeń przychodzących i wychodzących jest wysoka, Stream Analytics używa większych partii. Gdy szybkość ruchu wychodzącego jest niska, używa mniejszych partii, aby zachować niski czas oczekiwania.

W poniższej tabeli objaśniono niektóre zagadnienia dotyczące tworzenia wsadowych danych wyjściowych:

| Typ danych wyjściowych |    Maksymalny rozmiar komunikatu | Optymalizacja rozmiaru partii |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zobacz [limity Data Lake Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Użyj do 4 MB na operację zapisu. |
| Azure SQL Database | Konfigurowalne przy użyciu maksymalnej liczby partii. 10 000 maksymalna i 100 minimalna liczba wierszy na pojedynczy wkład zbiorczy.<br />Zobacz [limity usługi Azure SQL](../sql-database/sql-database-resource-limits.md). |  Każda partia jest początkowo wstawiana zbiorczo z maksymalną liczbą partii. Partia jest dzielona w połowie (do momentu minimalnej liczby partii) na podstawie błędów z możliwością powtarzania z bazy danych SQL. |
| Azure Blob Storage | Zobacz [limity usługi Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Maksymalny rozmiar bloku obiektu BLOB to 4 MB.<br />Maksymalna liczba Bock obiektów BLOB to 50 000. |
| Azure Event Hubs    | 256 KB lub 1 MB na wiadomość. <br />Zobacz [limity Event Hubs](../event-hubs/event-hubs-quotas.md). |    Gdy Partycjonowanie danych wejściowych/wyjściowych nie jest wyrównane, każde zdarzenie jest spakowane osobno w `EventData` i wysyłane w partii o maksymalnym rozmiarze komunikatu. Dzieje się tak, jeśli są używane [niestandardowe właściwości metadanych](#custom-metadata-properties-for-output) . <br /><br />  Gdy Partycjonowanie danych wejściowych/wyjściowych jest wyrównane, do jednego wystąpienia są pakowane wiele zdarzeń `EventData` , do którego zostanie wysłany maksymalny rozmiar komunikatu.    |
| Power BI | Zobacz [Power BI limity interfejsu API REST](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Zobacz [limity usługi Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Wartość domyślna to 100 jednostek na pojedynczą transakcję. W razie potrzeby można skonfigurować ją na mniejszą wartość. |
| Kolejka usługi Azure Service Bus    | 256 KB na komunikat dla warstwy Standard, 1 MB dla warstwy Premium.<br /> Zobacz [limity Service Bus](../service-bus-messaging/service-bus-quotas.md). | Użyj pojedynczego zdarzenia na komunikat. |
| Temat usługi Azure Service Bus | 256 KB na komunikat dla warstwy Standard, 1 MB dla warstwy Premium.<br /> Zobacz [limity Service Bus](../service-bus-messaging/service-bus-quotas.md). | Użyj pojedynczego zdarzenia na komunikat. |
| Azure Cosmos DB    | Zobacz [limity Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | Rozmiar partii i częstotliwość zapisu są dostosowywane dynamicznie na podstawie Azure Cosmos DB odpowiedzi. <br /> Nie ma żadnych wstępnie zdefiniowanych ograniczeń z Stream Analytics. |
| Azure Functions    | | Domyślny rozmiar wsadu to 262 144 bajtów (256 KB). <br /> Domyślna liczba zdarzeń na partię to 100. <br /> Rozmiar wsadu można skonfigurować i zwiększyć lub zmniejszyć w [opcjach danych wyjściowych](#azure-functions)Stream Analytics.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> 
> [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
