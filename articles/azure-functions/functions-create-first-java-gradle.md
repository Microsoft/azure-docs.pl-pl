---
title: Publikowanie funkcji na platformie Azure przy użyciu języka Java i Gradle
description: Utwórz i Opublikuj funkcję wyzwalaną przez protokół HTTP na platformie Azure przy użyciu języka Java i Gradle.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: a9592d848398c71bc573c073f0b712898f666640
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104873"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Tworzenie i publikowanie funkcji na platformie Azure przy użyciu języka Java i Gradle

W tym artykule opisano sposób kompilowania i publikowania projektu funkcji języka Java w celu Azure Functions za pomocą narzędzia wiersza polecenia Gradle. Gdy wszystko będzie gotowe, kod funkcji jest uruchamiany na platformie Azure w [planie hostingu bezserwerowym](functions-scale.md#consumption-plan) i jest wyzwalany przez żądanie HTTP. 

> [!NOTE]
> Jeśli Gradle nie jest preferowanym narzędziem programistycznym, zapoznaj się z naszymi samouczkami dotyczącymi deweloperów języka Java za pomocą [Maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java), [IntelliJ pomysłu](/azure/developer/java/toolkit-for-intellij/quickstart-functions) i [vs Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).

## <a name="prerequisites"></a>Wymagania wstępne

Aby opracowywać funkcje przy użyciu języka Java, musisz mieć zainstalowane następujące składniki:

- Zestaw [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8
- [Interfejs wiersza polecenia platformy Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.6.666 lub nowszej
- [Gradle](https://gradle.org/), wersja 4,10 lub nowsza

Potrzebna jest również aktywna subskrypcja platformy Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="prepare-a-functions-project"></a>Przygotuj projekt funkcji

Użyj następującego polecenia, aby sklonować przykładowy projekt:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Otwórz `build.gradle` i Zmień `appName` nazwę w poniższej sekcji, aby uniknąć konfliktu nazw domen podczas wdrażania na platformie Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Otwórz plik New Function. Java ze ścieżki *src/Main/Java* w edytorze tekstów i przejrzyj wygenerowany kod. Ten kod jest funkcją [wyzwalaną przez protokół http](functions-bindings-http-webhook.md) , która umożliwia echo treści żądania. 

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom następujące polecenie, aby skompilować, a następnie Uruchom projekt funkcji:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Poniższe dane wyjściowe są wyświetlane w Azure Functions Core Tools podczas lokalnego uruchamiania projektu:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Wyzwól funkcję z wiersza polecenia przy użyciu następującego polecenia zwinięcie w nowym oknie terminalu:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Oczekiwane dane wyjściowe są następujące:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Jeśli ustawisz authLevel na `FUNCTION` lub `ADMIN` , [klucz funkcji](functions-bindings-http-webhook-trigger.md#authorization-keys) nie jest wymagany w przypadku uruchamiania lokalnego.  

Aby zatrzymać wykonywanie kodu funkcji, użyj polecenia `Ctrl+C` w oknie terminala.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrożenia aplikacji funkcji. Przed wdrożeniem programu Użyj polecenia [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI, aby zalogować się do subskrypcji platformy Azure. 

```azurecli
az login
```

> [!TIP]
> Jeśli Twoje konto ma dostęp do wielu subskrypcji, użyj polecenie [AZ Account Set](/cli/azure/account#az-account-set) , aby ustawić domyślną subskrypcję tej sesji. 

Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji. 

```bash
gradle azureFunctionsDeploy
```

Spowoduje to utworzenie następujących zasobów na platformie Azure na podstawie wartości w pliku Build. Gradle:

+ Grupa zasobów. Nazwana z _podaną w podanej_ nazwie.
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazw kont magazynu.
+ App Service plan. Hostowanie bezserwerowego planu zużycia dla aplikacji funkcji w określonym _appRegion_. Nazwa jest generowana losowo.
+ Aplikacja funkcji. Aplikacja funkcji jest jednostką wdrażania i wykonywania dla funkcji. Nazwa jest _nazwą użytkownika,_ dołączona przy użyciu losowo wygenerowanego numeru. 

Wdrożenie obejmuje również pakiety plików projektu i wdraża je w nowej aplikacji funkcji przy użyciu [wdrożenia zip](functions-deployment-technologies.md#zip-deploy)z włączonym trybem uruchamiania z pakietu.

Wyzwalacz authLevel dla protokołu HTTP w przykładowym projekcie to `ANONYMOUS` , co spowoduje pominięcie uwierzytelniania. Jeśli jednak używasz innych authLevel, takich jak `FUNCTION` lub `ADMIN` , musisz uzyskać klucz funkcji, aby wywołać punkt końcowy funkcji za pośrednictwem protokołu HTTP. Najprostszym sposobem uzyskania klucza funkcji jest z [Azure Portal].

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>Pobierz adres URL wyzwalacza HTTP

Możesz uzyskać adres URL wymagany do wyzwolenia funkcji przy użyciu klucza funkcji z Azure Portal. 

1. Przejdź do [Azure Portal], zaloguj się, _wpisz nazwę aplikacji funkcji w polu_ **wyszukiwania** w górnej części strony, a następnie naciśnij klawisz ENTER.
 
1. W aplikacji funkcji wybierz pozycję **funkcje**, wybierz funkcję, a następnie kliknij pozycję **</> Pobierz adres URL funkcji** w prawym górnym rogu. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Kopiowanie adresu URL funkcji z witryny Azure Portal":::

1. Wybierz pozycję **domyślne (klawisz funkcyjny)** i wybierz pozycję **Kopiuj**. 

Możesz teraz użyć skopiowanego adresu URL, aby uzyskać dostęp do funkcji.

## <a name="verify-the-function-in-azure"></a>Weryfikowanie funkcji na platformie Azure

Aby sprawdzić aplikację funkcji działającą na platformie Azure przy użyciu programu `cURL` , Zastąp adres URL z poniższego przykładu adresem URL skopiowanym z portalu.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Spowoduje to wysłanie żądania POST do punktu końcowego funkcji `AzureFunctions` w treści żądania. Zostanie wyświetlona następująca odpowiedź.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji języka Java z funkcją wyzwalaną przez protokół HTTP, uruchom go na komputerze lokalnym i wdrożony na platformie Azure. Teraz możesz rozłożyć funkcję przez...

> [!div class="nextstepaction"]
> [Dodawanie powiązania danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-java.md)


[Interfejs wiersza polecenia platformy Azure]: /cli/azure
[Witryna Azure Portal]: https://portal.azure.com