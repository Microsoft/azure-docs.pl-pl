---
title: Powiązanie danych wyjściowych IoT Hub platformy Azure dla Azure Functions
description: Dowiedz się, jak zapisywać komunikaty w strumieniach usługi Azure IoT Hub przy użyciu Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91871783"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Powiązanie danych wyjściowych IoT Hub platformy Azure dla Azure Functions

W tym artykule opisano sposób pracy z Azure Functions powiązaniami wyjściowymi IoT Hub. Obsługa IoT Hub jest oparta na [usłudze Azure Event Hubs Binding](functions-bindings-event-hubs.md).

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Chociaż poniższe przykłady kodu używają interfejsu API centrum zdarzeń, dana składnia ma zastosowanie do funkcji IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Następne kroki

- [Odpowiadanie na zdarzenia wysyłane do strumienia zdarzeń centrum zdarzeń (wyzwalacz)](./functions-bindings-event-iot-trigger.md)
