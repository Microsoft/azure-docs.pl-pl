---
title: Monitorowanie magazynu kluczy za pomocą usługi Azure Event Grid
description: Subskrybowanie zdarzeń usługi Key Vault za pomocą usługi Azure Event Grid
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: cc12cc9a4828404e960aee239bd388af5b1ea3b7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431906"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Monitorowanie magazynu kluczy za pomocą usługi Azure Event Grid (wersja zapoznawcza)

Integracja magazynu kluczy z siatką zdarzeń jest obecnie w wersji zapoznawczej. Umożliwia użytkownikom powiadamianie o zmianie stanu klucza tajnego przechowywanego w magazynie kluczy. Zmiana stanu jest zdefiniowana jako klucz tajny, który wkrótce wygaśnie (w ciągu 30 dni od wygaśnięcia), klucz tajny, który wygasł lub klucz tajny, który ma dostępną nową wersję. Obsługiwane są powiadomienia dla wszystkich trzech typów tajnych (klucz, certyfikat i klucz tajny).

Aplikacje mogą reagować na te zdarzenia przy użyciu nowoczesnych architektur bezserwerowych, bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zdarzenia są wypychane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obsługi zdarzeń, takich jak usługi Azure [Functions,](https://azure.microsoft.com/services/functions/) [Usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), lub nawet do własnego elementu Webhook i płacisz tylko za to, czego używasz. Aby uzyskać informacje o cenach, zobacz [Ustalanie cen w siatce zdarzeń](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Zdarzenia i schematy magazynu kluczy

Siatka zdarzeń używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania wiadomości zdarzeń do subskrybentów. Zdarzenia usługi Key Vault zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Można zidentyfikować zdarzenie Usługi Key Vault, ponieważ właściwość eventType zaczyna się od "Microsoft.KeyVault".

Aby uzyskać więcej informacji, zobacz [schemat zdarzenia Usługi Key Vault](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Zdarzenia powiadomień są wyzwalane tylko w nowych wersjach wpisów tajnych, kluczy i certyfikatów i należy najpierw subskrybować zdarzenie w magazynie kluczy, aby otrzymywać te powiadomienia.
> 
> Zdarzenia powiadomień będą otrzymywane na certyfikatach tylko wtedy, gdy certyfikat zostanie automatycznie odnowiony zgodnie z zasadami określonymi dla certyfikatu.

## <a name="practices-for-consuming-events"></a>Praktyki dotyczące spożywania zdarzeń

Aplikacje obsługujące zdarzenia usługi Key Vault powinny stosować się do kilku zalecanych rozwiązań:

* Wiele subskrypcji można skonfigurować do kierowania zdarzeń do tego samego programu obsługi zdarzeń. Ważne jest, aby nie zakładać, że zdarzenia pochodzą z określonego źródła, ale aby sprawdzić temat wiadomości, aby upewnić się, że pochodzi z magazynu kluczy, którego oczekujesz.
* Podobnie sprawdź, czy eventType jest jednym jesteś przygotowany do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymasz będą typy, których oczekujesz.
* Ignoruj pola, których nie rozumiesz.  Ta praktyka pomoże Ci zachować odporność na nowe funkcje, które mogą być dodawane w przyszłości.
* Użyj prefiksu "tematu" i dopasowania sufiksu, aby ograniczyć zdarzenia do określonego zdarzenia.

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Key Vault](overview.md))
- [Omówienie usługi Azure Event Grid](../../event-grid/overview.md)
- Jak: [Kierowanie zdarzeń magazynu kluczy do uruchomieniu księgi uruchomieniu automatyzacji (wersja zapoznawcza)](event-grid-tutorial.md).
- Jak: [Odbieranie wiadomości e-mail po zmianie klucza tajnego magazynu](event-grid-logicapps.md)
- [Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Key Vault (wersja zapoznawcza)](../../event-grid/event-schema-key-vault.md)
- [Omówienie usługi Azure Automation](../../automation/index.yml)
