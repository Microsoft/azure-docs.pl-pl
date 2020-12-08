---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a3cb0986637d5ce238930fb87aef71fed684097a
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842299"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Visual Studio Code integruje się z [narzędziami Azure Functions Core](../articles/azure-functions/functions-run-local.md) , aby umożliwić uruchomienie tego projektu na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

1. Aby wywołać funkcję, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**. Jeśli masz problemy z uruchamianiem w systemie Windows, upewnij się, że domyślny Terminal dla Visual Studio Code nie jest ustawiony na **WSL bash**.

1. Jeśli nie zainstalowano jeszcze Azure Functions Core Tools, wybierz opcję **Zainstaluj** w wierszu polecenia. Po zainstalowaniu podstawowych narzędzi aplikacja zostanie uruchomiona na panelu **terminalu** . Można zobaczyć punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP, działającej lokalnie.

    ![Funkcja lokalna VS Code danych wyjściowych](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Po uruchomieniu podstawowych narzędzi przejdź do następującego adresu URL, aby wykonać żądanie GET, które zawiera `?name=Functions` ciąg zapytania.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Zostanie zwrócona odpowiedź, która wygląda podobnie do następującej w przeglądarce:

    ![Przeglądarka — przykładowe dane wyjściowe hosta lokalnego](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informacje o żądaniu są wyświetlane w panelu **terminalu** .

    ![Rozpoczęcie VS Code hosta zadań — dane wyjściowe terminalu](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia i rozłączyć debuger.
