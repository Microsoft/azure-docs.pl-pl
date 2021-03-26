---
title: Aktualizacja urządzenia dla IoT Hub wymagania dotyczące sieci | Microsoft Docs
description: Aktualizacja urządzenia dla IoT Hub używa wielu portów sieciowych do różnych celów.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558384"
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

[Dowiedz się więcej](../iot-hub/iot-hub-devguide-protocols.md) na temat bieżącej listy obsługiwanych protokołów.
