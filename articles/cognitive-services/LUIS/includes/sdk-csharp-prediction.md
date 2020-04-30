---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 2ba136cd479da0cd394b5e5afe6ebe7c22b539d5
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732100"
---
Użyj biblioteki klienta przewidywania Language Understanding (LUIS) dla platformy .NET, aby:

* Pobierz prognozowanie według miejsca
* Prognozowanie według wersji

[Biblioteka dokumentacji](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | referencyjnej —[przykłady w języku C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x) [(NuGet Code przewidywania](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | źródłowego)

## <a name="prerequisites"></a>Wymagania wstępne

* Language Understanding (LUIS) — konto portalu — [Utwórz je bezpłatnie](https://www.luis.ai)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Identyfikator aplikacji LUIS — Użyj publicznego identyfikatora aplikacji IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. Zapytanie użytkownika używane w kodzie szybkiego startu jest specyficzne dla tej aplikacji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

Przy użyciu klucza i nazwy zasobu Utwórz dwa zmienne środowiskowe do uwierzytelniania:

* `LUIS_PREDICTION_KEY`— Klucz zasobu do uwierzytelniania żądań.
* `LUIS_ENDPOINT_NAME`— Nazwa zasobu skojarzona z kluczem.

Skorzystaj z instrukcji dotyczących systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej Uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[macOS](#tab/unix)

`.bash_profile`Edytuj i Dodaj zmienną środowiskową:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.

---

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE.

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj polecenia dotnet `new` , aby utworzyć nową aplikację konsolową o nazwie. `language-understanding-quickstart` To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Zmień katalog na nowo utworzony folder aplikacji.

1. Aplikację można skompilować przy użyciu:

    ```dotnetcli
    dotnet build
    ```

    Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta przewidywania środowiska uruchomieniowego Language Understanding (LUIS) dla platformy .NET przy użyciu następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektów

Klient predykcyjny w środowisku uruchomieniowym Language Understanding (LUIS) to obiekt [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure, który zawiera klucz zasobu.

Po utworzeniu klienta Użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Prognozowanie według [przemieszczania lub miejsca produktu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Prognozowanie według [wersji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej środowiska uruchomieniowego Language Understanding (LUIS) dla platformy .NET:

* [Prognozowanie według miejsca](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Zastąp istniejący `using` kod następującymi `using` dyrektywami:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

1. Utwórz zmienne dla klucza, nazwy i identyfikatora aplikacji:

    Zmienne służące do zarządzania kluczem przewidywania pobierane ze zmiennej środowiskowej o nazwie `LUIS_PREDICTION_KEY`. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie załadować Edytor, środowisko IDE lub powłokę, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

    Utwórz zmienną do przechowywania nazwy `LUIS_ENDPOINT_NAME`zasobu.

    Utwórz zmienną dla identyfikatora aplikacji jako zmienną środowiskową o nazwie `LUIS_APP_ID`. Ustaw zmienną środowiskową na publiczną aplikację IoT:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Utwórz obiekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) .

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Pobierz prognozowanie z środowiska uruchomieniowego

Dodaj następującą metodę, aby utworzyć żądanie do środowiska uruchomieniowego przewidywania.

Wypowiedź użytkownika jest częścią obiektu [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) .

Metoda **GetSlotPredictionAsync** wymaga kilku parametrów, takich jak identyfikator aplikacji, nazwa gniazda, obiekt żądania prognozowania w celu spełnienia żądania. Inne opcje, takie jak verbose, pokazują wszystkie intencje i dzienniki są opcjonalne.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Kod główny do prognozowania

Użyj następującej metody Main, aby powiązać zmienne i metody w celu uzyskania prognozowania.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `dotnet run` polecenia z katalogu aplikacji.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po wykonaniu prognoz Wyczyść prace z tego przewodnika Szybki Start, usuwając plik program.cs i jego podkatalogi.
