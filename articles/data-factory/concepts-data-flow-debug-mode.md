---
title: Tryb debugowania przepływu mapowania danych
description: Uruchamianie interaktywnej sesji debugowania podczas budowania przepływów danych
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ac0e088c587132b32f2112d21bce936eac35dc72
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515873"
---
# <a name="mapping-data-flow-debug-mode"></a>Tryb debugowania przepływu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Omówienie

Azure Data Factory debugowania przepływu mapowania danych umożliwia interaktywne obserwowanie transformacji kształtu danych podczas kompilowania i debugowania przepływów danych. Sesji debugowania można używać zarówno w Przepływ danych projektowych, jak i podczas wykonywania debugowania potoku przepływów danych. Aby włączyć tryb debugowania, użyj Przepływ danych **Debugowanie** na górnym pasku kanwy przepływu danych lub kanwy potoku, jeśli masz działania przepływu danych.

![Suwak debugowania 1](media/data-flow/debugbutton.png "Suwak debugowania")

![Suwak debugowania 2](media/data-flow/debug-button-4.png "Suwak debugowania")

Po włączeniu suwaka zostanie wyświetlony monit o wybranie konfiguracji środowiska Integration Runtime, której chcesz użyć. Jeśli zostanie wybrana opcja AutoResolveIntegrationRuntime, zostanie automatycznie uruchomić klaster z ośmioma rdzeniami obliczeń ogólnych z domyślnym 60-minutowym czasem działania. Jeśli chcesz zezwolić na więcej bezczynnych reprezentacji zespołu przed przechyłem czasu sesji, możesz wybrać wyższe ustawienie czasu wygaśnięcia. Aby uzyskać więcej informacji na temat środowisk Data Flow Integration Runtime, zobacz [Wydajność przepływu danych](concepts-data-flow-performance.md#ir).

![Wybór debugowania ir](media/data-flow/debug-new-1.png "Wybór debugowania ir")

Gdy tryb debugowania jest wł., będziesz interaktywnie kompilować przepływ danych przy użyciu aktywnego klastra Spark. Sesja zostanie zamkniena po zamknięciu debugowania w Azure Data Factory. Należy pamiętać o opłatach godzinowych naliczanych przez Azure Databricks w czasie, gdy sesja debugowania jest włączona.

W większości przypadków dobrym rozwiązaniem jest skompilowanie przepływów danych w trybie debugowania, aby można było zweryfikować logikę biznesową i wyświetlić przekształcenia danych przed opublikowaniem pracy w Azure Data Factory. Użyj przycisku "Debuguj" na panelu potoku, aby przetestować przepływ danych w potoku.

![Wyświetlanie sesji debugowania przepływu danych](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Każda sesja debugowania, która rozpoczyna się od interfejsu użytkownika przeglądarki usługi ADF, jest nową sesją z własnym klastrem Spark. Widok monitorowania dla powyższych sesji debugowania umożliwia wyświetlanie sesji debugowania dla 1 fabryki i zarządzanie nimi. Opłaty są naliczane za każdą godzinę wykonywania każdej sesji debugowania, w tym za czas wygaśnięcia.

## <a name="cluster-status&quot;></a>Stan klastra

Wskaźnik stanu klastra w górnej części powierzchni projektowej zmieni kolor na zielony, gdy klaster jest gotowy do debugowania. Jeśli klaster jest już ciepły, zielony wskaźnik pojawi się niemal natychmiast. Jeśli klaster nie był jeszcze uruchomiony po wprowadzeniu trybu debugowania, klaster Spark wykona zimny rozruch. Wskaźnik będzie się kręcić, dopóki środowisko nie będzie gotowe do debugowania interakcyjnego.

Po zakończeniu debugowania wyłącz przełącznik Debugowanie, aby klaster Spark mógł zakończyć działanie i nie będą już naliczane żadne rachunki za działanie debugowania.

## <a name=&quot;debug-settings&quot;></a>Ustawienia debugowania

Po włączeniu trybu debugowania możesz edytować sposób wyświetlania podglądu danych przez przepływ danych. Ustawienia debugowania można edytować, klikając pozycję &quot;Ustawienia debugowania&quot; Przepływ danych pasku narzędzi kanwy. W tym miejscu możesz wybrać limit wierszy lub źródło pliku do użycia dla poszczególnych przekształceń źródła. Limity wierszy w tym ustawieniu są tylko dla bieżącej sesji debugowania. Możesz również wybrać przejściowe połączone usługi do Azure Synapse Analytics źródłowego. 

![Ustawienia debugowania](media/data-flow/debug-settings.png &quot;Ustawienia debugowania")

Jeśli masz parametry w Przepływ danych lub dowolny z przywołynych zestawów danych, możesz określić wartości do użycia podczas debugowania, wybierając **kartę** Parametry.

Użyj ustawień próbkowania w tym miejscu, aby wskazać przykładowe pliki lub przykładowe tabele danych, aby nie trzeba było zmieniać źródłowych zestawów danych. Korzystając z przykładowego pliku lub tabeli w tym miejscu, można zachować te same ustawienia logiki i właściwości w przepływie danych podczas testowania podzestawu danych.

![Parametry ustawień debugowania](media/data-flow/debug-settings2.png "Parametry ustawień debugowania")

Domyślnym węzłem IR używanym w trybie debugowania w przepływach danych ADF jest mały, 4-rdzeniowy węzeł pojedynczego procesu roboczego z 4-rdzeniowym pojedynczym węzłem sterownika. Działa to dobrze w przypadku mniejszych próbek danych podczas testowania logiki przepływu danych. Jeśli rozwiniesz limity wierszy w ustawieniach debugowania podczas podglądu danych lub ustawisz większą liczbę przykładowych wierszy w źródle podczas debugowania potoku, warto rozważyć ustawienie większego środowiska obliczeniowego w nowym Azure Integration Runtime. Następnie możesz ponownie uruchomić sesję debugowania przy użyciu większego środowiska obliczeniowego.

## <a name="data-preview"></a>Podgląd danych

Po wł. debugowania na dolnym panelu zostanie wyzjemniana karta Podgląd danych. Bez trybu debugowania Przepływ danych tylko bieżące metadane w i z poszczególnych przekształceń na karcie Inspekcja. Podgląd danych będzie dotyczył tylko liczby wierszy ustawionych jako limit w ustawieniach debugowania. Kliknij **przycisk Odśwież,** aby pobrać podgląd danych.

![Podgląd danych](media/data-flow/datapreview.png "Podgląd danych")

> [!NOTE]
> Źródła plików ograniczają tylko wiersze, które widzisz, a nie odczytywane wiersze. W przypadku bardzo dużych zestawów danych zaleca się użycie niewielkiej części tego pliku do testowania. Możesz wybrać plik tymczasowy w ustawieniach debugowania dla każdego źródła, które jest typem zestawu danych pliku.

W trybie debugowania w Przepływ danych dane nie będą zapisywane do przekształcenia ujścia. Sesja debugowania ma służyć jako wykorzystanie testów do przekształceń. Ujścia nie są wymagane podczas debugowania i są ignorowane w przepływie danych. Jeśli chcesz przetestować zapisywanie danych w ujściu, wykonaj Przepływ danych z potoku Azure Data Factory i użyj wykonywania debugowania z potoku.

Wersja zapoznawcza danych to migawka przekształcone danych korzystająca z limitów wierszy i próbkowania danych z ramek danych w pamięci platformy Spark. W związku z tym sterowniki ujścia nie są używane ani testowane w tym scenariuszu.

### <a name="testing-join-conditions"></a>Testowanie warunków sprzężenia

Podczas przekształceń sprzężenia, istnieje lub wyszukiwania testów jednostkowych upewnij się, że używasz małego zestawu znanych danych do celów testowych. Możesz użyć powyższej opcji Ustawienia debugowania, aby ustawić plik tymczasowy do użycia podczas testowania. Jest to konieczne, ponieważ podczas ograniczania lub próbkowania wierszy z dużego zestawu danych nie można przewidzieć, które wiersze i które klucze zostaną odczytane do przepływu na potrzeby testowania. Wynik nie jest deterministyczny, co oznacza, że warunki sprzężenia mogą się nie powieść.

### <a name="quick-actions"></a>Szybkie akcje

Po wyświetleniu podglądu danych można wygenerować szybkie przekształcenie w celu emisji typu, usunięcia lub modyfikacji kolumny. Kliknij nagłówek kolumny, a następnie wybierz jedną z opcji na pasku narzędzi podglądu danych.

![Zrzut ekranu przedstawiający pasek narzędzi podglądu danych z opcjami: Emisji typu, Modyfikuj, Statystyka i Usuń.](media/data-flow/quick-actions1.png "Szybkie akcje")

Po wybraniu modyfikacji podgląd danych zostanie natychmiast odświeżony. Kliknij **przycisk Potwierdź** w prawym górnym rogu, aby wygenerować nowe przekształcenie.

![Zrzut ekranu przedstawiający przycisk Potwierdź.](media/data-flow/quick-actions2.png "Szybkie akcje")

**Typcast** and **Modify** wygeneruje przekształcenie Kolumny pochodnej, a **polecenie Usuń** wygeneruje przekształcenie Select.

![Zrzut ekranu przedstawia ustawienia kolumn pochodnych.](media/data-flow/quick-actions3.png "Szybkie akcje")

> [!NOTE]
> Jeśli edytujesz Przepływ danych, musisz ponownie pobrać podgląd danych przed dodaniem szybkiego przekształcenia.

### <a name="data-profiling"></a>Profilowanie danych

Wybranie kolumny na karcie podglądu danych i kliknięcie pozycji Statystyki na pasku narzędzi podglądu danych spowoduje wyskakowanie wykresu po prawej stronie siatki danych ze szczegółowymi statystykami dla każdego pola.  Azure Data Factory dokona ustalenia na podstawie próbkowania danych, jakiego typu wykres ma być wyświetlany. Pola o wysokiej kardynalności będą domyślnie wyświetlane na wykresach NULL/NOT NULL, natomiast dane kategorii i liczbowe o niskiej kardynalności będą wyświetlać wykresy słupkowe pokazujące częstotliwość wartości danych. Zobaczysz również maksymalną długość/długość pola ciągu, wartości minimalne/maksymalne w polach liczbowych, standardowe dev, percentyły, liczby i średnią.

![Statystyki kolumn](media/data-flow/stats.png "Statystyki kolumn")

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu budowania i debugowania przepływu danych wykonaj [go z potoku.](control-flow-execute-data-flow-activity.md)
* Podczas testowania potoku przy użyciu przepływu danych użyj opcji [Wykonywania uruchomienia debugowania potoku.](iterative-development-debugging.md)
