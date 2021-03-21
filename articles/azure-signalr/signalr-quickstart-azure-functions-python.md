---
title: Funkcja szybkiego startu usługi Azure Signal Service — Python
description: Przewodnik Szybki Start dotyczący korzystania z usługi Azure Signal Service i Azure Functions tworzenia pokoju rozmów przy użyciu języka Python.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.custom: devx-track-python
ms.openlocfilehash: aaaf9011d38e7ec02e83db63757c434329b835e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960295"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Szybki Start: Tworzenie pokoju rozmów z usługami Azure Functions i sygnalizującymi przy użyciu języka Python

Usługa Azure SignalR Service umożliwia łatwe dodawanie funkcji czasu rzeczywistego do aplikacji. Usługa Azure Functions to bezserwerowa platforma, która pozwala uruchamiać kod bez zarządzania jakąkolwiek infrastrukturą. W tym przewodniku Szybki start dowiesz się, jak utworzyć bezserwerową aplikację czatu w czasie rzeczywistym za pomocą usług SignalR Service i Functions.

## <a name="prerequisites"></a>Wymagania wstępne

Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

Upewnij się, że masz zainstalowany edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/).

Zainstaluj [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (w wersji 2.7.1505 lub nowszej), aby lokalnie uruchamiać aplikacje funkcji platformy Azure w języku Python.

Azure Functions wymaga języka [Python 3,6 lub 3,7](https://www.python.org/downloads/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qspython).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qspython).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurowanie i uruchamianie aplikacji funkcji platformy Azure

1. W przeglądarce z otwartą witryną Azure Portal potwierdź, że wdrożone wcześniej wystąpienie usługi SignalR Service zostało pomyślnie utworzone, wyszukując jego nazwę w polu wyszukiwania w górnej części witryny. Wybierz wystąpienie, aby je otworzyć.

    ![Wyszukiwanie wystąpienia usługi SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

    ![Zaznacz i skopiuj podstawowe parametry połączenia.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. W edytorze kodu Otwórz folder *src/Chat/Python* w sklonowanym repozytorium.

1. Aby lokalnie opracowywać i testować funkcje języka Python, musisz pracować w środowisku Python 3,6 lub 3,7. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`.

    **System Linux lub macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Systemy**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Funkcje języka Python są zorganizowane w foldery. W każdym folderze są dwa pliki: *function.json* definiuje powiązania, które są używane w funkcji, a *\_ \_ init \_ \_ . PR* jest treścią funkcji. W tej aplikacji funkcji znajdują się dwie funkcje wyzwalane przez protokół HTTP:

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **messages** — ta funkcja otrzymuje wiadomość czatu w treści żądania i używa powiązania danych wyjściowych *SignalR* do rozgłoszenia wiadomości do wszystkich połączonych aplikacji klienckich.

1. Upewnij się, że jesteś w folderze *src/Chat/Python* w terminalu ze aktywowanym środowiskiem wirtualnym. Zainstaluj wymagane pakiety języka Python przy użyciu narzędzia PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Uruchom aplikację funkcji.

    ```bash
    func start
    ```

    ![Uruchom aplikację funkcji](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qspython).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qspython).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono i uruchomiono aplikację bezserwerową w czasie rzeczywistym w VS Code. Teraz dowiedz się więcej o sposobie wdrażania usługi Azure Functions z programu VS Code.

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Functions za pomocą programu VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

