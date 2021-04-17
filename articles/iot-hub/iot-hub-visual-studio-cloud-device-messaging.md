---
title: Zarządzanie komunikatami urządzeń Azure IoT Hub VS Cloud Explorer
description: Dowiedz się, jak używać eksploratora chmury na Visual Studio do monitorowania komunikatów w chmurze i wysyłania komunikatów z chmury do urządzeń w Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 8461a77d06a63c2ac319323a91b5577ca4dce1cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567034"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Użyj eksploratora chmury Visual Studio do wysyłania i odbierania komunikatów między urządzeniem i IoT Hub

![Diagram end-to-end](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

Z tego artykułu dowiesz się, jak używać programu Cloud Explorer for Visual Studio do monitorowania komunikatów z urządzenia do chmury i wysyłania komunikatów z chmury do urządzeń. Komunikaty z urządzenia do chmury mogą być danymi czujników zbieranych przez urządzenie, a następnie wysyłanych do IoT Hub. Komunikaty z chmury do urządzenia mogą być poleceniami, które IoT Hub do urządzenia. Na przykład miga dioda LED podłączona do urządzenia.

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) to przydatne rozszerzenie Visual Studio, które umożliwia wyświetlanie zasobów platformy Azure, sprawdzanie ich właściwości i wykonywanie kluczowych akcji dewelopera z poziomu Visual Studio. W tym artykule opisano, jak używać eksploratora chmury do wysyłania i odbierania komunikatów między urządzeniem i centrum.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure.

- Konto Azure IoT Hub subskrypcji.

- Microsoft Visual Studio 2017 Update 9 lub nowszy. W tym artykule Visual Studio [2019 r.](https://www.visualstudio.com/vs/)

- Składnik Cloud Explorer z usługi Instalator programu Visual Studio, który jest domyślnie wybierany z obciążeniem platformy Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualizowanie programu Cloud Explorer do najnowszej wersji

Składnik Cloud Explorer z programu Instalator programu Visual Studio for Visual Studio 2017 obsługuje tylko monitorowanie komunikatów z urządzenia do chmury i z chmury do urządzenia. Aby użyć programu Visual Studio 2017, pobierz i zainstaluj najnowszą wersję programu [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum

Aby uzyskać dostęp do centrum, wykonaj następujące kroki:

1. W Visual Studio wybierz pozycję **Wyświetl Eksplorator**  >  **chmury,** aby otworzyć Eksplorator chmury.

1. Wybierz ikonę Zarządzanie kontem, aby wyświetlić subskrypcje.

    ![Ikona Zarządzanie kontami](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Jeśli zalogowano się na platformie Azure, zostaną wyświetlone Twoje konta. Aby zalogować się do platformy Azure po raz pierwszy, wybierz **pozycję Dodaj konto.**

1. Wybierz subskrypcje platformy Azure, których chcesz użyć, a następnie wybierz pozycję **Zastosuj.**

1. Rozwiń swoją subskrypcję, a następnie **rozwiń węzeł IoT Hubs.**  W każdym koncentratorze można zobaczyć urządzenia dla tego centrum.

    ![Lista urządzeń](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorowanie komunikatów z urządzenia do chmury

Aby monitorować komunikaty wysyłane z urządzenia do IoT Hub, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie lub IoT Hub i wybierz **polecenie Rozpocznij monitorowanie komunikatu D2C.**

    ![Rozpocznij monitorowanie komunikatu D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. Monitorowane komunikaty są wyświetlane w obszarze **Dane wyjściowe**.

    ![Monitorowanie wyniku komunikatu D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Aby zatrzymać monitorowanie, kliknij prawym przyciskiem myszy dowolne urządzenie lub IoT Hub i wybierz polecenie **Zatrzymaj monitorowanie komunikatu D2C.**

## <a name="send-cloud-to-device-messages"></a>Wysyłanie komunikatów z chmury do urządzeń

Aby wysłać wiadomość z IoT Hub do urządzenia, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Wyślij komunikat C2D.**

1. Wprowadź komunikat w polu wejściowym.

    ![Wysyłanie komunikatu C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Wyniki są wyświetlane w **obszarze Dane wyjściowe**.

    ![Wynik wysyłania komunikatu C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak monitorować komunikaty z urządzenia do chmury i wysyłać komunikaty z chmury do urządzenia między urządzeniem IoT a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]