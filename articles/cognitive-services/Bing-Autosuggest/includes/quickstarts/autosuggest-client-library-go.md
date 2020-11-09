---
title: automatyczne sugerowanie Bing przejdź do biblioteki klienta — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 8c0715b3570bf60205c83390ab93b272e49e8733
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371569"
---
Rozpocznij pracę z biblioteką klienta automatyczne sugerowanie Bing dla języka go. Wykonaj następujące kroki, aby zainstalować bibliotekę i wypróbuj nasze przykłady dla podstawowych zadań.

Użyj biblioteki klienta automatyczne sugerowanie Bing dla języka go, aby uzyskać sugestie dotyczące wyszukiwania na podstawie częściowych ciągów zapytań.

[Dokumentacja](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)  |  źródłowy biblioteki [Przykładowy kod](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz ją [utworzyć bezpłatnie](https://azure.microsoft.com/free/cognitive-services).
* Najnowsza wersja języka [go](https://golang.org/dl/).

Aby rozpocząć korzystanie z biblioteki klienta automatyczne sugerowanie Bing, utwórz zasób platformy Azure. Wybierz odpowiedni typ zasobu:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Tworzenie zmiennych środowiskowych

>[!NOTE]
> Punkty końcowe dla zasobów utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](../../../cognitive-services-custom-subdomains.md).

Przy użyciu klucza i punktu końcowego z utworzonego zasobu Utwórz dwa zmienne środowiskowe do uwierzytelnienia:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: Klucz zasobu do uwierzytelniania żądań.
* `AUTOSUGGEST_ENDPOINT`: Punkt końcowy zasobu do wysyłania żądań interfejsu API. Powinien wyglądać następująco: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`

Skorzystaj z instrukcji dotyczących systemu operacyjnego.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej Uruchom ponownie okno konsoli.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

### <a name="macos"></a>[macOS](#tab/unix)

Edytuj `.bash_profile` i Dodaj zmienną środowiskową:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

## <a name="create-a-new-go-project"></a>Utwórz nowy projekt przejdź

W oknie konsoli (cmd, PowerShell, Terminal, bash) Utwórz nowy obszar roboczy dla projektu go i przejdź do niego. Obszar roboczy będzie zawierać trzy foldery:

* **src** : ten katalog zawiera kod źródłowy i pakiety. Wszystkie pakiety zainstalowane z `go get` poleceniem będą znajdować się w tym miejscu.
* **pkg** : ten katalog zawiera skompilowane obiekty pakietu języka go. Wszystkie te pliki mają `.a` rozszerzenie.
* **bin** : ten katalog zawiera binarne pliki wykonywalne, które są tworzone podczas uruchamiania programu `go install` .

> [!TIP]
> Dowiedz się więcej o strukturze [obszaru roboczego go](https://golang.org/doc/code.html#Workspaces). Ten przewodnik zawiera informacje dotyczące ustawiania `$GOPATH` i `$GOROOT` .

Utwórzmy obszar roboczy o nazwie `my-app` i wymagane podkatalogi dla `src` , `pkg` i `bin` :

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Zainstaluj bibliotekę kliencką dla języka go

Teraz Zainstalujmy bibliotekę kliencką dla języka go:

```bash
$ go get -u <library-location-or-url>
```

a jeśli używasz programu dep, w ramach repozytorium uruchom:

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Tworzenie aplikacji języka go

Następnie utwórz plik o nazwie `src/sample-app.go` :

```bash
$ cd src
$ touch sample-app.go
```

Otwórz `sample-app.go` , Dodaj nazwę pakietu, a następnie zaimportuj następujące biblioteki:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Utwórz funkcję o nazwie `main` . Następnie utwórz zmienne środowiskowe dla klucza automatyczne sugerowanie Bing i punktu końcowego:

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Przykłady kodu

Te przykłady kodu pokazują, jak wykonać podstawowe zadania przy użyciu biblioteki klienta automatyczne sugerowanie Bing dla języka go:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wyślij żądanie interfejsu API](#send-an-api-request)

### <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start założono, że [utworzono zmienną środowiskową](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)  dla klucza automatycznego sugerowania usługi Bing o nazwie `BING_AUTOSUGGEST_SUBSCRIPTION_KEY` i jeden dla punktu końcowego o nazwie `BING_AUTOSUGGEST_ENDPOINT` .

W `main()` funkcji Utwórz wystąpienie klienta z punktem końcowym i kluczem.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>Wyślij żądanie interfejsu API

W tej samej metodzie Użyj metody [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) klienta, aby wysłać zapytanie do usługi Bing. Następnie należy wykonać iterację odpowiedzi na [sugestie](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) i wydrukować pierwszą sugestię.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację go za pomocą `go run [arguments]` polecenia z katalogu aplikacji.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Usuń grupę zasobów z Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Usuń grupę zasobów w interfejsie wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Bing Autosuggest tutorial (Samouczek dotyczący automatycznego sugerowania Bing)](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Zobacz też

- [Czym jest funkcja automatycznego sugerowania Bing?](../../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 reference (Dokumentacja dotycząca automatycznego sugerowania Bing w wersji 7)](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)