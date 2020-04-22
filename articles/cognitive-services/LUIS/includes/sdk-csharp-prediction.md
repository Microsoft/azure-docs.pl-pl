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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732100"
---
Użyj biblioteki klienta przewidywania zrozumienia języka (LUIS) dla platformy .NET, aby:

* Uzyskaj przewidywanie według gniazda
* Przewidywanie według wersji

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [dokumentacja dokumentacja Biblioteka kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [źródłowy Przewidywanie pakiet runtime (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# Przykłady](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto portalu language understanding (LUIS) — [utwórz je bezpłatnie](https://www.luis.ai)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Identyfikator aplikacji usługi LUIS — użyj publicznego identyfikatora aplikacji IoT . `df67dcdb-c37d-46af-88e1-8b97951ca1c2` Kwerenda użytkownika używana w kodzie szybkiego startu jest specyficzna dla tej aplikacji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-environment-variable"></a>Tworzenie zmiennej środowiskowej

Używając klucza i nazwy zasobu, utwórz dwie zmienne środowiskowe do uwierzytelniania:

* `LUIS_PREDICTION_KEY`- Klucz zasobu do uwierzytelniania żądań.
* `LUIS_ENDPOINT_NAME`- Nazwa zasobu skojarzona z kluczem.

Użyj instrukcji dla systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[macOS](#tab/unix)

Edytuj `.bash_profile`swój program i dodaj zmienną środowiskową:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.

---

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze lub ide.

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia `new` dotnet, `language-understanding-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# `Program.cs`z jednym plikiem źródłowym: .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Zmień katalog na nowo utworzony folder aplikacji.

1. Aplikację można utworzyć za pomocą:

    ```dotnetcli
    dotnet build
    ```

    Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta prognozowania języka (LUIS) dla platformy .NET za pomocą następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektu

Klient środowiska uruchomieniowego przewidywania języka (LUIS) jest obiektem [LUISRuntimeClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) który uwierzytelnia się na platformie Azure, który zawiera klucz zasobu.

Po utworzeniu klienta użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Przewidywanie przez [przemieszczania lub gniazda produktu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Przewidywanie według [wersji](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta przewidywania języka (LUIS) dla platformy .NET:

* [Przewidywanie według szczeliny](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w programie IDE. Zastąp `using` istniejący `using` kod następującymi dyrektywami:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

1. Tworzenie zmiennych dla klucza, nazwy i identyfikatora aplikacji:

    Zmienne do zarządzania kluczem przewidywania pobrane `LUIS_PREDICTION_KEY`ze zmiennej środowiskowej o nazwie . Jeśli utworzono zmienną środowiskową po uruchomieniu aplikacji, edytor, IDE lub powłoki uruchomionej będzie musiał zostać zamknięty i ponownie załadowany, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

    Utwórz zmienną do `LUIS_ENDPOINT_NAME`przechowywania nazwy zasobu .

    Utwórz zmienną dla identyfikatora aplikacji `LUIS_APP_ID`jako zmienną środowiskową o nazwie . Ustaw zmienną środowiskową na publiczną aplikację IoT:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Utwórz [obiekt ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) za pomocą klucza i użyj go z punktem końcowym do utworzenia obiektu [LUISRuntimeClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Pobierz przewidywanie ze środowiska wykonawczego

Dodaj następującą metodę, aby utworzyć żądanie do środowiska uruchomieniowego przewidywanie.

Wypowiedź użytkownika jest częścią [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) obiektu.

**Metoda GetSlotPredictionAsync** wymaga kilku parametrów, takich jak identyfikator aplikacji, nazwa gniazda, obiekt żądania przewidywania, aby spełnić żądanie. Inne opcje, takie jak pełne, pokaż wszystkie intencje i dziennik są opcjonalne.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Główny kod do przewidywania

Użyj następującej metody głównej, aby powiązać zmienne i metody razem, aby uzyskać przewidywanie.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `dotnet run` pomocą polecenia z katalogu aplikacji.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu z prognoz, oczyścić pracę z tego przewodnika Szybki start, usuwając plik program.cs i jego podkatalogów.
