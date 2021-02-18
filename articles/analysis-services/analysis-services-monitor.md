---
title: Monitorowanie metryk serwera Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak Analysis Services używać platformy Azure Eksplorator metryk, bezpłatnego narzędzia w portalu, aby ułatwić monitorowanie wydajności i kondycji serwerów.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1cc517ac3c903930eddb95a4813a8146cae2ec2c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582671"
---
# <a name="monitor-server-metrics"></a>Monitorowanie metryk serwera

Analysis Services dostarcza metryki na platformie Azure Eksplorator metryk, bezpłatnego narzędzia w portalu, które ułatwia monitorowanie wydajności i kondycji serwerów. Na przykład Monitoruj użycie pamięci i procesora, liczbę połączeń klientów i użycie zasobów zapytań. Analysis Services korzysta z tego samego środowiska monitorowania co w przypadku większości innych usług platformy Azure. Aby dowiedzieć się więcej, zobacz Rozpoczynanie [pracy z usługą Azure Eksplorator metryk](../azure-monitor/essentials/metrics-getting-started.md).

Aby bardziej szczegółowo przeprowadzić diagnostykę, śledzić wydajność i identyfikować trendy wielu zasobów usług w grupie zasobów lub subskrypcji, użyj [Azure monitor](../azure-monitor/overview.md). Azure Monitor (usługa) może spowodować powstanie usługi do rozliczenia.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Aby monitorować metryki dla serwera Analysis Services

