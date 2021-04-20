---
title: Omówienie magazynu — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej o magazynie danych w Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: cd26df1de86ee4bdb33050d0bc4769663707733e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725030"
---
# <a name="data-storage"></a>Magazyn danych

W tym artykule opisano magazyn danych w Azure Time Series Insights Gen2. Obejmuje ona ciepło i zimno, dostępność danych i najlepsze rozwiązania.

## <a name="provisioning"></a>Inicjowanie obsługi

Podczas tworzenia środowiska Azure Time Series Insights Gen2 dostępne są następujące opcje:

* Zimny magazyn danych:
  * Utwórz nowy zasób usługi Azure Storage w subskrypcji i regionie wybranym dla środowiska.
  * Dołącz istniejące konto usługi Azure Storage. Ta opcja jest dostępna tylko po wdrożeniu z Azure Resource Manager [szablonu](/azure/templates/microsoft.timeseriesinsights/allversions)i nie jest widoczna w Azure Portal.
* Ciepły magazyn danych:
  * Ciepły magazyn jest opcjonalny i można go włączyć lub wyłączyć podczas aprowizowania lub po jego zakończeniu. Jeśli zdecydujesz się włączyć ciepły magazyn w późniejszym czasie i [](concepts-storage.md#warm-store-behavior) w zimnym magazynie znajdują się już dane, zapoznaj się z tą sekcją poniżej, aby zrozumieć oczekiwane zachowanie. Czas przechowywania danych magazynu na ciepło można skonfigurować na 7–31 dni. Można go również dostosować odpowiednio do potrzeb.

Po pozyszeniu zdarzenie jest indeksowane zarówno w magazynie ciepłym (jeśli jest włączony), jak i zimnym magazynie.

[![Omówienie magazynu](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Jako właściciel konta usługi Azure Blob Storage, na którym znajdują się dane zimnego magazynu, masz pełny dostęp do wszystkich danych na koncie. Ten dostęp obejmuje uprawnienia do zapisu i usuwania. Nie należy edytować ani usuwać danych, które są Azure Time Series Insights Gen2, ponieważ może to spowodować utratę danych.

## <a name="data-availability"></a>Dostępność danych

Azure Time Series Insights Gen2 partycje i indeksy danych w celu uzyskania optymalnej wydajności zapytań. Dane stają się dostępne do wykonywania zapytań z ciepłych (jeśli są włączone) i zimnych magazynów po ich zindeksacji. Ilość danych, które są zbierane, i szybkość przepływności na partycję może mieć wpływ na dostępność. Zapoznaj się z ograniczeniami [przepływności](./concepts-streaming-ingress-throughput-limits.md) źródła zdarzeń i [najlepszymi rozwiązaniami w](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) celu zwiększenia wydajności. Możesz również skonfigurować alert o [opóźnień, aby był](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) powiadamiany, jeśli w środowisku występują problemy z przetwarzaniem danych.

> [!IMPORTANT]
> Może upłynieć do 60 sekund, zanim dane będą dostępne. Jeśli masz duże opóźnienie większe niż 60 sekund, prześlij bilet pomocy technicznej za pośrednictwem Azure Portal.

## <a name="warm-store"></a>Ciepły magazyn

Dane w ciepłym magazynie są dostępne tylko za pośrednictwem interfejsów [API](./concepts-query-overview.md)zapytań szeregów czasu, Azure Time Series Insights [eksploratora](./concepts-ux-panels.md)usługi TSI [lub łącznika Power BI.](./how-to-connect-power-bi.md) Zapytania magazynu gorącego są bezpłatne i nie ma limitu przydziału, ale istnieje [limit 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) żądań współbieżnych.

### <a name="warm-store-behavior"></a>Zachowanie ciepłych magazynów

* Po włączeniu wszystkie dane przesyłane strumieniowo do środowiska będą kierowane do ciepłego magazynu, niezależnie od znacznika czasu zdarzenia. Należy pamiętać, że potok pozyskiwania danych przesyłanych strumieniowo jest zbudowany dla przesyłania strumieniowego niemal w czasie rzeczywistym, a pozyskiwanie zdarzeń historycznych nie [jest obsługiwane.](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)
* Okres przechowywania jest obliczany na podstawie tego, kiedy zdarzenie zostało zindeksowane w ciepłym magazynie, a nie na znaczniku czasu zdarzenia. Oznacza to, że dane nie są już dostępne w ciepłym magazynie po upływie okresu przechowywania, nawet jeśli sygnatura czasowa zdarzenia ma być na przyszłość.
  * Przykład: zdarzenie z 10-dniowymi prognozami pogody jest pozytywowane i indeksowane w ciepłym kontenerze magazynu skonfigurowanym z 7-dniowym okresem przechowywania. Po upływie siedmiu dni przewidywanie nie będzie już dostępne w ciepłym magazynie, ale będzie można wytypować je z zimna.
* Jeśli włączysz ciepły magazyn w istniejącym środowisku, w którym ostatnio indeksowane są dane w zimnym magazynie, pamiętaj, że ciepły magazyn nie zostanie ponownie wypełniony danymi.
* Jeśli właśnie włączono ciepły magazyn i występują problemy z wyświetlaniem ostatnich danych w Eksploratorze, możesz tymczasowo wyłączyć zapytania o ciepły magazyn:

   [![Wyłączanie ciepłych zapytań](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Zimny magazyn

W tej sekcji opisano szczegóły usługi Azure Storage dotyczące usługi Azure Time Series Insights Gen2.

Aby uzyskać szczegółowy opis usługi Azure Blob Storage, przeczytaj wprowadzenie [do obiektów blob usługi Storage.](../storage/blobs/storage-blobs-introduction.md)

### <a name="your-cold-storage-account"></a>Konto magazynu zimnego

Azure Time Series Insights Gen2 przechowuje do dwóch kopii każdego zdarzenia na koncie usługi Azure Storage. Jedna kopia przechowuje zdarzenia uporządkowane według czasu pozyskiwania, zawsze zezwalając na dostęp do zdarzeń w kolejności czasowej. Z czasem Azure Time Series Insights Gen2 tworzy również ponownie partycjonacyjną kopię danych w celu optymalizacji pod kątem wykonywania zapytań.

Wszystkie dane są przechowywane przez czas nieokreślony na koncie usługi Azure Storage.

> [!WARNING]
> Nie ograniczaj publicznego dostępu do Internetu do centrum lub źródła zdarzeń używanego przez Time Series Insights lub wymagane połączenie zostanie przerwane.

#### <a name="writing-and-editing-blobs"></a>Pisanie i edytowanie obiektów blob

Aby zapewnić wydajność zapytań i dostępność danych, nie należy edytować ani usuwać żadnych obiektów blob Azure Time Series Insights Gen2.

#### <a name="accessing-cold-store-data"></a>Uzyskiwanie dostępu do danych zimnego magazynu

Oprócz uzyskiwania dostępu do danych z interfejsów API zapytań usługi [Azure Time Series Insights Explorer](./concepts-ux-panels.md) i Time [Series Query](./concepts-query-overview.md)możesz również uzyskać dostęp do danych bezpośrednio z plików Parquet przechowywanych w zimnym magazynie. Na przykład możesz odczytywać, przekształcać i czyścić dane w notesie Jupyter, a następnie używać ich do trenowania modelu Azure Machine Learning w tym samym przepływie pracy platformy Spark.

Aby uzyskać dostęp do danych bezpośrednio z konta usługi Azure Storage, musisz mieć dostęp do odczytu do konta używanego do przechowywania danych Azure Time Series Insights Gen2. Następnie możesz odczytywać wybrane dane na podstawie czasu utworzenia pliku Parquet znajdującego się w folderze opisanym poniżej w sekcji Format pliku `PT=Time` [Parquet.](#parquet-file-format-and-folder-structure)  Aby uzyskać więcej informacji na temat włączania dostępu do odczytu do konta magazynu, zobacz [Zarządzanie dostępem do zasobów konta magazynu.](../storage/blobs/anonymous-read-access-configure.md)

#### <a name="data-deletion"></a>Usuwanie danych

Nie usuwaj plików Azure Time Series Insights Gen2. Zarządzanie powiązanymi danymi tylko z Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Format pliku Parquet i struktura folderów

Parquet to format pliku kolumnowego typu open source zaprojektowany pod kątem wydajnego magazynowania i wydajności. Azure Time Series Insights Gen2 używa usługi Parquet, aby włączyć wydajność zapytań na podstawie identyfikatora szeregów czasu na dużą skalę.

Aby uzyskać więcej informacji na temat typu pliku Parquet, zapoznaj się z [dokumentacją usługi Parquet.](https://parquet.apache.org/documentation/latest/)

Azure Time Series Insights Gen2 przechowuje kopie danych w następujący sposób:

* Pierwsza, kopia początkowa jest partycjonowana według czasu pozyskiwania i przechowuje dane w przybliżeniu w kolejności od nadejścia. Te dane znajdują się w `PT=Time` folderze :

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druga, ponownie partycjonatyzowana kopia jest pogrupowana według identyfikatorów szeregów czasowych i znajduje się w `PT=TsId` folderze :

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Znacznik czasu w nazwach obiektów blob w folderze odpowiada godzinie nadejścia danych Azure Time Series Insights Gen2, a nie sygnaturze czasowej `PT=Time` zdarzeń.

Dane w `PT=TsId` folderze zostaną zoptymalizowane pod kątem zapytań w czasie i nie będą statyczne. Podczas ponownego partycji niektóre zdarzenia mogą być obecne w wielu obiektach blob. Nie ma gwarancji, że nazwy obiektów blob w tym folderze pozostaną takie same.

Ogólnie rzecz biorąc, jeśli musisz uzyskać dostęp do danych bezpośrednio za pośrednictwem plików Parquet, użyj `PT=Time` folderu .  Przyszłe funkcje umożliwią wydajny dostęp do `PT=TsId` folderu.

> [!NOTE]
>
> * `<YYYY>` mapuje na czterocyfrową reprezentację roku.
> * `<MM>` mapuje na dwucyfrową reprezentację miesiąca.
> * `<YYYYMMDDHHMMSSfff>` mapuje na reprezentację sygnatury czasowej z czterocyfrowym rokiem (), dwucyfrowym miesiącem (), dwucyfrowym dniem (), dwucyfrową godziną (), dwucyfrową minutą (), dwucyfrową sekundą () i `YYYY` `MM` `DD` `HH` `MM` `SS` trzema cyframi `fff` ().

Azure Time Series Insights Gen2 są mapowane na zawartość pliku Parquet w następujący sposób:

* Każde zdarzenie jest mapowe na jeden wiersz.
* Każdy wiersz zawiera **kolumnę znacznika czasu** ze znacznikiem czasu zdarzenia. Właściwość sygnatury czasowej nigdy nie ma wartości null. Jeśli właściwość sygnatury czasowej nie zostanie określona w źródle zdarzenia, wartość domyślna to czas zdarzenia w kolejkach.  Przechowywana sygnatura czasowa jest zawsze w czasie UTC.
* Każdy wiersz zawiera kolumny identyfikatorów szeregów czasu (TSID) zgodnie z definicją podczas tworzenia Azure Time Series Insights Gen2. Nazwa właściwości TSID zawiera `_string` sufiks.
* Wszystkie inne właściwości wysyłane jako dane telemetryczne są mapowane na nazwy kolumn, które kończą się `_bool` na (wartość logiczna), (sygnatura czasowa), `_datetime` `_long` (długa), `_double` (double), `_string` (ciąg) lub `dynamic` (dynamiczna) w zależności od typu właściwości.  Aby uzyskać więcej informacji, przeczytaj o [obsługiwanych typach danych.](./concepts-supported-data-types.md)
* Ten schemat mapowania dotyczy pierwszej wersji formatu pliku, o nazwie **V=1** i przechowywanej w folderze bazowym o tej samej nazwie. W związku z rozwojem tej funkcji ten schemat mapowania może ulec zmianie i nazwa odwołania będzie zwiększana.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [modelowaniu danych.](./concepts-model-overview.md)

* Zaplanuj środowisko [Azure Time Series Insights Gen2.](./how-to-plan-your-environment.md)
