---
title: Stany zadań Azure Stream Analytics
description: W tym artykule opisano cztery różne stany zadania Stream Analyticsowego. Uruchamianie, zatrzymywanie, obniżanie wydajności i niepowodzenie.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: fc46b4f24c3c21cda190f80556373991f6c711c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82130939"
---
# <a name="azure-stream-analytics-job-states"></a>Stany zadań Azure Stream Analytics

Zadanie Stream Analytics może znajdować się w jednym z czterech stanów w dowolnym momencie: uruchamianie, zatrzymywanie, obniżanie wydajności lub niepowodzenie. Stan zadania można znaleźć na stronie Przegląd zadania Stream Analytics w Azure Portal. 

| Stan | Opis | Zalecane akcje |
| --- | --- | --- |
| **Uruchomienie** | Zadanie jest uruchamiane na platformie Azure zdarzenia, które pochodzą ze zdefiniowanych źródeł wejściowych, przetwarzania i zapisywania wyników do skonfigurowanych ujścia danych wyjściowych. | Najlepszym rozwiązaniem jest śledzenie wydajności zadania przez monitorowanie [kluczowych metryk](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Zatrzymano** | Zadanie zostało zatrzymane i nie przetwarza zdarzeń. | Nie dotyczy | 
| **Obniżona wydajność** | Mogą występować sporadyczne problemy z połączeniami wejściowymi i wyjściowymi. Te błędy są nazywane błędami przejściowymi, co może spowodować, że zadanie wprowadzi stan obniżonej sprawności. Stream Analytics natychmiast podejmie próbę odzyskania błędów i powrotu do stanu uruchomienia (w ciągu kilku minut). Te błędy mogą wystąpić z powodu problemów z siecią, dostępności innych zasobów platformy Azure, błędów deserializacji itp. Wydajność zadania może mieć wpływ, gdy zadanie jest w stanie obniżonej wydajności.| Aby dowiedzieć się więcej o przyczynach błędów przejściowych, można zapoznać się z [dziennikami diagnostyki lub aktywności](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) . W przypadkach takich jak błędy deserializacji zaleca się podejmowanie działań naprawczych w celu zapewnienia, że zdarzenia nie są źle sformułowane. Jeśli zadanie utrzymuje limit wykorzystania zasobów, spróbuj zwiększyć numer SU lub [zrównoleglanie zadanie](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). W innych przypadkach, gdy nie można wykonać żadnych działań, Stream Analytics podejmie próbę odzyskania do stanu *uruchomienia* . <br> Możesz użyć metryki [opóźnienia znaku wodnego](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) , aby zrozumieć, czy te błędy przejściowe wpływają na wydajność zadania.|
| **Awarii** | W zadaniu Wystąpił błąd krytyczny, który spowodowało awarię. Zdarzenia nie są odczytywane i przetwarzane. Błędy środowiska uruchomieniowego są typową przyczyną zadań zakończonych niepowodzeniem. | Alerty można [skonfigurować](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) w taki sposób, aby otrzymywać powiadomienia o stanie niepowodzenia zadania. <br> <br>Można debugować za pomocą [dzienników działań i zasobów](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) , aby identyfikować główną przyczynę i rozwiązać problem.|

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie alertów dla zadań Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Metryki dostępne w Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Rozwiązywanie problemów przy użyciu dzienników aktywności i zasobów](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
