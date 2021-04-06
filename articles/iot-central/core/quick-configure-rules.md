---
title: Szybki Start — Konfigurowanie reguł i akcji na platformie Azure IoT Central
description: W tym przewodniku szybki start pokazano, jak za pomocą konstruktora, jak skonfigurować reguły i akcje na podstawie danych telemetrycznych w aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 90fc1385afb2ef921828465ba030674281e96ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833851"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Szybki start: Konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

W tym przewodniku szybki start utworzysz regułę, która wysyła wiadomość e-mail, gdy wilgotność zgłoszona przez czujnik urządzenia przekracza 55%.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać dwa poprzednie Przewodniki Szybki Start [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) i [Dodawanie symulowanego urządzenia do aplikacji IoT Central](./quick-create-simulated-device.md) w celu utworzenia szablonu urządzenia **kontrolera czujnika** do pracy z programem.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na telemetrii do aplikacji, w okienku po lewej stronie wybierz pozycję **reguły**.

1. Aby utworzyć nową regułę, wybierz pozycję **+ Nowy**.

1. Wprowadź wartość w polu **wilgotność środowiskowa** jako nazwę reguły.

1. W sekcji **urządzenia docelowe** wybierz pozycję **kontroler czujnika** jako szablon urządzenia. Ta opcja umożliwia filtrowanie urządzeń, do których odnosi się reguła według typu szablonu urządzenia. Aby dodać więcej kryteriów filtrowania, wybierz pozycję **+ Filtr**.

1. W sekcji **warunki** definiujesz, co wyzwala regułę. Poniższe informacje służą do definiowania warunków opartych na telemetrii temperatury:

    | Pole        | Wartość            |
    | ------------ | ---------------- |
    | Miara  | SensorHumid      |
    | Operator     | jest większe niż  |
    | Wartość        | 55               |

    Aby dodać więcej warunków, wybierz pozycję **+ warunek**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Zrzut ekranu przedstawiający warunek reguły":::

1. Aby dodać akcję poczty e-mail do uruchomienia, gdy reguła jest wyzwalana, wybierz pozycję **+ poczta e-mail**.

1. Użyj informacji podanych w poniższej tabeli, aby zdefiniować akcję, a następnie wybierz pozycję **gotowe**:

    | Ustawienie   | Wartość                                             |
    | --------- | ------------------------------------------------- |
    | Nazwa wyświetlana | Akcja e-mail operatora                          |
    | Działanie        | Twój adres e-mail                                |
    | Uwagi     | Wilgotność środowiska przekroczyła wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer.md), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Zrzut ekranu pokazujący akcję wiadomości e-mail dodaną do reguły":::

1. Wybierz pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**.

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule są spełnione, aplikacja wysyła wiadomość e-mail na adres określony w akcji.

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie reguły opartej na danych telemetrycznych
* Dodawanie akcji

Aby dowiedzieć się więcej o monitorowaniu urządzeń połączonych z aplikacją, przejdź do przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Użyj usługi Azure IoT Central do monitorowania urządzeń](quick-monitor-devices.md).
