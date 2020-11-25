---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: ff5d04a2923f16c763e1529ecb365f60d6275ca2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027188"
---
### <a name="general-query-limits"></a>Ogólne limity zapytania

| Limit | Opis |
|:---|:---|
| Język zapytań | Azure Monitor używa tego samego [języka zapytań Kusto](/azure/kusto/query/) jako platformy Azure Eksplorator danych. Zobacz, [Azure monitor różnice w języku zapytań dziennika](/azure/data-explorer/kusto/query/) dla elementów języka KQL nie są obsługiwane w programie Azure monitor. |
| Regiony świadczenia usługi Azure | Zapytania dziennika mogą wystąpić nadmierne obciążenie, gdy dane zajmują Log Analytics obszary robocze w wielu regionach świadczenia usługi Azure. Szczegóły można znaleźć w temacie [limity zapytań](../articles/azure-monitor/log-query/scope.md#query-scope-limits) . |
| Zapytania obejmujące wiele zasobów | Maksymalna liczba zasobów Application Insights i Log Analytics obszarów roboczych w pojedynczym zapytaniu ograniczonym do 100.<br>Zapytanie krzyżowe nie jest obsługiwane w projektancie widoków.<br>Zapytanie między zasobami w ramach alertów dziennika jest obsługiwane w nowym interfejsie API scheduledQueryRules.<br>Aby uzyskać szczegółowe informacje, zobacz [limity zapytań między zasobami](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |

### <a name="user-query-throttling"></a>Ograniczanie kwerendy użytkownika
Azure Monitor ma kilka ograniczeń ograniczania ochrony przed użytkownikami wysyłającymi zbyt wiele zapytań. Takie zachowanie może potencjalnie spowodować Przeciążenie zasobów zaplecza systemu i zagrozić odpowiedzi usługi. Poniższe limity zostały zaprojektowane w celu ochrony klientów przed zakłóceniami i zapewnienia spójnego poziomu usług. Ograniczanie i limity użytkowników są przeznaczone do wpływu na tylko ekstremalny scenariusz użycia i nie powinny być istotne w przypadku typowego użycia.


| Measure | Ogranicz na użytkownika | Opis |
|:---|:---|:---|
| Zapytania współbieżne | 5 | Jeśli dla użytkownika jest już 5 zapytań, wszystkie nowe zapytania są umieszczane w kolejce współbieżności dla poszczególnych użytkowników. Po zakończeniu jednego z uruchomionych zapytań następne zapytanie zostanie pobrane z kolejki i rozpoczęte. Nie obejmuje to zapytań z reguł alertów.
| Czas w kolejce współbieżności | 3 minuty | Jeśli zapytanie znajduje się w kolejce przez więcej niż 3 minuty bez uruchomienia, zostanie zakończone z odpowiedzią na błąd HTTP z kodem 429. |
| Łączna liczba zapytań w kolejce współbieżności | 200 | Gdy liczba zapytań w kolejce osiągnie 200, wszelkie dodatkowe zapytania zostaną odrzucone z kodem błędu HTTP 429. Ta liczba jest uzupełnieniem 5 zapytań, które mogą być uruchamiane jednocześnie. |
| Częstotliwość zapytań | 200 zapytań na 30 sekund | Jest to ogólna szybkość, z jaką zapytania mogą być przesyłane przez jednego użytkownika do wszystkich obszarów roboczych.  Ten limit dotyczy zapytań programistycznych lub zapytań inicjowanych przez części wizualizacji, takich jak pulpity nawigacyjne platformy Azure i strona podsumowania obszaru roboczego Log Analytics. |

- Zoptymalizuj zapytania zgodnie z opisem w temacie [Optymalizacja zapytań dzienników w Azure monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Pulpity nawigacyjne i skoroszyty mogą zawierać wiele zapytań w jednym widoku, które generują szereg zapytań przy każdym załadowaniu lub odświeżeniu. Rozważ ich rozdzielenie na wiele widoków, które ładują się na żądanie. 
- W Power BI Rozważ wyodrębnienie tylko zagregowanych wyników zamiast dzienników nieprzetworzonych.