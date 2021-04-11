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
ms.openlocfilehash: ed4341c1c8df588bf735bdc9c531c2165514d610
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061268"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

W tym artykule opisano przyczyny i rozwiązania **404103 błędów DeviceNotOnline** .

## <a name="symptoms"></a>Objawy

Bezpośrednia Metoda do urządzenia kończy się niepowodzeniem z błędem **404103 DeviceNotOnline** nawet wtedy, gdy urządzenie jest w trybie online. 

## <a name="cause"></a>Przyczyna

Jeśli wiesz, że urządzenie jest w trybie online i nadal pojawia się błąd, prawdopodobnie na urządzeniu nie zarejestrowano wywołania zwrotnego metody bezpośredniej.

## <a name="solution"></a>Rozwiązanie

Aby prawidłowo skonfigurować urządzenie dla wywołań zwrotnych metody bezpośredniej, zobacz [Obsługa metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).