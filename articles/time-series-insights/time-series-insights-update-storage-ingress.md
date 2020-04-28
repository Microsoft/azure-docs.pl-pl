---
title: Magazyn danych i ruch przychodzący w wersji zapoznawczej — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej na temat przechowywania i transferów danych w wersji zapoznawczej Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: e3af10e5e9b56b537fedf0af7ffa7ddb37030c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189185"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Magazyn danych i ruch przychodzący w wersji zapoznawczej Azure Time Series Insights

W tym artykule opisano aktualizacje usługi Data Storage i transfer danych przychodzących na potrzeby wersji zapoznawczej Azure Time Series Insights. Opisuje on podstawową strukturę magazynu, format pliku i Właściwość identyfikatora szeregów czasowych. Opisano również podstawowe procesy związane z transferem danych przychodzących, najlepsze rozwiązania i bieżące ograniczenia wersji zapoznawczej.

## <a name="data-ingress"></a>Dane wejściowe

Środowisko Azure Time Series Insights zawiera *aparat* pozyskiwania do zbierania, przetwarzania i przechowywania danych szeregów czasowych.

Istnieją pewne kwestie, które należy wziąć pod uwagę w celu zapewnienia, że wszystkie dane przychodzące są przetwarzane, aby osiągnąć wysoką skalę transferu danych przychodzących, i zminimalizować *opóźnienia* pozyskiwania (czas potrzebny na Time Series Insights odczytywania i przetwarzania dane ze źródła zdarzeń) podczas [planowania środowiska](time-series-insights-update-plan.md).

Time Series Insights w wersji zapoznawczej zasady dotyczące danych przychodzących określają, z których danych mogą pochodzić dane i jakie dane powinny mieć format.

### <a name="ingress-policies"></a>Zasady dotyczące transferu danych przychodzących

Transfer *danych* przychodzących polega na tym, jak dane są wysyłane do środowiska Azure Time Series Insights w wersji zapoznawczej.

Poniżej zestawiono podstawowe wskazówki dotyczące konfiguracji, formatowania i najlepszych rozwiązań.

#### <a name="event-sources"></a>Źródła zdarzeń

Wersja zapoznawcza Azure Time Series Insights obsługuje następujące źródła zdarzeń:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Wersja zapoznawcza Azure Time Series Insights obsługuje maksymalnie dwa źródła zdarzeń na wystąpienie. Po nawiązaniu połączenia ze źródłem zdarzeń środowisko TSI odczytuje wszystkie zdarzenia, które są obecnie przechowywane w usłudze IoT lub centrum zdarzeń, rozpoczynając od najstarszego zdarzenia.

> [!IMPORTANT]
>
> * W przypadku dołączania źródła zdarzeń do środowiska w wersji zapoznawczej mogą wystąpić wysokie początkowe opóźnienia.
> Opóźnienie źródła zdarzenia zależy od liczby zdarzeń znajdujących się obecnie w IoT Hub lub centrum zdarzeń.
> * Duże opóźnienie zostanie umieszczone po pierwszym pozyskaniu danych źródła zdarzenia. Prześlij bilet pomocy technicznej za pomocą Azure Portal, jeśli wystąpią duże opóźnienia.

#### <a name="supported-data-format-and-types"></a>Obsługiwane formaty i typy danych

Azure Time Series Insights obsługuje zakodowany w formacie JSON format UTF-8 wysłany z usługi Azure IoT Hub lub Azure Event Hubs. 

Obsługiwane typy danych to:

