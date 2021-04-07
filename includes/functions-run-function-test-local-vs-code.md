---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 5bc1d2ddb5b9ddb185a8349fb42d5f5db79c1498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201385"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Visual Studio Code integruje się z [narzędziami Azure Functions Core](../articles/azure-functions/functions-run-local.md) , aby umożliwić uruchomienie tego projektu na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

1. Aby wywołać funkcję, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**. Aplikacja zostanie uruchomiona na panelu **terminalu** . Można zobaczyć punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP, działającej lokalnie.

    ![Funkcja lokalna VS Code danych wyjściowych](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Jeśli masz problemy z uruchamianiem w systemie Windows, upewnij się, że domyślny Terminal dla Visual Studio Code nie jest ustawiony na **WSL bash**.

1. Po uruchomieniu podstawowych narzędzi przejdź do obszaru **Azure: Functions** . W obszarze **funkcje** rozwiń pozycję funkcje **projektu lokalnego**  >  . Kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Wykonaj funkcję teraz z Visual Studio Code":::
    
1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` . Naciśnij klawisz ENTER, aby wysłać ten komunikat żądania do funkcji. 

   Zamiast tego wysłano żądanie HTTP GET do <http://localhost:7071/api/HttpExample> adresu w przeglądarce internetowej.

1. Gdy funkcja jest wykonywana lokalnie i zwraca odpowiedź, zostanie zgłoszone powiadomienie w Visual Studio Code. Informacje o wykonywaniu funkcji są wyświetlane w panelu **terminalu** .

1. Naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia i rozłączyć debuger.
