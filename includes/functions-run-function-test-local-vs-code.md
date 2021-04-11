---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: eae828d03431dd339c5399d8db8c6e46141ab11b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075353"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Visual Studio Code integruje się z [narzędziami Azure Functions Core](../articles/azure-functions/functions-run-local.md) , aby umożliwić uruchomienie tego projektu na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

1. Aby wywołać funkcję, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**. Aplikacja zostanie uruchomiona na panelu **terminalu** . Można zobaczyć punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP, działającej lokalnie.

    ![Funkcja lokalna VS Code danych wyjściowych](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Jeśli masz problemy z uruchamianiem w systemie Windows, upewnij się, że domyślny Terminal dla Visual Studio Code nie jest ustawiony na **WSL bash**.

1. Po uruchomieniu podstawowych narzędzi przejdź do obszaru **Azure: Functions** . W obszarze **funkcje** rozwiń pozycję funkcje **projektu lokalnego**  >  . Kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Wykonaj funkcję teraz z Visual Studio Code":::
    
1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` . Naciśnij klawisz ENTER, aby wysłać ten komunikat żądania do funkcji. 

   Zamiast tego wysłano żądanie HTTP GET do `http://localhost:7071/api/HttpExample` adresu w przeglądarce internetowej.

1. Gdy funkcja jest wykonywana lokalnie i zwraca odpowiedź, zostanie zgłoszone powiadomienie w Visual Studio Code. Informacje o wykonywaniu funkcji są wyświetlane w panelu **terminalu** .

1. Naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia i rozłączyć debuger.
