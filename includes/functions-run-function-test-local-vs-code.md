---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 43da0ea4ddfc5410425465d436522523739218fe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91408581"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Visual Studio Code integruje się z [narzędziami Azure Functions Core](../articles/azure-functions/functions-run-local.md) , aby umożliwić uruchomienie tego projektu na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

1. Aby wywołać funkcję, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. Jeśli nie zainstalowano jeszcze Azure Functions Core Tools, wybierz opcję **Zainstaluj** w wierszu polecenia. Po zainstalowaniu podstawowych narzędzi aplikacja zostanie uruchomiona na panelu **terminalu** . Można zobaczyć punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP, działającej lokalnie.

    ![Funkcja lokalna VS Code danych wyjściowych](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Po uruchomieniu podstawowych narzędzi przejdź do następującego adresu URL, aby wykonać żądanie GET, które zawiera `?name=Functions` ciąg zapytania.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Zostanie zwrócona odpowiedź, która wygląda podobnie do następującej w przeglądarce:

    ![Przeglądarka — przykładowe dane wyjściowe hosta lokalnego](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informacje o żądaniu są wyświetlane w panelu **terminalu** .

    ![Rozpoczęcie VS Code hosta zadań — dane wyjściowe terminalu](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia i rozłączyć debuger.
