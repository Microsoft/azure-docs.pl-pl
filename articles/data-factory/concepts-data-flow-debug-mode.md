---
title: Tryb debugowania przepływu danych mapowania
description: Rozpoczynanie interaktywnej sesji debugowania podczas tworzenia przepływów danych
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 4a763a3bb4d46ba03808423d4d1283381c1174a3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605391"
---
# <a name="mapping-data-flow-debug-mode"></a>Tryb debugowania przepływu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Omówienie

Tryb debugowania przepływu danych usługi Azure Data Factory umożliwia interaktywne obserwowanie przekształcania kształtu danych podczas tworzenia i debugowania przepływów danych. Sesja debugowania może być używana zarówno w sesjach projektowania przepływu danych, jak i podczas wykonywania przepływów danych debugowania potoku. Aby włączyć tryb debugowania, użyj przycisku "Debugowanie przepływu danych" u góry powierzchni projektowej.

![Suwak debugowania](media/data-flow/debugbutton.png "Suwak debugowania")

Po włączeniu suwaka zostanie wyświetlony monit o wybranie konfiguracji środowiska uruchomieniowego integracji, której chcesz użyć. Jeśli autoresolveIntegrationRuntime zostanie wybrany, klaster z ośmioma rdzeniami obliczeń ogólnych z 60-minutowym czasem wygaśnięcia zostanie spun up. Aby uzyskać więcej informacji na temat środowiska uruchomień integracji przepływu danych, zobacz [Wydajność przepływu danych](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime).

![Wybór podczerwenia debugowania](media/data-flow/debugbutton2.png "Wybór podczerwenia debugowania")

Gdy tryb debugowania jest włączony, interaktywnie skompilujesz przepływ danych za pomocą aktywnego klastra platformy Spark. Sesja zostanie zamknięta po wyłączeniu debugowania w usłudze Azure Data Factory. Należy pamiętać o opłatach godzinowych ponoszonych przez usługę Azure Databricks w czasie, w którym masz włączoną sesję debugowania.

W większości przypadków jest dobrą praktyką do tworzenia przepływów danych w trybie debugowania, dzięki czemu można sprawdzić poprawność logiki biznesowej i wyświetlić przekształcenia danych przed opublikowaniem pracy w usłudze Azure Data Factory. Użyj przycisku "Debugowanie" na panelu potoku, aby przetestować przepływ danych w potoku.

## <a name="cluster-status"></a>Stan klastra

Wskaźnik stanu klastra w górnej części powierzchni projektowej zmienia kolor na zielony, gdy klaster jest gotowy do debugowania. Jeśli klaster jest już ciepły, zielony wskaźnik pojawi się niemal natychmiast. Jeśli klaster nie był jeszcze uruchomiony po wejściu w tryb debugowania, musisz odczekać 5-7 minut, aż klaster zostanie rozdzielony. Wskaźnik będzie obracać aż do jego gotowości.

Po zakończeniu debugowania, należy wyłączyć debugowanie, tak aby klaster usługi Azure Databricks można zakończyć i nie będzie już naliczane na użytkownika za działanie debugowania.

## <a name="debug-settings"></a>Ustawienia debugowania

Ustawienia debugowania można edytować, klikając "Ustawienia debugowania" na pasku narzędzi obszaru roboczego przepływ danych. W tym miejscu można wybrać limit wierszy lub źródło pliku, które mają być używane dla każdego przekształcenia źródła. Limity wierszy w tym ustawieniu są tylko dla bieżącej sesji debugowania. Można również wybrać usługę przemieszczania połączone, które mają być używane dla źródła DW SQL. 

![Ustawienia debugowania](media/data-flow/debug-settings.png "Ustawienia debugowania")

Jeśli masz parametry w przepływie danych lub którykolwiek z jego zestawów danych, do których istnieją odwołania, można określić, jakie wartości mają być używane podczas debugowania, wybierając kartę **Parametry.**

![Parametry ustawień debugowania](media/data-flow/debug-settings2.png "Parametry ustawień debugowania")

## <a name="data-preview"></a>Podgląd danych

