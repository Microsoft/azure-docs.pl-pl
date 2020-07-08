---
title: dołączanie pliku
description: dołączanie pliku
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82072641"
---
### <a name="general-query-limits"></a>Ogólne limity zapytania

| Limit | Opis |
|:---|:---|
| Język zapytań | Azure Monitor używa tego samego [języka zapytań Kusto](/azure/kusto/query/) jako platformy Azure Eksplorator danych. Zobacz, [Azure monitor różnice w języku zapytań dziennika](../articles/azure-monitor/log-query/data-explorer-difference.md) dla elementów języka KQL nie są obsługiwane w programie Azure monitor. |
| Regiony świadczenia usługi Azure | Zapytania dziennika mogą wystąpić nadmierne obciążenie, gdy dane zajmują Log Analytics obszary robocze w wielu regionach świadczenia usługi Azure. Szczegóły można znaleźć w temacie [limity zapytań](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Zapytania między zasobami | Maksymalna liczba zasobów Application Insights i Log Analytics obszarów roboczych w pojedynczym zapytaniu ograniczonym do 100.<br>Zapytanie krzyżowe nie jest obsługiwane w projektancie widoków.<br>Zapytanie między zasobami w ramach alertów dziennika jest obsługiwane w nowym interfejsie API scheduledQueryRules.<br>Aby uzyskać szczegółowe informacje, zobacz [limity zapytań między zasobami](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |

### <a name="user-query-throttling"></a>Ograniczanie kwerendy użytkownika
Azure Monitor ma kilka ograniczeń ograniczania ochrony przed użytkownikami wysyłającymi zbyt wiele zapytań. Takie zachowanie może potencjalnie spowodować Przeciążenie zasobów zaplecza systemu i zagrozić odpowiedzi usługi. Poniższe limity zostały zaprojektowane w celu ochrony klientów przed zakłóceniami i zapewnienia spójnego poziomu usług. Ograniczanie i limity użytkowników są przeznaczone do wpływu na tylko ekstremalny scenariusz użycia i nie powinny być istotne w przypadku typowego użycia.


| Measure | Ogranicz na użytkownika | Opis |
|:---|:---|:---|
| Zapytania współbieżne | 5 | Jeśli dla użytkownika jest już 5 zapytań, wszystkie nowe zapytania są umieszczane w kolejce współbieżności dla poszczególnych użytkowników. Po zakończeniu jednego z uruchomionych zapytań następne zapytanie zostanie pobrane z kolejki i rozpoczęte. Nie obejmuje to zapytań z reguł alertów.
| Czas w kolejce współbieżności | 2,5 minut | Jeśli zapytanie znajduje się w kolejce przez ponad 2,5 minut bez uruchomienia, zostanie zakończone z odpowiedzią na błąd HTTP z kodem 429. |
| Łączna liczba zapytań w kolejce współbieżności | 40 | Gdy liczba zapytań w kolejce osiągnie 40, wszelkie dodatkowe zapytania zostaną odrzucone z kodem błędu HTTP 429. Ta liczba jest uzupełnieniem 5 zapytań, które mogą być uruchamiane jednocześnie. |
| Częstotliwość zapytań | 200 zapytań na 30 sekund | Jest to ogólna szybkość, z jaką zapytania mogą być przesyłane przez jednego użytkownika do wszystkich obszarów roboczych.  Ten limit dotyczy zapytań programistycznych lub zapytań inicjowanych przez części wizualizacji, takich jak pulpity nawigacyjne platformy Azure i strona podsumowania obszaru roboczego Log Analytics. |

- Zoptymalizuj zapytania zgodnie z opisem w temacie [Optymalizacja zapytań dzienników w Azure monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Pulpity nawigacyjne i skoroszyty mogą zawierać wiele zapytań w jednym widoku, które generują szereg zapytań przy każdym załadowaniu lub odświeżeniu. Rozważ ich rozdzielenie na wiele widoków, które ładują się na żądanie. 
- W Power BI Rozważ wyodrębnienie tylko zagregowanych wyników zamiast dzienników nieprzetworzonych.