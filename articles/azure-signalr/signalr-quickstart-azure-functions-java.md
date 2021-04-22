---
title: Używanie języka Java do tworzenia pokoju rozmów z Azure Functions i SignalR Service
description: Przewodnik Szybki start, w Azure SignalR Service i Azure Functions tworzenia pokoju rozmów przy użyciu języka Java.
author: sffamily
ms.author: zhshang
ms.date: 03/04/2019
ms.topic: quickstart
ms.service: signalr
ms.devlang: java
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: 0d93b9b645aaf4190a36dbc523d40dec2757a18b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869801"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Szybki start: używanie języka Java do tworzenia pokoju rozmów z Azure Functions i SignalR Service

Azure SignalR Service umożliwia łatwe dodawanie funkcji czasu rzeczywistego do aplikacji, a Azure Functions to platforma bez serwera, która umożliwia uruchamianie kodu bez zarządzania infrastrukturą. W tym przewodniku Szybki start użyjemy języka Java do tworzenia bez serwera aplikacji do czatów w czasie rzeczywistym przy użyciu SignalR Service i Functions.

## <a name="prerequisites"></a>Wymagania wstępne

- Edytor kodu, taki [jak Visual Studio Code](https://code.visualstudio.com/)
- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools .](https://github.com/Azure/azure-functions-core-tools#installing) Służy do lokalnego uruchamiania aplikacji funkcji platformy Azure.

   > [!NOTE]
   > Wymagane powiązania SignalR Service w języku Java są obsługiwane tylko w narzędziach Azure Function Core Tools w wersji 2.4.419 (wersja hosta 2.0.12332) lub nowsza.

   > [!NOTE]
   > Aby zainstalować rozszerzenia, Azure Functions Core Tools wymaga [zestaw .NET Core SDK](https://dotnet.microsoft.com/download) instalacji. Do tworzenia aplikacji funkcji platformy Azure dla w języku JavaScript nie jest jednak wymagana jakakolwiek wiedza dotycząca platformy .NET.

- Zestaw [Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8
- Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza

> [!NOTE]
> Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów](signalr-howto-troubleshoot-guide.md) lub [daj nam znać](https://aka.ms/asrs/qsjava).

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurowanie i uruchamianie aplikacji funkcji platformy Azure

1. W przeglądarce z otwartą witryną Azure Portal potwierdź, że wdrożone wcześniej wystąpienie usługi SignalR Service zostało pomyślnie utworzone, wyszukując jego nazwę w polu wyszukiwania w górnej części witryny. Wybierz wystąpienie, aby je otworzyć.

    ![Wyszukiwanie wystąpienia usługi SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

    ![Tworzenie usługi SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. W edytorze kodu otwórz folder *src/chat/java* w sklonowanym repozytorium.

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Główny plik zawierający funkcje znajduje się w pliku *src/chat/java/src/main/java/com/function/Functions.java:*

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **sendMessage** — odbiera komunikat czatu w treści żądania i używa powiązania danych wyjściowych *signalr* do rozgłas ich emisji do wszystkich połączonych aplikacji klienckich.

1. W terminalu upewnij się, że jesteś w folderze *src/chat/java.* Skompilowanie aplikacji funkcji.

    ```bash
    mvn clean package
    ```

1. Uruchom aplikację funkcji lokalnie.

    ```bash
    mvn azure-functions:run
    ```
    
Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsjava).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsjava).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Masz problemy? Wypróbuj przewodnik [rozwiązywania problemów lub](signalr-howto-troubleshoot-guide.md) [daj nam znać](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start sbudowaliśmy i uruchomiliśmy bez serwera w czasie rzeczywistym aplikację przy użyciu narzędzia Maven. Następnie dowiedz się, jak utworzyć środowisko Java Azure Functions od podstaw.

> [!div class="nextstepaction"]
> [Tworzenie pierwszej funkcji przy użyciu języka Java i narzędzia Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)
