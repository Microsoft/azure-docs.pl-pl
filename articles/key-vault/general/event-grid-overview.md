---
title: Key Vault monitorowania z Azure Event Grid
description: Subskrybowanie zdarzeń Key Vault za pomocą Azure Event Grid
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ee38196f0b1a6e00d385dc0d2c88d45a4291d3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90087440"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Key Vault monitorowania z Azure Event Grid

Key Vault integracja z usługą Event Grid umożliwia użytkownikom otrzymywanie powiadomień o zmianie stanu wpisu tajnego przechowywanego w magazynie kluczy. Zmiana stanu jest definiowana jako klucz tajny, który wkrótce wygaśnie (30 dni przed wygaśnięciem), klucz tajny, który wygasł, lub wpis tajny, który ma dostępną nową wersję. Obsługiwane są powiadomienia dla wszystkich trzech typów tajnych (kluczy, certyfikatów i wpisów tajnych).

Aplikacje mogą reagować na te zdarzenia przy użyciu nowoczesnych architektur bezserwerowych, bez potrzeby skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zdarzenia są wypychane za pomocą [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obsługi zdarzeń, takich jak [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)lub nawet do własnego elementu webhook i płacisz tylko za to, czego używasz. Aby uzyskać informacje o cenach, zobacz [Cennik usługi Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Key Vault zdarzenia i schematy

Funkcja Event Grid używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Zdarzenia Key Vault zawierają wszystkie informacje potrzebne do reagowania na zmiany danych. Można zidentyfikować zdarzenie Key Vault, ponieważ właściwość eventType rozpoczyna się od "Microsoft. kluczy.

Aby uzyskać więcej informacji, zobacz [schemat zdarzeń Key Vault](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Zdarzenia powiadomień są wyzwalane tylko w nowych wersjach kluczy tajnych, kluczy i certyfikatów i należy najpierw subskrybować zdarzenie w magazynie kluczy, aby otrzymywać te powiadomienia.

## <a name="practices-for-consuming-events"></a>Praktyki związane z zużywaniem zdarzeń

Aplikacje, które obsługują zdarzenia Key Vault, powinny spełniać kilka zalecanych praktyk:

* Można skonfigurować wiele subskrypcji, aby kierować zdarzenia do tego samego programu obsługi zdarzeń. Ważne jest, aby nie przyjmować zdarzeń z konkretnego źródła, ale w celu sprawdzenia tematu komunikatu, aby upewnić się, że pochodzi on z magazynu kluczy, którego oczekujesz.
* Podobnie Sprawdź, czy typ zdarzenia jest przygotowana do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwanymi typami.
* Ignoruj pola, które nie są zrozumiałe.  Ta metoda pomaga w zachowaniu odporności na nowe funkcje, które mogą zostać dodane w przyszłości.
* Użyj prefiksu "podmiot" i dopasowania sufiksu, aby ograniczyć zdarzenia do określonego zdarzenia.

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure Key Vault](overview.md)
- [Omówienie usługi Azure Event Grid](../../event-grid/overview.md)
- Instrukcje: [kierowanie zdarzeń Key Vault do elementu Runbook usługi Automation](event-grid-tutorial.md).
- Instrukcje: [otrzymywanie wiadomości e-mail po zmianie wpisu tajnego magazynu kluczy](event-grid-logicapps.md)
- [Schemat zdarzeń Azure Event Grid dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Przegląd Azure Automation](../../automation/index.yml)
