---
title: Łączenie Azure Functions z usługą Azure Storage przy użyciu narzędzi wiersza polecenia
description: Dowiedz się, jak połączyć Azure Functions z kolejką usługi Azure Storage, dodając powiązanie danych wyjściowych do projektu wiersza polecenia.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 6d0d559579e841553e04c26bf4dcc78934de3e95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94920206"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Łączenie Azure Functions z usługą Azure Storage przy użyciu narzędzi wiersza polecenia

W tym artykule opisano integrację kolejki usługi Azure Storage z funkcją i kontem magazynu utworzonym w poprzednim artykule Szybki Start. Tę integrację można osiągnąć za pomocą *powiązania danych wyjściowych* , które zapisuje dane z żądania HTTP do wiadomości w kolejce. Wykonanie tego artykułu nie wiąże się z żadnymi dodatkowymi kosztami powyżej kilku centów z poprzedniego przewodnika Szybki Start. Aby dowiedzieć się więcej na temat powiązań, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

::: zone pivot="programming-language-csharp"  
Przed rozpoczęciem należy wykonać czynności opisane w artykule [Szybki Start: Tworzenie projektu Azure Functions z poziomu wiersza polecenia](create-first-function-cli-csharp.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.  
::: zone-end  
::: zone pivot="programming-language-javascript"  
Przed rozpoczęciem należy wykonać czynności opisane w artykule [Szybki Start: Tworzenie projektu Azure Functions z poziomu wiersza polecenia](create-first-function-cli-node.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.  
::: zone-end   
::: zone pivot="programming-language-java"  
Przed rozpoczęciem należy wykonać czynności opisane w artykule [Szybki Start: Tworzenie projektu Azure Functions z poziomu wiersza polecenia](create-first-function-cli-java.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.  
::: zone-end   
::: zone pivot="programming-language-typescript"  
Przed rozpoczęciem należy wykonać czynności opisane w artykule [Szybki Start: Tworzenie projektu Azure Functions z poziomu wiersza polecenia](create-first-function-cli-typescript.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.  
::: zone-end   
::: zone pivot="programming-language-python"  
Przed rozpoczęciem należy wykonać czynności opisane w artykule [Szybki Start: Tworzenie projektu Azure Functions z poziomu wiersza polecenia](create-first-function-cli-python.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.  
::: zone-end   
::: zone pivot="programming-language-powershell"  
Przed rozpoczęciem należy wykonać czynności opisane w artykule [Szybki Start: Tworzenie projektu Azure Functions z poziomu wiersza polecenia](create-first-function-cli-powershell.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.  
::: zone-end   

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Aby uzyskać więcej informacji na temat szczegółów powiązań, zobacz [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md) oraz [konfigurację wyjściową kolejki](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Dodaj kod, aby użyć powiązania danych wyjściowych

Po zdefiniowaniu powiązania kolejki można teraz zaktualizować funkcję w celu otrzymywania `msg` parametru wyjściowego i zapisu komunikatów do kolejki.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Zwróć uwagę, że *nie* musisz pisać żadnego kodu do uwierzytelniania, pobieranie odwołania do kolejki lub zapisywanie danych. Wszystkie te zadania integracji są wygodnie obsługiwane w środowisku uruchomieniowym Azure Functions i powiązania danych wyjściowych kolejki.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie komunikatu w kolejce usługi Azure Storage

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Ponowne wdrażanie projektu na platformie Azure

Po sprawdzeniu, czy funkcja zapisała komunikat do kolejki usługi Azure Storage, można ponownie wdrożyć projekt w celu zaktualizowania punktu końcowego uruchomionego na platformie Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
W folderze *LocalFunctionsProj* Użyj [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) polecenia, aby ponownie wdrożyć projekt, zastępując go `<APP_NAME>` nazwą aplikacji.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

W folderze projektu lokalnego Użyj następującego polecenia Maven, aby ponownie opublikować projekt:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Weryfikuj na platformie Azure

1. Tak jak w poprzednim przewodniku Szybki Start, użyj przeglądarki lub zapełnienia, aby przetestować ponownie wdrożoną funkcję.

    # <a name="browser"></a>[Przeglądarka](#tab/browser)
    
    Skopiuj pełny **adres URL Wywołaj** pokazany w danych wyjściowych polecenia Publikuj na pasku adresu przeglądarki, dołączając parametr zapytania `&name=Functions` . Przeglądarka powinna wyświetlać podobne dane wyjściowe, jak w przypadku lokalnego uruchomienia funkcji.

    ![Dane wyjściowe funkcji są uruchamiane na platformie Azure w przeglądarce](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Uruchom [`curl`](https://curl.haxx.se/) polecenie **Invoke URL**, dodając parametr `&name=Functions` . Danymi wyjściowymi polecenia powinien być tekst "Hello Functions".
    
    ![Dane wyjściowe funkcji są uruchamiane na platformie Azure przy użyciu zwinięcia](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Sprawdź ponownie kolejkę magazynu, zgodnie z opisem w poprzedniej sekcji, aby sprawdzić, czy zawiera ona nowy komunikat zapisany w kolejce.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Teraz można dowiedzieć się więcej na temat opracowywania funkcji z poziomu wiersza polecenia przy użyciu narzędzi podstawowych i interfejsu CLI platformy Azure:

+ [Korzystanie z narzędzi Azure Functions Core Tools](functions-run-local.md)  

+ [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)

::: zone pivot="programming-language-csharp"  
+ [Przykłady kompletnych projektów funkcji w języku C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Dokumentacja dla deweloperów Azure Functions C#](functions-dotnet-class-library.md)  

[previous-quickstart]: create-first-function-cli-csharp.md

::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Przykłady kompletnych projektów funkcji w języku JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Przewodnik dla deweloperów Azure Functions JavaScript](functions-reference-node.md)  

[previous-quickstart]: create-first-function-cli-javascript.md
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Przykłady całych projektów funkcji w języku TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions przewodnik dewelopera języka TypeScript](functions-reference-node.md#typescript)  

[previous-quickstart]: create-first-function-cli-typescript.md
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Przykłady kompletnych projektów funkcji w języku Python](/samples/browse/?products=azure-functions&languages=python).

+ [Przewodnik dewelopera w języku Python Azure Functions](functions-reference-python.md)  

[previous-quickstart]: create-first-function-cli-python.md
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Przykłady kompletnych projektów funkcji w programie PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Przewodnik dewelopera programu Azure Functions PowerShell](functions-reference-powershell.md) 

[previous-quickstart]: create-first-function-cli-powershell.md
::: zone-end
