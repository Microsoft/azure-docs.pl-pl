---
title: Dołączanie do usługi Defender dla rozwiązania IoT na podstawie agenta
description: Dowiedz się, jak dołączyć i włączyć usługę Defender for IoT Security na platformie Azure IoT Hub.
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
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809137"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Dołączanie do usługi Defender dla rozwiązania IoT na podstawie agenta

W tym artykule wyjaśniono, jak włączyć usługę Defender for IoT na istniejącym IoT Hub. Jeśli obecnie nie masz IoT Hub, zobacz [tworzenie IoT Hub przy użyciu Azure Portal](../iot-hub/iot-hub-create-through-portal.md) , aby rozpocząć pracę.

Możesz zarządzać bezpieczeństwem IoT za pomocą IoT Hub w usłudze Defender for IoT. Portal zarządzania znajdujący się w IoT Hub umożliwia wykonywanie następujących czynności: 

- Zarządzanie zabezpieczeniami IoT Hub.

- Podstawowe zarządzanie bezpieczeństwem urządzenia IoT bez instalowania agenta na podstawie telemetrii IoT Hub. 

- Zaawansowane zarządzanie zabezpieczeniami urządzenia IoT w oparciu o Micro Agent.

> [!NOTE]
> Usługa Defender for IoT obecnie obsługuje tylko centra usługi IoT w warstwie Standardowa.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>Dołączanie do usługi Defender for IoT w IoT Hub

W przypadku wszystkich nowych centrów IoT usługa Defender for IoT jest domyślnie ustawiona na wartość **włączone** . Podczas procesu tworzenia IoT Hub można sprawdzić, czy usługa Defender for IoT jest przełączana **do usługi** .

Aby sprawdzić, czy przełącznik jest ustawiony **na:**

1. Przejdź do witryny Azure Portal.

1. Wybierz pozycję **IoT Hub** z listy usług platformy Azure.

1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Na górnym pasku narzędzi wybierz przycisk Utwórz." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Wybierz kartę **Zarządzanie** i sprawdź, czy usługa **Defender for IoT** jest ustawiona na wartość **włączone**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Upewnij się, że przełącznik Defender for IoT jest ustawiony na wartość włączone.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Dołączanie usługi Defender for IoT do istniejącej IoT Hub

Aby uruchomić usługę, możesz monitorować zarządzanie tożsamościami urządzeń, urządzenia do chmury oraz wzorce komunikacji z chmurą. 

1. Przejdź do IoT Hub. 

1. Wybierz menu **Przegląd zabezpieczeń**   . 

1. Kliknij pozycję Zabezpiecz swoje rozwiązanie IoT i wypełnij formularz dołączania. 


## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby skonfigurować Twoje rozwiązanie...

> [!div class="nextstepaction"]
> [Tworzenie sznurka modułu programu Defender IoT Micro Agent (wersja zapoznawcza)](quickstart-create-micro-agent-module-twin.md)
