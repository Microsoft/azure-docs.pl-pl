---
title: Przypadki użycia Gen2 — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej na temat Azure Time Series Insights przypadków użycia Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b5b6233c0978f15aad94b7f66f8935c2d6f6f1c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666963"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure Time Series Insights przypadki użycia Gen2

W tym artykule opisano kilka typowych przypadków użycia Azure Time Series Insights Gen2. Zalecenia przedstawione w tym artykule stanowią punkt wyjścia do opracowywania aplikacji i rozwiązań w Azure Time Series Insights Gen2.

W tym artykule znajdują się odpowiedzi na następujące pytania:

* Jakie są typowe przypadki użycia Azure Time Series Insights Gen2?
* Jakie korzyści wynikają z używania Azure Time Series Insights Gen2 do [eksploracji danych i wykrywania anomalii w wizualizacji](#data-exploration-and-visual-anomaly-detection)?
* Jakie korzyści wynikają z używania Azure Time Series Insights Gen2 do [analizy operacyjnej i wydajności procesów](#operational-analysis-and-driving-process-efficiency)?
* Jakie korzyści wynikają z używania Azure Time Series Insights Gen2 do [zaawansowanej analizy](#advanced-analytics)?

Przegląd tych scenariuszy użycia opisano w poniższych sekcjach.

## <a name="introduction"></a>Wprowadzenie

Azure Time Series Insights Gen2 to kompleksowa oferta typu "platforma jako usługa". Służy do zbierania, przetwarzania, przechowywania, analizowania i wykonywania zapytań o wysoce zastrzeżonych, zoptymalizowanych od szeregów czasowych danych na skalę IoT. Doskonale sprawdza się w przypadku eksploracji danych ad hoc i analizy operacyjnej. Azure Time Series Insights Gen2 to unikatowa rozszerzalna, dostosowana oferta usługi, która spełnia szeroki zakres potrzeb przemysłowych wdrożeń IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Eksploracja danych i wykrywanie anomalii w wizualizacji

Błyskawicznie Eksploruj i Analizuj miliardy zdarzeń, aby wypróbować anomalie i odkrywać ukryte trendy w danych. Azure Time Series Insights Gen2 zapewnia wydajność niemal w czasie rzeczywistym dla obciążeń analizy IoT i DevOps.

[![Eksplorator danych](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Większość klientów wyraża zgodę na to, że minimalny czas wymagany do uzyskania wglądu jest jedną z wyróżniającąch funkcji programu Azure Time Series Insights Gen2:

* Azure Time Series Insights Gen2 nie wymaga przygotowania danych z góry.
* Umożliwia ona szybkie łączenie się z miliardami zdarzeń w usłudze Azure IoT Hub lub wystąpieniami Event Hubs platformy Azure w ciągu kilku minut.
* Po nawiązaniu połączenia można wizualizować i analizować miliardy zdarzeń, aby wypróbować anomalie i odkrywać ukryte trendy w danych.

Azure Time Series Insights Gen2 jest intuicyjna i prosta do użycia. Możesz korzystać z danych bez konieczności pisania jednego wiersza kodu. Nie ma również żadnego nowego języka, który jest wymagany do uczenia się, chociaż Azure Time Series Insights Gen2 udostępnia szczegółowy język zapytań oparty na tekście dla zaawansowanych użytkowników, którzy znają program SQL. Udostępnia również eksplorację z wybieraniem i kliknięciem dla początkujących.

Klienci mogą korzystać z szybkości, aby szybko zdiagnozować problemy związane z zasobami. Mogą przeprowadzić analizę DevOps, aby uzyskać dostęp do głównej przyczyny błędu w rozwiązaniu IoT. Mogą również identyfikować obszary, które mają być poddane kontroli w celu dalszej analizy w ramach swoich inicjatyw dotyczących analizy danych.

Istnieją trzy podstawowe sposoby współpracy z danymi przechowywanymi w Azure Time Series Insights Gen2:

* Pierwszym i najprostszym sposobem rozpoczęcia pracy jest wprowadzenie Azure Time Series Insights Gen2 Explorer. Za jego pomocą można szybko wizualizować wszystkie dane IoT w jednym miejscu. Udostępnia narzędzia takie jak mapa cieplna, które ułatwiają wykrywanie anomalii w danych. Zawiera również widok perspektywy. Służy do porównywania do czterech widoków z co najmniej jednego Azure Time Series Insights środowisk Gen2 na jednym pulpicie nawigacyjnym. Pulpit nawigacyjny umożliwia wyświetlenie danych szeregów czasowych we wszystkich lokalizacjach. Dowiedz się więcej na temat [Azure Time Series Insights Explorer Gen2](./time-series-insights-update-explorer.md). Aby zaplanować środowisko, Przeczytaj [Azure Time Series Insights Gen2 planowanie](./time-series-insights-update-plan.md).

* Drugim sposobem na rozpoczęcie pracy jest użycie zestawu SDK języka JavaScript w celu szybkiego osadzenia zaawansowanych wykresów i grafów w aplikacji sieci Web. Wystarczy kilka wierszy kodu, które umożliwiają tworzenie zaawansowanych zapytań. Służą one do wypełniania wykresów liniowych, wykresów kołowych, wykresów słupkowych, map cieplnych, siatek danych i nie tylko. Wszystkie te elementy istnieją jako wbudowane przy użyciu zestawu SDK. Zestaw SDK jest również abstrakcyjny Azure Time Series Insights interfejsów API zapytań Gen2. Za ich pomocą można tworzyć predykaty podobne do języka SQL, aby wykonywać zapytania dotyczące danych, które mają być wyświetlane na pulpicie nawigacyjnym. W przypadku hybrydowych rozwiązań warstwy prezentacji Azure Time Series Insights Gen2 oferuje sparametryzowane adresy URL. Zapewniają one bezproblemowe punkty połączenia z Azure Time Series Insights Gen2 Explorer, aby uzyskać szczegółowe omówieniach do danych.

  * Zapoznaj się z [biblioteką kliencką js](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) i [przykładową dokumentacją klienta](https://github.com/Microsoft/tsiclient) , aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript.

  * Dowiedz się więcej o udostępnianiu adresów URL i nowym interfejsie użytkownika, przeglądając temat [Wizualizacja danych w Azure Time Series Insights Gen2 Explorer](time-series-insights-update-explorer.md).

* Trzecim sposobem na rozpoczęcie pracy jest użycie zaawansowanych interfejsów API do wykonywania zapytań dotyczących danych przechowywanych w Azure Time Series Insights Gen2. Azure Time Series Insights Gen2 ma operatory czasowe, takie jak `from` ,, `to` `first` , i `last` . Ma agregacje i przekształcenia, takie jak `average` ,,,,, `sum` `min` `max` `time-weighted average` `time-weighted sum` itd. Umożliwia także filtrowanie, operatory arytmetyczne i logiczne, funkcje skalarne itp. Wszystkie te operatory umożliwiają aplikacjom podrzędnym szybkie znajdowanie interesujących trendów i wzorców w danych. Służą one do wypełniania wizualizacji Homegrown na potrzeby wykrywania anomalii.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analiza operacyjna i wydajność procesu tworzenia

Użyj Azure Time Series Insights Gen2 do monitorowania kondycji, użycia i wydajności sprzętu w odpowiedniej skali i mierzenia wydajności operacyjnej. Azure Time Series Insights Gen2 ułatwia zarządzanie różnorodnymi i nieprzewidywalnymi obciążeniami IoT bez poświęcania na pozyskiwanie lub wydajność zapytań.

[![Zrzut ekranu przedstawia urządzenia/dane aplikacji, przetwarzanie strumienia, efektywność operacyjną, inteligencję i szczegółowe informacje oraz zaawansowaną analizę w Azure Time Series Insights Gen2.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Przesyłanie strumieniowe i ciągłe przetwarzanie danych pochodzących z procesów operacyjnych może pomyślnie przekształcić każdą firmę, jeśli jest ona połączona z odpowiednią technologią lub rozwiązaniem. Często te rozwiązania są kombinacją wielu systemów. Umożliwiają one eksplorację i analizę danych, które są stale zmieniane, szczególnie w obszarze IoT, i dzielą się wspólnym wzorcem.

Te wzorce często zaczynają się od platformy z obsługą IoT, która pozyskuje miliardy zdarzeń z urządzeń i czujników obejmujących różne ustawienia regionalne. Te systemy przetwarzają i analizują dane przesyłane strumieniowo, aby uzyskiwać szczegółowe informacje i akcje w czasie rzeczywistym. Dane są zwykle archiwizowane dla magazynu ciepłego i zimnego na potrzeby analiz w czasie rzeczywistym i w usłudze Batch.

Gromadzone dane przechodzą przez serię przetwarzania, aby czyścić i contextualize je na potrzeby scenariuszy zapytań podrzędnych i analizy. Platforma Azure oferuje bogate usługi, które mogą być stosowane do scenariuszy IoT, takich jak konserwacja zasobów i produkcja. Do tych usług należą Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning i Power BI.

Architekturę rozwiązania można osiągnąć w następujący sposób:

* Pozyskiwanie danych za pośrednictwem IoT Hub lub Event Hubs w celu uzyskania najlepszych w swojej klasie zabezpieczeń, przepływności i opóźnień.
* Wykonywanie przetwarzania i obliczeń danych. Pozyskaj dane przy użyciu usług takich jak Stream Analytics, Logic Apps i Azure Functions. Używana usługa zależy od konkretnych potrzeb związanych z przetwarzaniem danych.
* Obliczane sygnały z potoku przetwarzania są przekazywane do Azure Time Series Insights Gen2 do przechowywania i analizowania.

Azure Time Series Insights Gen2 oferuje około eksploracji danych w czasie rzeczywistym i szczegółowe informacje oparte na zasobach za pośrednictwem danych historycznych. W zależności od potrzeb firmy zadania MapReduce i Hive mogą być uruchamiane na danych przechowywanych w Azure Time Series Insights Gen2 przez połączenie Azure Time Series Insights Gen2 z usługą Azure HDInsight. Dane przechowywane w Azure Time Series Insights Gen2 są dostępne dla Power BI i innych aplikacji klienta za pośrednictwem interfejsów API Azure Time Series Insights Gen2 publicznych. Te dane mogą być używane w scenariuszach analizy biznesowej i funkcjonalnej.

## <a name="advanced-analytics"></a>Analiza zaawansowana

Integruj z zaawansowanymi usługami analitycznymi, takimi jak Machine Learning i Azure Databricks. Azure Time Series Insights Gen2 ingresses danych pierwotnych z milionów urządzeń. Dodaje kontekstowe dane, które mogą być bezproblemowo wykorzystywane przez pakiet usług analitycznych platformy Azure.

[![Analiza](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Zaawansowana analiza i uczenie maszynowe zużywają i przetwarzają duże ilości danych. Te dane służą do podejmowania decyzji opartych na danych i wykonywania analizy predykcyjnej. W przypadku przypadków użycia IoT zaawansowane algorytmy analizy uczyją się od danych zebranych z milionów urządzeń. Te urządzenia przesyłają dane wiele razy na sekundę. Dane zbierane z urządzeń IoT są surowe. Brak informacji kontekstowych, takich jak lokalizacja urządzenia i jednostka odczytywania czujnika. W związku z tym dane pierwotne są trudne do użycia bezpośrednio dla zaawansowanej analizy.

Azure Time Series Insights Gen2 mostkuje przerwy między danymi IoT a zaawansowaną analizą na dwa proste i ekonomiczne metody:

* Najpierw Azure Time Series Insights Gen2 zbiera nieprzetworzone dane telemetryczne z milionów urządzeń przy użyciu IoT Hub. Wzbogaca dane z informacjami kontekstowymi i przekształca dane w format Parquet. Ten format można łatwo zintegrować z innymi zaawansowanymi usługami analitycznymi, takimi jak Machine Learning, Azure Databricks i aplikacje innych firm.

    Azure Time Series Insights Gen2 może stanowić źródło prawdy dla wszystkich danych w organizacji. Tworzy centralne repozytorium dla obciążeń analizy podrzędnej do użycia. Ponieważ Azure Time Series Insights Gen2 jest usługą magazynu niemal w czasie rzeczywistym, zaawansowane modele analityczne mogą się ciągle uczyć od przychodzących danych telemetrycznych IoT. W efekcie modele mogą wprowadzać dokładniejsze przewidywania.

* Po drugie dane wyjściowe modeli uczenia maszynowego i przewidywania mogą być wprowadzane do Azure Time Series Insights Gen2, aby wizualizować i przechowywać wyniki. Ta procedura ułatwia organizacjom optymalizowanie i dostosowywanie modeli. Azure Time Series Insights Gen2 ułatwia wizualizację danych telemetrycznych przesyłanych strumieniowo na tej samej płaszczyźnie co w przypadku wyników przeszkolonych modeli. W ten sposób ułatwiają zespołom analizy danych wykrywanie anomalii i identyfikowanie wzorców.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [Azure Time Series Insights Explorer Gen2](./time-series-insights-update-explorer.md).
* Przeczytaj [Azure Time Series Insights Gen2 najlepsze rozwiązania](./time-series-insights-update-plan.md) w celu zaplanowania środowiska.
* Przeczytaj [przykładową dokumentację klienta](https://github.com/Microsoft/tsiclient) .
