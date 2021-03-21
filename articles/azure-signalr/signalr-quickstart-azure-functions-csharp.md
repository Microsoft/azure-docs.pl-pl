---
title: 'Serwer usługi Azure sygnalizujący Niemniej — Szybki Start — C #'
description: Przewodnik Szybki Start dotyczący korzystania z usługi Azure Signal Service i Azure Functions tworzenia pokoju rozmów przy użyciu języka C#.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/25/2020
ms.author: zhshang
ms.openlocfilehash: 1f28058a766144ada3326b3ee4ce09ca503d1896
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94873883"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Szybki Start: Tworzenie pokoju rozmów z usługami Azure Functions i sygnalizacyjnymi przy użyciu języka C\#

Usługa Azure SignalR Service umożliwia łatwe dodawanie funkcji czasu rzeczywistego do aplikacji. Usługa Azure Functions to bezserwerowa platforma, która pozwala uruchamiać kod bez zarządzania jakąkolwiek infrastrukturą. W tym przewodniku Szybki start dowiesz się, jak utworzyć bezserwerową aplikację czatu w czasie rzeczywistym za pomocą usług SignalR Service i Functions.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

Ten samouczek można również uruchomić w wierszu polecenia (macOS, Windows lub Linux) przy użyciu [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing), [zestaw .NET Core SDK](https://dotnet.microsoft.com/download)i ulubionego edytora kodu.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet) przed rozpoczęciem.

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qscsharp).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qscsharp).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurowanie i uruchamianie aplikacji funkcji platformy Azure

1. Uruchom program Visual Studio (lub inny edytor kodu) i Otwórz rozwiązanie w folderze *src/Chat/CSharp* sklonowanego repozytorium.

1. W przeglądarce z otwartą witryną Azure Portal potwierdź, że wdrożone wcześniej wystąpienie usługi SignalR Service zostało pomyślnie utworzone, wyszukując jego nazwę w polu wyszukiwania w górnej części witryny. Wybierz wystąpienie, aby je otworzyć.

    ![Wyszukiwanie wystąpienia usługi SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

1. Wróć do programu Visual Studio — **Eksplorator rozwiązań** zmień nazwę *local.settings.sample.jsna* , aby *local.settings.js*.

1. W pliku *local.settings.json* wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Otwórz plik *Functions.cs*. W tej aplikacji funkcji znajdują się dwie funkcje wyzwalane przez protokół HTTP:

    - **GetSignalRInfo** — używa `SignalRConnectionInfo` powiązania danych wejściowych do generowania i zwracania prawidłowych informacji o połączeniu.
    - **SendMessage** — ta funkcja otrzymuje wiadomość rozmowy w treści żądania i używa powiązania danych wyjściowych usługi *SignalR* do rozpowszechnienia komunikatu do wszystkich połączonych aplikacji klienckich.

1. Użyj jednej z następujących opcji, aby lokalnie uruchomić aplikację funkcji platformy Azure.

    - **Visual Studio**: w menu *debugowanie* wybierz *Rozpocznij debugowanie* , aby uruchomić aplikację.

        ![Debugowanie aplikacji](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Wiersz polecenia**: wykonaj następujące polecenie, aby uruchomić hosta funkcji.

        ```bash
        func start
        ```
Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qscsharp)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono i uruchomiono aplikację bezserwerową w czasie rzeczywistym w programie Visual Studio. W dalszej kolejności zapoznaj się ze sposobem opracowywania i wdrażania funkcji usługi Azure Functions za pomocą programu Visual Studio.

> [!div class="nextstepaction"]
> [Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio](../azure-functions/functions-develop-vs.md)

