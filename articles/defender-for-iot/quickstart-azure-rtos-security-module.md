---
title: 'Szybki Start: Konfigurowanie i włączanie modułu zabezpieczeń dla usługi Azure RTO'
description: Dowiedz się, jak dołączyć i włączyć moduł zabezpieczeń dla usługi Azure RTO na IoT Hub platformie Azure.
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
ms.openlocfilehash: e8e9eae576c330f17cd33d027152d463fe19a82b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123897"
---
# <a name="quickstart-security-module-for-azure-rtos"></a>Szybki Start: moduł zabezpieczeń dla usługi Azure RTO 

Ten artykuł zawiera opis wymagań wstępnych przed rozpoczęciem pracy i wyjaśnia, jak włączyć moduł zabezpieczeń dla usługi Azure RTO na IoT Hub. Jeśli obecnie nie masz IoT Hub, zobacz [tworzenie IoT Hub przy użyciu Azure Portal](../iot-hub/iot-hub-create-through-portal.md) , aby rozpocząć pracę.

## <a name="prerequisites"></a>Wymagania wstępne 

### <a name="supported-devices"></a>Obsługiwane urządzenia

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikroukład SAM E54 Xplained Pro EVK

Pobieraj, Kompiluj i uruchamiaj jeden z plików. zip dla konkretnej tablicy i narzędzia (IAR, IDE lub PC) wybranego z [modułu zabezpieczeń dla zasobu usługi Azure RTO](https://github.com/azure-rtos/azure-iot-preview/releases)w serwisie GitHub.

### <a name="azure-resources"></a>Zasoby platformy Azure

Następny etap rozpoczynania pracy polega na przygotowaniu zasobów platformy Azure. Będziesz potrzebować IoT Hub i sugerujemy obszar roboczy Log Analytics. W przypadku IoT Hub potrzebne będą IoT Hub parametry połączenia w celu nawiązania połączenia z urządzeniem. 
  
### <a name="iot-hub-connection"></a>Połączenie IoT Hub

Aby rozpocząć, musisz mieć połączenie IoT Hub. 

1. Otwórz **IoT Hub** w Azure Portal.

1. Przejdź do **urządzeń IoT**.

1. Wybierz przycisk **Utwórz**.

1. Skopiuj parametry połączenia IoT do [pliku konfiguracji](how-to-azure-rtos-security-module.md).

Poświadczenia połączeń są pobierane z konfiguracji aplikacji użytkownika **HOST_NAME**, **DEVICE_ID** i **DEVICE_SYMMETRIC_KEY**.

Moduł zabezpieczeń usługi Azure RTO korzysta z połączeń usługi Azure IoT pośredniczącego na podstawie protokołu **MQTT** .

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby zakończyć konfigurowanie i dostosowywanie rozwiązania.

> [!div class="nextstepaction"]
> [Konfigurowanie modułu zabezpieczeń dla usługi Azure RTOS](how-to-azure-rtos-security-module.md)