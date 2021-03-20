---
title: Podłącz środowisko do Power BI-Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak połączyć Azure Time Series Insights, aby Power BI do udostępniania, tworzenia wykresów i wyświetlania danych w całej organizacji.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374171"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Wizualizuj dane z Azure Time Series Insights w Power BI

Azure Time Series Insights to platforma do przechowywania i wizualizacji danych szeregów czasowych w chmurze oraz zarządzania nimi. [Power BI](https://powerbi.microsoft.com) to narzędzie do analizy biznesowej z rozbudowanymi możliwościami wizualizacji, które umożliwiają udostępnianie szczegółowych informacji i wyników w całej organizacji. Obie usługi mogą teraz zostać zintegrowane, co pozwala rozszerzyć zaawansowaną analizę Azure Time Series Insights przy użyciu mocnej wizualizacji danych i możliwości łatwego udostępniania Power BI.

Omawiane tematy:

* Łączenie Azure Time Series Insights Power BI za pomocą natywnego łącznika Azure Time Series Insights
* Twórz wizualizacje z danymi szeregów czasowych w Power BI
* Opublikuj raport w celu Power BI i udostępnienia w pozostałej części organizacji


## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.
* Pobierz i zainstaluj najnowszą wersję programu [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Posiadanie lub tworzenie [środowiska Azure Time Series Insights Gen2](./how-to-provision-manage.md)

Sprawdź [zasady dostępu do środowiska](./concepts-access-policies.md) i upewnij się, że masz bezpośredni dostęp lub dostęp gościa do środowiska Azure Time Series Insights Gen2. 

> [!IMPORTANT]
> * Pobierz i zainstaluj najnowszą wersję programu [Power BI Desktop](https://powerbi.microsoft.com/downloads/). Aby wykonać kroki opisane w tym artykule, upewnij się, że masz zainstalowaną wersję Power BI Desktop z grudnia 2020 (2.88.321.0). 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>Eksportowanie danych z Azure Time Series Insights do Power BI Desktop

Aby rozpocząć:

1. Otwórz Eksploratora Azure Time Series Insights Gen2 i zanadzoruj swoje dane. To są dane, które zostaną wyeksportowane do Power BI.
1. Po utworzeniu widoku, którego jesteś zadowolony, przejdź do menu rozwijanego **więcej akcji** i wybierz polecenie **Połącz z Power BI**.

    [![Azure Time Series Insights eksportu Eksploratora Gen2](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Ustaw parametry do eksportu:

   * **Format danych**: Wybierz, czy chcesz wyeksportować **dane zagregowane** lub **nieprzetworzone zdarzenia** do Power BI. 

       > [!NOTE]
       > W przypadku eksportowania zdarzeń pierwotnych można agregować te dane później w Power BI. Jednak w przypadku eksportowania zagregowanych danych nie można przywrócić danych pierwotnych w Power BI. Istnieje limit liczby zdarzeń 250 000 dla danych na poziomie nieprzetworzonych zdarzeń.

   * **Zakres czasu**: Wybierz, czy chcesz zobaczyć **stały** zakres czasu, czy też **najnowsze** dane w Power BI. Wybranie ustalonego zakresu czasu oznacza, że dane z przedziału wyszukiwania zostaną wyeksportowane do Power BI. Wybranie najnowszego zakresu czasu oznacza, że Power BI będą obejmować najnowsze dane dla wybranej ilości wyszukiwania (np. w przypadku grafu 1 godziny danych i wybrania ustawienia "Najnowsze" łącznik Power BI będzie zawsze wykonywać zapytania dla najnowszej ilości danych).
  
   * **Typ magazynu**: Wybierz, czy chcesz uruchomić wybrane zapytanie w **sklepie ciepłym** czy w **chłodnym magazynie**. Jeśli wybrano zakres, który rozciąga się zarówno na chłodną, jak i ciepłą, zapytanie zostanie domyślnie rozesłane do magazynu zimnego, ponieważ sklep ciepły będzie zawierać tylko najnowsze dane. Ręczne zmienianie parametru StoreType jest dozwolone, ale nie jest to zalecane w przypadku najlepszego środowiska. 

    > [!TIP] 
    > Program Azure Time Series Insights Explorer automatycznie wybierze zalecane parametry w zależności od zakresu wyszukiwania i widoku danych wybranych do wyeksportowania. 

1. Po skonfigurowaniu ustawień wybierz pozycję **Kopiuj zapytanie do schowka**.

    [![Modalne eksportowanie Azure Time Series Insights Explorer](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. Uruchom program Power BI Desktop.
   
1. W Power BI Desktop na karcie **Narzędzia główne** wybierz pozycję **Pobierz dane** w lewym górnym rogu, a następnie kliknij pozycję **więcej**.

    [![Pobieranie danych w usłudze Power BI](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. Wyszukaj **Azure Time Series Insights**, wybierz pozycję **Azure Time Series Insights (beta)**, a następnie **Połącz**.

    [![Połącz Power BI z Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    Alternatywnie przejdź do karty **Azure** , wybierz pozycję **Azure Time Series Insights (beta)**, a następnie **Połącz się**.

1. Wklej zapytanie skopiowane z programu Azure Time Series Insights Explorer do **niestandardowego pola zapytania** , a następnie naciśnij przycisk **OK**.

    [![Wklej w zapytaniu niestandardowym i wybierz pozycję OK.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  Tabela danych zostanie teraz załadowana. Naciśnij pozycję **Załaduj** , aby załadować do Power BI. Jeśli chcesz wykonać przekształcenia danych, możesz to zrobić teraz przez kliknięcie przycisku **Przekształć dane**. Możesz również przekształcić dane po ich załadowaniu.

    [![Przejrzyj dane w tabeli i wybierz pozycję Załaduj.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Tworzenie raportu z wizualizacjami

Teraz, gdy dane zostały zaimportowane do Power BI, można utworzyć raport z wizualizacjami.

1. Upewnij się, że wybrano widok **raport** po lewej stronie okna.

    [![Zrzut ekranu przedstawia ikonę widok raportu.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. W kolumnie **wizualizacje** wybierz swoją wizualizację. Na przykład wybierz pozycję **Wykres liniowy**. Spowoduje to dodanie pustego wykresu liniowego do kanwy.

1.  Na liście **pola** wybierz pozycję **_Timestamp** i przeciągnij ją do pola **oś** , aby wyświetlić czas wzdłuż osi X. Upewnij się, że **_Timestamp** jako wartość **osi** (domyślnie **Hierarchia dat**).

    [![Wybierz _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Na liście **pola** wybierz zmienną, która ma zostać wykreślona, i przeciągnij ją do pola **wartości** , aby wyświetlić wartości wzdłuż osi Y. Wybierz wartość identyfikatora szeregów czasowych i przeciągnij ją do pola **Legenda** , aby utworzyć wiele linii na wykresie, jeden na identyfikator szeregów czasowych. Spowoduje to wyświetlenie widoku podobnego do przedstawionego w Eksploratorze Azure Time Series Insights. 

    [![Tworzenie podstawowego wykresu liniowego](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Aby dodać kolejny wykres do kanwy, zaznacz dowolne miejsce na kanwie poza wykresem liniowym i powtórz ten proces.

    [![Utwórz dodatkowe wykresy do udostępnienia](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Po utworzeniu raportu można go opublikować, aby Power BI usługi Reporting Services i udostępniać je innym osobom w organizacji.

## <a name="advanced-editing"></a>Edycja zaawansowana
Jeśli zestaw danych został już załadowany w Power BI ale chcesz zmodyfikować zapytanie (takie jak parametry daty/godziny lub identyfikatora środowiska), możesz to zrobić za pomocą funkcji Edytor zaawansowany Power BI. Zapoznaj się z [dokumentacją Power BI](/power-bi/desktop-query-overview) , aby dowiedzieć się więcej na temat wprowadzania zmian przy użyciu **edytora Power Query**. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Power BI pulpicie](/power-bi/desktop-query-overview).

* Dowiedz się więcej o wysyłaniu [zapytań do danych](concepts-query-overview.md) w Azure Time Series Insights Gen2.