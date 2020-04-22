---
title: Publikowanie funkcji na platformie Azure za pomocą języka Java i Gradle
description: Tworzenie i publikowanie funkcji wyzwalanej przez protokół HTTP na platformie Azure za pomocą aplikacji Java i Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 34aab24bf39e387715cfa5783b801d45ed488750
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732724"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Tworzenie i publikowanie funkcji na platformie Azure za pomocą funkcji Java i Gradle

W tym artykule pokazano, jak skompilować i opublikować projekt funkcji Java do usługi Azure Functions za pomocą narzędzia wiersza polecenia Gradle. Po zakończeniu kodu funkcji jest uruchamiany na platformie Azure w [planie hostingu bezserwerowym](functions-scale.md#consumption-plan) i jest wyzwalany przez żądanie HTTP. 

> [!NOTE]
> Jeśli Gradle nie jest preferowanym narzędziem programistycznym, sprawdź nasze podobne samouczki dla programistów Java za pomocą [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) i [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Wymagania wstępne

Aby opracowywać funkcje przy użyciu języka Java, musisz mieć zainstalowane następujące składniki:

- Zestaw [Java Developer Kit](https://aka.ms/azure-jdks), wersja 8
- [Interfejs wiersza polecenia platformy Azure]
- [Narzędzia podstawowe usług Azure Functions w](./functions-run-local.md#v2) wersji 2.6.666 lub wyższej
- [Gradle](https://gradle.org/), wersja 4.10 i powyżej

Potrzebna jest również aktywna subskrypcja platformy Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="prepare-a-functions-project"></a>Przygotowywanie projektu funkcji

Użyj następującego polecenia, aby sklonować przykładowy projekt:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Otwórz `build.gradle` i `appName` zmień w poniższej sekcji na unikatową nazwę, aby uniknąć konfliktu nazw domen podczas wdrażania na platformie Azure. 

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

Otwórz nowy plik Function.java ze ścieżki *src/main/java* w edytorze tekstu i przejrzyj wygenerowany kod. Ten kod jest funkcją [wyzwalaną HTTP,](functions-bindings-http-webhook.md) która odzwierciedla treść żądania. 

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom następujące polecenie do kompilacji, a następnie uruchom projekt funkcji:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Po uruchomieniu projektu lokalnie dostępne są następujące dane wyjściowe:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Wyzwolić funkcję z wiersza polecenia za pomocą następującego polecenia cURL w nowym oknie terminala:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Oczekiwane dane wyjściowe są następujące:

<pre>
Hello AzureFunctions!
</pre>

[Klucz funkcyjny](functions-bindings-http-webhook-trigger.md#authorization-keys) nie jest wymagany podczas uruchamiania lokalnego.  
Aby zatrzymać wykonywanie kodu funkcji, użyj polecenia `Ctrl+C` w oknie terminala.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrażania aplikacji funkcji. Przed wdrożeniem należy użyć polecenia interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure [az,](/cli/azure/authenticate-azure-cli) aby zalogować się do subskrypcji platformy Azure. 

```azurecli
az login
```

> [!TIP]
> Jeśli Twoje konto może uzyskać dostęp do wielu subskrypcji, użyj [konta AZ ustawionego,](/cli/azure/account#az-account-set) aby ustawić domyślną subskrypcję dla tej sesji. 

Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji. 

```bash
gradle azureFunctionsDeploy
```

Spowoduje to utworzenie następujących zasobów na platformie Azure, na podstawie wartości w pliku build.gradle:

+ Grupa zasobów. Nazwany z _resourceGroup,_ które podano.
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazwy konta magazynu.
+ Plan usługi aplikacji. Hosting planu zużycia bezserwerowego dla aplikacji funkcyjnej w określonej _aplikacjiRegion_. Nazwa jest generowana losowo.
+ Aplikacja funkcyjna. Aplikacja funkcji jest jednostką wdrażania i wykonywania dla funkcji. Nazwa jest _appName_, dołączane z losowo generowanym numerem. 

Wdrożenie również pakiety plików projektu i wdraża je do nowej aplikacji funkcji za pomocą [wdrożenia zip](functions-deployment-technologies.md#zip-deploy), z włączonym trybem uruchamiania z pakietu.

Ponieważ wyzwalacz HTTP, który `authLevel = AuthorizationLevel.FUNCTION`opublikowaliśmy używa, musisz uzyskać klawisz funkcyjny, aby wywołać punkt końcowy funkcji za pośrednictwem protokołu HTTP. Najprostszym sposobem uzyskania klucza funkcji jest [portal Azure.]

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Pobierz adres URL wyzwalacza HTTP

Możesz uzyskać adres URL wymagany do wyzwolenia funkcji za pomocą klucza funkcyjnego z witryny Azure portal. 

1. Przejdź do [witryny Azure portal], zaloguj się, wpisz _appName_ aplikacji funkcji w **search** u góry strony i naciśnij enter.
 
1. W aplikacji funkcji rozwiń **pozycję Funkcje (Tylko do odczytu)**, wybierz funkcję, a następnie wybierz **</> Pobierz adres URL funkcji** w prawym górnym rogu. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-java-maven/get-function-url-portal.png)

1. Wybierz **pozycję domyślną (klawisz funkcyjny)** i wybierz pozycję **Kopiuj**. 

Możesz teraz użyć skopiowanego adresu URL, aby uzyskać dostęp do swojej funkcji.

## <a name="verify-the-function-in-azure"></a>Weryfikowanie funkcji na platformie Azure

Aby zweryfikować aplikację funkcji `cURL`działającą na platformie Azure przy użyciu programu , zastąp adres URL z poniższego przykładu adresem URL skopiowanym z portalu.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Spowoduje to wysłanie żądania POST `AzureFunctions` do punktu końcowego funkcji z treścią żądania. Pojawi się następująca odpowiedź.

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji Java z funkcją wyzwalaną http, uruchom go na komputerze lokalnym i wdrożono go na platformie Azure. Teraz rozszerz swoją funkcję o...

> [!div class="nextstepaction"]
> [Dodawanie powiązania wyjściowego kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-java.md)


[Interfejs wiersza polecenia platformy Azure]: /cli/azure
[Azure Portal]: https://portal.azure.com
