---
title: Używanie narzędzi Azure IoT Tools for programu vscode do menedżerów komunikatów IT Hub
description: Dowiedz się, jak korzystać z narzędzi Azure IoT Tools for Visual Studio Code, aby monitorować urządzenia w chmurze i wysyłać komunikaty z chmury do urządzeń w usłudze Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: b9d8f0c2a573ba1749b414a389cd7181ddd5f95a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87321687"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Użyj narzędzi Azure IoT Tools, aby Visual Studio Code wysyłać i odbierać komunikaty między urządzeniem i IoT Hub

![Diagram kompleksowy](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Narzędzia Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) to przydatne Visual Studio Code rozszerzenie, które ułatwia tworzenie IoT Hub zarządzania aplikacjami i tworzeniem aplikacji IoT. W tym artykule omówiono sposób używania narzędzi Azure IoT Tools do Visual Studio Code wysyłania i odbierania komunikatów między urządzeniem a centrum IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Co się uczysz

Dowiesz się, jak używać narzędzi Azure IoT Tools for Visual Studio Code do monitorowania komunikatów wysyłanych z urządzenia do chmury oraz do wysyłania komunikatów z chmury do urządzenia. Komunikaty przesyłane z urządzenia do chmury mogą być danymi czujników, które urządzenie zbiera, a następnie wysyła do centrum IoT Hub. Komunikaty z chmury do urządzenia mogą być poleceniami wysyłanymi przez Centrum IoT do urządzenia w celu migania DIODy, która jest połączona z urządzeniem.

## <a name="what-you-will-do"></a>Co należy zrobić

* Użyj narzędzi Azure IoT Tools, aby Visual Studio Code do monitorowania komunikatów wysyłanych z urządzenia do chmury.

* Użyj narzędzi Azure IoT Tools, aby Visual Studio Code do wysyłania komunikatów z chmury do urządzenia.

## <a name="what-you-need"></a>Potrzebne elementy

* Aktywna subskrypcja platformy Azure.

* Usługa Azure IoT Hub w ramach Twojej subskrypcji.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Narzędzia usługi Azure IoT dla vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub skopiuj i wklej ten adres URL do okna przeglądarki: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT Hub

1. W widoku **eksploratora** vs Code rozwiń sekcję **urządzenia IoT Hub platformy Azure** w lewym dolnym rogu.

2. Kliknij pozycję **wybierz IoT Hub** w menu kontekstowym.

3. W prawym dolnym rogu zostanie wyświetlone okno podręczne, które umożliwi zalogowanie się do platformy Azure po raz pierwszy.

4. Po zalogowaniu zostanie wyświetlona lista subskrypcji platformy Azure, a następnie wybrana subskrypcja platformy Azure i IoT Hub.

5. Lista urządzeń zostanie wyświetlona na karcie **urządzenia usługi Azure IoT Hub** w ciągu kilku sekund.

   > [!Note]
   > Można również ukończyć konfigurację, wybierając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź parametry połączenia zasad **iothubowner** dla Centrum IoT Hub, z którym urządzenie IoT łączy się w oknie podręcznym.

## <a name="monitor-device-to-cloud-messages"></a>Monitorowanie komunikatów przesyłanych z urządzenia do chmury

Aby monitorować komunikaty wysyłane z urządzenia do usługi IoT Hub, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Monitorowane komunikaty będą wyświetlane w widoku **wyjście**  >  **IoT Hub platformy Azure** .

3. Aby zatrzymać monitorowanie, kliknij prawym przyciskiem myszy widok **danych wyjściowych** i wybierz polecenie **Zatrzymaj monitorowanie wbudowanego punktu końcowego zdarzenia**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać komunikat z usługi IoT Hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Wyślij wiadomość C2D do urządzenia**.

2. Wprowadź komunikat w polu wejściowym.

3. Wyniki będą wyświetlane w widoku **wyjście**  >  **IoT Hub platformy Azure** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak monitorować komunikaty z urządzenia do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT i usługą Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]