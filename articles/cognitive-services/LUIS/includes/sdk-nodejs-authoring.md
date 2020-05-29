---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 6e240a0c5d5d77489c92862238c2e5041bdeabe3
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171354"
---
Użyj biblioteki klienta tworzenia Language Understanding (LUIS) dla środowiska Node. js, aby:

* Utwórz aplikację.
* Dodawanie intencji, jednostek i przykładowych wyrażenia długości.
* Dodaj funkcje, takie jak lista fraz.
* Uczenie i publikowanie aplikacji.
* Usuń aplikację

[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring)  |  źródłowy biblioteki [Przykłady pakietu autorskiego (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [pakiet środowiska uruchomieniowego (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)  |  [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Wymagania wstępne

* Language Understanding tworzenia zasobu: [Utwórz go w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Konfigurowanie

### <a name="get-your-language-understanding-luis-starter-key"></a>Pobierz klucz początkowy Language Understanding (LUIS)

Pobierz swój [klucz początkowy](../luis-how-to-azure-subscription.md#starter-key) , tworząc zasób Luis Authoring. Zachowaj klucz i punkt końcowy klucza dla kolejnego kroku.

### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

Przy użyciu klucza i regionu klucza Utwórz dwa zmienne środowiskowe do uwierzytelniania:

* `LUIS_AUTHORING_KEY`— Klucz zasobu do uwierzytelniania żądań.
* `LUIS_AUTHORING_ENDPOINT`— Punkt końcowy skojarzony z kluczem.

Skorzystaj z instrukcji dotyczących systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Po dodaniu zmiennej środowiskowej Uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[macOS](#tab/unix)

Edytuj `.bash_profile` i Dodaj zmienną środowiskową:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Zainstaluj bibliotekę NPM na potrzeby tworzenia LUIS

W katalogu aplikacji Zainstaluj zależności przy użyciu następującego polecenia:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Model obiektów

Klient tworzenia Language Understanding (LUIS) to obiekt [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure, który zawiera klucz tworzenia.

Po utworzeniu klienta Użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Aplikacje — [Dodawanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [usuwanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [Publikowanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Przykład wyrażenia długości — [Dodaj przez partię](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [Usuń według identyfikatora](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Funkcje — zarządzanie [listami fraz](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Model — zarządzanie [intencjami](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) i jednostkami
* [Wzorce — zarządzanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-) wzorcami
* [Uczenie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) aplikacji i sondowanie jej pod kątem [stanu szkolenia](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Wersje](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) — zarządzanie przy użyciu klonowania, eksportowania i usuwania


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta tworzenia Language Understanding (LUIS) dla środowiska Node. js:

* [Tworzenie aplikacji](#create-a-luis-app)
* [Dodawanie jednostek](#create-entities-for-the-app)
* [Dodawanie intencji](#create-intent-for-the-app)
* [Dodawanie przykładowych wypowiedzi](#add-example-utterance-to-intent)
* [Uczenie aplikacji](#train-the-app)
* [Publikowanie aplikacji](#publish-a-language-understanding-app)
* [Usuwanie aplikacji](#delete-a-language-understanding-app)
* [Wyświetlanie listy aplikacji](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

Utwórz nowy plik tekstowy w preferowanym edytorze lub środowisku IDE o nazwie `luis_authoring_quickstart.js` . Następnie Dodaj następujące zależności.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Dependencies)]

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz obiekt [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) .

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Utwórz aplikację LUIS, aby zawierała model przetwarzania języka naturalnego (NLP), w którym znajdują się intencje, jednostki i przykład wyrażenia długości.

1. Utwórz metodę [Add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) obiektu [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) , aby utworzyć aplikację. Nazwa i kultura języka są wymagane właściwości.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Utwórz cel dla aplikacji
Zamiarem jest obiekt podstawowy w modelu aplikacji LUIS. Celem jest wyrównanie do grupy _zamiarów_wypowiedź użytkownika. Użytkownik może zadać pytanie lub utworzyć instrukcję poszukującą konkretnej _zamierzonej_ odpowiedzi z bot (lub innej aplikacji klienckiej). Przykłady zamiarów polegają na rezerwacji lotu, zaproszeniu o Pogoda w miejscu docelowym i zaproszeniu o informacje kontaktowe dotyczące usługi klienta.

Użyj metody [model. add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) z nazwą unikatowego zamiaru, a następnie Przekaż identyfikator aplikacji, identyfikator wersji i nową nazwę celu.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Tworzenie jednostek dla aplikacji

Jednostki, które nie są wymagane, są dostępne w większości aplikacji. Jednostka wyodrębnia informacje z wypowiedź użytkownika, niezbędne do fullfil zamiaru użytkownika. Istnieje kilka typów [wstępnie skompilowanych](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) i niestandardowych jednostek, z których każdy ma własne modele obiektów transformacji danych (DTO).  Typowe wstępnie skompilowane jednostki do dodania do aplikacji obejmują [Number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [porządkową](../luis-reference-prebuilt-ordinal.md).

Ta **add_entities** Metoda utworzyła `Location` prostą jednostkę z dwiema rolami, `Class` prostą jednostką, `Flight` jednostką złożoną i dodaje kilka wstępnie utworzonych jednostek.

Ważne jest, aby wiedzieć, że jednostki nie są oznaczone zamiarem. Mogą one i zwykle dotyczyć wielu intencji. Tylko przykład wyrażenia długości użytkownika jest oznaczony dla określonego, pojedynczego zamiaru.

Metody tworzenia dla jednostek są częścią klasy [modelu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) . Każdy typ jednostki ma własny model obiektów transformacji danych (DTO).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Dodawanie przykładu wypowiedź do celu

W celu określenia zamiaru i wyodrębnienia jednostek wypowiedź aplikacja wymaga przykładów wyrażenia długości. Przykłady muszą dotyczyć określonego, pojedynczego przeznaczenie i powinny oznaczać wszystkie jednostki niestandardowe. Wstępnie skompilowane jednostki nie muszą być oznaczone.

Dodaj przykład wyrażenia długości, tworząc listę obiektów [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) , jeden obiekt dla każdego przykładu wypowiedź. Każdy przykład powinien oznaczać wszystkie jednostki ze słownikiem par nazwa-wartość nazwy jednostki i wartości jednostki. Wartość jednostki powinna być dokładnie taka, jak pojawia się w tekście przykładu wypowiedź.

Wywołaj [przykłady. Batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) z identyfikatorem aplikacji, identyfikatorem wersji oraz listą przykładów. Wywołanie reaguje na listę wyników. Należy sprawdzić każdy przykład, aby upewnić się, że został pomyślnie dodany do modelu.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Uczenie aplikacji

Po utworzeniu modelu aplikacja LUIS musi być przeszkolone dla tej wersji modelu. Model przeszkolony może być używany w [kontenerze](../luis-container-howto.md)lub [publikowany](../luis-how-to-publish-app.md) w gniazdach tymczasowych lub produkcyjnych.

Metoda [uczenie. trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) wymaga identyfikatora aplikacji i identyfikatora wersji.

Bardzo mały model, taki jak ten przewodnik Szybki Start, będzie przeszkolać się bardzo szybko. W przypadku aplikacji na poziomie produkcyjnym szkolenie aplikacji powinno obejmować wywołanie sondowania do metody [get_Status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) , aby określić, kiedy lub czy szkolenie zakończyło się pomyślnie. Odpowiedź jest listą obiektów [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) z osobnym stanem dla każdego obiektu. Aby szkolenie zostało uznane za ukończone, wszystkie obiekty muszą się powieść.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Uczenie wszystkich modeli zabiera więcej czasu. Aby sprawdzić stan szkolenia, użyj klasy OperationResult.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publikowanie aplikacji Language Understanding

Opublikuj aplikację LUIS przy użyciu metody [App. publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) . Spowoduje to opublikowanie aktualnie przeszkolonej wersji do określonego miejsca w punkcie końcowym. Aplikacja kliencka używa tego punktu końcowego do wysyłania wyrażenia długości użytkownika w celu przewidywania założeń i wyodrębniania jednostek.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Usuwanie aplikacji Language Understanding

Usuń aplikację LUIS przy użyciu metody [App. deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) . Spowoduje to usunięcie bieżącej aplikacji.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Wyświetlanie listy aplikacji dotyczących języka

Pobierz listę aplikacji skojarzonych z kluczem interpretacji języka

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node luis_authoring_quickstart.js` polecenia w pliku szybkiego startu.

```console
node luis_authoring_quickstart.js
```

Dane wyjściowe wiersza polecenia aplikacji są następujące:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
