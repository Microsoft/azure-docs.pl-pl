---
title: Aktualizacja urządzenia dla IoT Hub wymagania dotyczące sieci | Microsoft Docs
description: Aktualizacja urządzenia dla IoT Hub używa wielu portów sieciowych do różnych celów.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679582"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Porty używane z aktualizacją urządzenia dla IoT Hub
ADU używa różnych portów sieciowych do różnych celów.

## <a name="default-ports"></a>Porty domyślne

Przeznaczenie|Numer portu |
---|---
Pobierz z sieci/sieci CDN  | 80 (protokół HTTP)
Pobierz z MCC/sieci CDN | 80 (protokół HTTP)
Połączenie agenta ADU z usługą Azure IoT Hub  | 8883 (protokół MQTT)

## <a name="use-azure-iot-hub-supported-protocols"></a>Korzystanie z obsługiwanych protokołów IoT Hub platformy Azure
Agenta ADU można zmodyfikować w taki sposób, aby korzystał z dowolnego z obsługiwanych protokołów IoT Hub platformy Azure.

[Dowiedz się więcej](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) na temat bieżącej listy obsługiwanych protokołów.
