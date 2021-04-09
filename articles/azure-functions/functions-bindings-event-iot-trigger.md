---
title: Wyzwalacz usługi Azure IoT Hub dla Azure Functions
description: Dowiedz się, jak odpowiedzieć na zdarzenia wysyłane do strumienia zdarzeń centrum IoT w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612290"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure IoT Hub dla Azure Functions

W tym artykule opisano sposób pracy z Azure Functions powiązaniami IoT Hub. Obsługa IoT Hub jest oparta na [usłudze Azure Event Hubs Binding](functions-bindings-event-hubs.md).

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Chociaż poniższe przykłady kodu używają interfejsu API centrum zdarzeń, dana składnia ma zastosowanie do funkcji IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.jswłaściwości

[host.jsw](functions-host-json.md#eventhub) pliku zawiera ustawienia kontrolujące zachowanie wyzwalacza centrum zdarzeń. Aby uzyskać szczegółowe informacje na temat dostępnych ustawień, zobacz sekcję [host.jsw ustawieniach](functions-bindings-event-iot.md#hostjson-settings) .

## <a name="next-steps"></a>Następne kroki

- [Zapisywanie zdarzeń w strumieniu zdarzeń (powiązanie danych wyjściowych)](./functions-bindings-event-iot-output.md)
