---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 55c64048e0604987c5a4c26961e5617106358e76
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84436179"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom funkcję przez uruchomienie lokalnego hosta Azure Functions środowiska uruchomieniowego z folderu *LocalFunctionProj* :

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
```
::: zone-end

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

Skopiuj adres URL `HttpExample` funkcji z tego danych wyjściowych do przeglądarki i dołącz ciąg zapytania `?name=<your-name>` , wprowadzając pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions` . W przeglądarce powinien zostać wyświetlony następujący komunikat `Hello Functions` :

![Wynik funkcji uruchomionej lokalnie w przeglądarce](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Na terminalu, w którym rozpoczął się projekt, są również wyświetlane dane wyjściowe dziennika podczas zgłaszania żądań.

Gdy wszystko będzie gotowe, użyj **klawiszy CTRL** + **C** i wybierz opcję `y` zatrzymania hosta funkcji.
