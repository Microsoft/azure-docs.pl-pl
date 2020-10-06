---
title: 'Szybki Start: Konfigurowanie i włączanie modułu zabezpieczeń dla usługi Azure RTO'
description: Dowiedz się, jak dołączyć i włączyć moduł zabezpieczeń dla usługi Azure RTO na IoT Hub platformie Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2020
ms.author: rkarlin
ms.openlocfilehash: 38e78b80fd14aec7557b563b2b33faf7d51bd114
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759736"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Szybki Start: moduł zabezpieczeń dla usługi Azure RTO (wersja zapoznawcza)

Ten artykuł zawiera opis wymagań wstępnych przed rozpoczęciem pracy i wyjaśnia, jak włączyć moduł zabezpieczeń dla usługi Azure RTO na IoT Hub. Jeśli obecnie nie masz IoT Hub, zobacz [tworzenie IoT Hub przy użyciu Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) , aby rozpocząć pracę.

> [!NOTE]
> Moduł zabezpieczeń usługi Azure RTO jest obsługiwany tylko w centrach IoT warstwy Standardowa.

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
1. Skopiuj parametry połączenia IoT do [pliku konfiguracji](how-to-azure-rtos-security-module.md).


Poświadczenia połączeń są pobierane z konfiguracji aplikacji użytkownika **HOST_NAME**, **DEVICE_ID**i **DEVICE_SYMMETRIC_KEY**.

Moduł zabezpieczeń usługi Azure RTO korzysta z połączeń usługi Azure IoT pośredniczącego na podstawie protokołu **MQTT** .


### <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics

Log Analytics pozyskiwanie w IoT Hub jest wyłączone domyślnie dla rozwiązania IoT. Aby umożliwić korzystanie z modułu zabezpieczeń dla usługi Azure RTO, wykonaj następujące czynności: 
1. W Azure Portal przejdź do IoT Hub.
1. Wybierz pozycję **Ustawienia** w menu **zabezpieczenia** .
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Opcja zbierania danych dostępu dla usługi Azure RTO"::: 
1. Wybierz pozycję **zbieranie danych**. 
1. W opcji **Konfiguracja obszaru roboczego** Przełącz przełącznik na wartość **włączone**. 
1. Utwórz nowy obszar roboczy Log Analytics lub Dołącz istniejący. Upewnij się, że wybrano opcję **dostęp do danych pierwotnych zabezpieczeń** . 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Opcja zbierania danych dostępu dla usługi Azure RTO":::
1. Wybierz pozycję **Zapisz**
1. Wróć do listy zasobów platformy Azure i upewnij się, że dla IoT Hub został włączony utworzony lub dołączony obszar roboczy Log Analytics.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Opcja zbierania danych dostępu dla usługi Azure RTO"::: 

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby zakończyć konfigurowanie i dostosowywanie rozwiązania.

> [!div class="nextstepaction"]
> [Konfigurowanie modułu zabezpieczeń dla usługi Azure RTOS](how-to-azure-rtos-security-module.md)
