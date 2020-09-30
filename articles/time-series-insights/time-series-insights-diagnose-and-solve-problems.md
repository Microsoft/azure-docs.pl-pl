---
title: Diagnozowanie, rozwiązywanie problemów i rozwiązywanie problemów — Azure Time Series Insights
description: W tym artykule opisano sposób diagnozowania i rozwiązywania typowych problemów w środowisku Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 231f2e4df1445c60378ac06aab0d0e56f410c1c8
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530140"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Diagnozowanie i rozwiązywanie problemów w Azure Time Series Insights środowisku Gen1

> [!CAUTION]
> To jest artykuł Gen1.

W tym artykule opisano problemy, które mogą wystąpić w środowisku Azure Time Series Insightsu. Artykuł zawiera potencjalne przyczyny i rozwiązania do rozwiązania.

## <a name="video"></a>Wideo

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Poznaj typowe wyzwania Azure Time Series Insights i środki zaradcze</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: nie są wyświetlane żadne dane

Jeśli w [eksploratorze Azure Time Series Insights](https://insights.timeseries.azure.com)nie są wyświetlane żadne dane, weź pod uwagę te typowe przyczyny.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Przyczyna: dane źródłowe zdarzenia nie są w formacie JSON

Azure Time Series Insights obsługuje tylko dane JSON. Aby zapoznać się z przykładami JSON, zobacz [obsługiwane kształty JSON](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Przyczyna B: klucz źródła zdarzenia nie ma wymaganego uprawnienia

* W przypadku usługi IoT Hub w usłudze Azure IoT Hub należy podać klucz z uprawnieniami do łączenia usług. Wybierz pozycję **iothubowner** lub zasady **usługi** . Oba mają uprawnienia do łączenia usług.

   [![IoT Hub uprawnień do nawiązywania połączenia z usługą](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* W przypadku centrum zdarzeń w usłudze Azure Event Hubs należy podać klucz, który ma uprawnienia do nasłuchiwania. Zasady **odczytu** i **zarządzania** będą działały, ponieważ obie osoby mają uprawnienia do nasłuchiwania.

   [![Uprawnienia do nasłuchiwania centrum zdarzeń](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>Przyczyna C: poświadczona Grupa odbiorców nie ma na wyłączność Azure Time Series Insights

Po zarejestrowaniu Centrum IoT Hub lub centrum zdarzeń należy ustawić grupę odbiorców, która ma być używana do odczytywania danych. *Nie można udostępnić*tej grupy odbiorców. Jeśli grupa konsumentów jest udostępniona, podstawowe Centrum IoT lub centrum zdarzeń automatycznie i losowo rozłącza jednego z nich. Podaj unikatową grupę odbiorców, w której ma zostać odczytana Azure Time Series Insights.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Przyczyna D: środowisko zostało właśnie zainicjowane

Dane zostaną wyświetlone w Eksploratorze Azure Time Series Insights w ciągu kilku minut od momentu utworzenia środowiska i jego danych.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: wyświetlane są pewne dane, ale brakuje danych

Gdy dane pojawiają się tylko częściowo i wydaje się, że dane są opóźnione, weź pod uwagę te ewentualne problemy.

### <a name="cause-a-your-environment-is-being-throttled"></a>Przyczyna: środowisko jest ograniczane

[Ograniczanie](time-series-insights-environment-mitigate-latency.md) jest typowym problemem, gdy środowiska są inicjowane po utworzeniu źródła zdarzeń zawierającego dane. Usługa Azure IoT Hub i usługi Azure Events Hub przechowują dane przez maksymalnie siedem dni. Azure Time Series Insights zawsze zaczyna się od najstarszego zdarzenia w źródle zdarzeń (pierwszy na początku, w pierwszej kolejności lub *FIFO*).

Jeśli na przykład w źródle zdarzeń znajdują się zdarzenia 5 000 000 w przypadku łączenia się ze środowiskiem S1 (Single-Unit Azure Time Series Insights), Azure Time Series Insights odczytuje około 1 000 000 zdarzeń dziennie. Może wyglądać tak, jak Azure Time Series Insights występuje pięć dni opóźnienia. Jednak dzieje się tak, że środowisko jest ograniczane.

Jeśli masz stare zdarzenia w źródle zdarzeń, możesz przyejść do ograniczania na jeden z dwóch sposobów:

* Zmień limity przechowywania źródła zdarzeń, aby pomóc w usunięciu starych zdarzeń, które nie mają być wyświetlane w Azure Time Series Insights.
* Zapewnij większy rozmiar środowiska (liczbę jednostek), aby zwiększyć przepływność starych zdarzeń. W poprzednim przykładzie, jeśli zwiększy się to samo środowisko S1 do pięciu jednostek przez jeden dzień, środowisko powinno przechwycić w ciągu dnia. Jeśli produkcja zdarzeń o stałym stanie wynosi 1 000 000 lub mniej zdarzeń dziennie, można zmniejszyć wydajność zdarzenia do jednej jednostki po Azure Time Series Insights przechwycić.

Limit wymuszonego ograniczania jest oparty na typie i pojemności jednostki SKU środowiska. Wszystkie źródła zdarzeń w środowisku współużytkują tę pojemność. Jeśli źródło zdarzeń dla Centrum IoT Hub lub centrum zdarzeń wypycha dane poza wymuszonymi limitami, nastąpi ograniczenie przepustowości i opóźnienia.

Na poniższej ilustracji przedstawiono środowisko Azure Time Series Insights, które ma jednostkę SKU S1 i pojemność 3. Może on przypadać na 3 000 000 zdarzeń dziennie.

[![Pojemność środowiska](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Wyobraź sobie środowisko, które pozyskuje komunikaty z centrum zdarzeń. Ma dzienny współczynnik ruchu przychodzącego dotyczący około 67 000 komunikatów. Ta częstotliwość tłumaczy na około 46 komunikatów co minutę.

* Jeśli każdy komunikat centrum zdarzeń zostanie spłaszczony do pojedynczego zdarzenia Azure Time Series Insights, ograniczenie nie wystąpi.
* Jeśli każdy komunikat centrum zdarzeń zostanie spłaszczony do 100 zdarzeń Azure Time Series Insights, zdarzenia 4 600 należy pozyskać co minutę.

Środowisko jednostki SKU S1 o pojemności 3 może przypadać tylko 2 100 zdarzeń co minutę (1 000 000 zdarzeń dziennie = 700 zdarzeń na minutę w trzech jednostkach = 2 100 zdarzeń na minutę).

Aby uzyskać ogólne omówienie sposobu działania logiki spłaszczania, zobacz [obsługiwane kształty JSON](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Zalecane rozwiązania do nadmiernego ograniczania przepustowości

Aby rozwiązać zwłokę, Zwiększ pojemność jednostki SKU środowiska. Aby uzyskać więcej informacji, zobacz [skalowanie środowiska Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Przyczyna B: początkowe pozyskiwanie danych historycznych spowalnia ruch przychodzący

Jeśli połączysz istniejące źródło zdarzeń, prawdopodobnie Centrum IoT lub centrum zdarzeń zawiera już dane. Środowisko rozpoczyna ściąganie danych od początku okresu przechowywania komunikatów źródła zdarzeń. Nie można zastąpić tego domyślnego przetwarzania. Ograniczenie można obsłużyć. Ograniczanie wydajności może potrwać trochę czasu, aby można było wychwycić dane historyczne.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Zalecane rozwiązania dla dużych początkowych pozyskiwania

Aby rozwiązać zwłokę:

1. Zwiększ pojemność jednostki SKU do maksymalnej dozwolonej wartości (10, w tym przypadku). Po zwiększeniu wydajności proces transferu danych przychodzących rozpocznie się znacznie szybciej. Opłata jest naliczana za zwiększoną pojemność. Aby wizualizować, jak szybko przechwytuje, możesz wyświetlić wykres dostępności w [Eksploratorze Azure Time Series Insights](https://insights.timeseries.azure.com).

2. Gdy zwłoka zostanie przechwycona, Zmniejsz pojemność jednostki SKU do normalnej stawki za transfer danych przychodzących.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problem: dane były wyświetlane wcześniej, ale nie są już wyświetlane

Jeśli Azure Time Series Insights nie pobiera już danych, ale Zdarzenia nadal są przesyłane strumieniowo do centrum IoT Hub lub centrum zdarzeń, weź pod uwagę tę potencjalną przyczynę.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Przyczyna: klucz dostępu centrum został wygenerowany ponownie, a Twoje środowisko wymaga aktualizacji

Ten problem występuje, gdy klucz podany podczas tworzenia źródła zdarzenia nie jest już prawidłowy. W centrum zobaczysz dane telemetryczne, ale w Azure Time Series Insights nie odebrano komunikatów przychodzących. Jeśli nie masz pewności, czy klucz został wygenerowany ponownie, możesz przeszukać dziennik aktywności centrum zdarzeń pod kątem "Tworzenie lub aktualizowanie reguł autoryzacji przestrzeni nazw". W przypadku usługi IoT Hub Wyszukaj ciąg "Tworzenie lub aktualizowanie zasobu IotHub".

Aby zaktualizować środowisko Azure Time Series Insights przy użyciu nowego klucza, Otwórz zasób centrum w Azure Portal i skopiuj nowy klucz. Przejdź do zasobu Azure Time Series Insights i wybierz pozycję **źródła zdarzeń**:

   [![Wybierz źródła zdarzeń](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Wybierz źródło lub źródła zdarzenia, z którego zostało zatrzymane pozyskiwanie, Wklej w nowym kluczu, a następnie wybierz pozycję **Zapisz**:

   [![Wklej w nowym kluczu](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: ustawienie nazwy właściwości sygnatury czasowej źródła zdarzenia nie działa

Upewnij się, że wartość właściwości timestamp, która pochodzi ze źródła zdarzenia jako ciąg JSON, jest w formacie _RRRR-MM-DDTgg: mm: SS. FFFFFFFK_. Oto przykład: **2008-04-12T12:53Z**.

Należy pamiętać, że w nazwie właściwości sygnatury czasowej rozróżniana jest wielkość liter.

Najprostszym sposobem, aby upewnić się, że nazwa właściwości sygnatury czasowej została przechwycona i działa prawidłowo, to użycie Eksploratora Azure Time Series Insights. W Eksploratorze Azure Time Series Insights przy użyciu wykresu Wybierz okres czasu po wprowadzeniu nazwy właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie, a następnie wybierz polecenie **Eksploruj zdarzenia**.

Pierwszy nagłówek kolumny powinien być nazwą właściwości sygnatury czasowej. Obok **sygnatury czasowej**wyrazu zostanie wyświetlona wartość **($TS)** .

Następujące wartości nie będą wyświetlane:

* *(ABC)*: wskazuje, że Azure Time Series Insights odczytuje wartości danych jako ciągi.
* *Ikona kalendarza*: wskazuje, że Azure Time Series Insights odczytuje wartości danych jako wartości DateTime.
* *#*: Wskazuje, że Azure Time Series Insights odczytuje wartości danych jako liczby całkowite.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [, jak ograniczyć opóźnienia w Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

* Dowiedz się [, jak skalować środowisko Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
