---
title: 'Szybki Start: dołączanie usługi Defender for IoT do rozwiązania opartego na agencie'
description: W tym przewodniku szybki start dowiesz się, jak dołączyć i włączyć usługę Defender for IoT Security na platformie Azure IoT Hub.
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: b795773ae0eb667cf7f53f4209b6476ba937b17f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780960"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Szybki Start: dołączanie usługi Defender for IoT do rozwiązania opartego na agencie

W tym artykule wyjaśniono, jak włączyć usługę Defender for IoT na istniejącym IoT Hub. Jeśli obecnie nie masz IoT Hub, zobacz [tworzenie IoT Hub przy użyciu Azure Portal](../iot-hub/iot-hub-create-through-portal.md) , aby rozpocząć pracę.

Możesz zarządzać bezpieczeństwem IoT za pomocą IoT Hub w usłudze Defender for IoT. Portal zarządzania znajdujący się w IoT Hub umożliwia wykonywanie następujących czynności: 

- Zarządzanie zabezpieczeniami IoT Hub.

- Podstawowe zarządzanie bezpieczeństwem urządzenia IoT bez instalowania agenta na podstawie telemetrii IoT Hub. 

- Zaawansowane zarządzanie zabezpieczeniami urządzenia IoT w oparciu o Micro Agent.

> [!NOTE]
> Usługa Defender for IoT obecnie obsługuje tylko centra usługi IoT w warstwie Standardowa.

## <a name="prerequisites"></a>Wymagania wstępne

Brak

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Dołączanie usługi Defender for IoT do IoT Hub

W przypadku wszystkich nowych centrów IoT usługa Defender for IoT jest domyślnie ustawiona na wartość **włączone** . Podczas procesu tworzenia IoT Hub można sprawdzić, czy usługa Defender for IoT jest przełączana **do usługi** .

Aby sprawdzić, czy przełącznik jest ustawiony **na:**

1. Przejdź do witryny Azure Portal.

1. Wybierz pozycję **IoT Hub** z listy usług platformy Azure.

1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Na górnym pasku narzędzi wybierz przycisk Utwórz." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Wybierz kartę **Zarządzanie** i sprawdź, czy usługa **Defender for IoT** jest ustawiona na wartość **włączone**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Upewnij się, że przełącznik Defender for IoT jest ustawiony na wartość włączone.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Dołączanie usługi Defender for IoT do istniejącej IoT Hub

Usługę Defender for IoT można dołączyć do istniejącej IoT Hub, w której można monitorować wzorce komunikacji urządzenia, urządzenia w chmurze i chmury do urządzeń.

Aby dołączyć do istniejącej IoT Hub usługę Defender for IoT:

1. Przejdź do IoT Hub. 

1. Wybierz IoT Hub do dołączenia.

1. Wybierz dowolną opcję w sekcji **zabezpieczenia** .

1. Kliknij pozycję **Zabezpiecz swoje rozwiązanie IoT**   i wypełnij formularz dołączania. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Wybierz przycisk Zabezpiecz swoje rozwiązanie IoT, aby zabezpieczyć swoje rozwiązanie.":::

Przycisk **Zabezpiecz Twoje rozwiązanie IoT** będzie wyświetlany tylko wtedy, gdy IoT Hub nie została jeszcze dołączona lub jeśli podczas dołączania po lewej stronie usługa Defender for IoT została **wyłączona**.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Jeśli przełącznik został ustawiony jako wyłączony podczas dołączania.":::

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby skonfigurować Twoje rozwiązanie...

> [!div class="nextstepaction"]
> [Tworzenie sznurka modułu programu Defender IoT Micro Agent (wersja zapoznawcza)](quickstart-create-micro-agent-module-twin.md)