| Typ danych | Opis |
|---|---|
| **bool** | Typ danych, który ma jeden z dwóch `true` Stanów `false`: lub. |
| **Datę** | Reprezentuje chwilę w czasie, zwykle wyrażoną jako datę i godzinę dnia. Wyrażony w formacie [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . |
| **double** | Podwójnie precyzyjne 64-bitowe [IEEE 754](https://ieeexplore.ieee.org/document/8766229) zmiennoprzecinkowe. |
| **parametry** | Wartości tekstowe składające się z znaków Unicode.          |

#### <a name="objects-and-arrays"></a>Obiekty i tablice

Można wysyłać złożone typy, takie jak obiekty i tablice, w ramach ładunku zdarzenia, ale dane zostaną przetworzone przez proces spłaszczania w przypadku przechowywania.

Szczegółowe informacje opisujące sposób kształtowania zdarzeń w formacie JSON, wysyłania typu złożonego i zagnieżdżonego spłaszczania obiektów są dostępne w [metodzie tworzenia kształtów JSON dla](./time-series-insights-update-how-to-shape-events.md) ruchu przychodzącego i zapytań w celu ułatwienia planowania i optymalizacji.

### <a name="ingress-best-practices"></a>Najlepsze rozwiązania związane z transferem danych przychodzących

Zalecamy stosowanie następujących najlepszych rozwiązań:

* Skonfiguruj Azure Time Series Insights i wszystkie IoT Hub lub centrum zdarzeń w tym samym regionie, aby zmniejszyć potencjalne opóźnienia.

* [Zaplanuj potrzeby skalowania](time-series-insights-update-plan.md) , obliczając przewidywany wskaźnik pozyskiwania i sprawdzając, czy znajduje się on w ramach obsługiwanej stawki wymienionej poniżej.

* Dowiedz się, jak optymalizować i kształtować dane JSON, a także bieżące ograniczenia w wersji zapoznawczej, odczytując [Informacje o sposobie tworzenia kształtu JSON dla](./time-series-insights-update-how-to-shape-events.md)ruchu przychodzącego i zapytań.

### <a name="ingress-scale-and-preview-limitations"></a>Ograniczenia skali i wersji zapoznawczej

Poniżej opisano ograniczenia związane z transferem danych przychodzących Azure Time Series Insights.

> [!TIP]
> Przeczytaj artykuł [Planowanie środowiska w wersji zapoznawczej](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) , aby uzyskać pełną listę wszystkich limitów wersji zapoznawczych.

#### <a name="per-environment-limitations"></a>Ograniczenia dotyczące środowiska

Ogólnie rzecz biorąc, stawki za transfer danych przychodzących są wyświetlane jako współczynnik liczby urządzeń w organizacji, częstotliwości emisji zdarzeń i rozmiaru każdego zdarzenia:

*  **Liczba urządzeń** × **częstotliwość emisji zdarzeń** × **rozmiar każdego zdarzenia**.

Domyślnie Time Series Insights w wersji zapoznawczej można pozyskać dane przychodzące z szybkością **do 1 megabajtów na sekundę (MB/s) na Time Series Insights środowisku**. Istnieją dodatkowe ograniczenia [na partycję centrum](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Obsługa środowiska w celu pozyskiwania prędkości do 16 MB/s może być świadczona przez żądanie.
> * Skontaktuj się z nami, jeśli potrzebujesz wyższej przepływności przez przesłanie biletu pomocy technicznej za pomocą Azure Portal.
 
* **Przykład 1:**

    Wysyłka firmy Contoso obejmuje 100 000 urządzeń, które emitują wydarzenie trzy razy na minutę. Rozmiar zdarzenia to 200 bajtów. Korzystają one z usługi IoT Hub z czterema partycjami jako źródłem zdarzeń Time Series Insights.

    * Szybkość pozyskiwania dla środowiska Time Series Insights: **100 000 urządzeń * 200 bajtów/zdarzenia * (3/60 Event/s) = 1 MB/s**.
    * Szybkość pozyskiwania na partycję 0,25 MB/s.
    * Stawka pozyskiwania kosztów wysyłki firmy Contoso będzie w ramach ograniczenia skalowania w wersji zapoznawczej.

* **Przykład 2:**

    Analiza floty firmy Contoso obejmuje 60 000 urządzeń, które emitują zdarzenia co sekundę. Korzystają one z centrum zdarzeń z liczbą partycji 4 jako źródłem zdarzenia Time Series Insights. Rozmiar zdarzenia to 200 bajtów.

    * Szybkość pozyskiwania środowiska: **60 000 urządzeń * 200 bajtów/zdarzenia * 1 zdarzenie/s = 12 MB/s**.
    * Stawka za partycje wynosi 3 MB/s.
    * Wskaźnik pozyskiwania danych z usługi contoso floty jest powyżej limitów środowiska i partycji. Mogą przesłać żądanie Time Series Insights przez Azure Portal, aby zwiększyć szybkość pozyskiwania dla środowiska i utworzyć centrum zdarzeń z większą liczbą partycji w ramach limitów wersji zapoznawczej.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partycje centrum i limity na partycję

Planując środowisko Time Series Insights, należy rozważyć konfigurację źródeł zdarzeń, z którymi będziesz się łączyć Time Series Insights. Zarówno IoT Hub platformy Azure, jak i Event Hubs używają partycji, aby włączyć skalowanie w poziomie na potrzeby przetwarzania zdarzeń. 

*Partycja* to uporządkowana sekwencja zdarzeń przechowywanych w centrum. Liczba partycji jest ustawiana podczas fazy tworzenia centrum i nie można jej zmienić.

Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi partycjonowania Event Hubs, sprawdź, [ile partycji jest potrzebnych?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Większość centrów IoT używanych z Azure Time Series Insights potrzebuje tylko czterech partycji.

Bez względu na to, czy tworzysz nowe centrum dla środowiska Time Series Insights, czy korzystasz z istniejącego, musisz obliczyć współczynnik pozyskiwania partycji, aby określić, czy jest on w ramach limitów wersji zapoznawczej. 

Wersja zapoznawcza Azure Time Series Insights ma obecnie **limit na partycję 0,5 MB/s**.

#### <a name="iot-hub-specific-considerations"></a>Zagadnienia dotyczące IoT Hub

Gdy urządzenie zostanie utworzone w IoT Hub, zostaje trwale przypisane do partycji. W takim przypadku IoT Hub jest w stanie zagwarantować porządkowanie zdarzeń (ponieważ nigdy nie zmieni się przypisanie).

Stałe przypisanie partycji ma także wpływ na Time Series Insights wystąpieniami, które pobierają dane wysyłane z IoT Hub podrzędnych. Gdy komunikaty z wielu urządzeń są przekazywane do koncentratora przy użyciu tego samego identyfikatora urządzenia bramy, mogą się one pojawić w tej samej partycji, co może spowodować przekroczenie limitów skalowania partycji.

**Wpływ**:

* Jeśli jedna partycja ma stałą częstotliwość pozyskiwania w ramach limitu wersji zapoznawczej, istnieje możliwość, że Time Series Insights nie zsynchronizuje wszystkich danych telemetrycznych urządzenia przed przekroczeniem okresu przechowywania IoT Hub. W związku z tym wysyłane dane mogą zostać utracone w przypadku stałego przekroczenia limitów pozyskiwania.

Aby wyeliminować to okoliczności, zalecamy stosowanie następujących najlepszych rozwiązań:

* Przed wdrożeniem rozwiązania Oblicz swoje licencje na środowisko i przejęcie na partycje.
* Upewnij się, że urządzenia IoT Hub są zrównoważone obciążenia w możliwie najszerszym zakresie.

> [!IMPORTANT]
> W przypadku środowisk, w których IoT Hub jako źródło zdarzeń, Oblicz wskaźnik pozyskiwania przy użyciu liczby używanych urządzeń Hub, aby upewnić się, że stawka spadnie poniżej limitu 0,5 MB/s na partycję w wersji zapoznawczej.
>
> * Nawet wtedy, gdy kilka zdarzeń dociera jednocześnie, limit wersji zapoznawczej nie zostanie przekroczony.

  ![Diagram partycji IoT Hub](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Zapoznaj się z poniższymi zasobami, aby dowiedzieć się więcej na temat optymalizowania przepływności i partycji centrum:

* [Skalowanie IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Skala centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partycje centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Magazyn danych

Po utworzeniu środowiska Time Series Insights w wersji zapoznawczej *płatność zgodnie z rzeczywistym* użyciem (PAYG) utworzysz dwa zasoby platformy Azure:

* Środowisko Azure Time Series Insights w wersji zapoznawczej, które można skonfigurować pod kątem magazynu z ciepłymi danymi.
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1 dla magazynu zimnych danych.

Dane w magazynie ciepłym są dostępne tylko za pośrednictwem [zapytania szeregów czasowych](./time-series-insights-update-tsq.md) i [Eksploratora Azure Time Series Insights Preview](./time-series-insights-update-explorer.md). Sklep ciepły będzie zawierał ostatnie dane w [okresie przechowywania](./time-series-insights-update-plan.md#the-preview-environment) wybranym podczas tworzenia środowiska Time Series Insights.

Time Series Insights w wersji zapoznawczej zapisuje dane w chłodnym sklepie w usłudze Azure Blob Storage w [formacie pliku Parquet](#parquet-file-format-and-folder-structure). Time Series Insights wersja zapoznawcza zarządza wyłącznie tym zimnym magazynem danych, ale jest dostępny do odczytu bezpośrednio jako standardowe pliki Parquet.

> [!WARNING]
> Jako właściciel konta usługi Azure Blob Storage, na którym znajdują się dane w chłodnym sklepie, masz pełny dostęp do wszystkich danych na koncie. Ten dostęp obejmuje uprawnienia do zapisu i usuwania. Nie Edytuj ani nie usuwaj danych, które Time Series Insights zapisywania w wersji zapoznawczej, ponieważ może to spowodować utratę danych.

### <a name="data-availability"></a>Dostępność danych

Azure Time Series Insights Podgląd partycji i indeksowanie danych w celu uzyskania optymalnej wydajności zapytań. Dane staną się dostępne do wykonywania zapytań z obu ciepłej (jeśli są włączone) i magazynu zimnego po jego indeksowaniu. Ilość danych, które są pozyskiwane, może mieć wpływ na tę dostępność.

> [!IMPORTANT]
> W trakcie korzystania z wersji zapoznawczej może wystąpić okres do 60 sekund, po upływie którego dane staną się dostępne. Jeśli wystąpi znaczący czas oczekiwania przekraczający 60 sekund, Prześlij bilet pomocy technicznej za pomocą Azure Portal.

## <a name="azure-storage"></a>Azure Storage

W tej sekcji opisano szczegóły usługi Azure Storage istotne dla Azure Time Series Insights wersji zapoznawczej.

Aby uzyskać dokładny opis magazynu obiektów blob platformy Azure, Przeczytaj [wprowadzenie do magazynu obiektów BLOB](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Twoje konto magazynu

W przypadku tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej usługa Azure Storage ogólnego przeznaczenia w wersji 1 zostanie utworzona jako długoterminowy chłodny magazyn.  

Azure Time Series Insights Preview zachowuje maksymalnie dwie kopie każdego zdarzenia na koncie usługi Azure Storage. Jedna kopia przechowuje zdarzenia uporządkowane według czasu pozyskiwania, które zawsze umożliwiają dostęp do zdarzeń w kolejności uporządkowanej według czasu. W miarę upływu czasu program Time Series Insights Preview tworzy również kopię ponownie partycjonowaną danych, która zostanie zoptymalizowana pod kątem wykonywania Time Series Insights kwerendy.

W publicznej wersji zapoznawczej dane są przechowywane na koncie usługi Azure Storage na czas nieokreślony.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Pisanie i edytowanie Time Series Insights obiektów BLOB

Aby zapewnić wydajność zapytań i dostępność danych, nie należy edytować ani usuwać obiektów blob, które są tworzone Time Series Insights Podgląd.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Uzyskiwanie dostępu do danych w chłodnym magazynie Time Series Insights Preview

Oprócz uzyskiwania dostępu do danych za pomocą zapytania programu [Time Series Insights Preview](./time-series-insights-update-explorer.md) i z [szeregów czasowych](./time-series-insights-update-tsq.md)możesz również uzyskać dostęp do danych bezpośrednio z plików Parquet przechowywanych w chłodnym magazynie. Na przykład można odczytywać, przekształcać i czyścić dane w notesie Jupyter, a następnie używać go do uczenia modelu Azure Machine Learning w tym samym przepływie pracy platformy Spark.

Aby uzyskać dostęp do danych bezpośrednio z konta usługi Azure Storage, musisz mieć dostęp do odczytu do konta używanego do przechowywania danych w wersji zapoznawczej Time Series Insights. Następnie można odczytać wybrane dane na podstawie czasu utworzenia pliku Parquet znajdującego się w `PT=Time` folderze opisanym poniżej w sekcji [Format pliku Parquet](#parquet-file-format-and-folder-structure) .  Aby uzyskać więcej informacji na temat włączania dostępu do odczytu do konta magazynu, zobacz [Zarządzanie dostępem do zasobów konta magazynu](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Usuwanie danych

Nie usuwaj plików w wersji zapoznawczej Time Series Insights. Zarządzaj powiązanymi danymi wyłącznie w programie Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet format pliku i struktura folderów

Parquet to format pliku kolumnowego Open Source zaprojektowany w celu zapewnienia wydajnego magazynu i wydajności. Time Series Insights w wersji zapoznawczej używa Parquet do włączenia wydajności zapytań opartych na IDENTYFIKATORach szeregów czasowych.  

Aby uzyskać więcej informacji na temat typu pliku Parquet, Przeczytaj [dokumentację dotyczącą Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights w wersji zapoznawczej przechowuje kopie danych w następujący sposób:

* Pierwsza kopia początkowa jest podzielona na partycje według czasu pozyskiwania i przechowuje dane w sposób przybliżony w kolejności przybycia. Te dane znajdują się w `PT=Time` folderze:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druga kopia z podziałem na partycje jest pogrupowana według identyfikatorów szeregów czasowych i znajduje się `PT=TsId` w folderze:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

W obu przypadkach Właściwość Time pliku Parquet odpowiada czasowi utworzenia obiektu BLOB. Dane w `PT=Time` folderze są zachowywane bez zmian po zapisaniu ich w pliku. Dane w `PT=TsId` folderze zostaną zoptymalizowane pod kątem zapytania w czasie i nie są statyczne.

> [!NOTE]
>
> * `<YYYY>`mapuje do czwartej reprezentacji rocznej.
> * `<MM>`mapuje na dwucyfrowe reprezentację miesiąca.
> * `<YYYYMMDDHHMMSSfff>`mapuje do sygnatury czasowej z`YYYY`czterocyfrowym rokiem (), dwucyfrowym miesiącem (`MM`), dwucyfrowym dniem (`DD`), dwucyfrowym godzinem`HH`(), dwucyfrowym (`MM`), dwucyfrowym (`SS`) i 3-cyfrowym milisekundy (`fff`).

Zdarzenia w wersji zapoznawczej Time Series Insights są mapowane do zawartości pliku Parquet w następujący sposób:

* Każde zdarzenie jest mapowane na jeden wiersz.
* Każdy wiersz zawiera kolumnę **timestamp** z sygnaturą czasową zdarzenia. Właściwość sygnatury czasowej nigdy nie ma wartości null. Wartość domyślna zdarzenia jest umieszczana w **kolejce czasu** , jeśli właściwość sygnatura czasowa nie została określona w źródle zdarzenia. Sygnatura czasowa przechowywana jest zawsze w formacie UTC.
* Każdy wiersz zawiera kolumny identyfikatora szeregów czasowych (TSID), zgodnie z definicją podczas tworzenia środowiska Time Series Insights. Nazwa właściwości identyfikatora TSID zawiera `_string` sufiks.
* Wszystkie inne właściwości wysyłane jako dane telemetryczne są mapowane na nazwy kolumn kończące się `_string` znakiem `_bool` (String), `_datetime` (wartość logiczna), `_double` (DateTime) lub (Double), w zależności od typu właściwości.
* Ten schemat mapowania dotyczy pierwszej wersji formatu pliku, do którego odwołuje się wartość **V = 1** i jest przechowywana w folderze podstawowym o tej samej nazwie. W miarę rozwoju tej funkcji ten schemat mapowania może ulec zmianie, a Nazwa odwołania jest zwiększana.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [, jak Shape JSON dla](./time-series-insights-update-how-to-shape-events.md)ruchu przychodzącego i zapytań.

- Przeczytaj o nowym [modelowaniu danych](./time-series-insights-update-tsm.md).
