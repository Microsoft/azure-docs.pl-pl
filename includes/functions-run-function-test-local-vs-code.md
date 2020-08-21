---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 052e0c93732b99efa37b029cad29dc2efded78ee
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88704399"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Visual Studio Code integruje się z [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) , aby umożliwić uruchomienie tego projektu na lokalnym komputerze deweloperskim przed opublikowaniem na platformie Azure.

1. Aby wywołać funkcję, naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

1. Jeśli nie zainstalowano jeszcze Azure Functions Core Tools, wybierz opcję **Zainstaluj** w wierszu polecenia. Po zainstalowaniu podstawowych narzędzi aplikacja zostanie uruchomiona na panelu **terminalu** . Można zobaczyć punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP, działającej lokalnie. 

    ![Lokalne dane wyjściowe platformy Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Po uruchomieniu podstawowych narzędzi przejdź do następującego adresu URL, aby wykonać żądanie GET, które zawiera `?name=Functions` ciąg zapytania.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Zostanie zwrócona odpowiedź, która wygląda podobnie do następującej w przeglądarce:

    ![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informacje o żądaniu są wyświetlane w panelu **terminalu** .

    ![Wykonanie funkcji w panelu terminalu](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Naciśnij klawisze CTRL + C, aby zatrzymać podstawowe narzędzia i rozłączyć debuger.
