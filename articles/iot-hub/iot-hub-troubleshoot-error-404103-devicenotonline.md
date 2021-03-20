---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 404103 DeviceNotOnline
description: Dowiedz się, jak naprawić błąd 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960817"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

W tym artykule opisano przyczyny i rozwiązania **404103 błędów DeviceNotOnline** .

## <a name="symptoms"></a>Objawy

Bezpośrednia Metoda do urządzenia kończy się niepowodzeniem z błędem **404103 DeviceNotOnline** nawet wtedy, gdy urządzenie jest w trybie online. 

## <a name="cause"></a>Przyczyna

Jeśli wiesz, że urządzenie jest w trybie online i nadal pojawia się błąd, prawdopodobnie na urządzeniu nie zarejestrowano wywołania zwrotnego metody bezpośredniej.

## <a name="solution"></a>Rozwiązanie

Aby prawidłowo skonfigurować urządzenie dla wywołań zwrotnych metody bezpośredniej, zobacz [Obsługa metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).