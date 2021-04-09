---
title: Szybki Start — monitorowanie urządzeń na platformie Azure IoT Central
description: Szybki Start — jako operator, Dowiedz się, jak monitorować urządzenia za pomocą aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833885"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Szybki Start: korzystanie z usługi Azure IoT Central do monitorowania urządzeń

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Ten przewodnik Szybki Start przedstawia Cię jako operatora, jak używać aplikacji Microsoft Azure IoT Central do monitorowania urządzeń i zmieniania ustawień.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać trzy poprzednie Przewodniki Szybki Start [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md), [dodać symulowane urządzenie do aplikacji IoT Central](./quick-create-simulated-device.md) i [skonfigurować reguły i akcje dla urządzenia](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Odbieranie powiadomienia

Usługa Azure IoT Central wysyła powiadomienia dotyczące urządzeń jako wiadomości e-mail. Jako Konstruktor dodano regułę wysyłania powiadomienia do operatora, gdy wilgotność w czujniku połączonego urządzenia przekroczyła wartość progową. Jako operator sprawdzasz wiadomości e-mail pod kątem powiadomień.

Otwórz wiadomość e-mail otrzymaną po zakończeniu [konfigurowania reguł i akcji dla](quick-configure-rules.md) szybkiego startu urządzenia. W wiadomości e-mail wybierz link do urządzenia:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Zrzut ekranu przedstawiający wiadomość e-mail z powiadomieniem":::

Widok **omówienia** symulowanego urządzenia utworzonego w poprzednich przewodnikach Szybki Start zostanie otwarty w przeglądarce:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Zrzut ekranu pokazujący przegląd urządzenia, które wyzwoliło powiadomienie":::

## <a name="investigate-an-issue"></a>Badanie problemu

Jako operator można wyświetlić informacje o urządzeniu w widokach **Przegląd**, **informacje** i **polecenia** . Konstruktor utworzył widok **Zarządzanie urządzeniem** służący do edytowania informacji o urządzeniu i ustawiania właściwości urządzeń.

Wykres na pulpicie nawigacyjnym pokazuje powierzchnię wilgotności urządzenia. Należy określić, że wilgotność urządzenia jest zbyt wysoka.

## <a name="remediate-an-issue"></a>Rozwiązywanie problemu

Aby wprowadzić zmianę na urządzeniu, użyj strony **Zarządzanie urządzeniem** .

Zmień **temperaturę docelową** na 80, aby obgrzać urządzenie i zmniejszyć wilgotność. Wybierz pozycję **Zapisz** , aby zaktualizować urządzenie. Gdy urządzenie potwierdzi zmianę ustawień, stan właściwości zmieni się na **zsynchronizowany**:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Zrzut ekranu przedstawiający zaktualizowaną wartość ustawienia temperatury docelowej dla urządzenia":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Odbieranie powiadomienia
* Badanie problemu
* Rozwiązywanie problemu

Teraz, gdy teraz wiesz już, jak monitorować urządzenie, sugerowanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Kompiluj szablon urządzenia i Zarządzaj nim](howto-set-up-template.md).
