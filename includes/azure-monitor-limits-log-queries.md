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
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072641"
---
### <a name="general-query-limits"></a>Ogólne limity zapytań

| Limit | Opis |
|:---|:---|
| Język zapytań | Usługa Azure Monitor używa tego samego [języka zapytań Kusto](/azure/kusto/query/) co Eksplorator danych platformy Azure. Zobacz [różnice językowe zapytań dziennika usługi Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) dla elementów języka KQL, które nie są obsługiwane w usłudze Azure Monitor. |
| Regiony świadczenia usługi Azure | Zapytania dziennika mogą wystąpić nadmierne obciążenie, gdy dane obejmuje obszarów roboczych usługi Log Analytics w wielu regionach platformy Azure. Szczegółowe informacje można znaleźć w [limitach kwerend.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Kwerendy między zasobami | Maksymalna liczba zasobów usługi Application Insights i obszarów roboczych usługi Log Analytics w jednej kwerendzie ograniczona do 100.<br>Kwerenda między zasobami nie jest obsługiwana w projektancie widoku.<br>Kwerenda między zasobami w alertach dziennika jest obsługiwana w nowym interfejsie API scheduledQueryRules.<br>Szczegółowe informacje można znaleźć w [limitach zapytań między zasobami.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |

### <a name="user-query-throttling"></a>Ograniczanie liczby zapytań użytkowników
Usługa Azure Monitor ma kilka limitów ograniczania przepustowości, aby chronić przed użytkownikami wysyłającymi nadmierną liczbę zapytań. Takie zachowanie może potencjalnie przeciążyć zasoby wewnętrznej bazy danych systemu i zagrozić czasomierzu usługi. Poniższe limity mają na celu ochronę klientów przed przerwami w pracy i zapewnienie spójnego poziomu usług. Ograniczanie i limity użytkownika są przeznaczone do wpływu tylko scenariusz skrajnego użycia i nie powinny być istotne dla typowego użycia.


| Measure | Limit na użytkownika | Opis |
|:---|:---|:---|
| Zapytania współbieżne | 5 | Jeśli istnieje już 5 kwerend uruchomionych dla użytkownika, wszystkie nowe zapytania są umieszczane w kolejce współbieżności na użytkownika. Po zakończeniu jednego z uruchomionych kwerend następna kwerenda zostanie pobrana z kolejki i uruchomiona. Nie obejmuje to zapytań z reguł alertów.
| Czas w kolejce współbieżności | 2,5 minuty | Jeśli kwerenda znajduje się w kolejce przez więcej niż 2,5 minuty bez rozpoczęcia, zostanie zakończona z odpowiedzią na błąd HTTP z kodem 429. |
| Łączna liczba zapytań w kolejce współbieżności | 40 | Gdy liczba zapytań w kolejce osiągnie 40, wszelkie dodatkowe zapytania zostaną odrzucone z kodem błędu HTTP 429. Ta liczba jest dodatkiem do 5 zapytań, które mogą być uruchomione jednocześnie. |
| Szybkość zapytań | 200 zapytań na 30 sekund | Jest to ogólna szybkość, że kwerendy mogą być przesyłane przez jednego użytkownika do wszystkich obszarów roboczych.  Ten limit ma zastosowanie do zapytań programowych lub zapytań inicjowanych przez części wizualizacji, takie jak pulpity nawigacyjne platformy Azure i strona podsumowania obszaru roboczego usługi Log Analytics. |

- Optymalizuj zapytania zgodnie z opisem w [obszarze Optymalizuj zapytania dziennika w usłudze Azure Monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Pulpity nawigacyjne i skoroszyty mogą zawierać wiele zapytań w jednym widoku, które generują serię zapytań za każdym razem, gdy ładują się lub odświeżają. Należy rozważyć podział ich na wiele widoków, które ładują się na żądanie. 
- W usłudze Power BI należy rozważyć wyodrębnienie tylko zagregowanych wyników, a nie nieprzetworzonych dzienników.