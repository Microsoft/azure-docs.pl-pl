---
title: Zarządzanie urządzeniami w usłudze Azure IoT w programie Visual Studio Cloud Explorer
description: Użyj programu Cloud Explorer dla programu Visual Studio dla usługi Azure IoT Hub zarządzanie urządzeniami, w tym metod bezpośrednich i opcji zarządzania żądanymi właściwościami sznurka.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73953178"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Korzystanie z Eksploratora chmury dla programu Visual Studio dla usługi Azure IoT Hub zarządzanie urządzeniami

![Diagram kompleksowy](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Eksplorator chmury](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) to przydatne rozszerzenie programu Visual Studio, które umożliwia wyświetlanie zasobów platformy Azure, badanie ich właściwości i wykonywanie kluczowych akcji deweloperskich z poziomu programu Visual Studio. Zawiera opcje zarządzania, których można użyć do wykonywania różnych zadań.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opcja zarządzania          | Zadanie                    |
|----------------------------|--------------------------------|
| Metody bezpośrednie             | Nadawanie urządzeniu działania takie jak uruchamianie lub zatrzymywanie wysyłania komunikatów lub ponowne uruchamianie urządzenia.                                        |
| Odczytaj sznurki urządzenia           | Pobierz zgłoszony stan urządzenia. Na przykład urządzenie raportuje diodę LED teraz.                                    |
| Aktualizacja sznurka urządzenia         | Umieść urządzenie w określonych stanach, takich jak ustawienie diody LED na zieloną lub ustawienie interwału wysyłania danych telemetrycznych na 30 minut.         |
| Komunikaty z chmury do urządzenia   | Wyślij powiadomienia do urządzenia. Na przykład "bardzo prawdopodobnie jest to deszcz. Nie zapomnij przenieść parasola ".              |

Aby uzyskać bardziej szczegółowe wyjaśnienie różnic i wskazówek dotyczących korzystania z tych opcji, zobacz [wskazówki dotyczące komunikacji między urządzeniami i chmurą](iot-hub-devguide-d2c-guidance.md) oraz [wskazówki dotyczące komunikacji między chmurą i urządzeniem](iot-hub-devguide-c2d-guidance.md).

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. IoT Hub utrzymuje sznurki urządzenia dla każdego urządzenia, które nawiązuje z nim połączenie. Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [wprowadzenie do usługi Device bliźniaczych reprezentacji](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym artykule dowiesz się, jak używać Eksploratora chmury dla programu Visual Studio z różnymi opcjami zarządzania na komputerze deweloperskim.

## <a name="what-you-do"></a>Co robisz

W tym artykule można uruchomić program Cloud Explorer dla programu Visual Studio z różnymi opcjami zarządzania.

## <a name="what-you-need"></a>Potrzebne elementy

Wymagane są następujące wymagania wstępne:

- Aktywna subskrypcja platformy Azure.

- IoT Hub platformy Azure w ramach subskrypcji.

- Microsoft Visual Studio 2017 Update 9 lub nowszy. W tym artykule jest wykorzystywany [program Visual studio 2017 lub Visual studio 2019](https://www.visualstudio.com/vs/).

- Składnik Cloud Explorer z Instalator programu Visual Studio, który jest domyślnie wybrany z obciążeniem platformy Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Zaktualizuj program Cloud Explorer do najnowszej wersji

Składnik Cloud Explorer Instalator programu Visual Studio dla programu Visual Studio 2017 obsługuje tylko monitorowanie komunikatów przesyłanych z urządzenia do chmury i z chmury do urządzenia. Aby użyć programu Visual Studio 2017, Pobierz i zainstaluj najnowszą wersję programu [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum

1. W programie Visual Studio wybierz pozycję **Wyświetl**program  >  **Cloud Explorer** , aby otworzyć Eksploratora chmury.

1. Wybierz ikonę Zarządzanie kontem, aby wyświetlić subskrypcje.

    ![Ikona zarządzania kontami](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Jeśli logujesz się do platformy Azure, Twoje konta są wyświetlane. Aby zalogować się do platformy Azure po raz pierwszy, wybierz pozycję **Dodaj konto**.

1. Wybierz subskrypcje platformy Azure, których chcesz użyć, a następnie wybierz pozycję **Zastosuj**.

1. Rozwiń swoją subskrypcję, a następnie rozwiń węzeł **centra IoT**.  W każdym centrum widoczne są urządzenia dla tego centrum. Kliknij prawym przyciskiem myszy jedno urządzenie, aby uzyskać dostęp do opcji zarządzania.

    ![Opcje zarządzania](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Metody bezpośrednie

Aby korzystać z metod bezpośrednich, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Wywołaj metodę bezpośrednią urządzenia**.

1. Wprowadź nazwę metody i ładunek w **metodzie Invoke Direct**, a następnie wybierz przycisk **OK**.

    Wyniki są wyświetlane w **danych wyjściowych**.

## <a name="update-device-twin"></a>Aktualizacja sznurka urządzenia

Aby edytować sznurki urządzenia, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz pozycję **Edytuj sznurek urządzeń**.

   Zostanie otwarty **azure-iot-device-twin.js** pliku z zawartością sznurka urządzenia.

1. Wprowadź edycję **tagów** lub **właściwości. żądane** pola do **azure-iot-device-twin.jsna** pliku.

1. Naciśnij **kombinację klawiszy Ctrl + S** , aby zaktualizować sznurki urządzenia.

   Wyniki są wyświetlane w **danych wyjściowych**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać komunikat z IoT Hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij wiadomość C2D**.

1. Wprowadź komunikat w polu **Wyślij wiadomość C2D** i wybierz pozycję **OK**.

   Wyniki są wyświetlane w **danych wyjściowych**.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak używać Eksploratora chmury dla programu Visual Studio z różnymi opcjami zarządzania.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
