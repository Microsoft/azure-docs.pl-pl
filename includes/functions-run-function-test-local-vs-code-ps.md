---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78191063"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Azure Functions Core Tools integruje się z Visual Studio Code, aby umożliwić uruchamianie i debugowanie Azure Functions projektu lokalnie. Aby uzyskać szczegółowe informacje na temat debugowania w Visual Studio Code, zobacz temat [debugowanie Azure Functions programu PowerShell lokalnie](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Dołącz ciąg `?name=<yourname>` zapytania do tego adresu URL, a następnie użyj `Invoke-RestMethod` w drugim wierszu polecenia programu PowerShell, aby wykonać żądanie w następujący sposób:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Możesz również wykonać żądanie GET z przeglądarki, korzystając z następującego adresu URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Po wywołaniu punktu końcowego HttpTrigger bez przekazywania `name` parametru jako parametru zapytania lub w treści funkcja zwraca `BadRequest` błąd. Gdy przeglądasz kod w programie Run. ps1, zobaczysz, że ten błąd występuje podczas projektowania.

1. Informacje o żądaniu są wyświetlane w panelu **terminalu** .

    ![Wykonanie funkcji w panelu terminalu](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Po zakończeniu naciśnij **klawisze CTRL + C** , aby zatrzymać podstawowe narzędzia.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.