---
title: Używanie języka Java do tworzenia pokoju rozmów z usługą Azure Functions i usługi sygnalizującej
description: Przewodnik Szybki Start dotyczący korzystania z usługi Azure Signal Service i Azure Functions tworzenia pokoju rozmów przy użyciu języka Java.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: 62f31d8426e86cc20e957f3a47b30109dee0a634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185678"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Szybki Start: używanie języka Java do tworzenia pokoju rozmów z usługą Azure Functions i usługi sygnalizującej

Usługa Azure Signal Service umożliwia łatwe dodawanie funkcji w czasie rzeczywistym do aplikacji, a Azure Functions jest platformą bezserwerową, która umożliwia uruchamianie kodu bez konieczności zarządzania infrastrukturą. W tym przewodniku szybki start używasz języka Java do kompilowania bezserwerowej aplikacji do rozmowy w czasie rzeczywistym przy użyciu usługi sygnalizującej i funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/)
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Służy do lokalnego uruchamiania aplikacji funkcji platformy Azure.

   > [!NOTE]
   > Wymagane powiązania usługi sygnalizujące w języku Java są obsługiwane tylko w podstawowych narzędziach funkcji platformy Azure w wersji 2.4.419 (host w wersji 2.0.12332) lub nowszej.

   > [!NOTE]
   > Aby zainstalować rozszerzenia, Azure Functions Core Tools wymaga zainstalowanego [zestaw .NET Core SDK](https://www.microsoft.com/net/download) . Do tworzenia aplikacji funkcji platformy Azure dla w języku JavaScript nie jest jednak wymagana jakakolwiek wiedza dotycząca platformy .NET.

- Zestaw [Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8
- Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza

> [!NOTE]
> Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurowanie i uruchamianie aplikacji funkcji platformy Azure

1. W przeglądarce z otwartą witryną Azure Portal potwierdź, że wdrożone wcześniej wystąpienie usługi SignalR Service zostało pomyślnie utworzone, wyszukując jego nazwę w polu wyszukiwania w górnej części witryny. Wybierz wystąpienie, aby je otworzyć.

    ![Wyszukiwanie wystąpienia usługi SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

    ![Tworzenie usługi SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. W edytorze kodu Otwórz folder *src/Chat/Java* w sklonowanym repozytorium.

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Główny plik, który zawiera funkcje są w języku *src/Chat/Java/src/Main/Java/com/funkcja/Functions. Java*:

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **SendMessage** — odbiera komunikat rozmowy w treści żądania i używa powiązania danych wyjściowych *sygnalizującego* , aby emitować komunikat do wszystkich połączonych aplikacji klienckich.

1. Upewnij się, że jesteś w folderze *src/Chat/Java* . Kompiluj aplikację funkcji.

    ```bash
    mvn clean package
    ```

1. Uruchom aplikację funkcji lokalnie.

    ```bash
    mvn azure-functions:run
    ```
    
Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Masz problemy? Wypróbuj [Przewodnik rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono i uruchomiono aplikację bezserwerową w czasie rzeczywistym przy użyciu Maven. Następnie Dowiedz się, jak utworzyć Azure Functions języka Java od podstaw.

> [!div class="nextstepaction"]
> [Tworzenie pierwszej funkcji przy użyciu języka Java i narzędzia Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)