---
title: Omówienie usługi Storage — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej na temat przechowywania danych w Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 748eaca93eaee5ec858ea43261995111cef8ceda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676855"
---
# <a name="data-storage"></a>Magazyn danych

W tym artykule opisano magazyn danych w Azure Time Series Insights Gen2. Obejmuje to ciepło i zimność, dostępność danych oraz najlepsze rozwiązania.

## <a name="provisioning"></a>Inicjowanie obsługi

Podczas tworzenia środowiska Azure Time Series Insights Gen2 dostępne są następujące opcje:

* Chłodny magazyn danych:
  * Utwórz nowy zasób usługi Azure Storage w ramach subskrypcji i regionu wybranego dla danego środowiska.
  * Dołącz już istniejące konto usługi Azure Storage. Ta opcja jest dostępna tylko przez wdrożenie z [szablonu](/azure/templates/microsoft.timeseriesinsights/allversions)Azure Resource Manager i nie jest widoczna w Azure Portal.
* Magazyn danych ciepłych:
  * Magazyn ciepły jest opcjonalny i można go włączyć lub wyłączyć w czasie aprowizacji lub po nim. Jeśli zdecydujesz się na włączenie sklepu w sieci w późniejszym czasie, a w chłodnym magazynie znajdują się już dane, zapoznaj się [z sekcją](concepts-storage.md#warm-store-behavior) poniżej, aby zrozumieć oczekiwane zachowanie. Czas przechowywania danych w sklepie ciepłym można skonfigurować od 7 do 31 dni. można go również dostosować w razie potrzeby.

Gdy zdarzenie jest pozyskiwane, jest indeksowane w obu sklepach (jeśli są włączone) i w chłodnym magazynie.

[![Magazyn — Omówienie](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Jako właściciel konta usługi Azure Blob Storage, na którym znajdują się dane w chłodnym sklepie, masz pełny dostęp do wszystkich danych na koncie. Ten dostęp obejmuje uprawnienia do zapisu i usuwania. Nie należy edytować ani usuwać danych Azure Time Series Insights zapisów Gen2, ponieważ może to spowodować utratę danych.

## <a name="data-availability"></a>Dostępność danych

Azure Time Series Insights partycje Gen2 i indeksowanie danych w celu uzyskania optymalnej wydajności zapytań. Dane staną się dostępne do wykonywania zapytań z obu ciepłej (jeśli są włączone) i magazynu zimnego po jego indeksowaniu. Ilość danych, które są pozyskiwane, a szybkość przepływności na partycję może mieć wpływ na dostępność. Przejrzyj [ograniczenia przepływności](./concepts-streaming-ingress-throughput-limits.md) źródła zdarzeń i [najlepsze rozwiązania](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) w celu uzyskania najlepszej wydajności. Możesz również skonfigurować [alert](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) z opóźnieniem, aby otrzymywać powiadomienia o problemach z przetwarzaniem danych w środowisku.

> [!IMPORTANT]
> Zanim dane staną się dostępne, może wystąpić okres do 60 sekund. Jeśli wystąpi znaczący czas oczekiwania przekraczający 60 sekund, Prześlij bilet pomocy technicznej za pomocą Azure Portal.

## <a name="warm-store"></a>Sklep ciepły

Dane w magazynie ciepłym są dostępne tylko za pośrednictwem [interfejsów API zapytań szeregów czasowych](./concepts-query-overview.md), [Eksploratora TSI Azure Time Series Insights](./concepts-ux-panels.md)lub [łącznika Power BI](./how-to-connect-power-bi.md). Zapytania magazynu w sieci ciepłej są bezpłatne i nie ma limitu przydziału, ale obowiązuje [limit 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) współbieżnych żądań.

### <a name="warm-store-behavior"></a>Zachowanie magazynu ciepłego

* Po włączeniu wszystkie dane przesyłane strumieniowo do Twojego środowiska będą kierowane do magazynu ciepłego, niezależnie od sygnatury czasowej zdarzenia. Należy zauważyć, że potok pozyskiwania strumieniowego został skompilowany dla przesyłania strumieniowego niemal w czasie rzeczywistym i pozyskiwanie zdarzeń historycznych [nie jest obsługiwane](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* Okres przechowywania jest obliczany w oparciu o to, kiedy zdarzenie zostało zindeksowane w sklepie, a nie sygnatura czasowa zdarzenia. Oznacza to, że dane nie są już dostępne w sklepie ciepłym po upływie okresu przechowywania, nawet jeśli sygnatura czasowa zdarzenia jest w przyszłości.
  * Przykład: zdarzenie z 10-dniowymi prognozami pogody jest pozyskiwane i indeksowane w kontenerze magazynu ciepłego skonfigurowanym z 7-dniowym okresem przechowywania. Po upływie 7 dni przewidywanie nie jest już dostępne w sklepie ciepłym, ale można je zbadać na zimno.
* Jeśli włączysz magazyn ciepły w istniejącym środowisku, które ma już ostatnie dane indeksowane w chłodnym magazynie, pamiętaj, że magazyn ciepły nie zostanie wypełniony ponownie przy użyciu tych danych.
* Jeśli po prostu włączono sklep ciepły i występują problemy z wyświetlaniem najnowszych danych w Eksploratorze, można tymczasowo przełączać zapytania z magazynu w sieci w sieci:

   [![Wyłącz zapytania ciepłe](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Chłodna składowa

W tej sekcji opisano szczegóły usługi Azure Storage istotne dla Azure Time Series Insights Gen2.

Aby uzyskać dokładny opis magazynu obiektów blob platformy Azure, Przeczytaj [wprowadzenie do magazynu obiektów BLOB](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-cold-storage-account"></a>Twoje konto w chłodnym magazynie

Azure Time Series Insights Gen2 zachowuje maksymalnie dwie kopie każdego zdarzenia na koncie usługi Azure Storage. Jedna kopia przechowuje zdarzenia uporządkowane według czasu pozyskiwania, które zawsze umożliwiają dostęp do zdarzeń w kolejności uporządkowanej według czasu. W miarę upływu czasu program Azure Time Series Insights Gen2 również tworzy kopię danych ponownie partycjonowaną w celu optymalizacji pod kątem wykonywania zapytań.

Wszystkie dane są przechowywane na koncie usługi Azure Storage na czas nieokreślony.

#### <a name="writing-and-editing-blobs"></a>Zapisywanie i edytowanie obiektów BLOB

Aby zapewnić wydajność zapytań i dostępność danych, nie należy edytować ani usuwać obiektów Blob Azure Time Series Insights utworzonych przez program Gen2.

#### <a name="accessing-cold-store-data"></a>Uzyskiwanie dostępu do danych zimnego magazynu

Oprócz uzyskiwania dostępu do danych z interfejsów API zapytań programu [Azure Time Series Insights Explorer](./concepts-ux-panels.md) i [szeregów czasowych](./concepts-query-overview.md)możesz również uzyskać dostęp do danych bezpośrednio z plików Parquet przechowywanych w chłodnym magazynie. Na przykład można odczytywać, przekształcać i czyścić dane w notesie Jupyter, a następnie używać go do uczenia modelu Azure Machine Learning w tym samym przepływie pracy platformy Spark.

Aby uzyskać dostęp do danych bezpośrednio z konta usługi Azure Storage, musisz mieć dostęp do odczytu do konta używanego do przechowywania Azure Time Series Insights danych Gen2. Następnie można odczytać wybrane dane na podstawie czasu utworzenia pliku Parquet znajdującego się w `PT=Time` folderze opisanym poniżej w sekcji [Format pliku Parquet](#parquet-file-format-and-folder-structure) .  Aby uzyskać więcej informacji na temat włączania dostępu do odczytu do konta magazynu, zobacz [Zarządzanie dostępem do zasobów konta magazynu](../storage/blobs/anonymous-read-access-configure.md).

#### <a name="data-deletion"></a>Usuwanie danych

Nie usuwaj plików Azure Time Series Insights Gen2. Zarządzaj powiązanymi danymi tylko z poziomu usługi Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet format pliku i struktura folderów

Parquet to format pliku kolumnowego Open Source zaprojektowany w celu zapewnienia wydajnego magazynu i wydajności. Azure Time Series Insights Gen2 używa Parquet do włączania wydajności zapytań opartych na IDENTYFIKATORach szeregów czasowych.  

Aby uzyskać więcej informacji na temat typu pliku Parquet, Przeczytaj [dokumentację dotyczącą Parquet](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 przechowuje kopie danych w następujący sposób:

* Pierwsza kopia początkowa jest podzielona na partycje według czasu pozyskiwania i przechowuje dane w sposób przybliżony w kolejności przybycia. Te dane znajdują się w `PT=Time` folderze:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druga kopia z podziałem na partycje jest pogrupowana według identyfikatorów szeregów czasowych i znajduje się w `PT=TsId` folderze:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Sygnatura czasowa w nazwach obiektów BLOB w `PT=Time` folderze odpowiada czasowi przybycia danych do Azure Time Series Insights Gen2, a nie sygnatury czasowej zdarzeń.

Dane w `PT=TsId` folderze zostaną zoptymalizowane pod kątem zapytania w czasie i nie są statyczne. Podczas ponownego partycjonowania niektóre zdarzenia mogą być obecne w wielu obiektach Blob. Nazwy obiektów BLOB w tym folderze nie mogą pozostać takie same.

Ogólnie rzecz biorąc, jeśli musisz uzyskać dostęp do danych bezpośrednio za pośrednictwem plików Parquet, użyj tego `PT=Time` folderu.  W przyszłości będzie możliwe wydajne dostęp do `PT=TsId` folderu.

> [!NOTE]
>
> * `<YYYY>` mapuje do czwartej reprezentacji rocznej.
> * `<MM>` mapuje na dwucyfrowe reprezentację miesiąca.
> * `<YYYYMMDDHHMMSSfff>` mapuje do sygnatury czasowej z czterocyfrowym rokiem ( `YYYY` ), dwucyfrowym miesiącem ( `MM` ), dwucyfrowym dniem ( `DD` ), dwucyfrowym godzinem ( `HH` ), dwucyfrowym ( `MM` ), dwucyfrowym ( `SS` ) i 3-cyfrowym milisekundy ( `fff` ).

Azure Time Series Insights zdarzenia Gen2 są mapowane do zawartości pliku Parquet w następujący sposób:

* Każde zdarzenie jest mapowane na jeden wiersz.
* Każdy wiersz zawiera kolumnę **timestamp** z sygnaturą czasową zdarzenia. Właściwość sygnatury czasowej nigdy nie ma wartości null. Wartość domyślna zdarzenia jest umieszczana w **kolejce czasu** , jeśli właściwość sygnatura czasowa nie została określona w źródle zdarzenia. Sygnatura czasowa przechowywana jest zawsze w formacie UTC.
* Każdy wiersz zawiera kolumny identyfikatora szeregów czasowych (TSID), zgodnie z definicją podczas tworzenia środowiska Azure Time Series Insights Gen2. Nazwa właściwości identyfikatora TSID zawiera `_string` sufiks.
* Wszystkie inne właściwości wysyłane jako dane telemetryczne są mapowane na nazwy kolumn kończące się znakiem `_bool` (Boolean), `_datetime` (Sygnatura czasowa), `_long` (Long), `_double` (Double), `_string` (ciąg) lub `dynamic` (dynamiczny), w zależności od typu właściwości.  Aby uzyskać więcej informacji, Przeczytaj o [obsługiwanych typach danych](./concepts-supported-data-types.md).
* Ten schemat mapowania dotyczy pierwszej wersji formatu pliku, do którego odwołuje się wartość **V = 1** i jest przechowywana w folderze podstawowym o tej samej nazwie. W miarę rozwoju tej funkcji ten schemat mapowania może ulec zmianie, a Nazwa odwołania jest zwiększana.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [modelowaniu danych](./concepts-model-overview.md).

* Zaplanuj [środowisko Azure Time Series Insights Gen2](./how-to-plan-your-environment.md).
