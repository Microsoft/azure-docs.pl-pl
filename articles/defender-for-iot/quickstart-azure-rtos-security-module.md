---
title: 'Szybki Start: Konfigurowanie i Włączanie usługi Defender-IoT-Micro-Agent dla platformy Azure RTO'
description: Dowiedz się, jak dołączyć i włączyć usługę Defender-IoT-Micro-Agent for Azure RTO na platformie IoT Hub Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 3c1af1128b99cbd3263ddffc834eb27ab9dec564
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489849"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Szybki Start: Defender-IoT-Micro-Agent for Azure RTO (wersja zapoznawcza)

Ten artykuł zawiera informacje o wymaganiach wstępnych przed rozpoczęciem i wyjaśniono, jak włączyć usługę Defender-IoT-Micro-Agent for Azure RTO na IoT Hub. Jeśli obecnie nie masz IoT Hub, zobacz [tworzenie IoT Hub przy użyciu Azure Portal](../iot-hub/iot-hub-create-through-portal.md) , aby rozpocząć pracę.

## <a name="prerequisites"></a>Wymagania wstępne 

### <a name="supported-devices"></a>Obsługiwane urządzenia

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikroukład SAM E54 Xplained Pro EVK

Pobieraj, Kompiluj i uruchamiaj jeden z plików ZIP dla określonej tablicy i narzędzia (IAR, IDE lub PC) wybranego z usługi [Defender-IoT-Micro-Agent for Azure RTO GitHub](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Zasoby platformy Azure

Następny etap rozpoczynania pracy polega na przygotowaniu zasobów platformy Azure. Będziesz potrzebować IoT Hub i sugerujemy obszar roboczy Log Analytics. W przypadku IoT Hub potrzebne będą IoT Hub parametry połączenia w celu nawiązania połączenia z urządzeniem. 
  
### <a name="iot-hub-connection"></a>Połączenie IoT Hub

Aby rozpocząć, musisz mieć połączenie IoT Hub. 

1. Otwórz **IoT Hub** w Azure Portal.

1. Przejdź do **urządzeń IoT**.

1. Wybierz przycisk **Utwórz**.

1. Skopiuj parametry połączenia IoT do [pliku konfiguracji](how-to-azure-rtos-security-module.md).

Poświadczenia połączeń są pobierane z konfiguracji aplikacji użytkownika **HOST_NAME**, **DEVICE_ID** i **DEVICE_SYMMETRIC_KEY**.

Usługa Defender-IoT-Micro-Agent for Azure RTO korzysta z połączeń usługi Azure IoT pośredniczącego na podstawie protokołu **MQTT** .

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby zakończyć konfigurowanie i dostosowywanie rozwiązania.

> [!div class="nextstepaction"]
> [Konfigurowanie i dostosowywanie usługi Defender-IoT-Micro-Agent for Azure RTO (wersja zapoznawcza)](how-to-azure-rtos-security-module.md)
