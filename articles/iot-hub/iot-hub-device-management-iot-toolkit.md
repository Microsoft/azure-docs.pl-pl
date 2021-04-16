---
title: Zarządzanie urządzeniami usługi Azure IoT za pomocą Azure IoT Tools dla programu VSCode
description: Użyj Azure IoT Tools do Visual Studio Code zarządzania urządzeniami Azure IoT Hub, oferując metody Bezpośrednie i opcje zarządzania żądanymi właściwościami bliźniaczej reprezentacji.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: b48def283ea27fdd0eaa3230a2eb9a8327461ff1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567017"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Używanie Azure IoT Tools na Visual Studio Code zarządzania Azure IoT Hub urządzeniami

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

Z tego artykułu dowiesz się, jak używać aplikacji Azure IoT Tools dla Visual Studio Code z różnymi opcjami zarządzania na komputerze dewelopera. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) to przydatne rozszerzenie Visual Studio Code, które ułatwia zarządzanie IoT Hub i tworzenie aplikacji IoT. Zawiera ona opcje zarządzania, których można użyć do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                    |
|----------------------------|--------------------------------|
| Metody bezpośrednie             | Nakłoń urządzenie do działania, takiego jak uruchamianie lub zatrzymywanie wysyłania komunikatów lub ponowne uruchamianie urządzenia.                                        |
| Odczytywanie bliźniaczej reprezentacji urządzenia           | Pobierz raportny stan urządzenia. Na przykład urządzenie zgłasza, że dioda LED miga teraz.                                    |
| Aktualizowanie bliźniaczej reprezentacji urządzenia         | Umieść urządzenie w pewnych stanach, takich jak ustawienie diody LED na zielono lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Komunikaty z chmury do urządzenia   | Wysyłanie powiadomień do urządzenia. Na przykład: "Dzisiaj bardzo prawdopodobne jest, że będzie płakać. Pamiętaj, aby przyniesić do niej wszystkiego".              |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [Wskazówki](iot-hub-devguide-d2c-guidance.md) dotyczące komunikacji między urządzeniami a chmurą i Wskazówki dotyczące komunikacji z chmury [do urządzeń.](iot-hub-devguide-c2d-guidance.md)

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). IoT Hub bliźniaczej reprezentacji urządzenia dla każdego urządzenia, które nawiązuje z nim połączenie. Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [Wprowadzenie do bliźniaczych reprezentacji urządzeń.](iot-hub-node-node-twin-getstarted.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywna subskrypcja platformy Azure.
* Centrum Azure IoT Hub w ramach Twojej subskrypcji.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools, VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub skopiuj ten adres URL i wklej go w oknie przeglądarki: `vscode:extension/vsciot-vscode.azure-iot-tools` .

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT

1. W **widoku** Eksploratora VS Code rozwiń **Azure IoT Hub Urządzenia** w lewym dolnym rogu.

2. Kliknij **pozycję IoT Hub** w menu kontekstowym.

3. W prawym dolnym rogu pojawi się okno podręczne, które umożliwia zalogowanie się do platformy Azure po raz pierwszy.

4. Po zalogowaniu zostanie wyświetlona lista subskrypcji platformy Azure, a następnie wybierz pozycję Subskrypcja platformy Azure i wybierz IoT Hub.

5. Lista urządzeń zostanie wyświetlona na karcie **Azure IoT Hub urządzenia** w ciągu kilku sekund.

   > [!Note]
   > Można również ukończyć konfigurację, wybierając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź w oknie podręcznym ciąg połączenia zasad **iothubowner** dla centrum IoT, z które łączy się urządzenie IoT.

## <a name="direct-methods"></a>Metody bezpośrednie

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wywołaj metodę bezpośrednią.** 

2. Wprowadź nazwę metody i ładunek w polu wejściowym.

3. Wyniki będą wyświetlane w widoku **DANYCH**  >  **WYJŚCIOWYCH Azure IoT Hub** widoku.

## <a name="read-device-twin"></a>Odczytywanie bliźniaczej reprezentacji urządzenia

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Edytuj bliźniacze reprezentacji urządzenia.** 

2. Zostanie **azure-iot-device-twin.jsw pliku** z zawartością bliźniaczej reprezentacji urządzenia.

## <a name="update-device-twin"></a>Aktualizowanie bliźniaczej reprezentacji urządzenia

1. Dokonaj edycji **tagów lub** **pola properties.desired.**

2. Kliknij prawym przyciskiem myszy pozycję **azure-iot-device-twin.jspliku.**

3. Wybierz **pozycję Aktualizuj bliźniacze reprezentacji** urządzenia, aby zaktualizować bliźniacze reprezentacji urządzenia.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać komunikat z centrum IoT hub do urządzenia, wykonaj następujące kroki:
 
1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij komunikat C2D do urządzenia.** 

2. Wprowadź komunikat w polu wejściowym.

3. Wyniki będą wyświetlane w widoku **danych**  >  **wyjściowych Azure IoT Hub** wyjściowego.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak używać rozszerzenia Azure IoT Tools dla Visual Studio Code różnych opcji zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
