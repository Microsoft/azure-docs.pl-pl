---
title: Szybki start — konfigurowanie reguł i akcji w Azure IoT Central
description: W tym przewodniku Szybki start przedstawiono, jak konstruktor konfiguruje reguły i akcje oparte na telemetrii w Azure IoT Central danych.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f65614de97e8ff6eed732e624ae30c3f2b70bd60
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589010"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Szybki start: Konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central

W tym przewodniku Szybki start utworzysz regułę, która wysyła wiadomość e-mail, gdy wilgotność zgłoszona przez czujnik urządzenia przekroczy 55%.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać czynności z dwóch poprzednich przewodników Szybki start: Tworzenie aplikacji [Azure IoT Central](./quick-deploy-iot-central.md) i Dodawanie  symulowanego urządzenia do aplikacji [usługi IoT Central](./quick-create-simulated-device.md) w celu utworzenia szablonu urządzenia Kontroler czujników do pracy.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na telemetrii do aplikacji, w okienku po lewej stronie wybierz pozycję **Reguły**.

1. Aby utworzyć nową regułę, wybierz **pozycję + Nowy.**

1. Wprowadź **wartość Wilgotność środowiskowa** jako nazwę reguły.

1. W sekcji **Urządzenia docelowe** wybierz pozycję **Kontroler czujnika** jako szablon urządzenia. Ta opcja filtruje urządzenia, których dotyczy reguła, według typu szablonu urządzenia. Możesz dodać więcej kryteriów filtrowania, wybierając **pozycję + Filtruj.**

1. W **sekcji Warunki** zdefiniuj, co wyzwala regułę. Użyj następujących informacji, aby zdefiniować warunek na podstawie telemetrii temperatury:

    | Pole        | Wartość            |
    | ------------ | ---------------- |
    | Miara  | SensorHumid      |
    | Operator     | jest większe niż  |
    | Wartość        | 55               |

    Aby dodać więcej warunków, wybierz **pozycję + Warunek**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Zrzut ekranu przedstawiający warunek reguły":::

1. Aby dodać akcję poczty e-mail, która ma być uruchamiana po wyzwoleniu reguły, wybierz **pozycję + Wyślij wiadomość e-mail.**

1. Skorzystaj z informacji w poniższej tabeli, aby zdefiniować akcję, a następnie wybierz pozycję **Gotowe:**

    | Ustawienie   | Wartość                                             |
    | --------- | ------------------------------------------------- |
    | Nazwa wyświetlana | Akcja poczty e-mail operatora                          |
    | Działanie        | Twój adres e-mail                                |
    | Uwagi     | Wilgotność środowiskowa przekroczyła wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer.md), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Zrzut ekranu przedstawiający akcję poczty e-mail dodaną do reguły":::

1. Wybierz pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**.

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regułę zostaną spełnione, aplikacja wyśle wiadomość e-mail na adres określony w akcji.

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie reguły opartej na danych telemetrycznych
* Dodawanie akcji

Aby dowiedzieć się więcej na temat monitorowania urządzeń połączonych z aplikacją, przejdź do przewodnika Szybki start:

> [!div class="nextstepaction"]
> [Użyj Azure IoT Central, aby monitorować urządzenia.](quick-monitor-devices.md)
