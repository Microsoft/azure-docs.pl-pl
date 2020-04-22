---
title: Tworzenie pierwszej funkcji na platformie Azure przy użyciu programu Visual Studio Code
description: Tworzenie prostej funkcji wyzwalanej przez protokół HTTP i publikowanie jej przy użyciu rozszerzenia usługi Azure Functions w programie Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 08575164334db965a9e5592b761a06205208de9e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732770"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Szybki start: tworzenie projektu usługi Azure Functions przy użyciu kodu programu Visual Studio

W tym artykule używasz visual studio kod do utworzenia funkcji, która odpowiada na żądania HTTP. Po przetestowaniu kodu lokalnie, można wdrożyć go w środowisku bezserwerowym usługi Azure Functions. Ukończenie tego przewodnika Szybki start wiąże się z niewielkim kosztem kilku centów USD lub mniej na koncie platformy Azure. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Istnieje również wersja tego artykułu [oparta na interfejsu wiersza](functions-create-first-azure-function-azure-cli.md) polecenia.
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Jeśli VS Code nie jest preferowanym narzędziem programistycznym, sprawdź nasze podobne samouczki dla programistów Java za pomocą [Maven,](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java) [Gradle](/azure/azure-functions/functions-create-first-java-gradle) i [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).
::: zone-end  

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Zanim zaczniesz, upewnij się, że masz następujące wymagania:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node.js](https://nodejs.org/), wymagane przez system Windows dla npm. Tylko [aktywne wersje LTS i konserwacji LTS](https://nodejs.org/about/releases/). Użyj `node --version` polecenia, aby sprawdzić wersję.
    Nie jest wymagane dla lokalnego rozwoju w systemach macOS i Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node.js](https://nodejs.org/), Active LTS i konserwacja wersji LTS (10.14.1 zalecane). Użyj `node --version` polecenia, aby sprawdzić wersję.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) są obsługiwane przez usługę Azure Functions (x64).
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [Program PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ .NET [Core SDK 2.2+](https://www.microsoft.com/net/download)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Zestaw Java Developer Kit](https://aka.ms/azure-jdks), wersja 8.

+ [Apache Maven](https://maven.apache.org), wersja 3.0 lub wyższa.
::: zone-end  
+ [Visual Studio Code](https://code.visualstudio.com/) na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"  
+ [Rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dla programu Visual Studio Code.  
::: zone-end  
::: zone pivot="programming-language-python"
+ [Rozszerzenie Języka Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) dla programu Visual Studio Code.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ [Rozszerzenie programu PowerShell dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Pakiet rozszerzeń Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end  

+ [Rozszerzenie usługi Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla kodu programu Visual Studio. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Tworzenie lokalnego projektu 

W tej sekcji używasz Visual Studio Code do utworzenia lokalnego projektu usługi Azure Functions w wybranym języku. W dalszej części tego artykułu opublikujesz kod funkcji na platformie Azure. 

1. Wybierz ikonę platformy Azure na pasku aktywności, a następnie w obszarze **Azure: Functions** wybierz ikonę **Utwórz nowy projekt....**

    ![Wybieranie opcji Utwórz nowy projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu i wybierz pozycję **Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do wykonania poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Podaj następujące informacje w monitach:

    ::: zone pivot="programming-language-csharp"
    + **Wybierz język dla projektu**funkcji `C#`: Wybierz .
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Wybierz język dla projektu**funkcji `JavaScript`: Wybierz .
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Wybierz język dla projektu**funkcji `TypeScript`: Wybierz .
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Wybierz język dla projektu**funkcji `PowerShell`: Wybierz .
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Wybierz język dla projektu**funkcji `Python`: Wybierz .

    + **Wybierz alias Języka Python, aby utworzyć środowisko wirtualne:** wybierz lokalizację interpretera języka Python. Jeśli lokalizacja nie jest wyświetlana, wpisz pełną ścieżkę do pliku binarnego języka Python.  
    ::: zone-end

    ::: zone pivot="programming-language-java"  
    + **Wybierz język dla projektu**funkcji `Java`: Wybierz .

    + **Podaj identyfikator**grupy `com.function`: Wybierz .

    + **Podaj identyfikator artefaktu:** Wybierz `myFunction`.

    + **Podaj**wersję `1.0-SNAPSHOT`: Wybierz .

    + **Podaj nazwę** `com.function`pakietu : Wybierz .

    + **Podaj nazwę** `myFunction-12345`aplikacji : Wybierz .
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Wybierz szablon dla pierwszej funkcji projektu** `HTTP trigger`: Wybierz .
    
    + **Podaj nazwę** `HttpExample`funkcji : Type .
    ::: zone-end  
    ::: zone pivot="programming-language-csharp"
    + **Podaj obszar nazw:** Wpisz `My.Functions`. 
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Poziom autoryzacji:** Wybierz `Anonymous`, który umożliwia każdemu wywołanie punktu końcowego funkcji. Aby dowiedzieć się więcej o poziomie autoryzacji, zobacz [Klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).
    ::: zone-end  
    + **Wybierz sposób otwierania projektu**: `Add to workspace`Wybierz .

1. Korzystając z tych informacji, Visual Studio Code generuje projekt usługi Azure Functions z wyzwalaczem HTTP. Pliki projektu lokalnego można wyświetlić w Eksploratorze. Aby dowiedzieć się więcej o tworzonych plikach, zobacz [Generowane pliki projektu](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, nadszedł czas, aby użyć programu Visual Studio Code do publikowania projektu bezpośrednio na platformie Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

1. Powrót do obszaru **Azure: Functions** na pasku bocznym rozwiń nową aplikację funkcji w ramach subskrypcji. Rozwiń **polecenie Rozwiń funkcje**, kliknij prawym przyciskiem myszy (Windows) lub Ctrl + kliknij (macOS) na **stronie HttpExample**, a następnie wybierz polecenie **Kopiuj adres URL funkcji**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Wklej ten adres URL żądania HTTP na pasku `name` adresu przeglądarki, dodaj ciąg zapytania na `?name=Functions` końcu tego adresu URL, a następnie wykonaj żądanie. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    Poniższy przykład przedstawia odpowiedź w przeglądarce na zdalne żądanie GET zwrócone przez funkcję: 

    ![Odpowiedź funkcji wyświetlona w przeglądarce](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po kontynuowaniu następnego kroku [dodaj powiązanie kolejki usługi Azure Storage do funkcji,](functions-add-output-binding-storage-queue-vs-code.md)musisz zachować wszystkie zasoby w miejscu, aby kontynuować to, co już zrobiłeś.

W przeciwnym razie można użyć następujących kroków, aby usunąć aplikację funkcji i jej powiązanych zasobów, aby uniknąć ponoszenia dalszych kosztów.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Aby dowiedzieć się więcej o kosztach funkcji, zobacz [Szacowanie kosztów planu zużycia](functions-consumption-costs.md).

## <a name="next-steps"></a>Następne kroki

W programie Visual Studio Code utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP. W następnym artykule można rozwinąć tę funkcję, dodając powiązanie danych wyjściowych. To powiązanie zapisuje ciąg z żądania HTTP do wiadomości w kolejce usługi Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Dodawanie powiązania kolejki usługi Azure Storage do funkcji](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
