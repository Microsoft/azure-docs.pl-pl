---
title: Copy Activity performance and tuning guide (Przewodnik dotyczący wydajności i dostosowywania działania kopiowania)
description: Zapoznaj się z najważniejszymi czynnikami wpływającymi na wydajność przenoszenia danych w Azure Data Factory w przypadku korzystania z działania kopiowania.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9a890719de39a71d8336d39f9932e73f7baccf87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377214"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copy Activity performance and tuning guide (Przewodnik dotyczący wydajności i dostosowywania działania kopiowania)

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-copy-activity-performance.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-performance.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania dla Data Factory](../copy-activity-performance.md).

Azure Data Factory działanie kopiowania zapewnia pierwszą klasę rozwiązanie bezpiecznego i niezawodnego ładowania danych o wysokiej wydajności. Pozwala ona na kopiowanie dziesiątki danych dziennie w wielu różnych magazynach danych w chmurze i lokalnych. Niezwykle — szybka szybkość ładowania danych to klucz, aby zapewnić możliwość skoncentrowania się na podstawowym problemie dotyczącym danych Big Data: tworzenie zaawansowanych rozwiązań analitycznych i uzyskiwanie szczegółowych informacji ze wszystkich tych danych.

Platforma Azure udostępnia zestaw rozwiązań do magazynowania danych klasy korporacyjnej i magazynów danych, a działanie kopiowania oferuje wysoce zoptymalizowane środowisko ładowania danych, które jest łatwe do skonfigurowania i skonfigurowania. Za pomocą zaledwie jednego działania kopiowania można osiągnąć następujące czynności:

* Ładowanie danych do **usługi Azure Synapse Analytics** przy **1,2 GB/s**. Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Ładowanie danych do **usługi Azure Blob Storage** o **1,0 GB/s**
* Ładowanie danych do **Azure Data Lake Store** o **1,0 GB/s**

W tym artykule opisano:

