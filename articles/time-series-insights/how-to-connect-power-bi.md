---
title: Podłącz środowisko do Power BI-Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak połączyć Azure Time Series Insights, aby Power BI do udostępniania, tworzenia wykresów i wyświetlania danych w całej organizacji.
author: deepakpalled
ms.author: dpalled
manager: diviso
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 105faf796583eb0f272eea7a316648993fdafd0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653778"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Wizualizuj dane z Azure Time Series Insights w Power BI

Azure Time Series Insights to platforma do przechowywania i wizualizacji danych szeregów czasowych w chmurze oraz zarządzania nimi. [Power BI](https://powerbi.microsoft.com) to narzędzie do analizy biznesowej z rozbudowanymi możliwościami wizualizacji, które umożliwiają udostępnianie szczegółowych informacji i wyników w całej organizacji. Obie usługi mogą być teraz zintegrowane w celu uzyskania najlepszych możliwości wizualizacji w Azure Time Series Insights ", jak i Power BI.

Omawiane tematy:

* Łączenie Azure Time Series Insights Power BI przy użyciu łącznika Cloud
* Twórz wizualizacje z danymi w Power BI
* Opublikuj raport w celu Power BI i udostępnienia w pozostałej części organizacji

Na koniec dowiesz się, jak wizualizować dane szeregów czasowych za pomocą Azure Time Series Insights i ulepszać je za pomocą silnych wizualizacji danych i możliwości łatwego udostępniania Power BI.

Pamiętaj o zarejestrowaniu się w celu uzyskania [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz i zainstaluj najnowszą wersję programu [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Posiadanie lub tworzenie [środowiska Azure Time Series Insights Gen2](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
>
> * Łącznik jest obecnie obsługiwany w środowiskach Azure Time Series Insights Gen2 skonfigurowanych **tylko w sklepie ciepłym**.
> * Jeśli masz dostęp gościa do Azure Time Series Insights środowiska Gen2 z innej dzierżawy usługi Azure AD, nie będziesz w stanie uzyskać dostępu do łącznika. Przeczytaj o [zasadach dostępu do środowiska](./concepts-access-policies.md).

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Połącz dane z Azure Time Series Insights Power BI

Aby połączyć środowisko Azure Time Series Insights z Power BI, wykonaj następujące kroki:

1. Otwórz Eksploratora Azure Time Series Insights
1. Eksportowanie danych jako zapytania lub danych pierwotnych
1. Otwieranie programu Power BI Desktop
1. Ładowanie z kwerendy niestandardowej

### <a name="export-data-into-power-bi-desktop"></a>Eksportowanie danych do Power BI Desktop

Aby rozpocząć:

1. Otwórz Eksploratora Azure Time Series Insights i zanadzoruj swoje dane.
1. Po utworzeniu widoku, którego jesteś zadowolony, przejdź do menu rozwijanego **więcej akcji** i wybierz polecenie **Połącz z Power BI**.

    [![Eksport Azure Time Series Insights Explorer](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Ustaw parametry na tej karcie:

   1. Określ względną przedział czasu do wyświetlenia. Jeśli jesteś zadowolony z istniejącego widoku, pozostaw ten okres jako **istniejący przedział czasu**.

   1. Wybierz między **zagregowanymi** a **nieprzetworzonymi zdarzeniami**.

       > [!NOTE]
       > Dane można zawsze agregować w dalszej części Power BI, ale nie będzie można przywrócić danych pierwotnych po agregacji.

       > [!NOTE]
       > Istnieje limit liczby zdarzeń 250 000 dla danych na poziomie nieprzetworzonych zdarzeń.

       [![Łączone](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Jeśli środowisko Azure Time Series Insights nie zostało skonfigurowane z **magazynem ciepłym**, zostanie wyświetlone ostrzeżenie.

       [![Ostrzeżenie dotyczące magazynu ciepłego](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Istniejące wystąpienie dla **magazynu ciepłego** można skonfigurować w Azure Portal.

1. Wybierz pozycję **Kopiuj zapytanie do schowka**.
1. Teraz uruchom Power BI Desktop.
1. W Power BI Desktop na karcie **Narzędzia główne** wybierz pozycję **Pobierz dane** w lewym górnym rogu, a następnie kliknij pozycję **więcej**.

    [![Lista rozwijana Narzędzia główne](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Wyszukaj **Azure Time Series Insights**, wybierz pozycję **Azure Time Series Insights (beta)**, a następnie **Połącz**.

    [![Połącz Power BI z Azure Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Alternatywnie przejdź do karty **Azure** , wybierz pozycję **Azure Time Series Insights (beta)**, a następnie **Połącz się**.

1. Zostanie wyświetlone okno dialogowe komunikatu z pytaniem o uprawnienia do łączenia się z zasobami innych firm. Wybierz pozycję **Kontynuuj**.

    [![Wybierz pozycję Utwórz zapytanie niestandardowe](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. W menu rozwijanym w obszarze **Źródło danych**wybierz polecenie **Utwórz zapytanie niestandardowe**. Wklej ze schowka do opcjonalnego pola **zapytania niestandardowego (opcjonalnie)** , a następnie naciśnij przycisk **OK**.

    [![Przekaż zapytanie niestandardowe i wybierz pozycję OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Tabela danych zostanie teraz załadowana. Naciśnij pozycję **Załaduj** , aby załadować do Power BI.

    [![Przejrzyj dane załadowane w tabeli i wybierz pozycję Załaduj.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Jeśli wykonano te kroki, przejdź do następnej sekcji.

## <a name="create-a-report-with-visuals"></a>Tworzenie raportu z wizualizacjami

Teraz, gdy dane zostały zaimportowane do Power BI, można utworzyć raport z wizualizacjami.

1. Upewnij się, że wybrano widok **raport** po lewej stronie okna.

    [![Zrzut ekranu przedstawia ikonę widok raportu.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. W kolumnie **wizualizacje** wybierz swoją wizualizację. Na przykład wybierz pozycję **Wykres liniowy**. Spowoduje to dodanie pustego wykresu liniowego do kanwy.

1. Na liście **pola** wybierz pozycję **_Timestamp** i przeciągnij ją do pola **oś** , aby wyświetlić elementy wzdłuż osi X. Upewnij się, że **_Timestamp** jako wartość **osi** (domyślnie **Hierarchia dat**).

    [![Zrzut ekranu przedstawia menu sygnatur czasowych z wybranym _Timestamp.](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1. Na liście **pola** wybierz pozycję **TimeSeriesId** i przeciągnij ją do pola **wartości** , aby wyświetlić elementy wzdłuż osi Y.

    [![Tworzenie wykresu liniowego](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Aby dodać kolejny wykres do kanwy, zaznacz dowolne miejsce na kanwie poza wykresem liniowym i powtórz ten proces.

    [![Utwórz dodatkowe wykresy do udostępnienia](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Po utworzeniu raportu można go opublikować w usłudze Power BI Reporting Services.

## <a name="advanced-editing"></a>Edycja zaawansowana

Jeśli zestaw danych został już załadowany w Power BI ale chcesz zmodyfikować zapytanie (takie jak parametry daty/godziny lub identyfikatora środowiska), możesz to zrobić za pomocą funkcji Edytor zaawansowany Power BI. Zapoznaj się z [dokumentacją Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) , aby dowiedzieć się więcej.

Przegląd:

1. W Power BI Desktop wybierz pozycję **Edytuj zapytania**.
1. Naciśnij **Edytor zaawansowany**.

    [![Edytuj zapytania w Edytor zaawansowany](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Zmodyfikuj ładunek JSON zgodnie z potrzebami.
1. Wybierz pozycję **gotowe** , a następnie **Zamknij & Zastosuj** w **oknie edytora Power Query**.

Interfejs będzie teraz odzwierciedlać wymagane zmiany, które zostały zastosowane.  

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z tematem [Power BI pojęć dotyczących łącznika](https://docs.microsoft.com/power-bi/desktop-query-overview) Azure Time Series Insights.

* Dowiedz się więcej o [Power BI pulpicie](https://docs.microsoft.com/power-bi/desktop-query-overview).