1. W Azure Portal wybierz pozycję **metryki**.

    ![Monitorowanie w witrynie Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. W obszarze **Metryka** wybierz metryki do uwzględnienia na wykresie. 

    ![Monitorowanie wykresu](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metryki serwera

Użyj tej tabeli, aby określić, które metryki najlepiej sprawdzają się w scenariuszu monitorowania. Na tym samym wykresie może być pokazywanych tylko metryki tej samej jednostki.

|Metric|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Długość kolejki zadań puli poleceń|Liczba|Średnia|Liczba zadań w kolejce puli wątków poleceń.|
|Wartości CurrentConnections|Połączenie: bieżące połączenia|Liczba|Średnia|Bieżąca liczba ustanowionych połączeń klientów.|
|CurrentUserSessions|Bieżące sesje użytkowników|Liczba|Średnia|Bieżąca liczba ustanowionych sesji użytkowników.|
|mashup_engine_memory_metric|Pamięć aparatu M|Bajty|Średnia|Użycie pamięci przez procesy aparatu mashupów|
|mashup_engine_qpu_metric|M QPU aparatu|Liczba|Średnia|QPU użycie przez procesy aparatu mashupów|
|memory_metric|Pamięć|Bajty|Średnia|Memory (pamięć). Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|
|memory_thrashing_metric|Przeładowywanie pamięci|Procent|Średnia|Średnia pamięć migotanie.|
|CleanerCurrentPrice|Pamięć: bieżąca cena oczyszczarki|Liczba|Średnia|Bieżąca cena pamięci, $/Byte/Time, znormalizowana do 1000.|
|CleanerMemoryNonshrinkable|Pamięć: pamięć oczyszczarki niemożliwa do zmniejszenia|Bajty|Średnia|Ilość pamięci (w bajtach), która nie podlega wyprzeczyszczaniu przez oczyszczarkę w tle.|
|CleanerMemoryShrinkable|Pamięć: zmniejszanie ilości pamięci czyszczącej|Bajty|Średnia|Ilość pamięci, w bajtach, podlegająca przeczyszczaniu przez oczyszczarkę w tle.|
|MemoryLimitHard|Pamięć: sztywny limit pamięci|Bajty|Średnia|Limit pamięci twardej z pliku konfiguracji.|
|MemoryLimitHigh|Pamięć: limit pamięci jest wysoki|Bajty|Średnia|Górny limit pamięci z pliku konfiguracji.|
|MemoryLimitLow|Pamięć: limit pamięci — niski|Bajty|Średnia|Limit braku pamięci z pliku konfiguracji.|
|MemoryLimitVertiPaq|Pamięć: limit pamięci — tryb VertiPaq|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|
|MemoryUsage|Pamięć: użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera jako używany do obliczania ceny pamięci czyszczącej. Równe licznikowi Process\PrivateBytes i rozmiar danych mapowanych na pamięć, ignorowanie wszelkich pamięci, które zostały zamapowane lub przydzielone przez aparat analityczny w pamięci (VertiPaq) przekraczające limit pamięci aparatu.|
|private_bytes_metric|Bajty prywatne |Bajty|Średnia|Całkowita ilość pamięci przydzielona przez proces aparatu Analysis Services i procesy kontenera mashupów, bez uwzględnienia pamięci współdzielonej z innymi procesami.|
|virtual_bytes_metric|Bajty wirtualne |Bajty|Średnia|Bieżący rozmiar wirtualnej przestrzeni adresowej używanej przez proces aparatu i procesy kontenera mashupów Analysis Services.|
|mashup_engine_private_bytes_metric|Liczba prywatnych bajtów aparatu M |Bajty|Średnia|Łączna ilość pamięci w procesach kontenera programu mashup pamięć została przydzielona, bez uwzględnienia pamięci współdzielonej z innymi procesami.|
|mashup_engine_virtual_bytes_metric|Liczba bajtów wirtualnych aparatu M |Bajty|Średnia|Bieżący rozmiar wirtualnej przestrzeni adresowej, z której korzystają procesy kontenera mashupu.|
|limit przydziału|Pamięć: limit przydziału|Bajty|Średnia|Bieżący limit pamięci (w bajtach). Przydział pamięci jest również nazywany przydziałem pamięci lub rezerwacją pamięci.|
|QuotaBlocked|Pamięć: zablokowany limit przydziału|Liczba|Średnia|Bieżąca liczba żądań przydziałów blokowanych do momentu zwolnienia innych limitów przydziału pamięci.|
|VertiPaqNonpaged|Pamięć: VertiPaq niestronicowana|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|
|VertiPaqPaged|Pamięć: stronicowana na stronie VertiPaq|Bajty|Średnia|Bajty stronicowanej pamięci w użyciu dla danych w pamięci.|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Liczba|Średnia|Liczba zadań innych niż zadania we/wy w kolejce puli wątków przetwarzania.|
|RowsConvertedPerSec|Przetwarzanie: przekonwertowane wiersze na sekundę|CountPerSecond|Średnia|Szybkość wierszy przekonwertowanych podczas przetwarzania.|
|RowsReadPerSec|Przetwarzanie: odczytane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|
|RowsWrittenPerSec|Przetwarzanie: liczba wierszy na sekundę|CountPerSecond|Średnia|Częstotliwość wierszy zapisywana podczas przetwarzania.|
|qpu_metric|QPU|Liczba|Średnia|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|
|QueryPoolBusyThreads|Wątki zajęte w puli zapytań|Liczba|Średnia|Liczba zajętych wątków w puli wątków zapytań.|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Średnia|Szybkość pomyślnych połączeń.|
|CommandPoolBusyThreads|Wątki: zajęte wątki w puli poleceń|Liczba|Średnia|Liczba zajętych wątków w puli wątków poleceń.|
|CommandPoolIdleThreads|Wątki: bezczynne wątki w puli poleceń|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|
|LongParsingBusyThreads|Wątki: zajęte wątki o długotrwałej analizie|Liczba|Średnia|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|
|LongParsingIdleThreads|Wątki: długotrwałe wątki bezczynne analizy|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|
|LongParsingJobQueueLength|Wątki: Długość kolejki zadań o długotrwałej analizie|Liczba|Średnia|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|
|ProcessingPoolBusyIOJobThreads|Wątki: wątki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|
|ProcessingPoolBusyNonIOThreads|Wątki: zajęte wątki innych niż we/wy puli przetwarzania|Liczba|Średnia|Liczba wątków uruchamiających zadania inne niż we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleIOJobThreads|Wątki: wątki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleNonIOThreads|Wątki: bezczynne wątki inne niż we/wy puli przetwarzania|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż zadania we/wy.|
|QueryPoolIdleThreads|Wątki: bezczynne wątki puli zapytań|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytań.|
|ShortParsingBusyThreads|Wątki: zajęte wątki z krótkim analizowaniem|Liczba|Średnia|Liczba zajętych wątków w puli wątków o krótkiej analizie.|
|ShortParsingIdleThreads|Wątki: bezczynne wątki z krótkim analizowaniem|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków o krótkiej analizie.|
|ShortParsingJobQueueLength|Wątki: Długość kolejki zadań o krótkiej analizie|Liczba|Średnia|Liczba zadań w kolejce puli wątków o krótkiej analizie.|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Liczba|Średnia|Łączna liczba nieudanych prób połączenia.|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Liczba|Średnia|Łączna liczba żądań połączenia. |

## <a name="next-steps"></a>Następne kroki
[Przegląd Azure Monitor](../azure-monitor/overview.md)      
[Wprowadzenie do usługi Azure Eksplorator metryk](../azure-monitor/essentials/metrics-getting-started.md)      
[Metryki w interfejsie API REST Azure Monitor](/rest/api/monitor/metrics)