Po włączeniu debugowania karta Podgląd danych zapali się na dolnym panelu. Bez trybu debugowania włączony przepływ danych pokaże tylko bieżące metadane w i z każdego przekształcenia w inspekcji kartę. Podgląd danych będzie wysyłać tylko zapytania o liczbę wierszy ustawionych jako limit w ustawieniach debugowania. Kliknij **przycisk Odśwież,** aby pobrać podgląd danych.

![Podgląd danych](media/data-flow/datapreview.png "Podgląd danych")

> [!NOTE]
> Źródła plików ograniczają tylko wyświetlane wiersze, a nie odczytywane wiersze. W przypadku bardzo dużych zestawów danych zaleca się wykonanie niewielkiej części tego pliku i użycie go do testowania. Można wybrać plik tymczasowy w ustawieniach debugowania dla każdego źródła, które jest typem zestawu danych pliku.

Podczas uruchamiania w trybie debugowania w przepływie danych, dane nie zostaną zapisane do transformacji sink. Sesja debugowania ma służyć jako wiązka testowa dla przekształceń. Pochłaniacze nie są wymagane podczas debugowania i są ignorowane w przepływie danych. Jeśli chcesz przetestować zapisywanie danych w usłudze Sink, należy wykonać przepływ danych z potoku fabryki danych platformy Azure i użyć wykonania debugowania z potoku.

### <a name="testing-join-conditions"></a>Warunki przystąpienia do testowania

Podczas testowania jednostkowego sprzężenia, istnieje lub odnoś transformacje, upewnij się, że używasz niewielki zestaw znanych danych dla testu. Można użyć opcji Ustawienia debugowania powyżej, aby ustawić plik tymczasowy do użycia do testowania. Jest to konieczne, ponieważ podczas ograniczania lub próbkowania wierszy z dużego zestawu danych, nie można przewidzieć, które wiersze i które klucze będą odczytywane do przepływu do testowania. Wynik jest niedeterministyczne, co oznacza, że warunki sprzężenia może zakończyć się niepowodzeniem.

### <a name="quick-actions"></a>Szybkie akcje

Po wyświetleniu podglądu danych można wygenerować szybką transformację w celu wygenerowania tekstu, usunięcia lub wykonania modyfikacji w kolumnie. Kliknij nagłówek kolumny, a następnie wybierz jedną z opcji z paska narzędzi podglądu danych.

![Szybkie akcje](media/data-flow/quick-actions1.png "Szybkie akcje")

Po wybraniu modyfikacji podgląd danych zostanie natychmiast odświeżony. Kliknij **przycisk Potwierdź** w prawym górnym rogu, aby wygenerować nową transformację.

![Szybkie akcje](media/data-flow/quick-actions2.png "Szybkie akcje")

**Typecast** i **Modify** wygeneruje transformację kolumny pochodnej i **Usuń** wygeneruje transformację Select.

![Szybkie akcje](media/data-flow/quick-actions3.png "Szybkie akcje")

> [!NOTE]
> W przypadku edytowania przepływu danych należy ponownie pobrać podgląd danych przed dodaniem szybkiej transformacji.

### <a name="data-profiling"></a>Profilowanie danych

Wybranie kolumny na karcie podglądu danych i **kliknięcie przycisku Statystyka** na pasku narzędzi podglądu danych spowoduje wyświetlenie wykresu po prawej stronie siatki danych ze szczegółowymi statystykami dotyczącymi każdego pola. Usługa Azure Data Factory dokona określenia na podstawie próbkowania danych, który typ wykresu do wyświetlenia. Pola wysokiej kardynalności będą domyślnie wyświetlane na wykresach NULL/NOT NULL, podczas gdy dane kategorii i liczbowe o niskiej kardynalności będą wyświetlać wykresy słupkowe z częstotliwością wartości danych. Zobaczysz również maksymalną/len długości pól ciągu, wartości min/max w polach liczbowych, dewelopera standardowego, percentyli, liczby i średniej.

![Statystyki kolumn](media/data-flow/stats.png "Statystyki kolumn")

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu tworzenia i debugowania przepływu danych, [należy wykonać go z potoku.](control-flow-execute-data-flow-activity.md)
* Podczas testowania potoku z przepływem danych należy użyć [opcji wykonywania uruchamiania debugowania potoku.](iterative-development-debugging.md)
