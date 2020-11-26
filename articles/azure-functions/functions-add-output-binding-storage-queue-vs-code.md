---
title: Łączenie Azure Functions z usługą Azure Storage przy użyciu Visual Studio Code
description: Dowiedz się, jak połączyć Azure Functions z kolejką usługi Azure Storage, dodając powiązanie danych wyjściowych do projektu Visual Studio Code.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 1729e0b27dd50519359cf6a39bfa81ba7b3b41e9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185151"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Łączenie Azure Functions z usługą Azure Storage przy użyciu Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak za pomocą Visual Studio Code połączyć usługę Azure Storage z funkcją utworzoną w poprzednim artykule Szybki Start. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce usługi Azure queue storage. 

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić sobie korzystanie z konta magazynu utworzonego za pomocą aplikacji funkcji programu. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .  

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem pracy z tym artykułem należy spełnić następujące wymagania:

* Zainstaluj [rozszerzenie usługi Azure Storage dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Zainstaluj [Eksplorator usługi Azure Storage](https://storageexplorer.com/). Eksplorator usługi Storage jest narzędziem używanym do badania komunikatów w kolejce generowanych przez powiązanie danych wyjściowych. Eksplorator usługi Storage jest obsługiwana w systemach operacyjnych macOS, Windows i Linux.

::: zone pivot="programming-language-csharp"
* Zainstaluj [narzędzia interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

::: zone pivot="programming-language-csharp"  
* Wykonaj kroki opisane w [części 1 Visual Studio Code szybki start](create-first-function-vs-code-csharp.md). 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* Wykonaj kroki opisane w [części 1 Visual Studio Code szybki start](create-first-function-vs-code-node.md). 
::: zone-end   
::: zone pivot="programming-language-java"  
* Wykonaj kroki opisane w [części 1 Visual Studio Code szybki start](create-first-function-vs-code-java.md). 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* Wykonaj kroki opisane w [części 1 Visual Studio Code szybki start](create-first-function-vs-code-typescript.md). 
::: zone-end   
::: zone pivot="programming-language-python"  
* Wykonaj kroki opisane w [części 1 Visual Studio Code szybki start](create-first-function-vs-code-python.md). 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* Wykonaj kroki opisane w [części 1 Visual Studio Code szybki start](create-first-function-vs-code-powershell.md). 
::: zone-end   

W tym artykule przyjęto założenie, że użytkownik jest już zalogowany do subskrypcji platformy Azure z Visual Studio Code. Możesz się zalogować, uruchamiając `Azure: Sign In` polecenie z palety poleceń. 

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

W [poprzednim artykule szybki start](./create-first-function-vs-code-csharp.md)utworzono aplikację funkcji na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule opisano pisanie komunikatów w kolejce magazynu w ramach tego samego konta. Aby nawiązać połączenie z kontem magazynu podczas lokalnego uruchamiania funkcji, musisz pobrać ustawienia aplikacji do local.settings.jspliku. 

1. Naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Functions: Download Remote Settings....` . 

1. Wybierz aplikację funkcji utworzoną w poprzednim artykule. Wybierz opcję **tak, aby** zastąpić istniejące ustawienia lokalne. 

    > [!IMPORTANT]  
    > Ponieważ zawiera wpisy tajne, local.settings.jsw pliku nigdy nie są publikowane i jest wykluczony z kontroli źródła.

1. Skopiuj wartość `AzureWebJobsStorage` , która jest kluczem dla wartości parametrów połączenia konta magazynu. To połączenie służy do sprawdzania, czy powiązanie danych wyjściowych działa zgodnie z oczekiwaniami.

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Ze względu na to, że jest używane powiązanie danych wyjściowych usługi queue storage, przed uruchomieniem projektu musisz mieć zainstalowane rozszerzenie powiązania magazynu. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Twój projekt został skonfigurowany do używania [zestawów rozszerzeń](functions-bindings-register.md#extension-bundles), które automatycznie instalują wstępnie zdefiniowany zestaw pakietów rozszerzenia. 

Użycie pakietów rozszerzeń jest włączone w host.jsw pliku w katalogu głównym projektu, który jest wyświetlany w następujący sposób:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane jako pakiety rozszerzeń. Uruchom następujące polecenie [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) w oknie terminalu, aby dodać pakiet rozszerzenia magazynu do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcjach każdy typ powiązania wymaga `direction` , `type` i unikatowy `name` do zdefiniowania w function.jsna pliku. Sposób definiowania tych atrybutów zależy od języka aplikacji funkcji.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po zdefiniowaniu powiązania można użyć `name` powiązania, aby uzyskać do niego dostęp jako atrybut w sygnaturze funkcji. Za pomocą powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Nowa kolejka o nazwie **requeue** została utworzona na koncie magazynu przez środowisko uruchomieniowe funkcji, gdy zostanie użyte powiązanie danych wyjściowych. Użyjesz Eksplorator usługi Storage, aby sprawdzić, czy kolejka została utworzona wraz z nową wiadomością.

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>Łączenie Eksploratora usługi Storage z kontem

Pomiń tę sekcję, jeśli już zainstalowano Eksplorator usługi Azure Storage i połączono ją z kontem platformy Azure.

1. Uruchom narzędzie [Eksplorator usługi Azure Storage], wybierz ikonę Połącz po lewej stronie, a następnie wybierz pozycję **Dodaj konto**.

    ![Dodaj konto platformy Azure do Eksplorator usługi Microsoft Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. W oknie dialogowym **Połącz** wybierz pozycję **Dodaj konto platformy** Azure, wybierz **środowisko platformy Azure**, a następnie wybierz pozycję **Zaloguj się...**. 

    ![Zaloguj się do swojego konta platformy Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Po pomyślnym zalogowaniu się do konta zobaczysz wszystkie subskrypcje platformy Azure skojarzone z Twoim kontem.

### <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Storage: Open in Storage Explorer` i wybierz nazwę konta magazynu. Twoje konto magazynu zostanie otwarte w Eksplorator usługi Azure Storage.  

1. Rozwiń węzeł **Kolejki**, a następnie wybierz kolejkę o nazwie **outqueue**. 

   Kolejka zawiera komunikat utworzony za pomocą powiązania danych wyjściowych kolejki po uruchomieniu funkcji wyzwalanej przez protokół HTTP. Jeśli funkcja została wywołana przy użyciu domyślnego elementu `name` o wartości *Azure*, komunikat w kolejce to *Nazwa przekazana do funkcji: Azure*.

    ![Komunikat w kolejce wyświetlany w Eksplorator usługi Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Uruchom ponownie funkcję, Wyślij kolejne żądanie, a w kolejce zobaczysz nowy komunikat.  

Teraz można ponownie opublikować zaktualizowaną aplikację funkcji na platformie Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Wdróż ponownie i sprawdź zaktualizowaną aplikację

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Deploy to function app...` .

1. Wybierz aplikację funkcji utworzoną w pierwszym artykule. Ponieważ wdrażasz ponownie projekt w tej samej aplikacji, wybierz pozycję **Wdróż** , aby odrzucić ostrzeżenie o zastąpieniu plików.

1. Po zakończeniu wdrażania możesz ponownie użyć zapełnienia lub przeglądarki, aby przetestować ponownie wdrożoną funkcję. Tak jak wcześniej, dołącz ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ponownie [Wyświetl komunikat w kolejce magazynu](#examine-the-output-queue) , aby sprawdzić, czy powiązanie danych wyjściowych ponownie generuje nowy komunikat w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Na platformie Azure *zasoby* odnoszą się do aplikacji funkcji, funkcji, kont magazynu i tak dalej. Są one pogrupowane w *grupy zasobów*, a wszystkie elementy w grupie można usunąć, usuwając grupę.

Aby ukończyć te przewodniki Szybki start, zostały utworzone zasoby. Za te zasoby może zostać naliczona opłata — zależy to od Twojego [stanu konta](https://azure.microsoft.com/account/) i [cennika usług](https://azure.microsoft.com/pricing/). Jeśli nie potrzebujesz już tych zasobów, oto jak możesz je usunąć:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Teraz można dowiedzieć się więcej na temat opracowywania funkcji przy użyciu Visual Studio Code:

+ [Opracowywanie Azure Functions przy użyciu Visual Studio Code](functions-develop-vs-code.md)

+ [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Przykłady kompletnych projektów funkcji w języku C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Dokumentacja dla deweloperów Azure Functions C#](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Przykłady kompletnych projektów funkcji w języku JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Przewodnik dla deweloperów Azure Functions JavaScript](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Przykłady kompletnych projektów funkcji w języku Java](/samples/browse/?products=azure-functions&languages=java).

+ [Przewodnik dewelopera Azure Functions Java](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Przykłady całych projektów funkcji w języku TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions przewodnik dewelopera języka TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Przykłady kompletnych projektów funkcji w języku Python](/samples/browse/?products=azure-functions&languages=python).

+ [Przewodnik dewelopera w języku Python Azure Functions](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Przykłady kompletnych projektów funkcji w programie PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Przewodnik dewelopera programu Azure Functions PowerShell](functions-reference-powershell.md) 
::: zone-end