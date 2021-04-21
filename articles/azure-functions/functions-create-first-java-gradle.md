---
title: Publikowanie funkcji na platformie Azure przy użyciu języka Java i narzędzia Gradle
description: Utwórz i opublikuj funkcję wyzwalaną przez protokół HTTP na platformie Azure przy użyciu języka Java i narzędzia Gradle.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: d7f8aa990f5a5e64d2d5c59b52457149187acddd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773985"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Tworzenie i publikowanie funkcji na platformie Azure przy użyciu języka Java i narzędzia Gradle

W tym artykule pokazano, jak skompilować i opublikować projekt funkcji języka Java Azure Functions użyciu narzędzia wiersza polecenia Gradle. Gdy wszystko będzie gotowe, kod funkcji będzie uruchamiany na platformie Azure w ramach bez serwera [planu hostingu](consumption-plan.md) i będzie wyzwalany przez żądanie HTTP. 

> [!NOTE]
> Jeśli gradle nie jest preferowanym narzędziem deweloperskich, zapoznaj się z naszymi podobnymi samouczkami dla deweloperów języka Java korzystającymi z narzędzi [Maven,](./create-first-function-cli-java.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) [i VS Code](./create-first-function-vs-code-java.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby opracowywać funkcje przy użyciu języka Java, musisz mieć zainstalowane następujące składniki:

- Zestaw [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8
- [Interfejs wiersza polecenia platformy Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) wersja 2.6.666 lub nowsza
- [Gradle,](https://gradle.org/)wersja 4.10 i nowsza

Potrzebna jest również aktywna subskrypcja platformy Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="prepare-a-functions-project"></a>Przygotowywanie projektu funkcji

Użyj następującego polecenia, aby sklonować przykładowy projekt:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Otwórz i zmień w poniższej sekcji unikatową nazwę, aby uniknąć konfliktu nazw `build.gradle` `appName` domen podczas wdrażania na platformie Azure. 

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

Otwórz nowy plik Function.java ze ścieżki *src/main/java* w edytorze tekstów i przejrzyj wygenerowany kod. Ten kod jest [funkcją wyzwalaną](functions-bindings-http-webhook.md) przez protokół HTTP, która powtarza treść żądania. 

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom następujące polecenie, aby skompilować, a następnie uruchomić projekt funkcji:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Po uruchomieniu projektu lokalnie dane wyjściowe Azure Functions Core Tools podobne do następujących:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Wyzwolij funkcję z wiersza polecenia, używając następującego polecenia cURL w nowym oknie terminalu:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Oczekiwane dane wyjściowe są następujące:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> W przypadku ustawienia authLevel na `FUNCTION` wartość lub klucz funkcji nie jest wymagany w przypadku uruchamiania `ADMIN` lokalnego. [](functions-bindings-http-webhook-trigger.md#authorization-keys)  

Aby zatrzymać wykonywanie kodu funkcji, użyj polecenia `Ctrl+C` w oknie terminala.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrażania aplikacji funkcji. Przed wdrożeniem użyj polecenia [az login interfejsu](/cli/azure/authenticate-azure-cli) wiersza polecenia platformy Azure, aby zalogować się do subskrypcji platformy Azure. 

```azurecli
az login
```

> [!TIP]
> Jeśli Twoje konto może uzyskać dostęp do wielu subskrypcji, użyj [az account set,](/cli/azure/account#az_account_set) aby ustawić domyślną subskrypcję dla tej sesji. 

Użyj następującego polecenia, aby wdrożyć projekt w nowej aplikacji funkcji. 

```bash
gradle azureFunctionsDeploy
```

Powoduje to utworzenie następujących zasobów na platformie Azure na podstawie wartości w pliku build.gradle:

+ Grupa zasobów. Nazwa nadana _podanej grupie resourceGroup._
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazwy konta magazynu.
+ App Service planu. Hosting planu zużycie bez serwera dla aplikacji funkcji w określonym _appRegion_. Nazwa jest generowana losowo.
+ Aplikacja funkcji. Aplikacja funkcji to jednostka wdrażania i wykonywania dla funkcji. Nazwa to nazwa _aplikacji_ z losowo wygenerowaną liczbą. 

Wdrożenie pakuje również pliki projektu i wdraża je w nowej aplikacji funkcji przy użyciu wdrożenia zip [z](functions-deployment-technologies.md#zip-deploy)włączonym trybem uruchamiania z pakietu.

AuthLevel dla wyzwalacza HTTP w przykładowym projekcie to `ANONYMOUS` , co spowoduje pominięcie uwierzytelniania. Jeśli jednak używasz innego authLevel, takiego jak lub , musisz uzyskać klucz funkcji w celu wywołania punktu końcowego funkcji za `FUNCTION` `ADMIN` pośrednictwem protokołu HTTP. Najprostszym sposobem uzyskania klucza funkcji jest Azure Portal [.]

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>Uzyskiwanie adresu URL wyzwalacza HTTP

Adres URL wymagany do wyzwolenia funkcji możesz uzyskać za pomocą klucza funkcji z Azure Portal. 

1. Przejdź do [witryny Azure Portal], zaloguj się, wpisz _nazwę aplikacji_ funkcji w polu Wyszukaj w górnej części strony, a następnie naciśnij klawisz Enter. 
 
1. W aplikacji funkcji wybierz pozycję **Funkcje,** wybierz funkcję, a następnie kliknij pozycję **</> Pobierz adres URL** funkcji w prawym górnym rogu. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Kopiowanie adresu URL funkcji z witryny Azure Portal":::

1. Wybierz **wartość domyślną (klucz funkcji), a** następnie wybierz pozycję **Kopiuj**. 

Teraz możesz użyć skopiowanego adresu URL, aby uzyskać dostęp do funkcji.

## <a name="verify-the-function-in-azure"></a>Weryfikowanie funkcji na platformie Azure

Aby sprawdzić, czy aplikacja funkcji działa na platformie Azure przy użyciu funkcji , zastąp adres URL z poniższego przykładu adresem URL skopiowanym `cURL` z portalu.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Powoduje to wysłanie żądania POST do punktu końcowego funkcji z w `AzureFunctions` treści żądania. Zostanie wyświetlony następujący kod odpowiedzi.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji języka Java z funkcją wyzwalaną przez protokół HTTP, uruchomiliśmy go na komputerze lokalnym i wdrożyliśmy na platformie Azure. Teraz rozszerz funkcję o...

> [!div class="nextstepaction"]
> [Dodawanie powiązania wyjściowego kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-java.md)


[Interfejs wiersza polecenia platformy Azure]: /cli/azure
[Witryna Azure Portal]: https://portal.azure.com