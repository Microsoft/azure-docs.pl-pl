---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 504101 GatewayTimeout
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81759556"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

W tym artykule opisano przyczyny i rozwiązania **504101 błędów GatewayTimeout** .

## <a name="symptoms"></a>Objawy

Gdy próba wywołania metody bezpośredniej z IoT Hub na urządzenie, żądanie kończy się niepowodzeniem z błędem **504101 GatewayTimeout**.

## <a name="cause"></a>Przyczyna

### <a name="cause-1"></a>Przyczyna 1

IoT Hub napotkał błąd i nie może potwierdzić, czy metoda bezpośrednia została ukończona przed upływem limitu czasu.

### <a name="cause-2"></a>Przyczyna 2

W przypadku korzystania ze starszej wersji zestawu SDK języka C# usługi Azure IoT (<1.19.0), link AMQP między urządzeniem i IoT Hub może zostać porzucony dyskretnie z powodu błędu.

## <a name="solution"></a>Rozwiązanie

### <a name="solution-1"></a>Rozwiązanie 1

Wydaj ponowną próbę.

### <a name="solution-2"></a>Rozwiązanie 2

Uaktualnij do najnowszej wersji zestawu SDK języka C# usługi Azure IOT.