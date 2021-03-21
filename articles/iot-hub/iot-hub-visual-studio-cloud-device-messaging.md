---
title: Korzystanie z programu VS Cloud Explorer do zarządzania usługą Azure IoT Hub Device Messaging
description: Dowiedz się, jak za pomocą Eksploratora chmury dla programu Visual Studio monitorować urządzenia w chmurze i wysyłać komunikaty z chmury do urządzeń w usłudze Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74079489"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Użyj Eksploratora chmury dla programu Visual Studio, aby wysyłać i odbierać komunikaty między urządzeniem i IoT Hub

![Diagram kompleksowy](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Eksplorator chmury](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) to przydatne rozszerzenie programu Visual Studio, które umożliwia wyświetlanie zasobów platformy Azure, badanie ich właściwości i wykonywanie kluczowych akcji deweloperskich z poziomu programu Visual Studio. W tym artykule opisano sposób korzystania z programu Cloud Explorer do wysyłania i odbierania komunikatów między urządzeniem a centrum.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym artykule dowiesz się, jak używać Eksploratora chmury dla programu Visual Studio do monitorowania komunikatów wysyłanych z urządzenia do chmury i wysyłania komunikatów z chmury do urządzenia. Komunikaty przesyłane z urządzenia do chmury mogą być danymi czujników, które urządzenie zbiera, a następnie wysyła do IoT Hub. Komunikaty z chmury do urządzenia mogą być poleceniami wysyłanymi przez IoT Hub do urządzenia. Na przykład miganie diody LED połączonej z urządzeniem.

## <a name="what-you-do"></a>Co robisz

W tym artykule przedstawiono następujące zadania:

- Użyj Eksploratora chmury dla programu Visual Studio do monitorowania komunikatów wysyłanych z urządzenia do chmury.

- Użyj Eksploratora chmury dla programu Visual Studio, aby wysyłać komunikaty z chmury do urządzenia.

## <a name="what-you-need"></a>Potrzebne elementy

Wymagane są następujące wymagania wstępne:

- Aktywna subskrypcja platformy Azure.

- IoT Hub platformy Azure w ramach subskrypcji.

- Microsoft Visual Studio 2017 Update 9 lub nowszy. W tym artykule jest wykorzystywany [program Visual Studio 2019](https://www.visualstudio.com/vs/).

- Składnik Cloud Explorer Instalator programu Visual Studio, który jest domyślnie wybrany z obciążeniem platformy Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Zaktualizuj program Cloud Explorer do najnowszej wersji

Składnik Cloud Explorer Instalator programu Visual Studio dla programu Visual Studio 2017 obsługuje tylko monitorowanie komunikatów przesyłanych z urządzenia do chmury i z chmury do urządzenia. Aby użyć programu Visual Studio 2017, Pobierz i zainstaluj najnowszą wersję programu [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum

Aby uzyskać dostęp do centrum, wykonaj następujące kroki:

1. W programie Visual Studio wybierz pozycję **Wyświetl** program  >  **Cloud Explorer** , aby otworzyć Eksploratora chmury.

1. Wybierz ikonę Zarządzanie kontem, aby wyświetlić subskrypcje.

    ![Ikona zarządzania kontami](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Jeśli logujesz się do platformy Azure, Twoje konta są wyświetlane. Aby zalogować się do platformy Azure po raz pierwszy, wybierz pozycję **Dodaj konto**.

1. Wybierz subskrypcje platformy Azure, których chcesz użyć, a następnie wybierz pozycję **Zastosuj**.

1. Rozwiń swoją subskrypcję, a następnie rozwiń węzeł **centra IoT**.  W każdym centrum widoczne są urządzenia dla tego centrum.

    ![Lista urządzeń](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorowanie komunikatów przesyłanych z urządzenia do chmury

Aby monitorować komunikaty wysyłane z urządzenia do IoT Hub, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy IoT Hub lub urządzenie, a następnie wybierz pozycję **Rozpocznij monitorowanie komunikatu D2C**.

    ![Rozpocznij monitorowanie komunikatu D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. Monitorowane komunikaty są wyświetlane w obszarze **dane wyjściowe**.

    ![Wynik monitorowania komunikatu D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Aby zatrzymać monitorowanie, kliknij prawym przyciskiem myszy dowolny IoT Hub lub urządzenie, a następnie wybierz polecenie **Zatrzymaj monitorowanie komunikatu D2C**.

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać komunikat z IoT Hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij wiadomość C2D**.

1. Wprowadź komunikat w polu wejściowym.

    ![Wyślij wiadomość C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Wyniki są wyświetlane w obszarze **dane wyjściowe**.

    ![Wyślij wynik komunikatu C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak monitorować komunikaty z urządzenia do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT i usługą Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]