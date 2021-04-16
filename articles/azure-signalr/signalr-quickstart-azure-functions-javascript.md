---
title: Używanie języka JavaScript do tworzenia pokoju rozmów z Azure Functions i SignalR Service
description: Przewodnik Szybki start, w Azure SignalR Service i Azure Functions do tworzenia pokoju rozmów przy użyciu języka JavaScript.
author: sffamily
ms.author: zhshang
ms.date: 12/14/2019
ms.topic: quickstart
ms.service: signalr
ms.devlang: javascript
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 5e2c9dcd1efc85e1ea3fe6381cbfbff0c5e62fc5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533218"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Szybki start: używanie języka JavaScript do tworzenia pokoju rozmów z Azure Functions i SignalR Service

Azure SignalR Service umożliwia łatwe dodawanie funkcji czasu rzeczywistego do aplikacji, a Azure Functions to platforma bez serwera, która umożliwia uruchamianie kodu bez zarządzania infrastrukturą. W tym przewodniku Szybki start użyjemy języka JavaScript do tworzenia bez serwera aplikacji do czatów w czasie rzeczywistym przy użyciu SignalR Service i Functions.

## <a name="prerequisites"></a>Wymagania wstępne

- Edytor kodu, taki [jak Visual Studio Code](https://code.visualstudio.com/)
- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), wersja 2 lub nowsza. Służy do lokalnego uruchamiania aplikacji funkcji platformy Azure.
- [Node.js](https://nodejs.org/en/download/), wersja 10.x

   > [!NOTE]
   > Przykłady powinny współpracować z innymi wersjami środowiska Node.js zobacz dokumentację dotyczącą Azure Functions [środowiska uruchomieniowego,](../azure-functions/functions-versions.md#languages) aby uzyskać więcej informacji.

> [!NOTE]
> Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjs).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjs).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjs).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjs).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurowanie i uruchamianie aplikacji funkcji platformy Azure

1. W przeglądarce z otwartą witryną Azure Portal potwierdź, że wdrożone wcześniej wystąpienie usługi SignalR Service zostało pomyślnie utworzone, wyszukując jego nazwę w polu wyszukiwania w górnej części witryny. Wybierz wystąpienie, aby je otworzyć.

    ![Wyszukiwanie wystąpienia usługi SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

    ![Zrzut ekranu przedstawiający najważniejsze podstawowe ciągi połączenia.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. W edytorze kodu otwórz folder *src/chat/javascript* w sklonowanym repozytorium.

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Funkcje języka JavaScript są organizowane w foldery. W każdym folderze znajdują się dwa pliki: plik *function.json* definiuje powiązania używane w funkcji, a plik *index.js* to treść funkcji. W tej aplikacji funkcji znajdują się dwie funkcje wyzwalane przez protokół HTTP:

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **messages** — ta funkcja otrzymuje wiadomość czatu w treści żądania i używa powiązania danych wyjściowych *SignalR* do rozgłoszenia wiadomości do wszystkich połączonych aplikacji klienckich.

1. W terminalu upewnij się, że jesteś w folderze *src/chat/javascript.* Uruchom aplikację funkcji.

    ```bash
    func start
    ```

    ![Tworzenie usługi SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)
    
Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsjs).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsjs).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsjs).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start sbudowaliśmy i uruchomiliśmy bez serwera w czasie rzeczywistym aplikację w VS Code. Teraz dowiedz się więcej o sposobie wdrażania usługi Azure Functions z programu VS Code.

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Functions za pomocą programu VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
