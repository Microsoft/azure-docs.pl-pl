---
title: Zarządzanie urządzeniami Azure IoT za pomocą narzędzi Azure IoT Tools for programu vscode
description: Skorzystaj z narzędzi Azure IoT Tools for Visual Studio Code for Azure IoT Hub Device Management, korzystając z metod bezpośrednich i opcji zarządzania żądanymi właściwościami sznurka.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81688099"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Korzystanie z narzędzi Azure IoT Tools for Visual Studio Code na potrzeby zarządzania urządzeniami IoT Hub platformy Azure

![Diagram kompleksowy](media/iot-hub-get-started-e2e-diagram/2.png)

[Narzędzia Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) to przydatne Visual Studio Code rozszerzenie, które ułatwia tworzenie IoT Hub zarządzania aplikacjami i tworzeniem aplikacji IoT. Zawiera opcje zarządzania, których można użyć do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                    |
|----------------------------|--------------------------------|
| Metody bezpośrednie             | Nadawanie urządzeniu działania takie jak uruchamianie lub zatrzymywanie wysyłania komunikatów lub ponowne uruchamianie urządzenia.                                        |
| Odczytaj sznurki urządzenia           | Pobierz zgłoszony stan urządzenia. Na przykład urządzenie raportuje diodę LED teraz.                                    |
| Aktualizacja sznurka urządzenia         | Umieść urządzenie w określonych stanach, takich jak ustawienie diody LED na zieloną lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Komunikaty z chmury do urządzenia   | Wyślij powiadomienia do urządzenia. Na przykład "bardzo prawdopodobnie jest to deszcz. Nie zapomnij przenieść parasola ".              |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [wskazówki dotyczące komunikacji między urządzeniami i chmurą](iot-hub-devguide-d2c-guidance.md) oraz [wskazówki dotyczące komunikacji między chmurą i urządzeniem](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). IoT Hub utrzymuje sznurki urządzenia dla każdego urządzenia, które nawiązuje z nim połączenie. Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [wprowadzenie do usługi Device bliźniaczych reprezentacji](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak korzystać z narzędzi Azure IoT Tools for Visual Studio Code z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co robisz

Uruchom narzędzia Azure IoT Tools dla Visual Studio Code z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Potrzebne elementy

* Aktywna subskrypcja platformy Azure.
* Usługa Azure IoT Hub w ramach Twojej subskrypcji.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT dla vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub Skopiuj ten adres URL i wklej go do okna przeglądarki: `vscode:extension/vsciot-vscode.azure-iot-tools` .

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT Hub

1. W widoku **eksploratora** vs Code rozwiń sekcję **urządzenia IoT Hub platformy Azure** w lewym dolnym rogu.

2. Kliknij pozycję **wybierz IoT Hub** w menu kontekstowym.

3. W prawym dolnym rogu zostanie wyświetlone okno podręczne, które umożliwi zalogowanie się do platformy Azure po raz pierwszy.

4. Po zalogowaniu zostanie wyświetlona lista subskrypcji platformy Azure, a następnie wybrana subskrypcja platformy Azure i IoT Hub.

5. Lista urządzeń zostanie wyświetlona na karcie **urządzenia usługi Azure IoT Hub** w ciągu kilku sekund.

   > [!Note]
   > Można również ukończyć konfigurację, wybierając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź parametry połączenia zasad **iothubowner** dla Centrum IoT Hub, z którym urządzenie IoT łączy się w oknie podręcznym.

## <a name="direct-methods"></a>Metody bezpośrednie

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Wywołaj metodę bezpośrednią**. 

2. Wprowadź nazwę metody i ładunek w polu wejściowym.

3. Wyniki będą wyświetlane w widoku **wyjście**  >  **IoT Hub platformy Azure** .

## <a name="read-device-twin"></a>Odczytaj sznurki urządzenia

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **Edytuj sznurek urządzeń**. 

2. **azure-iot-device-twin.jsw** pliku zostanie otwarty z zawartością sznurka urządzenia.

## <a name="update-device-twin"></a>Aktualizacja sznurka urządzenia

1. Wprowadź edycję **tagów** lub **właściwości. żądane** pole.

2. Kliknij prawym przyciskiem myszy **azure-iot-device-twin.jsna** pliku.

3. Wybierz pozycję **Aktualizuj sznurek urządzenia** , aby zaktualizować sznurek urządzeń.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać komunikat z usługi IoT Hub do urządzenia, wykonaj następujące kroki:
 
1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Wyślij wiadomość C2D do urządzenia**. 

2. Wprowadź komunikat w polu wejściowym.

3. Wyniki będą wyświetlane w widoku **wyjście**  >  **IoT Hub platformy Azure** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak używać rozszerzenia narzędzi Azure IoT Tools dla Visual Studio Code z różnymi opcjami zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
