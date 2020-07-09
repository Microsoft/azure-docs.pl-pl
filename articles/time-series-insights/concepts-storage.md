---
title: Omówienie usługi Storage — Azure Time Series Insights | Microsoft Docs
description: Informacje na temat przechowywania danych w Azure Time Series Insights.
author: esung22
ms.author: elsung
manager: diegoviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: fc6b6b42293b4f2028f1a12af950e96e28febc87
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085569"
---
# <a name="data-storage"></a>Magazyn danych

W przypadku tworzenia środowiska jednostki SKU Time Series Insights z *opcją płatność zgodnie z rzeczywistym* użyciem (PAYG) można utworzyć dwa zasoby platformy Azure:

* Środowisko Azure Time Series Insights, które można skonfigurować do przechowywania w pamięci podgrzanej.
* Konto usługi Azure Storage na potrzeby magazynu zimnych danych.

Dane w magazynie ciepłym są dostępne tylko za pośrednictwem [zapytania szeregów czasowych](./time-series-insights-update-tsq.md) i [Eksploratora Azure Time Series Insights](./time-series-insights-update-explorer.md). Sklep ciepły będzie zawierał ostatnie dane w [okresie przechowywania](./time-series-insights-update-plan.md#the-preview-environment) wybranym podczas tworzenia środowiska Time Series Insights.

Azure Time Series Insights zapisuje dane zimnego magazynu w usłudze Azure Blob Storage w [formacie pliku Parquet](#parquet-file-format-and-folder-structure). Time Series Insights zarządza wyłącznie tym zimnym magazynem danych, ale jest dostępny do odczytu bezpośrednio jako standardowe pliki Parquet.

> [!WARNING]
> Jako właściciel konta usługi Azure Blob Storage, na którym znajdują się dane w chłodnym sklepie, masz pełny dostęp do wszystkich danych na koncie. Ten dostęp obejmuje uprawnienia do zapisu i usuwania. Nie należy edytować ani usuwać danych Azure Time Series Insights zapisów, ponieważ może to spowodować utratę danych.

## <a name="data-availability"></a>Dostępność danych

Azure Time Series Insights partycje i indeksowanie danych w celu uzyskania optymalnej wydajności zapytań. Dane staną się dostępne do wykonywania zapytań z obu ciepłej (jeśli są włączone) i magazynu zimnego po jego indeksowaniu. Ilość danych, które są pozyskiwane, może mieć wpływ na tę dostępność.

> [!IMPORTANT]
> Zanim dane staną się dostępne, może wystąpić okres do 60 sekund. Jeśli wystąpi znaczący czas oczekiwania przekraczający 60 sekund, Prześlij bilet pomocy technicznej za pomocą Azure Portal.

## <a name="azure-storage"></a>Azure Storage

W tej sekcji opisano szczegóły usługi Azure Storage istotne dla Azure Time Series Insights.

Aby uzyskać dokładny opis magazynu obiektów blob platformy Azure, Przeczytaj [wprowadzenie do magazynu obiektów BLOB](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Twoje konto magazynu

W przypadku tworzenia środowiska Azure Time Series Insights PAYG konto usługi Azure Storage zostanie utworzone jako długoterminowy chłodny magazyn.  

Azure Time Series Insights zachowuje maksymalnie dwie kopie każdego zdarzenia na koncie usługi Azure Storage. Jedna kopia przechowuje zdarzenia uporządkowane według czasu pozyskiwania, które zawsze umożliwiają dostęp do zdarzeń w kolejności uporządkowanej według czasu. W miarę upływu czasu program Time Series Insights tworzy również kopię ponownie partycjonowaną danych, która zostanie zoptymalizowana pod kątem wykonywania Time Series Insights kwerendy.

Wszystkie dane są przechowywane na koncie usługi Azure Storage na czas nieokreślony.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Pisanie i edytowanie Time Series Insights obiektów BLOB

Aby zapewnić wydajność zapytań i dostępność danych, nie należy edytować ani usuwać obiektów BLOB tworzonych Time Series Insights.

#### <a name="accessing-time-series-insights-cold-store-data"></a>Uzyskiwanie dostępu do danych Time Series Insights zimnego magazynu

Oprócz uzyskiwania dostępu do danych z poziomu zapytania [eksploratora Time Series Insights](./time-series-insights-update-explorer.md) i [szeregów czasowych](./time-series-insights-update-tsq.md)możesz również uzyskać dostęp do danych bezpośrednio z plików Parquet przechowywanych w chłodnym magazynie. Na przykład można odczytywać, przekształcać i czyścić dane w notesie Jupyter, a następnie używać go do uczenia modelu Azure Machine Learning w tym samym przepływie pracy platformy Spark.

Aby uzyskać dostęp do danych bezpośrednio z konta usługi Azure Storage, musisz mieć dostęp do odczytu do konta używanego do przechowywania danych w wersji zapoznawczej Time Series Insights. Następnie można odczytać wybrane dane na podstawie czasu utworzenia pliku Parquet znajdującego się w `PT=Time` folderze opisanym poniżej w sekcji [Format pliku Parquet](#parquet-file-format-and-folder-structure) .  Aby uzyskać więcej informacji na temat włączania dostępu do odczytu do konta magazynu, zobacz [Zarządzanie dostępem do zasobów konta magazynu](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Usuwanie danych

Nie usuwaj plików Azure Time Series Insights. Zarządzaj danymi powiązanymi tylko z poziomu Azure Time Series Insights.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet format pliku i struktura folderów

Parquet to format pliku kolumnowego Open Source zaprojektowany w celu zapewnienia wydajnego magazynu i wydajności. Azure Time Series Insights używa Parquet do włączania wydajności zapytań opartych na IDENTYFIKATORach szeregów czasowych na dużą skalę.  

Aby uzyskać więcej informacji na temat typu pliku Parquet, Przeczytaj [dokumentację dotyczącą Parquet](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights przechowuje kopie danych w następujący sposób:

* Pierwsza kopia początkowa jest podzielona na partycje według czasu pozyskiwania i przechowuje dane w sposób przybliżony w kolejności przybycia. Te dane znajdują się w `PT=Time` folderze:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druga kopia z podziałem na partycje jest pogrupowana według identyfikatorów szeregów czasowych i znajduje się w `PT=TsId` folderze:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Sygnatura czasowa w nazwach obiektów BLOB w `PT=Time` folderze odpowiada czasowi przybycia danych do Azure Time Series Insights, a nie sygnatury czasowej zdarzeń.

Dane w `PT=TsId` folderze zostaną zoptymalizowane pod kątem zapytania w czasie i nie są statyczne. Podczas ponownego partycjonowania niektóre zdarzenia mogą być obecne w wielu obiektach Blob. Nazwy obiektów BLOB w tym folderze nie mogą pozostać takie same. 

Ogólnie rzecz biorąc, jeśli musisz uzyskać dostęp do danych bezpośrednio za pośrednictwem plików Parquet, użyj tego `PT=Time` folderu.  W przyszłości będzie możliwe wydajne dostęp do `PT=TsId` folderu. 

> [!NOTE]
>
> * `<YYYY>`mapuje do czwartej reprezentacji rocznej.
> * `<MM>`mapuje na dwucyfrowe reprezentację miesiąca.
> * `<YYYYMMDDHHMMSSfff>`mapuje do sygnatury czasowej z czterocyfrowym rokiem ( `YYYY` ), dwucyfrowym miesiącem ( `MM` ), dwucyfrowym dniem ( `DD` ), dwucyfrowym godzinem ( `HH` ), dwucyfrowym ( `MM` ), dwucyfrowym ( `SS` ) i 3-cyfrowym milisekundy ( `fff` ).

Zdarzenia w wersji zapoznawczej Time Series Insights są mapowane do zawartości pliku Parquet w następujący sposób:

* Każde zdarzenie jest mapowane na jeden wiersz.
* Każdy wiersz zawiera kolumnę **timestamp** z sygnaturą czasową zdarzenia. Właściwość sygnatury czasowej nigdy nie ma wartości null. Wartość domyślna zdarzenia jest umieszczana w **kolejce czasu** , jeśli właściwość sygnatura czasowa nie została określona w źródle zdarzenia. Sygnatura czasowa przechowywana jest zawsze w formacie UTC.
* Każdy wiersz zawiera kolumny identyfikatora szeregów czasowych (TSID), zgodnie z definicją podczas tworzenia środowiska Time Series Insights. Nazwa właściwości identyfikatora TSID zawiera `_string` sufiks.
* Wszystkie inne właściwości wysyłane jako dane telemetryczne są mapowane na nazwy kolumn kończące się znakiem `_bool` (Boolean), `_datetime` (Sygnatura czasowa), `_long` (Long), `_double` (Double), `_string` (ciąg) lub `dynamic` (dynamiczny), w zależności od typu właściwości.  Aby uzyskać więcej informacji, Przeczytaj o [obsługiwanych typach danych](./concepts-supported-data-types.md).
* Ten schemat mapowania dotyczy pierwszej wersji formatu pliku, do którego odwołuje się wartość **V = 1** i jest przechowywana w folderze podstawowym o tej samej nazwie. W miarę rozwoju tej funkcji ten schemat mapowania może ulec zmianie, a Nazwa odwołania jest zwiększana.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [modelowaniu danych](./time-series-insights-update-tsm.md).

* Zaplanuj [środowisko Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-plan.md).
