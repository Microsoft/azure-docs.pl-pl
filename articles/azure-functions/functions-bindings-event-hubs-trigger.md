---
title: Wyzwalacz usługi Azure Event Hubs dla Azure Functions
description: Dowiedz się, jak używać wyzwalacza usługi Azure Event Hubs w programie Azure Functions.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608919"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure Event Hubs dla Azure Functions

W tym artykule opisano sposób pracy z wyzwalaczem [usługi Azure Event Hubs](../event-hubs/event-hubs-about.md) w przypadku Azure Functions. Azure Functions obsługuje powiązania wyzwalacza i [wyjścia](functions-bindings-event-hubs-output.md) dla Event Hubs.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>host.jsustawień

[host.jsw](functions-host-json.md#eventhub) pliku zawiera ustawienia kontrolujące zachowanie wyzwalacza centrum zdarzeń. Aby uzyskać szczegółowe informacje na temat dostępnych ustawień, zobacz sekcję [host.jsw ustawieniach](functions-bindings-event-hubs.md#hostjson-settings) .

## <a name="next-steps"></a>Następne kroki

- [Zapisywanie zdarzeń w strumieniu zdarzeń (powiązanie danych wyjściowych)](./functions-bindings-event-hubs-output.md)
