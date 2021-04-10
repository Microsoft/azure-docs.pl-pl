---
title: 'Szybki Start: Konfigurowanie i Włączanie usługi Defender-IoT-Micro-Agent dla platformy Azure RTO'
description: W tym przewodniku szybki start dowiesz się, jak dołączyć i włączyć usługę Defender-IoT-Micro-Agent for Azure RTO na IoT Hub platformie Azure.
services: defender-for-iot
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: 0575e9303068b203a5d8cd51c8a8cc1843de6f4d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384639"
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
