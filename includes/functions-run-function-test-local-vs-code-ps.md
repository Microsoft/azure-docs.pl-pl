---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: a5c113849296275432acf1f5603377a1909a2c04
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842304"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Azure Functions Core Tools integruje się z Visual Studio Code, aby umożliwić uruchamianie i debugowanie Azure Functions projektu lokalnie. Aby uzyskać szczegółowe informacje na temat debugowania w Visual Studio Code, zobacz temat [debugowanie Azure Functions programu PowerShell lokalnie](../articles/azure-functions/functions-debug-powershell-local.md). 
1. Aby wywołać funkcję, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**. Jeśli masz problemy z uruchamianiem w systemie Windows, upewnij się, że domyślny Terminal dla Visual Studio Code nie jest ustawiony na **WSL bash**.

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL, a następnie użyj `Invoke-RestMethod` w drugim wierszu polecenia programu PowerShell, aby wykonać żądanie w następujący sposób:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Możesz również wykonać żądanie GET z przeglądarki, korzystając z następującego adresu URL:

    `http://localhost:7071/api/HttpExample?name=PowerShell`

    Po wywołaniu punktu końcowego HttpTrigger bez przekazywania `name` parametru jako parametru zapytania lub w treści funkcja zwraca `BadRequest` błąd. Podczas przeglądania kodu w run.ps1, zobaczysz, że ten błąd występuje podczas projektowania.

1. Informacje o żądaniu są wyświetlane w panelu **terminalu** .

    ![Wykonanie funkcji w panelu terminalu](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Po zakończeniu naciśnij **klawisze CTRL + C** , aby zatrzymać podstawowe narzędzia.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.
