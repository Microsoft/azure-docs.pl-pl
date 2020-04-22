---
title: Zarządzanie urządzeniami Usługi Azure IoT za pomocą narzędzi IoT usługi Azure dla vscode
description: Użyj narzędzia Azure IoT Tools for Visual Studio Code for Azure IoT Hub zarządzania, wyposażony w metody bezpośrednie i bliźniaczej żądane opcje zarządzania właściwościami.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688099"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Korzystanie z narzędzi Usługi Azure IoT tools dla kodu programu Visual Studio dla zarządzania urządzeniami usługi Azure IoT Hub

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[Narzędzia Azure IoT to](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) przydatne rozszerzenie kodu programu Visual Studio, które ułatwia zarządzanie centrum IoT i tworzenie aplikacji IoT. Jest wyposażony w opcje zarządzania, których można używać do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                    |
|----------------------------|--------------------------------|
| Metody bezpośrednie             | Ustaw działanie urządzenia, takie jak uruchamianie lub zatrzymywanie wysyłania wiadomości lub ponowne uruchamianie urządzenia.                                        |
| Odczyt urządzenia bliźniaczego           | Pobierz zgłoszony stan urządzenia. Na przykład urządzenie zgłasza, że dioda LED miga teraz.                                    |
| Aktualizacja bliźniaczej reprezentacji urządzenia         | Umieść urządzenie w określonych stanach, takich jak ustawienie diody LED na zielono lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Komunikaty z chmury do urządzenia   | Wysyłanie powiadomień do urządzenia. Na przykład, "Jest bardzo prawdopodobne, że dziś będzie padać. Nie zapomnij zabrać ze sobą parasola."              |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [Wskazówki dotyczące komunikacji między urządzeniami](iot-hub-devguide-d2c-guidance.md) a chmurą oraz [wskazówki dotyczące komunikacji z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Centrum IoT hub utrzymuje bliźniaczej reprezentacji urządzenia dla każdego urządzenia, które łączy się z nim. Aby uzyskać więcej informacji na temat bliźniąt urządzeń, zobacz [Wprowadzenie do bliźniąt urządzeń](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiedz się więcej przy użyciu narzędzi Azure IoT Tools for Visual Studio Code z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co robisz

Uruchom narzędzia Azure IoT Tools for Visual Studio Code z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Co jest potrzebne

* Aktywna subskrypcja platformy Azure.
* Centrum Usługi Azure IoT w ramach subskrypcji.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia IoT usługi Azure dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub skopiuj ten adres URL i wklej go do okna przeglądarki:`vscode:extension/vsciot-vscode.azure-iot-tools`.

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT

1. W **widoku Eksploratora** programu VS Code rozwiń **sekcję Urządzenia usługi Azure IoT Hub** w lewym dolnym rogu.

2. Kliknij **polecenie Wybierz Centrum IoT** w menu kontekstowym.

3. W prawym dolnym rogu pojawi się okno podręczne, które umożliwia zalogowanie się na platformie Azure po raz pierwszy.

4. Po zalogowaniu się zostanie wyświetlona lista subskrypcji platformy Azure, a następnie wybierz pozycję Subskrypcja platformy Azure i Centrum IoT.

5. Lista urządzeń zostanie wyświetlona na karcie **Urządzenia usługi Azure IoT Hub** w ciągu kilku sekund.

   > [!Note]
   > Można również ukończyć konfigurację, wybierając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź ciąg połączenia zasad **iothubowner** dla centrum IoT, z którym łączy się urządzenie IoT w wyskakującym oknie.

## <a name="direct-methods"></a>Metody bezpośrednie

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wywołaj metodę bezpośrednią**. 

2. Wprowadź nazwę metody i ładunek w polu wejściowym.

3. Wyniki będą wyświetlane w widoku **usługi OUTPUT** > **Azure IoT Hub.**

## <a name="read-device-twin"></a>Odczyt urządzenia bliźniaczego

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Edytuj bliźniacze urządzenie**. 

2. Otworzy się plik **azure-iot-device-twin.json** z zawartością bliźniaczej reprezentacji urządzenia.

## <a name="update-device-twin"></a>Aktualizacja bliźniaczej reprezentacji urządzenia

1. Wykonuj niektóre zmiany **znaczników** lub **pól właściwości.desired.**

2. Kliknij prawym przyciskiem myszy plik **azure-iot-device-twin.json.**

3. Wybierz **opcję Aktualizuj bliźniaczej reprezentacji urządzenia,** aby zaktualizować bliźniaczątkę urządzenia.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z centrum IoT hub do urządzenia, wykonaj następujące czynności:
 
1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij wiadomość C2D do urządzenia**. 

2. Wprowadź komunikat w polu wprowadzania.

3. Wyniki będą wyświetlane w widoku **usługi OUTPUT** > **Azure IoT Hub.**

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak używać rozszerzenia Narzędzia Azure IoT dla programu Visual Studio Code z różnymi opcjami zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
