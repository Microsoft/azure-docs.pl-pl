---
title: Powiązania IoT Hub platformy Azure dla Azure Functions
description: Dowiedz się, jak używać wyzwalacza IoT Hub i powiązania w programie Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77586137"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Powiązania IoT Hub platformy Azure dla Azure Functions

Ten zestaw artykułów wyjaśnia, jak współpracować z Azure Functions powiązaniami IoT Hub. Obsługa IoT Hub jest oparta na [usłudze Azure Event Hubs Binding](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> Chociaż poniższe przykłady kodu używają interfejsu API centrum zdarzeń, dana składnia ma zastosowanie do funkcji IoT Hub.

| Akcja | Typ |
|--------|------|
| Odpowiadanie na zdarzenia wysyłane do strumienia zdarzeń centrum IoT Hub. | [Wyzwalacz](./functions-bindings-event-iot-trigger.md) |
| Zapisuj zdarzenia w strumieniu zdarzeń IoT | [Powiązanie danych wyjściowych](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Następne kroki

- [Odpowiadanie na zdarzenia wysyłane do strumienia zdarzeń centrum zdarzeń (wyzwalacz)](./functions-bindings-event-iot-trigger.md)
- [Zapisywanie zdarzeń w strumieniu zdarzeń (powiązanie danych wyjściowych)](./functions-bindings-event-iot-output.md)
