---
title: Usługa Azure cache for Redis — Omówienie Event Grid
description: Użyj Azure Event Grid, aby opublikować pamięć podręczną platformy Azure dla zdarzeń Redis.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056306"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Usługa Azure cache for Redis — Omówienie Event Grid 

Usługa Azure cache dla zdarzeń Redis, takich jak funkcja poprawek, skalowanie, importowanie/eksportowanie (RDB), jest wypychana przy użyciu [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak Azure Functions, Azure Logic Apps lub nawet do własnego odbiornika http. Event Grid zapewnia niezawodne dostarczanie zdarzeń do aplikacji przez rozbudowane zasady ponawiania prób i utraconych wiadomości.

Zobacz artykuł dotyczący [schematu zdarzeń usługi Azure cache for Redis](../event-grid/event-schema-azure-cache.md) , aby wyświetlić pełną listę zdarzeń obsługiwanych przez usługę Azure cache for Redis.

Jeśli chcesz wypróbować usługę Azure cache dla zdarzeń Redis, zobacz dowolny z tych przewodników szybki start:

|Jeśli chcesz użyć tego narzędzia:    |Zobacz ten przewodnik Szybki Start: |
|--|-|
|Witryna Azure Portal    |[Szybki Start: kierowanie pamięci podręcznej platformy Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu Azure Portal](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Szybki Start: kierowanie pamięci podręcznej Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu programu PowerShell](cache-event-grid-quickstart-powershell.md)|
|Interfejs wiersza polecenia platformy Azure    |[Szybki Start: kierowanie pamięci podręcznej Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu interfejsu wiersza polecenia](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Model zdarzenia

Event Grid używa [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Ten obraz przedstawia relację między wydawcami zdarzeń, subskrypcjami zdarzeń i programami obsługi zdarzeń.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Model siatki zdarzeń.":::

Najpierw Zasubskrybuj punkt końcowy do zdarzenia. Następnie po wyzwoleniu zdarzenia usługa Event Grid wyśle dane dotyczące tego zdarzenia do punktu końcowego.

Zobacz artykuł dotyczący [zdarzeń usługi Azure cache for Redis](../event-grid/event-schema-azure-cache.md) , aby wyświetlić:

> [!div class="checklist"]
> * Pełna lista pamięci podręcznej platformy Azure dla zdarzeń Redis oraz sposobu wyzwalania każdego zdarzenia.
> * Przykład danych wysyłanych przez Event Grid dla każdego z tych zdarzeń.
> * Przeznaczenie poszczególnych par klucz-wartość, które pojawiają się w danych.


## <a name="best-practices-for-consuming-events"></a>Najlepsze rozwiązania związane z zużywaniem zdarzeń

Aplikacje obsługujące usługę Azure cache dla zdarzeń Redis powinny spełniać kilka zalecanych praktyk:
> [!div class="checklist"]
> * Jako że można skonfigurować wiele subskrypcji do kierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie przyjąć zdarzeń z określonego źródła, ale w celu sprawdzenia, czy pochodzi ona z pamięci podręcznej platformy Azure dla wystąpienia usługi Redis.
> * Podobnie Sprawdź, czy typ zdarzenia jest przygotowana do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwanymi typami.
> * Pamięć podręczna systemu Azure dla zdarzeń Redis gwarantuje co najmniej jednokrotne dostarczenie subskrybentom, co gwarantuje, że wszystkie komunikaty są zwracane. Jednak ze względu na ponowną próbę lub dostępność subskrypcji mogą czasami wystąpić zduplikowane komunikaty. Aby dowiedzieć się więcej na temat dostarczania komunikatów i ponawiania prób, zobacz [Event Grid dostarczania komunikatów i ponów próbę](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i Udostępnij pamięć podręczną platformy Azure dla zdarzeń Redis spróbuj wykonać następujące instrukcje:

- [Event Grid — informacje](../event-grid/overview.md)
- [Schemat zdarzeń usługi Azure cache for Redis](../event-grid/event-schema-azure-cache.md)
- [Kierowanie pamięci podręcznej platformy Azure pod kątem zdarzeń Redis do punktu końcowego usługi Web](cache-event-grid-quickstart-cli.md)
- [Kierowanie pamięci podręcznej platformy Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu Azure Portal](cache-event-grid-quickstart-portal.md)
- [Kierowanie pamięci podręcznej Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu programu PowerShell](cache-event-grid-quickstart-powershell.md)
