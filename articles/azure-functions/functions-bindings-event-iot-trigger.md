---
title: Wyzwalacz usługi Azure IoT Hub dla Azure Functions
description: Dowiedz się, jak odpowiedzieć na zdarzenia wysyłane do strumienia zdarzeń centrum IoT w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 25396da3fb9a4293633308bf2e9d3c6b3d07265d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87041648"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure IoT Hub dla Azure Functions

W tym artykule opisano sposób pracy z Azure Functions powiązaniami IoT Hub. Obsługa IoT Hub jest oparta na [usłudze Azure Event Hubs Binding](functions-bindings-event-hubs.md).

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Chociaż poniższe przykłady kodu używają interfejsu API centrum zdarzeń, dana składnia ma zastosowanie do funkcji IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Następne kroki

- [Zapisywanie zdarzeń w strumieniu zdarzeń (powiązanie danych wyjściowych)](./functions-bindings-event-iot-output.md)
