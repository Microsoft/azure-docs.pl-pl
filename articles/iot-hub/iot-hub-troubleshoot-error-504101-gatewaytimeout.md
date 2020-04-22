---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 504101 GatewayTimeout
description: Dowiedz się, jak naprawić błąd 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759556"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

W tym artykule opisano przyczyny i rozwiązania dla **błędów 504101 GatewayTimeout.**

## <a name="symptoms"></a>Objawy

Podczas próby wywołania metody bezpośredniej z Usługi IoT Hub na urządzenie, żądanie kończy się niepowodzeniem z błędem **504101 GatewayTimeout**.

## <a name="cause"></a>Przyczyna

### <a name="cause-1"></a>Przyczyna 1

Centrum IoT hub napotkał błąd i nie można potwierdzić, jeśli metoda bezpośrednia została ukończona przed limitem czasu.

### <a name="cause-2"></a>Przyczyna 2

Podczas korzystania z wcześniejszej wersji usługi Azure IoT C# SDK (<1.19.0), łącze AMQP między urządzeniem a centrum IoT hub można po cichu upuścić z powodu błędu.

## <a name="solution"></a>Rozwiązanie

### <a name="solution-1"></a>Rozwiązanie 1

Wystaw ponowienie próby.

### <a name="solution-2"></a>Rozwiązanie 2

Uaktualnij do najnowszej wersji SDK języka Azure IOT C#.