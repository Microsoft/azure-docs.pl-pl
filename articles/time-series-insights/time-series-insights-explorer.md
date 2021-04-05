---
title: Eksplorowanie danych przy użyciu Eksploratora Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak wyświetlać dane IoT za pomocą Eksploratora Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 639f2ef12d190a56e04a9b48d96ea0a6537d243c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020116"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights Explorer Gen1

> [!CAUTION]
> To jest artykuł Gen1.

W tym artykule opisano funkcje i opcje dla [aplikacji sieci Web](https://insights.timeseries.azure.com/)programu Azure Time Series Insights Gen1 Explorer. W Eksploratorze Azure Time Series Insights przedstawiono zaawansowane możliwości wizualizacji danych zapewniane przez usługę i można uzyskać do nich dostęp w ramach własnego środowiska.

Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją, dzięki której z łatwością można badać i analizować miliardy zdarzeń IoT jednocześnie. Udostępnia on globalny widok danych, który umożliwia szybkie Weryfikowanie rozwiązania IoT i uniknięcie kosztownych przestojów w przypadku urządzeń o krytycznym znaczeniu. Można odkrywać ukryte trendy, wykrycia i przeprowadzać analizę głównych przyczyn niemal w czasie rzeczywistym.

> [!TIP]
> Aby zapoznać się z przewodnikiem w środowisku demonstracyjnym, Przeczytaj [Azure Time Series Insights przewodniku szybki start](time-series-quickstart.md).

## <a name="video"></a>Wideo

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Dowiedz się więcej o wysyłaniu zapytań do danych za pomocą Eksploratora Azure Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Odtwórz poprzednie wideo <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"wprowadzenie do Azure Time Series Insights przy użyciu akceleratora rozwiązań usługi Azure IoT".</a>

## <a name="prerequisites"></a>Wymagania wstępne

Aby móc korzystać z Eksploratora Azure Time Series Insights, musisz:

- Utwórz środowisko Azure Time Series Insights. Aby uzyskać więcej informacji, przeczytaj artykuł [jak rozpocząć pracę z usługą Azure Time Series Insights](./time-series-insights-get-started.md).
- [Zapewnianie dostępu](./concepts-access-policies.md) do Twojego konta w środowisku.
- Dodaj do niego źródło zdarzeń [Centrum IoT](./how-to-ingest-data-iot-hub.md) lub [centrum zdarzeń](./how-to-ingest-data-event-hub.md) .

## <a name="explore-and-query-data"></a>Eksplorowanie i wykonywanie zapytań dotyczących danych

W ciągu kilku minut od podłączenia źródła zdarzeń do środowiska Azure Time Series Insights można eksplorować dane szeregów czasowych i wysyłać do nich zapytania.

1. Aby rozpocząć, Otwórz [eksploratora Azure Time Series Insights](https://insights.timeseries.azure.com/) w przeglądarce internetowej. Po lewej stronie okna wybierz środowisko. Wszystkie środowiska, do których masz dostęp, są wyświetlane w kolejności alfabetycznej.

1. Po wybraniu środowiska Użyj opcji **od** i **do** w górnej części lub wybierz i przeciągnij kursor w żądanym przedziale czasu. Wybierz lupę w prawym górnym rogu lub kliknij prawym przyciskiem myszy wybrany przedział czasu i wybierz pozycję **Wyszukaj**.

1. Dostępność będzie również automatycznie odświeżana co minutę przez wybranie przycisku **automatycznie włączone** . Przycisk **Włącz** opcję dotyczy tylko wykresu dostępności, a nie zawartości głównej wizualizacji.

1. Ikona chmury platformy Azure przenosi do środowiska w Azure Portal.

   [![Wybór środowiska Azure Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Następnie zostanie wyświetlony wykres, który pokazuje liczbę wszystkich zdarzeń podczas wybranego przedziału czasu. Oto kilka kontrolek:

    - **Panel edytorów terminów**: termin jest miejscem, w którym można wykonywać zapytania dotyczące środowiska. Znajduje się po lewej stronie ekranu:
      - **Miara**: Ta lista rozwijana zawiera wszystkie kolumny liczbowe (**podwaja**).
      - **Podział według**: Ta lista rozwijana zawiera kolumny kategorii (**ciągi**).
      - Możesz włączyć interpolację krokową, pokazać wartość minimalną i maksymalną, a następnie dostosować oś y w panelu sterowania obok pozycji **pomiar**. Można również określić, czy pokazywane dane są liczbami, średnimi czy sumą danych.
      - Można dodać maksymalnie pięć wyrazów, aby wyświetlić je na tej samej osi x. Wybierz pozycję **Dodaj** , aby dodać nowy termin, lub użyj przycisku **Klonuj ten termin** , aby dodać kopię istniejącego terminu.

        [![Wybór warunków, filtrowanie i panel zapytania](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predykat**: Użyj predykatu, aby szybko filtrować zdarzenia przy użyciu zestawu argumentów operacji wymienionych w poniższej tabeli. W przypadku przeprowadzania wyszukiwania przez wybranie lub kliknięcie, predykat jest automatycznie aktualizowany na podstawie tego wyszukiwania. Obsługiwane typy operandów to:

         |Operacja  |Obsługiwane typy  |Uwagi  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**, **<>**     | **Ciąg**, **bool**, **Double**, **DateTime**, **TimeSpan**, **null**        |         |
         |**PODCZAS**     | **Ciąg**, **bool**, **Double**, **DateTime**, **TimeSpan**, **null**        |  Wszystkie operandy powinny być tego samego typu lub być stałe o **wartości null** .        |
         |**MA**     | **Ciąg**        |  Tylko stałe literały ciągu są dozwolone po prawej stronie. Pusty ciąg i **wartość null** są niedozwolone.       |

      - **Przykładowe zapytania**

         [![Przykładowe zapytania Gen1](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Za pomocą suwaka **rozmiar interwału** można powiększać i pomniejszać interwały dla tego samego przedziału czasu. Suwak zapewnia dokładniejszą kontrolę nad przenoszeniem między dużymi wycinkami czasu, które pokazują płynne trendy w dół do wycinków jako milisekundy, co pozwala na wyświetlanie i analizowanie szczegółowych danych o wysokiej rozdzielczości. Domyślny punkt początkowy suwaka jest ustawiany jako najbardziej optymalny widok danych z wyboru w celu zrównoważenia rozdzielczości, szybkości zapytania i stopnia szczegółowości.

1. Narzędzie **pędzel czasu** ułatwia nawigowanie z jednego przedziału czasowego do innego.

1. Wybierz ikonę **Zapisz** , aby zapisać bieżące zapytanie i udostępnić je innym użytkownikom środowiska. Po wybraniu ikony **Otwórz** można przejrzeć wszystkie zapisane zapytania i wszystkie zapytania udostępnione innym użytkownikom w środowiskach, do których masz dostęp.

   [![Zapytania](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Wizualizowanie danych

1. Użyj narzędzia **widok perspektywy** , aby zobaczyć równoczesny widok maksymalnie czterech unikatowych zapytań. Przycisk **widok perspektywy** znajduje się w prawym górnym rogu wykresu.

   [![Wybierz zapytania, które mają zostać dodane do okienka perspektywy](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Wyświetl wykres, aby wizualnie eksplorować dane, i użyj narzędzi **wykresu** :

    - **Zaznacz** lub **kliknij** określony przedział czasu lub jedną serię danych.
    - W ramach zaznaczenia TimeSpan możesz powiększać lub eksplorować zdarzenia.
    - W ramach serii danych można podzielić Serie według innej kolumny, dodać serię jako nowy termin, wyświetlić tylko wybraną serię, wykluczyć wybraną serię, wysłać polecenie ping do tej serii lub eksplorować zdarzenia z wybranej serii.
    - W obszarze filtru z lewej strony wykresu można przejrzeć wszystkie wyświetlane serie danych i zmienić kolejność według wartości lub nazwy. Można również wyświetlić wszystkie serie danych lub przypięte lub odpięte serie. Można wybrać jedną serię danych i podzielić serię według innej kolumny, dodać serię jako nowy termin, wyświetlić tylko wybraną serię, wykluczyć wybraną serię, przypiąć tę serię lub eksplorować zdarzenia z wybranej serii.
    - Gdy przeglądasz wiele warunków jednocześnie, możesz układać, rozstosować, przeglądać dodatkowe dane dotyczące serii danych i używać tej samej osi y dla wszystkich warunków. Użyj przycisków w prawym górnym rogu wykresu.

    [![Ustawienia opcji górnego prawego rogu narzędzia wykresu](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Użyj **mapę cieplną** , aby szybko wyszukiwać unikatowe lub nietypowe serie danych w danym zapytaniu. Tylko jeden termin wyszukiwania może być wizualny jako mapę cieplną.

    [Wykresy programu Time Series Insights Explorer mapę cieplną](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)] (Media/Time-Series-Insights — Eksplorator/tsi-ga-example-heatmap-charting.png#lightbox)

1. Gdy eksplorujesz zdarzenia, zaznaczając lub klikając prawym przyciskiem myszy, panel **zdarzenia** jest dostępny. W tym miejscu możesz przejrzeć wszystkie zdarzenia pierwotne i wyeksportować zdarzenia jako pliki JSON lub CSV. Azure Time Series Insights przechowuje wszystkie nieprzetworzone dane.

    [![Zdarzenia](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Wybierz kartę **statystyki** po eksplorowaniu zdarzeń w celu uwidocznienia wzorców i statystyk kolumn.

    - **Wzorce**: Ta funkcja aktywnie przydzieli najbardziej statystycznie znaczące wzorce w wybranym regionie danych. Nie trzeba przeglądać tysięcy zdarzeń, aby zrozumieć, jakie wzorce wymagają najwięcej czasu i energii. Za pomocą Azure Time Series Insights możesz przejść bezpośrednio do tych statystycznie znaczących wzorców, aby kontynuować przeprowadzanie analizy. Ta funkcja jest również przydatna w przypadku badań pośmiertnych w danych historycznych.
    - **Statystyki kolumn**: statystyki kolumn zawierają wykresy i tabele dzielące dane z każdej kolumny wybranej serii danych na wybrany przedział czasu.

      [![Wykresy i opcje z kolumnami STATYSTYCZNymi](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Teraz wiesz o najważniejszych funkcjach, ustawieniach konfiguracji i opcjach wyświetlania, które są dostępne w aplikacji sieci Web programu Azure Time Series Insights Explorer.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak diagnozować i rozwiązywać problemy](time-series-insights-diagnose-and-solve-problems.md) w środowisku Azure Time Series Insights.

- Zapoznaj się z przewodnikiem [szybki start Azure Time Series Insights](time-series-quickstart.md) .