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
ms.openlocfilehash: 53e6382cf8d046b2c9818b906890bc64642fd2ed
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77372054"
---
Użyj biblioteki klienta tworzenia Language Understanding (LUIS) dla platformy .NET, aby:

* Tworzenie aplikacji
* Dodawanie intencji, jednostek i przykładowych wyrażenia długości
* Dodaj funkcje, takie jak lista fraz
* Uczenie i publikowanie aplikacji

[Biblioteka dokumentacji](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | referencyjnej —[przykłady w języku C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs) ([kod źródłowy autora kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Language Understanding (LUIS) — konto portalu — [Utwórz je bezpłatnie](https://www.luis.ai)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).


## <a name="setting-up"></a>Konfigurowanie

### <a name="get-your-language-understanding-luis-starter-key"></a>Pobierz klucz początkowy Language Understanding (LUIS)

Pobierz swój [klucz początkowy](../luis-how-to-azure-subscription.md#starter-key) , tworząc zasób Luis Authoring. Zachowaj klucz oraz region klucza dla następnego kroku.

### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

Przy użyciu klucza i regionu klucza Utwórz dwa zmienne środowiskowe do uwierzytelniania:

* `COGNITIVESERVICE_AUTHORING_KEY`— Klucz zasobu do uwierzytelniania żądań.
* `COGNITIVESERVICE_REGION`— Region skojarzony z kluczem. Na przykład: `westus`.

Skorzystaj z instrukcji dotyczących systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Po dodaniu zmiennej środowiskowej Uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[macOS](#tab/unix)

`.bash_profile`Edytuj i Dodaj zmienną środowiskową:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

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

W katalogu aplikacji zainstaluj bibliotekę klienta tworzenia Language Understanding (LUIS) dla platformy .NET przy użyciu następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.


## <a name="object-model"></a>Model obiektów

Klient tworzenia Language Understanding (LUIS) to obiekt [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure, który zawiera klucz tworzenia.

Po utworzeniu klienta Użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Aplikacje — [Tworzenie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [usuwanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [Publikowanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Przykład wyrażenia długości — [Dodaj przez partię](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [Usuń według identyfikatora](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Funkcje — zarządzanie [listami fraz](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Model — zarządzanie [intencjami](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) i jednostkami
* [Wzorce — zarządzanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet) wzorcami
* [Uczenie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) aplikacji i sondowanie jej pod kątem [stanu szkolenia](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Wersje](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) — zarządzanie przy użyciu klonowania, eksportowania i usuwania


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta tworzenia Language Understanding (LUIS) dla platformy .NET:

* [Tworzenie aplikacji](#create-a-luis-app)
* [Dodawanie jednostek](#create-entities-for-the-app)
* [Dodawanie intencji](#create-intent-for-the-app)
* [Dodawanie przykładowych wypowiedzi](#add-example-utterance-to-intent)
* [Uczenie aplikacji](#train-the-app)
* [Publikowanie aplikacji](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Zastąp istniejący `using` kod następującymi `using` dyrektywami:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

1. Utwórz zmienną służącą do zarządzania kluczem tworzenia ściągniętą ze zmiennej środowiskowej o `COGNITIVESERVICES_AUTHORING_KEY`nazwie. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie załadować Edytor, środowisko IDE lub powłokę, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

1. Utwórz zmienne do przechowywania regionu i punktu końcowego tworzenia. Region klucza tworzenia zależy od tego, gdzie tworzysz. [Trzy regiony tworzenia](../luis-reference-regions.md) :

    * Australijski`australiaeast`
    * Terenie`westeurope`
    * Stany Zjednoczone i inne regiony `westus` — (domyślnie)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Utwórz obiekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) .

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Utwórz aplikację LUIS, aby zawierała model przetwarzania języka naturalnego (NLP), w którym znajdują się intencje, jednostki i przykład wyrażenia długości.

1. Utwórz element [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Nazwa i kultura języka są wymagane właściwości.

1. Wywołaj metodę [Apps. addasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) . Odpowiedź jest IDENTYFIKATORem aplikacji.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Utwórz cel dla aplikacji
Zamiarem jest obiekt podstawowy w modelu aplikacji LUIS. Celem jest wyrównanie do grupy _zamiarów_wypowiedź użytkownika. Użytkownik może zadać pytanie lub utworzyć instrukcję poszukującą konkretnej _zamierzonej_ odpowiedzi z bot (lub innej aplikacji klienckiej). Przykłady zamiarów polegają na rezerwacji lotu, zaproszeniu o Pogoda w miejscu docelowym i zaproszeniu o informacje kontaktowe dotyczące usługi klienta.

Utwórz [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) o nazwie unikatowego zamiaru, a następnie Przekaż identyfikator aplikacji, identyfikator wersji i ModelCreateObject do metody [model. AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) . Odpowiedź jest IDENTYFIKATORem intencji.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Tworzenie jednostek dla aplikacji

Jednostki, które nie są wymagane, są dostępne w większości aplikacji. Jednostka wyodrębnia informacje z wypowiedź użytkownika, niezbędne do fullfil zamiaru użytkownika. Istnieje kilka typów [wstępnie skompilowanych](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) i niestandardowych jednostek, z których każdy ma własne modele obiektów transformacji danych (DTO).  Typowe wstępnie skompilowane jednostki do dodania do aplikacji obejmują [Number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [porządkową](../luis-reference-prebuilt-ordinal.md).

Ta metoda **addentities** utworzyła `Location` prostą jednostkę z dwiema rolami, `Class` prostą jednostką `Flight` , jednostką złożoną i dodaje kilka wstępnie utworzonych jednostek.

Ważne jest, aby wiedzieć, że jednostki nie są oznaczone zamiarem. Mogą one i zwykle dotyczyć wielu intencji. Tylko przykład wyrażenia długości użytkownika jest oznaczony dla określonego, pojedynczego zamiaru.

Metody tworzenia dla jednostek są częścią klasy [modelu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) . Każdy typ jednostki ma własny model obiektów transformacji danych (DTO), zwykle zawierający wyraz `model` w przestrzeni nazw [modeli](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) .

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Dodawanie przykładu wypowiedź do celu

W celu określenia zamiaru i wyodrębnienia jednostek wypowiedź aplikacja wymaga przykładów wyrażenia długości. Przykłady muszą dotyczyć określonego, pojedynczego przeznaczenie i powinny oznaczać wszystkie jednostki niestandardowe. Wstępnie skompilowane jednostki nie muszą być oznaczone.

Dodaj przykład wyrażenia długości, tworząc listę obiektów [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) , jeden obiekt dla każdego przykładu wypowiedź. Każdy przykład powinien oznaczać wszystkie jednostki ze słownikiem par nazwa-wartość nazwy jednostki i wartości jednostki. Wartość jednostki powinna być dokładnie taka, jak pojawia się w tekście przykładu wypowiedź.

Wywołaj [przykłady. BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) z identyfikatorem aplikacji, identyfikatorem wersji oraz listą przykładów. Wywołanie reaguje na listę wyników. Należy sprawdzić każdy przykład, aby upewnić się, że został pomyślnie dodany do modelu.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

Metody **tworzenia i** **etykietowania** są metodami narzędzi, które ułatwiają tworzenie obiektów.

## <a name="train-the-app"></a>Uczenie aplikacji

Po utworzeniu modelu aplikacja LUIS musi być przeszkolone dla tej wersji modelu. Model przeszkolony może być używany w [kontenerze](../luis-container-howto.md)lub [publikowany](../luis-how-to-publish-app.md) w gniazdach tymczasowych lub produkcyjnych.

Metoda [uczenie. TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) wymaga identyfikatora aplikacji i identyfikatora wersji.

Bardzo mały model, taki jak ten przewodnik Szybki Start, będzie przeszkolać się bardzo szybko. W przypadku aplikacji na poziomie produkcyjnym szkolenie aplikacji powinno obejmować wywołanie sondowania metody [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) , aby określić, kiedy lub czy szkolenie zakończyło się pomyślnie. Odpowiedź jest listą obiektów [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) z osobnym stanem dla każdego obiektu. Aby szkolenie zostało uznane za ukończone, wszystkie obiekty muszą się powieść.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publikowanie aplikacji Language Understanding

Opublikuj aplikację LUIS przy użyciu metody [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) . Spowoduje to opublikowanie aktualnie przeszkolonej wersji do określonego miejsca w punkcie końcowym. Aplikacja kliencka używa tego punktu końcowego do wysyłania wyrażenia długości użytkownika w celu przewidywania założeń i wyodrębniania jednostek.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `dotnet run` polecenia z katalogu aplikacji.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić, możesz usunąć aplikację LUIS. Usuwanie aplikacji odbywa się przy użyciu metody [Apps. DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) . Możesz również usunąć aplikację z [portalu Luis](https://www.luis.ai).