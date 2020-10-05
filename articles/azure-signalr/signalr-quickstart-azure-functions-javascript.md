---
title: Używanie języka JavaScript do tworzenia pokoju rozmów z usługą Azure Functions i usługi sygnalizującej
description: Przewodnik Szybki Start dotyczący korzystania z usługi Azure Signal Service i Azure Functions tworzenia pokoju rozmów przy użyciu języka JavaScript.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: c210096c1765015378f12f8c5e01fc9f8f97e10b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327684"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Szybki Start: używanie języka JavaScript do tworzenia pokoju rozmów z usługą Azure Functions i usługi sygnalizującej

Usługa Azure Signal Service umożliwia łatwe dodawanie funkcji w czasie rzeczywistym do aplikacji, a Azure Functions jest platformą bezserwerową, która umożliwia uruchamianie kodu bez konieczności zarządzania infrastrukturą. W tym przewodniku szybki start używasz języka JavaScript do kompilowania bezserwerowej aplikacji czatu w czasie rzeczywistym przy użyciu usługi sygnalizującej i funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/)
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), wersja 2 lub nowsza. Służy do lokalnego uruchamiania aplikacji funkcji platformy Azure.
- [Node.js](https://nodejs.org/en/download/), wersja 10. x

   > [!NOTE]
   > Przykłady powinny współpracować z innymi wersjami Node.js, zobacz [dokumentację dotyczącą wersji środowiska uruchomieniowego Azure Functions](../azure-functions/functions-versions.md#languages) , aby uzyskać więcej informacji.

> [!NOTE]
> Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsjs)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsjs)

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurowanie i uruchamianie aplikacji funkcji platformy Azure

1. W przeglądarce z otwartą witryną Azure Portal potwierdź, że wdrożone wcześniej wystąpienie usługi SignalR Service zostało pomyślnie utworzone, wyszukując jego nazwę w polu wyszukiwania w górnej części witryny. Wybierz wystąpienie, aby je otworzyć.

    ![Wyszukiwanie wystąpienia usługi SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

    ![Tworzenie usługi SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. W edytorze kodu Otwórz folder *src/Chat/JavaScript* w sklonowanym repozytorium.

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Funkcje języka JavaScript są organizowane w foldery. W każdym folderze znajdują się dwa pliki: plik *function.json* definiuje powiązania używane w funkcji, a plik *index.js* to treść funkcji. W tej aplikacji funkcji znajdują się dwie funkcje wyzwalane przez protokół HTTP:

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **messages** — ta funkcja otrzymuje wiadomość czatu w treści żądania i używa powiązania danych wyjściowych *SignalR* do rozgłoszenia wiadomości do wszystkich połączonych aplikacji klienckich.

1. Upewnij się, że jesteś w folderze *src/Chat/JavaScript* . Uruchom aplikację funkcji.

    ```bash
    func start
    ```

    ![Tworzenie usługi SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsjs)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono i uruchomiono aplikację bezserwerową w czasie rzeczywistym w VS Code. Teraz dowiedz się więcej o sposobie wdrażania usługi Azure Functions z programu VS Code.

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Functions za pomocą programu VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsjs)
