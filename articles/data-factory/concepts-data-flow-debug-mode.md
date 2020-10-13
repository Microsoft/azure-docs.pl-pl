---
title: Mapowanie trybu debugowania przepływu danych
description: Rozpocznij interaktywną sesję debugowania podczas kompilowania przepływów danych
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/11/2020
ms.openlocfilehash: 41153c488825e87583284b23a287353f63ff8db8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90085097"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapowanie trybu debugowania przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Omówienie

Tryb debugowania Azure Data Factory mapowania przepływu danych pozwala interaktywnie monitorować transformację kształtu danych podczas kompilowania i debugowania przepływów danych. Sesja debugowania może być używana zarówno w sesjach projektu przepływu danych, jak i podczas wykonywania debugowania przez potok przepływów danych. Aby włączyć tryb debugowania, użyj przycisku "Debugowanie przepływu danych" w górnej części powierzchni projektowej.

![Suwak debugowania](media/data-flow/debugbutton.png "Suwak debugowania")

Po włączeniu suwaka zostanie wyświetlony monit z pytaniem o wybór konfiguracji środowiska Integration Runtime, która ma być używana. Jeśli wybrano AutoResolveIntegrationRuntime, klaster zawierający osiem rdzeni obliczeń ogólnych z 60-minutowym czasem na żywo zostanie przypadany. Aby uzyskać więcej informacji na temat środowisk integracji przepływu danych, zobacz [wydajność przepływu danych](concepts-data-flow-performance.md#ir).

![Debuguj wybór IR](media/data-flow/debugbutton2.png "Debuguj wybór IR")

Gdy tryb debugowania jest włączony, będziesz interaktywnie kompilować przepływ danych przy użyciu aktywnego klastra Spark. Sesja zostanie zamknięta po wyłączeniu debugowania w Azure Data Factory. Należy zwrócić uwagę na opłaty godzinowe naliczane przez Azure Databricks w czasie, gdy sesja debugowania jest włączona.

W większości przypadków dobrym sposobem jest skompilowanie przepływów danych w trybie debugowania, aby można było zweryfikować logikę biznesową i wyświetlić przekształcenia danych przed opublikowaniem pracy w Azure Data Factory. Użyj przycisku "Debuguj" w panelu potoku, aby przetestować przepływ danych w potoku.

![Wyświetl sesje debugowania przepływu danych](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Każda sesja debugowania uruchamiana przez użytkownika z poziomu interfejsu użytkownika przeglądarki ADF jest nową sesją z własnym klastrem Spark. Możesz użyć widoku monitorowanie dla sesji debugowania powyżej, aby wyświetlić sesje debugowania na fabrykę i zarządzać nimi.

## <a name="cluster-status"></a>Stan klastra

Wskaźnik stanu klastra w górnej części powierzchni projektowej zmieni kolor na zielony, gdy klaster jest gotowy do debugowania. Jeśli klaster jest już grzany, zielony wskaźnik zostanie wyświetlony niemal natychmiast. Jeśli klaster nie był jeszcze uruchomiony po wprowadzeniu trybu debugowania, należy poczekać 5-7 minut, aż klaster wyjdzie. Wskaźnik zostanie zastosowany do momentu jego gotowości.

Po zakończeniu debugowania należy wyłączyć przełącznik debugowania, aby klaster Azure Databricks mógł zakończyć pracę i nie będzie już rozliczany dla działania debugowania.

## <a name="debug-settings"></a>Ustawienia debugowania

Po włączeniu trybu debugowania można edytować sposób, w jaki przepływ danych przegląda dane. Ustawienia debugowania można edytować, klikając pozycję "ustawienia debugowania" na pasku narzędzi kanwy przepływu danych. W tym miejscu możesz wybrać limit wierszy lub źródło pliku do użycia dla każdego z przekształceń źródłowych. Limity wierszy w tym ustawieniu są tylko dla bieżącej sesji debugowania. Możesz również wybrać połączoną usługę, która ma być używana dla źródła analizy usługi Azure Synapse. 

![Ustawienia debugowania](media/data-flow/debug-settings.png "Ustawienia debugowania")

Jeśli masz parametry w przepływie danych lub w dowolnym z nich, do których istnieją odwołania, możesz określić wartości, które mają być używane podczas debugowania, wybierając kartę **Parametry** .

![Parametry ustawień debugowania](media/data-flow/debug-settings2.png "Parametry ustawień debugowania")

Domyślnym ustawieniem IR używanym w trybie debugowania w przepływach danych ADF jest mały dwurdzeniowy węzeł procesu roboczego z dwurdzeniowym węzłem jednego sterownika. Jest to dobre rozwiązanie przy użyciu mniejszych próbek danych podczas testowania logiki przepływu danych. W przypadku rozszerzenia limitów wierszy w ustawieniach debugowania podczas wyświetlania podglądu danych lub ustawienia większej liczby próbkowanych wierszy w źródle podczas debugowania potoku warto rozważyć skonfigurowanie większego środowiska obliczeniowego w nowym Azure Integration Runtime. Następnie można ponownie uruchomić sesję debugowania przy użyciu większego środowiska obliczeniowego.

## <a name="data-preview"></a>Podgląd danych

Po zakończeniu debugowania na karcie Podgląd danych zostanie wystawiony dolny panel. Bez trybu debugowania w przepływie danych będzie zawierać tylko bieżące metadane z i z każdego przekształcenia na karcie Inspekcja. W podglądzie danych zostanie zbadana tylko liczba wierszy, które zostały ustawione jako limit w ustawieniach debugowania. Kliknij przycisk **Odśwież** , aby pobrać Podgląd danych.

![Podgląd danych](media/data-flow/datapreview.png "Podgląd danych")

> [!NOTE]
> Źródła plików ograniczają tylko te wiersze, które są widoczne, a nie odczytywane wiersze. W przypadku bardzo dużych zestawów danych zaleca się wykonanie małej części tego pliku i użycie jej do testowania. Można wybrać plik tymczasowy w ustawieniach debugowania dla każdego źródła, które jest typem zestawu danych.

Podczas uruchamiania w trybie debugowania w przepływie danych dane nie będą zapisywane do transformacji ujścia. Sesja debugowania jest przeznaczona do użycia jako zespół testów dla transformacji. Ujścia nie są wymagane podczas debugowania i są ignorowane w przepływie danych. Jeśli chcesz przetestować zapisywanie danych w ujściach, wykonaj przepływ danych z potoku Azure Data Factory i użyj wykonywania debugowania z potoku.

Data Preview to migawka przekształconych danych przy użyciu limitów wierszy i próbkowania danych z ramek danych w pamięci platformy Spark. W związku z tym sterowniki ujścia nie są wykorzystywane ani testowane w tym scenariuszu.

### <a name="testing-join-conditions"></a>Testowanie warunków sprzężenia

Gdy Przekształć testy jednostkowe, istnieją lub przekształcenia wyszukiwania, upewnij się, że używasz małego zestawu znanych danych dla testu. Możesz użyć opcji Ustawienia debugowania powyżej, aby ustawić plik tymczasowy do użycia na potrzeby testowania. Jest to konieczne, ponieważ w przypadku ograniczania lub próbkowania wierszy z dużego zestawu danych nie można przewidzieć, które wiersze i które klucze będą odczytywane w przepływie na potrzeby testowania. Wynik nie jest deterministyczny, co oznacza, że warunki sprzężenia mogą się nie powieść.

### <a name="quick-actions"></a>Szybkie akcje

Gdy zobaczysz Podgląd danych, możesz wygenerować szybką transformację, aby rzutowanie, usunąć lub zmodyfikować kolumnę. Kliknij nagłówek kolumny, a następnie wybierz jedną z opcji na pasku narzędzi Podgląd danych.

![Szybkie akcje](media/data-flow/quick-actions1.png "Szybkie akcje")

Po wybraniu modyfikacji Podgląd danych zostanie natychmiast odświeżony. Kliknij przycisk **Potwierdź** w prawym górnym rogu, aby wygenerować nowe przekształcenie.

![Szybkie akcje](media/data-flow/quick-actions2.png "Szybkie akcje")

**Rzutowanie** i **Modify** wygenerują transformację kolumn pochodnych, a **usunięcie** spowoduje wygenerowanie transformacji SELECT.

![Szybkie akcje](media/data-flow/quick-actions3.png "Szybkie akcje")

> [!NOTE]
> W przypadku edycji przepływu danych należy ponownie pobrać Podgląd danych przed dodaniem szybkiej transformacji.

### <a name="data-profiling"></a>Profilowanie danych

Wybranie kolumny na karcie Podgląd danych i kliknięcie pozycji **statystyki** na pasku narzędzi Podgląd danych spowoduje wyczyszczenie wykresu z prawej strony siatki danych z szczegółowymi statystykami dotyczącymi każdego pola. Azure Data Factory wykona Określanie na podstawie próbkowania danych, którego wykres ma być wyświetlany. Pola o dużej kardynalności będą domyślnie mieć wartości NULL/NOT NULL, podczas gdy kategorii i dane liczbowe o niskiej kardynalności będą wyświetlać wykresy słupkowe pokazujące częstotliwość wartości danych. Zobaczysz również maksymalną/len długość pól ciągów, minimalną/maksymalną wartość w polach liczbowych, standardowe dev, percentyly, liczniki i średnie.

![Statystyki kolumn](media/data-flow/stats.png "Statystyki kolumn")

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu kompilowania i debugowania przepływu danych [Wykonaj go z potoku.](control-flow-execute-data-flow-activity.md)
* Podczas testowania potoku przy użyciu przepływu danych Użyj [opcji wykonywania uruchamiania debugowania](iterative-development-debugging.md) potoku.
