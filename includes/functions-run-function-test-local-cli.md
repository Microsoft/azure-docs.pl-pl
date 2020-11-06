---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 0bb0c22227946cba6790b536b3cb8db24af3ccbc
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422867"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Uruchom funkcję przez uruchomienie lokalnego hosta Azure Functions środowiska uruchomieniowego z folderu *LocalFunctionProj* :

    ```
    func start
    ```

    W kierunku końca danych wyjściowych powinny być wyświetlane następujące wiersze: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Jeśli HttpExample nie pojawia się, jak pokazano poniżej, prawdopodobnie uruchomiono hosta spoza folderu głównego projektu. W takim przypadku użyj **klawiszy CTRL** + **C** , aby zatrzymać hosta, przejdź do folderu głównego projektu i ponownie uruchom poprzednie polecenie.

1. Skopiuj adres URL `HttpExample` funkcji z tego danych wyjściowych do przeglądarki i dołącz ciąg zapytania `?name=<YOUR_NAME>` , wprowadzając pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony następujący komunikat `Hello Functions` :

    ![Wynik funkcji uruchomionej lokalnie w przeglądarce](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Na terminalu, w którym rozpoczął się projekt, są również wyświetlane dane wyjściowe dziennika podczas zgłaszania żądań.

1. Gdy skończysz, użyj **klawiszy CTRL** + **C** i wybierz opcję `y` zatrzymania hosta funkcji.
