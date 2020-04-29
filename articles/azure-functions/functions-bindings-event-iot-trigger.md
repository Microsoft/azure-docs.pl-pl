---
title: Powiązania IoT Hub platformy Azure dla Azure Functions
description: Dowiedz się, jak odpowiedzieć na zdarzenia wysyłane do strumienia zdarzeń centrum IoT w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77589729"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure IoT Hub dla Azure Functions

W tym artykule opisano sposób pracy z Azure Functions powiązaniami IoT Hub. Obsługa IoT Hub jest oparta na [usłudze Azure Event Hubs Binding](functions-bindings-event-hubs.md).

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Chociaż poniższe przykłady kodu używają interfejsu API centrum zdarzeń, dana składnia ma zastosowanie do funkcji IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Następne kroki

- [Zapisywanie zdarzeń w strumieniu zdarzeń (powiązanie danych wyjściowych)](./functions-bindings-event-iot-output.md)