* [Numery odwołań wydajności](#performance-reference) dla obsługiwanych magazynów danych źródłowych i ujścia, które ułatwiają planowanie projektu;
* Funkcje, które mogą zwiększyć przepływność kopiowania w różnych scenariuszach, w tym [jednostki przenoszenia danych w chmurze](#cloud-data-movement-units), [Kopiowanie równoległe](#parallel-copy)i [Kopiowanie etapowe](#staged-copy);
* [Wskazówki dotyczące dostrajania](#performance-tuning-steps) wydajności dotyczące dostrajania wydajności i kluczowych czynników, które mogą mieć wpływ na wydajność kopiowania.

> [!NOTE]
> Jeśli nie znasz ogólnie działania kopiowania, zobacz [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md) przed przeczytaniem tego artykułu.
>

## <a name="performance-reference"></a>Informacje o wydajności

W poniższej tabeli przedstawiono informacje o przepływności kopiowania w MB/s dla danej pary źródłowej i ujścia na podstawie testów wewnętrznych. W przypadku porównania pokazano również, w jaki sposób różne ustawienia [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units) lub [skalowalności bramy zarządzanie danymi](data-factory-data-management-gateway-high-availability-scalability.md) (wiele węzłów bramy) mogą pomóc w wydajności kopiowania.

![Macierz wydajności](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>W Azure Data Factory wersja 1 minimalna jednostka przenoszenia danych w chmurze dla kopii z chmury do chmury jest równa 2. Jeśli nie zostanie określony, zobacz domyślne jednostki przenoszenia danych używane w [jednostkach przenoszenia danych w chmurze](#cloud-data-movement-units).

**Punkty do uwagi:**
* Przepływność jest obliczana przy użyciu następującej formuły: [rozmiar danych odczytanych ze źródła]/[czas trwania uruchomienia działania kopiowania].
* Numery referencyjne wydajności w tabeli były mierzone przy użyciu zestawu danych [TPC-H](http://www.tpc.org/tpch/) w jednym przebiegu działania kopiowania.
* W magazynach danych platformy Azure źródło i ujścia są w tym samym regionie świadczenia usługi Azure.
* W przypadku kopiowania hybrydowego między magazynami lokalnymi i w chmurze każdy węzeł bramy był uruchomiony na komputerze, który był oddzielony od lokalnego magazynu danych, zgodnie z poniższą specyfikacją. Gdy jedno działanie zostało uruchomione na bramie, operacja kopiowania zużywał tylko niewielką część procesora CPU, pamięci lub przepustowości sieci. Dowiedz się więcej na temat [Zarządzanie danymi Gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>Procesor CPU</td>
        <td>32 rdzenie 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Pamięć</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Sieć</td>
        <td>Interfejs internetowy: 10 GB/s; Interfejs intranetowy: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Większą przepływność można osiągnąć, wykorzystując więcej jednostek przenoszenia danych (DMUs) niż domyślna maksymalna wartość DMUs, która jest 32 do uruchomienia działania kopiowania z chmury do chmury. Na przykład w przypadku 100 DMUs można osiągnąć kopiowanie danych z usługi Azure BLOB do Azure Data Lake Store przy użyciu **1.0 GB/s**. Aby uzyskać szczegółowe informacje na temat tej funkcji i obsługiwanego scenariusza, zobacz sekcję dotyczącą [jednostek przenoszenia danych w chmurze](#cloud-data-movement-units) . Skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/) , aby zażądać większej DMUs.

## <a name="parallel-copy"></a>Kopiowanie równoległe
Można odczytywać dane ze źródła lub zapisywać dane w miejscu docelowym **równolegle w ramach uruchomienia działania kopiowania**. Ta funkcja zwiększa przepływność operacji kopiowania i skraca czas przenoszenia danych.

To ustawienie różni się od właściwości **współbieżności** w definicji działania. Właściwość **współbieżność** określa liczbę **współbieżnych uruchomień działania kopiowania** do przetwarzania danych z różnych okien działania (1 am do 2 am, 2 am do 3 am, 3 am i tak dalej). Ta funkcja jest przydatna, gdy wykonujesz obciążenie historyczne. Możliwość kopiowania równoległego ma zastosowanie do **pojedynczego uruchomienia działania**.

Przyjrzyjmy się przykładowym scenariuszom. W poniższym przykładzie należy przetworzyć wiele wycinków z przeszłości. Data Factory uruchamia wystąpienie działania kopiowania (uruchomienie działania) dla każdego wycinka:

* Wycinek danych z pierwszego okna działania (od 1 do 2 AM) = => działania uruchomienia 1
* Wycinek danych z drugiego okna działania (od 2 do 3 AM) = => działania uruchomienia 2
* Wycinek danych z drugiego okna działania (3 AM do 4 AM) = => działania uruchomienia 3

I tak dalej.

W tym przykładzie, gdy wartość **współbieżności** jest ustawiona na 2, **uruchomienie działania 1** i **uruchomienie działania 2** kopiuje dane z dwóch okien działania **jednocześnie** w celu zwiększenia wydajności przenoszenia danych. Jeśli jednak wiele plików jest skojarzonych z uruchomieniem działania 1, Usługa przenoszenia danych kopiuje pliki z lokalizacji źródłowej do jednego pliku docelowego w danym momencie.

### <a name="cloud-data-movement-units"></a>Jednostki przenoszenia danych w chmurze
**Jednostka przenoszenia danych w chmurze (DMU)** to miara, która reprezentuje moc (KOMBINACJĘ procesora CPU, pamięci i alokacji zasobów sieciowych) pojedynczej jednostki w Data Factory. DMU ma zastosowanie do operacji kopiowania z chmury do chmury, ale nie w kopii hybrydowej.

**Minimalną liczbę jednostek przenoszenia danych w chmurze na potrzeby uruchomienia działania kopiowania są dwa.** Jeśli nie zostanie określona, Poniższa tabela zawiera listę domyślnych DMUs używanych w różnych scenariuszach kopiowania:

| Kopiuj scenariusz | Domyślna DMUs określona przez usługę |
|:--- |:--- |
| Kopiowanie danych między magazynami opartymi na plikach | Od 4 do 16 w zależności od liczby i rozmiaru plików. |
| Wszystkie inne scenariusze kopiowania | 4 |

Aby zastąpić to ustawienie domyślne, określ wartość właściwości **cloudDataMovementUnits** w następujący sposób. **Dozwolone wartości** właściwości **cloudDataMovementUnits** to 2, 4, 8, 16, 32. **Rzeczywista liczba DMUs w chmurze** , której operacja kopiowania używa w czasie wykonywania, jest równa lub mniejsza niż skonfigurowana wartość, w zależności od wzorca danych. Aby uzyskać informacje o poziomie wzmocnienia wydajności, które można uzyskać podczas konfigurowania większej liczby jednostek dla określonego źródła i ujścia kopii, zobacz [informacje dotyczące wydajności](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Jeśli potrzebujesz więcej DMUs w chmurze o wyższej przepływności, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/). Ustawienie 8 i nowsze działa obecnie tylko **w przypadku kopiowania wielu plików z usługi BLOB Storage/Data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP do usługi BLOB Storage/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Możesz użyć właściwości **parallelCopies** , aby wskazać równoległość, która ma być używana przez działanie kopiowania. Tę właściwość można traktować jako maksymalną liczbę wątków w ramach działania kopiowania, które mogą być jednocześnie odczytywane ze źródła lub zapisywać do magazynów danych ujścia.

Dla każdego przebiegu działania kopiowania Data Factory określa liczbę kopii równoległych, które mają być używane do kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych. Domyślna liczba kopii równoległych, których używa, zależy od typu używanego źródła i ujścia.

| Źródło i ujścia | Domyślna liczba kopii równoległych określona przez usługę |
| --- | --- |
| Kopiowanie danych między magazynami opartymi na plikach (BLOB Storage; Data Lake Store; Amazon S3; lokalny system plików; lokalny system plików HDFS) |Od 1 do 32. Zależy od rozmiaru plików i liczby jednostek przenoszenia danych w chmurze (DMUs) służących do kopiowania danych między dwoma magazynami danych w chmurze lub fizycznej konfiguracji maszyny bramy używanej do kopiowania hybrydowego (do kopiowania danych do lub z lokalnego magazynu danych). |
| Kopiowanie danych z **dowolnego źródłowego magazynu danych do usługi Azure Table Storage** |4 |
| Wszystkie inne pary źródłowe i ujścia |1 |

Zwykle zachowanie domyślne powinno zapewnić najlepszą przepływność. Aby jednak kontrolować obciążenie maszyn, które obsługują magazyny danych, lub dostosować wydajność kopiowania, można zastąpić wartość domyślną i określić wartość właściwości **parallelCopies** . Wartość musi zawierać się w przedziale od 1 do 32 (włącznie). W czasie wykonywania w celu uzyskania najlepszej wydajności działanie kopiowania używa wartości, która jest mniejsza lub równa ustawionej wartości.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Punkty do uwagi:

* Podczas kopiowania danych między magazynami opartymi na plikach **parallelCopies** określa równoległość na poziomie pliku. Fragmentowanie w pojedynczym pliku zostanie wykonane automatycznie i w sposób przezroczysty i zaprojektowane do użycia najlepszego odpowiedniego rozmiaru fragmentu dla danego typu magazynu danych źródłowych w celu załadowania danych równolegle i ortogonalnych do parallelCopies. Rzeczywista liczba kopii równoległych używanych przez usługę przenoszenia danych dla operacji kopiowania w czasie wykonywania nie jest większa niż liczba plików, które posiadasz. Jeśli zachowanie kopiowania ma wartość **mergeFile**, działanie Copy nie może korzystać z równoległości na poziomie plików.
* Po określeniu wartości właściwości **parallelCopies** należy wziąć pod uwagę wzrost obciążenia dla magazynów danych źródłowych i ujścia oraz bramę, jeśli jest to kopia hybrydowa. Zdarza się to szczególnie wtedy, gdy masz wiele działań lub współbieżne uruchomienia tych samych działań, które działają w tym samym magazynie danych. Jeśli zauważysz, że magazyn danych lub Brama są przeciążone, zmniejsz wartość **parallelCopies** , aby zwolnić obciążenie.
* Podczas kopiowania danych z magazynów, które nie są oparte na plikach, do magazynów, które są oparte na plikach, Usługa przenoszenia danych ignoruje Właściwość **parallelCopies** . Nawet w przypadku określenia równoległości nie jest on stosowany w tym przypadku.

> [!NOTE]
> Aby użyć funkcji **parallelCopies** w przypadku wykonywania kopii hybrydowej, należy użyć bramy zarządzanie danymi w wersji 1,11 lub nowszej.
>
>

Aby lepiej wykorzystać te dwie właściwości i zwiększyć przepływność przenoszenia danych, zobacz przykładowe przypadki użycia. Nie musisz konfigurować **parallelCopies** , aby korzystać z zachowania domyślnego. Jeśli skonfigurujesz i **parallelCopies** jest za mały, wiele DMUs w chmurze może nie być w pełni wykorzystane.

### <a name="billing-impact"></a>Wpływ rozliczeń
Należy pamiętać, że **opłata jest** naliczana na podstawie łącznego czasu operacji kopiowania. Jeśli zadanie kopiowania używane do podzielenia jednej godziny z jedną jednostką w chmurze, a teraz trwa 15 minut z czterema jednostkami w chmurze, cały rachunek pozostaje niemal taki sam. Na przykład należy użyć czterech jednostek chmur. Pierwsza jednostka chmury poświęca 10 minut, drugi, 10 minut, trzeci, 5 minut i czwarty, 5 minut, wszystkie w jednym uruchomieniu działania kopiowania. Opłata jest naliczana za całkowity czas kopiowania (przenoszenia danych), czyli 10 + 10 + 5 + 5 = 30 minut. Korzystanie z **parallelCopies** nie ma wpływu na rozliczenia.

## <a name="staged-copy"></a>Kopia przygotowana
W przypadku kopiowania danych ze źródłowego magazynu danych do magazynu danych ujścia można użyć usługi BLOB Storage jako tymczasowego magazynu przemieszczania. Przygotowanie jest szczególnie przydatne w następujących przypadkach:

1. Chcesz pozyskać **dane z różnych magazynów danych w usłudze Azure Synapse Analytics za pośrednictwem bazy**. Usługa Azure Synapse Analytics korzysta z bazy jako mechanizmu wysokiej przepływności w celu załadowania dużej ilości danych do usługi Azure Synapse Analytics. Dane źródłowe muszą jednak znajdować się w usłudze BLOB Storage i muszą spełniać dodatkowe kryteria. Podczas ładowania danych z magazynu danych innego niż magazyn obiektów blob, można aktywować kopiowanie danych za pośrednictwem tymczasowego tymczasowego magazynu obiektów BLOB. W takim przypadku Data Factory wykonuje wymagane przekształcenia danych, aby upewnić się, że spełnia on wymagania bazy. Następnie używa podstawy do ładowania danych do usługi Azure Synapse Analytics. Aby uzyskać więcej informacji, zobacz artykuł Tworzenie [bazy danych w usłudze Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics). Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Czasami trwa przeprowadzenie hybrydowego przenoszenia danych (czyli kopiowania między lokalnym magazynem danych i magazynem danych w chmurze) przez wolne połączenie sieciowe**. Aby zwiększyć wydajność, można skompresować dane lokalnie, aby przełączać dane do tymczasowego magazynu danych w chmurze. Następnie można zdekompresować dane w magazynie przemieszczania przed załadowaniem ich do docelowego magazynu danych.
3. **Nie chcesz otwierać portów innych niż port 80 i port 443 w zaporze ze względu na firmowe zasady IT**. Na przykład podczas kopiowania danych z lokalnego magazynu danych do ujścia Azure SQL Database lub ujścia usługi Azure Synapse Analytics należy aktywować wychodzącą komunikację TCP na porcie 1433 zarówno dla zapory systemu Windows, jak i zapory firmowej. W tym scenariuszu należy skorzystać z bramy, aby najpierw skopiować dane do wystąpienia tymczasowego magazynu obiektów BLOB za pośrednictwem protokołu HTTP lub HTTPS na porcie 443. Następnie Załaduj dane do SQL Database lub Azure Synapse Analytics z magazynu obiektów BLOB Storage. W tym przepływie nie trzeba włączać portu 1433.

### <a name="how-staged-copy-works"></a>Jak działa kopia przygotowana
Gdy uaktywniasz funkcję przemieszczania, najpierw dane są kopiowane z magazynu danych źródłowych do przemieszczania magazynu danych (własne). Następnie dane zostaną skopiowane z tymczasowego magazynu danych do magazynu danych ujścia. Data Factory automatycznie zarządza przepływem dwuetapowym. Data Factory również czyści dane tymczasowe z magazynu tymczasowego po zakończeniu przenoszenia danych.

W scenariuszu kopiowania w chmurze (magazyny danych źródłowych i ujścia znajdują się w chmurze) Brama nie jest używana. Usługa Data Factory wykonuje operacje kopiowania.

![Przygotowana kopia: scenariusz chmury](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

W scenariuszu kopiowania hybrydowego (Źródło jest lokalne i ujścia jest w chmurze), Brama przenosi dane z magazynu danych źródłowych do magazynu danych przemieszczania. Usługa Data Factory przenosi dane z tymczasowego magazynu danych do magazynu danych ujścia. Kopiowanie danych z magazynu danych w chmurze do lokalnego magazynu danych za pośrednictwem przemieszczania jest również obsługiwane przez odwrócony przepływ.

![Przygotowana kopia: scenariusz hybrydowy](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

W przypadku aktywowania przenoszenia danych przy użyciu magazynu przemieszczania można określić, czy dane mają być kompresowane przed przeniesieniem danych ze źródłowego magazynu danych do tymczasowego lub przejściowego magazynu danych, a następnie zdekompresować przed przeniesieniem danych z tymczasowego lub przejściowego magazynu danych do magazynu danych ujścia.

Obecnie nie można kopiować danych między dwoma lokalnymi magazynami danych przy użyciu magazynu przemieszczania. Oczekujemy, że ta opcja będzie dostępna wkrótce.

### <a name="configuration"></a>Konfigurowanie
Skonfiguruj ustawienie **enableStaging** w działaniu Copy, aby określić, czy dane mają zostać przygotowane w magazynie obiektów BLOB przed załadowaniem ich do docelowego magazynu danych. Po ustawieniu **enableStaging** na true, określ dodatkowe właściwości wymienione w następnej tabeli. Jeśli go nie masz, musisz również utworzyć usługę Azure Storage lub link dostępu współdzielonego do magazynu na potrzeby przemieszczania.

| Właściwość | Opis | Wartość domyślna | Wymagane |
| --- | --- | --- | --- |
| **enableStaging** |Określ, czy chcesz kopiować dane za pośrednictwem tymczasowego magazynu przemieszczania. |Fałsz |Nie |
| **linkedServiceName** |Określ nazwę połączonej usługi [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) , która odwołuje się do wystąpienia magazynu, którego używasz jako tymczasowego magazynu przemieszczania. <br/><br/> Nie można użyć magazynu z sygnaturą dostępu współdzielonego w celu załadowania danych do usługi Azure Synapse Analytics za pośrednictwem bazy. Można go używać we wszystkich innych scenariuszach. |Nie dotyczy |Tak, gdy **enableStaging** jest ustawiona na wartość true |
| **ścieżka** |Określ ścieżkę magazynu obiektów blob, która ma zawierać dane przemieszczane. Jeśli nie podasz ścieżki, usługa tworzy kontener do przechowywania danych tymczasowych. <br/><br/> Określ ścieżkę tylko wtedy, gdy używasz magazynu z sygnaturą dostępu współdzielonego lub potrzebujesz danych tymczasowych, aby znajdować się w określonej lokalizacji. |Nie dotyczy |Nie |
| **Ustawieniem EnableCompression** |Określa, czy dane mają być kompresowane przed skopiowaniem do lokalizacji docelowej. To ustawienie zmniejsza ilość przesyłanych danych. |Fałsz |Nie |

Oto przykładowa definicja działania kopiowania z właściwościami opisanymi w powyższej tabeli:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Wpływ rozliczeń
Opłata jest naliczana na podstawie dwóch kroków: Kopiuj czas trwania i typ kopiowania.

* W przypadku korzystania z przemieszczania w trakcie kopiowania w chmurze (kopiowanie danych z magazynu danych w chmurze do innego magazynu danych w chmurze) naliczane są opłaty [łączny czas trwania kopiowania dla krok 1 i krok 2] x [cena jednostkowa kopiowania w chmurze].
* W przypadku korzystania z przemieszczania w trakcie kopiowania hybrydowego (kopiowanie danych z lokalnego magazynu danych do magazynu danych w chmurze) opłata jest naliczana za [czas trwania kopiowania hybrydowego] x [cena jednostkowa kopiowania hybrydowego] + [czas trwania kopiowania w chmurze] x [cena jednostkowa kopiowania w chmurze].

## <a name="performance-tuning-steps"></a>Kroki dostrajania wydajności
Zalecamy wykonanie następujących kroków, aby dostroić wydajność usługi Data Factory za pomocą działania kopiowania:

1. **Ustanów linię bazową**. Podczas fazy tworzenia Przetestuj potok za pomocą działania kopiowania względem reprezentatywnej próbki danych. Możesz użyć [modelu](data-factory-scheduling-and-execution.md) rozcinania Data Factory, aby ograniczyć ilość danych, z którymi pracujesz.

   Zbierz charakterystyki czasu wykonywania i wydajności za pomocą **aplikacji do monitorowania i zarządzania**. Wybierz pozycję **monitoruj & Zarządzaj** na stronie głównej Data Factory. W widoku drzewa wybierz **wyjściowy zestaw danych**. Na liście **okna działania** wybierz przebieg działania kopiowania. **Okna działania** wyświetlają czas trwania działania kopiowania i rozmiar kopiowanych danych. Przepływność jest wymieniona w **Eksploratorze okien działania**. Aby dowiedzieć się więcej na temat aplikacji, zobacz [monitorowanie potoków Azure Data Factory i zarządzanie nimi za pomocą aplikacji do monitorowania i zarządzania](data-factory-monitor-manage-app.md).

   ![Szczegóły uruchamiania działania](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   W dalszej części artykułu można porównać wydajność i konfigurację Twojego scenariusza, aby skopiować [Informacje o wydajności](#performance-reference) działania z naszych testów.
2. **Diagnozuj i Optymalizuj wydajność**. Jeśli obserwowanie wydajności nie spełnia oczekiwań, należy zidentyfikować wąskie gardła wydajności. Następnie zoptymalizuj wydajność, aby usunąć lub zmniejszyć efekt wąskich gardeł. Pełny opis diagnostyki wydajności wykracza poza zakres tego artykułu, ale poniżej przedstawiono kilka typowych zagadnień:

   * Funkcje wydajności:
     * [Kopiowanie równoległe](#parallel-copy)
     * [Jednostki przenoszenia danych w chmurze](#cloud-data-movement-units)
     * [Kopia przygotowana](#staged-copy)
     * [Skalowalność bramy Zarządzanie danymi](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brama zarządzania danymi](#considerations-for-data-management-gateway)
   * [Element źródłowy](#considerations-for-the-source)
   * [Ujście](#considerations-for-the-sink)
   * [Serializacja i deserializacja](#considerations-for-serialization-and-deserialization)
   * [Kompresja](#considerations-for-compression)
   * [Mapowanie kolumn](#considerations-for-column-mapping)
   * [Inne zagadnienia](#other-considerations)
3. **Rozwiń konfigurację do całego zestawu danych**. Gdy wyniki wykonywania i wydajność są zadowalające, można rozwinąć definicję i aktywny okres potoku w celu pokrycia całego zestawu danych.

## <a name="considerations-for-data-management-gateway"></a>Zagadnienia dotyczące bramy Zarządzanie danymi
**Konfiguracja bramy**: zalecamy używanie dedykowanego komputera do hostowania bramy zarządzanie danymi. Zapoznaj się [z zagadnieniami dotyczącymi używania bramy zarządzanie danymi](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Monitorowanie i skalowanie bramy**: pojedyncza brama logiczna z co najmniej jednym węzłem bramy może jednocześnie obsłużyć wiele przebiegów kopiowania jednocześnie. Można wyświetlić migawkę wykorzystania zasobów (procesor CPU, pamięć, Sieć (WE/wychodzącą) itp.) na maszynie bramy oraz liczbę współbieżnych zadań uruchomionych w ramach Azure Portal, zobacz [monitorowanie bramy w portalu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Jeśli istnieje duże zapotrzebowanie na hybrydowe przenoszenie danych z dużą liczbą współbieżnych przebiegów kopiowania lub z dużą ilością danych do skopiowania, należy rozważyć [skalowanie bramy w górę lub w poziomie](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) w celu lepszego wykorzystania zasobów lub zapewnienia większej ilości zasobów w celu uzyskania uprawnień do kopiowania.

## <a name="considerations-for-the-source"></a>Zagadnienia dotyczące źródła
### <a name="general"></a>Ogólne
Upewnij się, że źródłowy magazyn danych nie jest przeciążony przez inne obciążenia, które są uruchomione w systemie lub.

W przypadku magazynów danych firmy Microsoft zapoznaj się z tematami dotyczącymi [monitorowania i dostrajania](#performance-reference) , które są specyficzne dla magazynów danych, i poznanie charakterystyki wydajności magazynu danych, minimalizacji czasów odpowiedzi i maksymalnej przepływności.

W przypadku kopiowania danych z magazynu obiektów BLOB do usługi Azure Synapse Analytics należy rozważyć użycie systemu **Base** , aby zwiększyć wydajność. Aby uzyskać szczegółowe informacje, zobacz temat Tworzenie [bazy danych w usłudze Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) . Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych oparte na plikach
*(W tym magazyn obiektów blob, Data Lake Store, Amazon S3, lokalne systemy plików i lokalne HDFS)*

* **Średni rozmiar pliku i liczba plików**: działanie Copy transferuje dane w jednym pliku naraz. Z tą samą ilością danych, która ma zostać przeniesiona, ogólna przepływność jest niższa, jeśli dane składają się z wielu małych plików, a nie z kilku dużych plików ze względu na fazę ładowania każdego pliku. W związku z tym, jeśli to możliwe, Połącz małe pliki w większe pliki, aby uzyskać większą przepływność.
* **Format pliku i kompresja**: Aby uzyskać więcej informacji na temat poprawy wydajności, zobacz [zagadnienia dotyczące serializacji i deserializacji](#considerations-for-serialization-and-deserialization) oraz [zagadnienia](#considerations-for-compression) dotyczące sekcji kompresji.
* W przypadku scenariusza **lokalnego systemu plików** , w którym **brama zarządzanie danymi** jest wymagana, zapoznaj się z sekcją [uwagi dotyczące zarządzanie danymi Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relacyjne magazyny danych
*(Zawiera SQL Database; Azure Synapse Analytics; Amazon RedShift; Bazy danych SQL Server; i bazy danych Oracle, MySQL, DB2, Teradata, Sybase i PostgreSQL itp.)*

* **Wzorzec danych**: schemat tabeli wpływa na przepływność kopiowania. Duży rozmiar wiersza zapewnia lepszą wydajność niż mały rozmiar wiersza w celu skopiowania tej samej ilości danych. Przyczyną jest to, że baza danych może efektywnie pobierać mniejszą liczbę partii danych zawierających mniej wierszy.
* **Zapytanie lub procedura składowana**: Optymalizacja logiki zapytania lub procedury składowanej określonej w źródle działania kopiowania w celu bardziej wydajnego pobierania danych.
* **W przypadku lokalnych relacyjnych baz danych**, takich jak SQL Server i Oracle, które wymagają korzystania z **bramy zarządzanie danymi**, zobacz sekcję Uwagi dotyczące bramy zarządzanie danymi.

## <a name="considerations-for-the-sink"></a>Zagadnienia dotyczące ujścia
### <a name="general"></a>Ogólne
Upewnij się, że źródłowy magazyn danych nie jest przeciążony przez inne obciążenia, które są uruchomione w systemie lub.

W przypadku magazynów danych firmy Microsoft należy zapoznać się z [tematami dotyczącymi monitorowania i dostrajania](#performance-reference) , które są specyficzne dla magazynów danych. Te tematy mogą pomóc w zrozumieniu charakterystyki wydajności magazynu danych i sposobach minimalizowania czasów odpowiedzi i maksymalizacji przepływności.

Jeśli kopiujesz dane z **magazynu obiektów BLOB** do **usługi Azure Synapse Analytics**, rozważ użycie **bazy danych Base** , aby zwiększyć wydajność. Aby uzyskać szczegółowe informacje, zobacz temat Tworzenie [bazy danych w usłudze Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) . Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Magazyny danych oparte na plikach
*(W tym magazyn obiektów blob, Data Lake Store, Amazon S3, lokalne systemy plików i lokalne HDFS)*

* **Zachowanie kopiowania**: w przypadku kopiowania danych z innego magazynu danych opartego na plikach, działanie kopiowania ma trzy opcje za pośrednictwem właściwości **copyBehavior** . Zachowuje hierarchie, spłaszcza hierarchie lub scala pliki. Zachowanie lub spłaszczenie hierarchii ma niewielkie obciążenie, ale scalanie plików powoduje zwiększenie wydajności.
* **Format pliku i kompresowanie**: Zapoznaj się z [zagadnieniami dotyczącymi serializacji i deserializacji](#considerations-for-serialization-and-deserialization) oraz [zagadnień dotyczących kompresji](#considerations-for-compression) , aby uzyskać więcej sposobów na zwiększenie wydajności.
* **BLOB Storage**: obecnie magazyn obiektów BLOB obsługuje tylko blokowe obiekty blob w celu zoptymalizowania transferu danych i przepływności.
* W przypadku scenariuszy **z lokalnymi systemami plików** , które wymagają korzystania z **bramy zarządzanie danymi**, zobacz sekcję [zagadnienia dotyczące zarządzanie danymi bramy](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relacyjne magazyny danych
*(Obejmuje SQL Database, Azure Synapse Analytics, bazy danych SQL Server i Oracle Database)*

* **Zachowanie kopiowania**: w zależności od właściwości ustawionych dla elementu **sqlsink**, działanie Copy zapisuje dane w docelowej bazie danych na różne sposoby.
  * Domyślnie usługa przenoszenia danych używa interfejsu API kopiowania zbiorczego do wstawiania danych w trybie dołączania, który zapewnia najlepszą wydajność.
  * W przypadku skonfigurowania procedury składowanej w ujściach baza danych będzie stosować jeden wiersz w czasie zamiast jako obciążenie zbiorcze. Wydajność spadnie znacznie. Jeśli zestaw danych jest duży, w razie potrzeby Rozważ przełączenie do użycia właściwości **sqlWriterCleanupScript** .
  * W przypadku skonfigurowania właściwości **sqlWriterCleanupScript** dla każdego przebiegu działania kopiowania usługa wyzwala skrypt, a następnie korzysta z interfejsu API kopiowania zbiorczego do wstawiania danych. Na przykład aby zastąpić całą tabelę najnowszymi danymi, można określić skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym załadowaniem nowych danych ze źródła.
* **Wzorzec danych i rozmiar wsadu**:
  * Schemat tabeli ma wpływ na przepływność kopiowania. Aby skopiować tę samą ilość danych, duży rozmiar wiersza zapewnia lepszą wydajność niż mały rozmiar wiersza, ponieważ baza danych może wydajnie zatwierdzić mniejszą liczbę partii danych.
  * Działanie kopiowania wstawia dane w serii partii. Można ustawić liczbę wierszy w partii przy użyciu właściwości **writeBatchSize** . Jeśli dane zawierają małe wiersze, można ustawić właściwość **writeBatchSize** o wyższej wartości, aby korzystać z niższych nakładów wsadowych i większej przepływności. Jeśli rozmiar wiersza danych jest duży, należy zachować ostrożność w przypadku zwiększenia **writeBatchSize**. Wysoka wartość może prowadzić do błędu kopiowania spowodowanego przeładowaniem bazy danych.
* **W przypadku lokalnych relacyjnych baz danych** , takich jak SQL Server i Oracle, które wymagają korzystania z **bramy zarządzanie danymi**, zobacz sekcję [uwagi dotyczące bramy zarządzanie danymi](#considerations-for-data-management-gateway) .

### <a name="nosql-stores"></a>Sklepy NoSQL
*(Obejmuje magazyn tabel i Azure Cosmos DB)*

* W przypadku usługi **Table Storage**:
  * **Partycja**: zapisywanie danych na partycjach z przeplotem znacznie zmniejsza wydajność. Posortuj dane źródłowe według klucza partycji, aby dane były wstawiane efektywnie do jednej partycji po drugiej, lub Dostosuj logikę, aby zapisać dane w jednej partycji.
* Dla **Azure Cosmos DB**:
  * **Rozmiar wsadu**: Właściwość **writeBatchSize** ustawia liczbę żądań równoległych do usługi Azure Cosmos DB, aby można było tworzyć dokumenty. Po zwiększeniu **writeBatchSize** można oczekiwać większej wydajności, ponieważ do Azure Cosmos DB są wysyłane więcej żądań równoległych. Należy jednak zapoznać się z ograniczeniami podczas zapisywania do Azure Cosmos DB (komunikat o błędzie: "częstotliwość żądań jest duża"). Różne czynniki mogą prowadzić do ograniczania przepustowości, w tym rozmiaru dokumentu, liczby warunków w dokumentach oraz zasad indeksowania kolekcji docelowej. Aby osiągnąć wyższą przepływność kopiowania, rozważ użycie większej kolekcji, na przykład S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Zagadnienia dotyczące serializacji i deserializacji
Serializacja i deserializacja może wystąpić, gdy zestaw danych wejściowych lub wyjściowy zestaw danych jest plikiem. Zobacz [obsługiwane formaty plików i kompresji](data-factory-supported-file-and-compression-formats.md) ze szczegółowymi informacjami na temat obsługiwanych formatów plików przez działanie kopiowania.

**Zachowanie kopiowania**:

* Kopiowanie plików między magazynami danych opartymi na plikach:
  * Gdy zestawy danych wejściowych i wyjściowych mają takie same ustawienia formatu plików, Usługa przenoszenia danych wykonuje kopię binarną bez serializacji lub deserializacji. Zostanie wyświetlona wyższa przepływność w porównaniu do scenariusza, w którym ustawienia pliku źródłowego i ujścia różnią się od siebie.
  * Gdy zestawy danych wejściowych i wyjściowych są w formacie tekstowym i tylko typ kodowania jest inny, Usługa przenoszenia danych wykonuje tylko konwersję kodowania. Nie wykonuje serializacji i deserializacji, co powoduje pewne obciążenie związane z wydajnością w porównaniu z kopią binarną.
  * Gdy zestawy danych wejściowych i wyjściowych mają różne formaty plików lub różne konfiguracje, takie jak ograniczniki, Usługa przenoszenia danych deserializacji dane źródłowe w celu przesyłania strumieniowego, przekształcania i serializacji go do wskazanego formatu danych wyjściowych. Ta operacja skutkuje znacznie bardziej znaczącym obciążeniem wydajności w porównaniu z innymi scenariuszami.
* Podczas kopiowania plików do/z magazynu danych, który nie jest oparty na plikach (na przykład z magazynu opartego na plikach do magazynu relacyjnego), wymagany jest krok serializacji lub deserializacji. Ten krok skutkuje znaczącym obciążeniem wydajności.

**Format pliku**: wybrany format pliku może mieć wpływ na wydajność kopiowania. Na przykład Avro jest kompaktowym formatem binarnym, który przechowuje metadane zawierające dane. Ma szeroką pomoc techniczną w ekosystemie usługi Hadoop na potrzeby przetwarzania i wykonywania zapytań. Jednak Avro jest droższy do serializacji i deserializacji, co skutkuje mniejszą przepływność kopiowania w porównaniu z formatem tekstowym. Należy całościowo wybierać format plików w całym przepływie przetwarzania. Zacznij od tego, w jaki sposób są przechowywane dane, czy magazyny danych źródłowych lub które mają zostać wyodrębnione z systemów zewnętrznych; najlepszy format magazynu, przetwarzania analitycznego i wykonywania zapytań; i w jakim formacie dane mają zostać wyeksportowane do składnic danych na potrzeby raportowania i narzędzi do wizualizacji. Czasami format pliku, który jest nieoptymalny dla wydajności odczytu i zapisu, może być dobrym wyborem podczas rozważania ogólnego procesu analitycznego.

## <a name="considerations-for-compression"></a>Zagadnienia dotyczące kompresji
Gdy zestaw danych wejściowych lub wyjściowych jest plikiem, można ustawić działanie kopiowania w celu przeprowadzenia kompresji lub dekompresji, ponieważ zapisuje dane do miejsca docelowego. Po wybraniu kompresji należy wykonać kompromis między wejściem/wyjściem (we/wy) i procesorem CPU. Kompresowanie kosztów danych dodatkowych w zasobach obliczeniowych. Ale w przypadku powrotu zmniejsza to szybkość sieci i/O i magazyn. W zależności od danych można zobaczyć zwiększenie ogólnej przepływności kopiowania.

**Koder-dekoder**: działanie Copy obsługuje typy kompresji gzip, BZIP2 i Wklęśnięcie. Usługa Azure HDInsight może zużywać wszystkie trzy typy do przetwarzania. Każdy koder-dekoder kompresji ma zalety. Na przykład bzip2 ma najniższą przepływność kopiowania, ale uzyskuje się najlepszą wydajność zapytań Hive z bzip2, ponieważ można ją podzielić na potrzeby przetwarzania. Gzip to najbardziej zrównoważona opcja i jest używana najczęściej. Wybierz koder-dekoder, który najlepiej odpowiada Twojemu kompleksowemu scenariuszowi.

**Poziom**: można wybrać jedną z dwóch opcji dla każdego kodera kodek kompresji: najszybszy skompresowany i optymalnie skompresowany. Opcja najszybsza skompresowana kompresuje dane tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany. Optymalnie skompresowana opcja poświęca więcej czasu na kompresję i zapewnia minimalną ilość danych. Możesz przetestować obie opcje, aby zobaczyć, co zapewnia lepszą ogólną wydajność w przypadku.

**Uwaga**: Aby skopiować dużą ilość danych między magazynem lokalnym i chmurą, należy rozważyć użycie tymczasowego magazynu obiektów blob z kompresją. Korzystanie z magazynu tymczasowego jest przydatne, gdy przepustowość sieci firmowej i usług platformy Azure jest czynnikiem ograniczającym i chcesz, aby zestaw danych wejściowych i zestaw danych wyjściowych były w postaci nieskompresowanej. Dokładniej, można przerwać pojedyncze działanie kopiowania w dwóch działaniach kopiowania. Pierwsze działanie kopiowania Kopiuje ze źródła do tymczasowego lub tymczasowego obiektu BLOB w postaci skompresowanej. Drugie działanie kopiowania Kopiuje skompresowane dane z przemieszczania, a następnie dekompresuje podczas zapisywania do ujścia.

## <a name="considerations-for-column-mapping"></a>Uwagi dotyczące mapowania kolumn
Można ustawić właściwość **ColumnMappings** w działaniu Copy, aby mapować wszystkie lub podzbiór kolumn wejściowych do kolumn danych wyjściowych. Gdy usługa przenoszenia danych odczytuje dane ze źródła, musi wykonać Mapowanie kolumn danych przed zapisem danych do ujścia. To dodatkowe przetwarzanie zmniejsza przepływność kopiowania.

Jeśli źródłowy magazyn danych to queryable, na przykład, jeśli jest to magazyn relacyjny, taki jak SQL Database lub SQL Server, lub jeśli jest to Magazyn NoSQL, taki jak Table Storage lub Azure Cosmos DB, rozważ wypchnięcie reguły filtrowania kolumn i zmiany kolejności dla właściwości **zapytania** zamiast korzystania z mapowania kolumn. W ten sposób rzutowanie występuje, gdy usługa przenoszenia danych odczytuje dane ze źródłowego magazynu danych, gdzie jest znacznie bardziej wydajne.

## <a name="other-considerations"></a>Inne zagadnienia
Jeśli rozmiar danych, które mają zostać skopiowane, jest duży, można dostosować logikę biznesową, aby dodatkowo podzielić dane przy użyciu mechanizmu odcinania w Data Factory. Następnie należy zaplanować działanie kopiowania częściej, aby zmniejszyć rozmiar danych dla każdego uruchomienia działania kopiowania.

Należy zachować ostrożność w zakresie liczby zestawów danych i działań kopiowania wymagających Data Factory łączników do tego samego magazynu danych w tym samym czasie. Wiele współbieżnych zadań kopiowania może ograniczać magazyn danych i prowadzić do obniżenia wydajności, kopiowania wewnętrznych ponownych prób zadań, a w niektórych przypadkach błędy wykonywania.

## <a name="sample-scenario-copy-from-a-sql-server-database-to-blob-storage"></a>Przykładowy scenariusz: kopiowanie z bazy danych SQL Server do magazynu obiektów BLOB
**Scenariusz**: potok został utworzony w celu skopiowania danych z bazy danych SQL Server do magazynu obiektów BLOB w formacie CSV. Aby zadanie kopiowania było szybsze, należy skompresować pliki CSV w formacie BZIP2.

**Testowanie i analiza**: przepływność działania kopiowania jest mniejsza niż 2 MB/s, co jest znacznie wolniejsze niż wynikowy test wydajności.

**Analiza wydajności i dostrajanie**: Aby rozwiązać problem z wydajnością, przyjrzyjmy się, jak dane są przetwarzane i przenoszone.

1. **Odczytaj dane**: Brama otwiera połączenie w celu SQL Server i wysyła zapytanie. SQL Server odpowiada, wysyłając strumień danych do bramy za pośrednictwem intranetu.
2. **Serializowanie i kompresowanie danych**: Brama serializować strumień danych do formatu CSV i kompresuje dane do strumienia bzip2.
3. **Zapisz dane**: Brama przekazuje strumień bzip2 do magazynu obiektów BLOB za pośrednictwem Internetu.

Jak widać, dane są przetwarzane i przenoszone w sposób sekwencyjny przesyłania strumieniowego: SQL Server > LAN > Gateway > sieci WAN > BLOB Storage. **Ogólna wydajność jest planowana przez minimalną przepływność w potoku**.

![Przepływ danych](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Co najmniej jeden z następujących czynników może spowodować wąskie gardła wydajności:

* **Źródło**: SQL Server samo ma niską przepływność ze względu na duże obciążenia.
* **Zarządzanie danymi Gateway**:
  * **LAN**: Brama znajduje się poza komputerem SQL Server i ma połączenie o niskiej przepustowości.
  * **Brama**: Brama osiągnęła swoje ograniczenia dotyczące obciążenia, aby wykonać następujące operacje:
    * **Serializacja**: Serializowanie strumienia danych do formatu CSV ma niską przepływność.
    * **Kompresowanie**: wybrano wolny koder-dekoder kompresji (na przykład bzip2, czyli 2,8 MB/s z rdzeniem Core i7).
  * **Sieć WAN**: przepustowość między siecią firmową i usługami platformy Azure jest niska (na przykład T1 = 1 544 KB/s; T2 = 6 312 KB/s).
* **Ujścia**: Magazyn obiektów BLOB ma niską przepływność. (Ten scenariusz jest mało prawdopodobne, ponieważ jego umowa SLA gwarantuje minimalnie 60 MB/s).

W takim przypadku kompresja danych bzip2 może spowalniać cały potok. Przełączenie na koder-dekoder kompresji gzip może ułatwić to wąskie gardło.

## <a name="sample-scenarios-use-parallel-copy"></a>Przykładowe scenariusze: Użyj kopiowania równoległego
**Scenariusz:** Skopiuj pliki 1 000 1-MB z lokalnego systemu plików do magazynu obiektów BLOB.

**Dostrajanie analizy i wydajności**: na przykład jeśli masz zainstalowaną bramę na maszynie z czterema procesorami, Data Factory używa 16 równoległych kopii do przenoszenia plików z systemu plików do magazynu obiektów BLOB współbieżnie. To wykonywanie równoległe powinno powodować wysoką przepływność. Można również jawnie określić liczbę kopii równoległych. W przypadku kopiowania wielu małych plików kopie równoległe znacznie ułatwiają przepływność przy użyciu zasobów.

![Scenariusz 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenariusz II**: Kopiuj 20 obiektów blob z 500 MB każdego z magazynu obiektów Blob do Data Lake Store Analytics, a następnie dostosuj wydajność.

**Dostrajanie analizy i wydajności**: w tym scenariuszu Data Factory kopiuje dane z usługi BLOB storage do Data Lake Store przy użyciu pojedynczej kopii (**parallelCopies** ) i jednostek przenoszenia danych w chmurze. Zaobserwuj przepływność w pobliżu tego, co zostało opisane w [sekcji Informacje o wydajności](#performance-reference).

![Scenariusz 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenariusz III**: rozmiar pojedynczego pliku jest większy niż dziesiątki MB, a całkowita ilość jest duża.

**Włączanie analizy i wydajności**: zwiększenie **parallelCopies** nie powoduje zwiększenia wydajności kopiowania ze względu na ograniczenia zasobów jednej chmury DMU. Zamiast tego należy określić więcej DMUs w chmurze, aby uzyskać więcej zasobów do przeprowadzenia przenoszenia danych. Nie określaj wartości właściwości **parallelCopies** . Data Factory obsługuje równoległości. W tym przypadku ustawienie **cloudDataMovementUnits** na 4 spowoduje przepełnienie przepływności około czterech razy.

![Scenariusz 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Dokumentacja
Poniżej znajdują się informacje dotyczące monitorowania wydajności i dostrajania dla niektórych obsługiwanych magazynów danych:

* Azure Blob Storage: [elementy docelowe skalowalności i wydajności dla magazynu obiektów BLOB](../../storage/blobs/scalability-targets.md) i [wydajności i skalowalności dla usługi BLOB Storage](../../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [elementy docelowe skalowalności i wydajności dla magazynu tabel](../../storage/tables/scalability-targets.md) oraz [listę kontrolną wydajności i skalowalności w magazynie tabel](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: można [monitorować wydajność](../../azure-sql/database/monitor-tune-overview.md) i sprawdzać wartość procentową jednostki transakcji bazy danych (DTU)
* Azure Synapse Analytics: jej możliwości są mierzone w jednostkach magazynu danych (jednostek dwu); Zobacz [zarządzanie mocą obliczeniową w usłudze Azure Synapse Analytics (omówienie)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [poziomy wydajności w Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* SQL Server lokalny: [monitorowanie i dostrajanie wydajności](/sql/relational-databases/performance/monitor-and-tune-for-performance)
* Lokalny serwer plików: [dostrajanie wydajności dla serwerów plików](/previous-versions//dn567661(v=vs.85))