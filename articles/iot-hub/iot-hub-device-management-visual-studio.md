---
title: Zarządzanie urządzeniami Usługi Azure IoT za pomocą narzędzia Visual Studio Cloud Explorer
description: Eksplorator chmury umożliwia Visual Studio zarządzania urządzeniami Azure IoT Hub, w tym metod bezpośrednich i opcji zarządzania żądanymi właściwościami bliźniaczej reprezentacji.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 96f93325e0f17daaaf2bad91123fea81531ca152
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566966"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Używanie eksploratora chmury do Visual Studio zarządzania Azure IoT Hub urządzeniami

![Diagram end-to-end](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

Z tego artykułu dowiesz się, jak używać Eksploratora chmury dla komputerów Visual Studio z różnymi opcjami zarządzania na komputerze dewelopera. [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) to przydatne rozszerzenie Visual Studio, które umożliwia wyświetlanie zasobów platformy Azure, sprawdzanie ich właściwości i wykonywanie kluczowych akcji dewelopera z poziomu Visual Studio. Zawiera ona opcje zarządzania, których można użyć do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                    |
|----------------------------|--------------------------------|
| Metody bezpośrednie             | Nakłoń urządzenie do działania, takiego jak uruchamianie lub zatrzymywanie wysyłania komunikatów lub ponowne uruchamianie urządzenia.                                        |
| Odczytywanie bliźniaczej reprezentacji urządzenia           | Pobierz raportny stan urządzenia. Na przykład urządzenie zgłasza, że dioda LED miga teraz.                                    |
| Aktualizowanie bliźniaczej reprezentacji urządzenia         | Umieść urządzenie w pewnych stanach, takich jak ustawienie diody LED na zielono lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Komunikaty z chmury do urządzenia   | Wysyłanie powiadomień do urządzenia. Na przykład: "Dzisiaj bardzo prawdopodobne jest, że będzie płakać. Pamiętaj, aby przyniesić do niej wszystkiego".              |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [Wskazówki](iot-hub-devguide-d2c-guidance.md) dotyczące komunikacji między urządzeniami a chmurą i Wskazówki dotyczące komunikacji z chmury [do urządzeń.](iot-hub-devguide-c2d-guidance.md)

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. IoT Hub bliźniaczej reprezentacji urządzenia dla każdego urządzenia, które nawiązuje z nim połączenie. Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [Wprowadzenie do bliźniaczych reprezentacji urządzeń.](iot-hub-node-node-twin-getstarted.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure.

- Konto Azure IoT Hub subskrypcji.

- Microsoft Visual Studio 2017 Update 9 lub nowszy. W tym artykule [Visual Studio 2017 lub Visual Studio 2019.](https://www.visualstudio.com/vs/)

- Składnik Cloud Explorer z Instalator programu Visual Studio, który jest domyślnie wybierany z obciążeniem platformy Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualizowanie programu Cloud Explorer do najnowszej wersji

Składnik Cloud Explorer z programu Instalator programu Visual Studio for Visual Studio 2017 obsługuje tylko monitorowanie komunikatów z urządzenia do chmury i z chmury do urządzenia. Aby użyć Visual Studio 2017, pobierz i zainstaluj najnowszą wersję programu [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum

1. W Visual Studio wybierz pozycję **Wyświetl Eksplorator**  >  **chmury,** aby otworzyć Eksplorator chmury.

1. Wybierz ikonę Zarządzanie kontem, aby wyświetlić subskrypcje.

    ![Ikona Zarządzanie kontami](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Jeśli zalogowano się na platformie Azure, zostaną wyświetlone Twoje konta. Aby zalogować się do platformy Azure po raz pierwszy, wybierz **pozycję Dodaj konto.**

1. Wybierz subskrypcje platformy Azure, których chcesz użyć, a następnie wybierz pozycję **Zastosuj.**

1. Rozwiń swoją subskrypcję, a następnie **rozwiń węzeł IoT Hubs.**  W każdym koncentratorze można zobaczyć urządzenia dla tego centrum. Kliknij prawym przyciskiem myszy jedno urządzenie, aby uzyskać dostęp do opcji zarządzania.

    ![Opcje zarządzania](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Metody bezpośrednie

Aby użyć metod bezpośrednich, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz **pozycję Wywołaj metodę bezpośrednią urządzenia.**

1. Wprowadź nazwę metody i ładunek w **poleceniu Invoke Direct Method (Wywołaj metodę bezpośrednią),** a następnie wybierz przycisk **OK.**

    Wyniki są wyświetlane w **danych wyjściowych**.

## <a name="update-device-twin"></a>Aktualizowanie bliźniaczej reprezentacji urządzenia

Aby edytować bliźniacze reprezentacji urządzenia, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Edytuj bliźniacze reprezentacji urządzenia.**

   Zostanie **azure-iot-device-twin.jsw pliku** z zawartością bliźniaczej reprezentacji urządzenia.

1. Dokonaj edycji **tagów lub** **pól properties.desired** wazure-iot-device-twin.js **pliku.**

1. Naciśnij **klawisze Ctrl+S,** aby zaktualizować bliźniacze reprezentacji urządzenia.

   Wyniki są wyświetlane w **danych wyjściowych**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z aplikacji IoT Hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij komunikat C2D.**

1. Wprowadź komunikat w wyślij **wiadomość C2D i** wybierz **przycisk OK.**

   Wyniki są wyświetlane w **danych wyjściowych**.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak używać eksploratora chmury na Visual Studio różnych opcjach zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
