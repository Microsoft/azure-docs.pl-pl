---
title: Use Azure IoT Tools for VSCode to manager IT Hub messaging (Używanie programu VSCode do obsługi komunikatów centrum IT)
description: Dowiedz się, jak używać Azure IoT Tools dla Visual Studio Code do monitorowania komunikatów w chmurze i wysyłania komunikatów z chmury do urządzeń w Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 1c4a840233e576c528e9c58d57eca0b3d524bf4d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566932"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Użyj Azure IoT Tools, aby Visual Studio Code wysyłać i odbierać komunikaty między urządzeniem i IoT Hub

![Diagram end-to-end](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

Z tego artykułu dowiesz się, jak używać usługi Azure IoT Tools for Visual Studio Code do monitorowania komunikatów z urządzenia do chmury i wysyłania komunikatów z chmury do urządzeń. Komunikaty z urządzenia do chmury mogą być danymi czujników, które urządzenie zbiera, a następnie wysyła do centrum IoT. Komunikaty z chmury do urządzenia mogą być poleceniami wysyłanymi do urządzenia przez centrum IoT w celu migania diody LED podłączonej do urządzenia.

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) to przydatne rozszerzenie Visual Studio Code, które ułatwia zarządzanie IoT Hub i tworzenie aplikacji IoT. W tym artykule opisano sposób używania usługi Azure IoT Tools Visual Studio Code do wysyłania i odbierania komunikatów między urządzeniem i centrum IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywna subskrypcja platformy Azure.

* Centrum Azure IoT Hub w ramach Twojej subskrypcji.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT Tools, VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) skopiuj i wklej ten adres URL w oknie przeglądarki: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT

1. W **widoku** Eksploratora VS Code rozwiń **Azure IoT Hub Urządzenia** w lewym dolnym rogu.

2. Kliknij **pozycję IoT Hub** w menu kontekstowym.

3. W prawym dolnym rogu pojawi się okno podręczne, które umożliwia zalogowanie się do platformy Azure po raz pierwszy.

4. Po zalogowaniu zostanie wyświetlona lista subskrypcji platformy Azure, a następnie wybierz pozycję Subskrypcja platformy Azure i wybierz IoT Hub.

5. Lista urządzeń zostanie wyświetlona na karcie **Azure IoT Hub urządzenia** w ciągu kilku sekund.

   > [!Note]
   > Można również ukończyć konfigurację, wybierając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź w oknie podręcznym ciąg połączenia zasad **iothubowner** dla centrum IoT, z którego łączy się urządzenie IoT.

## <a name="monitor-device-to-cloud-messages"></a>Monitorowanie komunikatów wysyłanych z urządzenia do chmury

Aby monitorować komunikaty wysyłane z urządzenia do centrum IoT Hub, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz **pozycję Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia.**

2. Monitorowane komunikaty będą wyświetlane w widoku **DANYCH**  >  **WYJŚCIOWYCH Azure IoT Hub** widoku.

3. Aby zatrzymać monitorowanie, kliknij prawym przyciskiem myszy widok **DANE WYJŚCIOWE** i wybierz pozycję **Zatrzymaj monitorowanie — wbudowany punkt końcowy zdarzenia**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać komunikat z centrum IoT hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij komunikat C2D do urządzenia.**

2. Wprowadź komunikat w polu wejściowym.

3. Wyniki będą wyświetlane w widoku **danych**  >  **wyjściowych Azure IoT Hub** wyjściowego.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak monitorować komunikaty z urządzenia do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]