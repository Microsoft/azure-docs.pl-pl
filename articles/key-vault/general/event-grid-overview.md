---
title: Monitorowanie Key Vault za pomocą Azure Event Grid
description: Używanie Azure Event Grid do subskrybowania Key Vault zdarzeń
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 4fb6d57bb84f4a3b4c5c138be9306489191bfce8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753366"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Monitorowanie Key Vault za pomocą Azure Event Grid

Key Vault z usługą Event Grid umożliwia użytkownikom powiadomienie o zmianie stanu klucza tajnego przechowywanego w magazynie kluczy. Zmiana stanu jest definiowana jako klucz tajny, który ma wygasnąć (30 dni przed wygaśnięciem), klucz tajny, który wygasł, lub klucz tajny, który ma dostępną nową wersję. Obsługiwane są powiadomienia dla wszystkich trzech typów kluczy tajnych (klucz, certyfikat i klucz tajny).

Aplikacje mogą reagować na te zdarzenia przy użyciu nowoczesnych architektur bez serwera bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zdarzenia są wypychane Azure Event Grid do programów obsługi zdarzeń, takich jak [Azure Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet do własnego modułu webhook, i płacisz tylko za to, czego używasz. [](https://azure.microsoft.com/services/event-grid/) Aby uzyskać informacje o cenach, zobacz [Event Grid cennik.](https://azure.microsoft.com/pricing/details/event-grid/)

## <a name="key-vault-events-and-schemas"></a>Key Vault zdarzeń i schematów

Event Grid używa [subskrypcji zdarzeń do](../../event-grid/concepts.md#event-subscriptions) rozsyłania komunikatów o zdarzeniach do subskrybentów. Key Vault zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Możesz zidentyfikować zdarzenie Key Vault, ponieważ właściwość eventType rozpoczyna się od "Microsoft.KeyVault".

Aby uzyskać więcej informacji, zobacz [Key Vault schematu zdarzeń.](../../event-grid/event-schema-key-vault.md)

> [!WARNING]
> Zdarzenia powiadomień są wyzwalane tylko w nowych wersjach wpisów tajnych, kluczy i certyfikatów. Aby otrzymywać te powiadomienia, należy najpierw zasubskrybować zdarzenie w magazynie kluczy.

## <a name="practices-for-consuming-events"></a>Rozwiązania dotyczące konsumowania zdarzeń

Aplikacje, które obsługują Key Vault zdarzeń, powinny postępować zgodnie z kilkoma zalecanymi rozwiązaniami:

* Wiele subskrypcji można skonfigurować do przekierowywu zdarzeń do tej samej procedury obsługi zdarzeń. Ważne jest, aby nie zakładać, że zdarzenia pochodzą z określonego źródła, ale aby sprawdzić temat komunikatu, aby upewnić się, że pochodzi on z oczekiwanego magazynu kluczy.
* Podobnie sprawdź, czy typ eventType jest gotowy do przetwarzania i nie zakładaj, że wszystkie odbierane zdarzenia będą typami, których oczekujesz.
* Ignoruj pola, których nie rozumiesz.  Takie rozwiązanie pomoże zapewnić odporność na nowe funkcje, które mogą zostać dodane w przyszłości.
* Użyj dopasowania prefiksu i sufiksu "podmiot", aby ograniczyć zdarzenia do określonego zdarzenia.

## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault omówienie](overview.md)
- [Omówienie usługi Azure Event Grid](../../event-grid/overview.md)
- Przewodnik: [przekieruj zdarzenia Key Vault do runbook usługi Automation.](event-grid-tutorial.md)
- Dzieje się tak: [otrzymywanie wiadomości e-mail po zmianie klucza tajnego magazynu kluczy](event-grid-logicapps.md)
- [Azure Event Grid zdarzeń dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Azure Automation omówienie](../../automation/index.yml)
