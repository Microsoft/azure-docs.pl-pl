---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c14dfadfb7e8724c196b125e9b93c439fac249c3
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109549"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Zainstaluj zestaw Speech SDK dla środowiska programistycznego i Utwórz pusty przykładowy projekt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji LUIS na potrzeby rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Następnie otwórz projekt w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj projekt i otwórz go `Program.cs` .

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu. Należy pamiętać, że utworzono metodę asynchroniczną o nazwie `RecognizeIntentAsync()` .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby można było zainicjować `IntentRecognizer` obiekt, należy utworzyć konfigurację, która używa klucza i lokalizacji dla zasobu przewidywania Luis.

> [!IMPORTANT]
> Klucz początkowy i klucze tworzenia nie będą działały. Musisz użyć utworzonego wcześniej klucza predykcyjnego i lokalizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

Wstaw ten kod w `RecognizeIntentAsync()` metodzie. Upewnij się, że te wartości są aktualizowane:

* Zamień `"YourLanguageUnderstandingSubscriptionKey"` na klucz predykcyjny Luis.
* Zamień `"YourLanguageUnderstandingServiceRegion"` na lokalizację Luis. Użyj **identyfikatora regionu** z [regionu](../../../../regions.md).

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tych wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

Ten przykład używa `FromSubscription()` metody do skompilowania `SpeechConfig` . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet).

Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer` . Ten obiekt jest tworzony wewnątrz instrukcji using, aby zapewnić odpowiednią wersję niezarządzanych zasobów. Wstaw ten kod w `RecognizeIntentAsync()` metodzie bezpośrednio poniżej konfiguracji mowy.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Musisz skojarzyć obiekt `LanguageUnderstandingModel` z aparatem zamierzania i dodać żądane intencje. Będziemy używać intencji z prekompilowanej domeny dla automatyzacji domowej. Wstaw ten kod w instrukcji using z poprzedniej sekcji. Upewnij się, że zastąpisz `"YourLanguageUnderstandingAppId"` za pomocą identyfikatora aplikacji Luis.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

Ten przykład używa `AddIntent()` funkcji, aby indywidualnie dodawać intencje. Jeśli chcesz dodać wszystkie intencje z modelu, użyj `AddAllIntents(model)` i przekaż model. 

> [!NOTE]
> Zestaw Speech SDK obsługuje tylko punkty końcowe LUIS v 2.0.
> Należy ręcznie zmodyfikować adres URL punktu końcowego programu v 3.0 znaleziony w przykładowym polu zapytania, aby użyć wzorca adresu URL w wersji 2.0.
> Punkty końcowe LUIS v 2.0 zawsze obserwują jeden z następujących dwóch wzorców:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z `IntentRecognizer` obiektu jest wywoływana `RecognizeOnceAsync()` Metoda. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy.

Wewnątrz instrukcji using Dodaj ten kod pod modelem.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Wyświetl wyniki (lub błędy) rozpoznawania

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie to proste i będzie można drukować wyniki w konsoli.

Wewnątrz instrukcji using poniżej `RecognizeOnceAsync()` Dodaj następujący kod:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

> [!NOTE]
> Dodaliśmy Komentarze do tej wersji.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz polecenie **Kompiluj**  >  **kompilację rozwiązania**.
2. **Uruchom aplikację** — z poziomu paska menu wybierz **Debuguj**  >  **Rozpocznij debugowanie** lub naciśnij klawisz <kbd>F5</kbd>.
3. **Rozpocznij rozpoznawanie** — spowoduje to wyświetlenie monitu o rozmowę w języku angielskim. Twoja mowa jest wysyłana do usługi mowy, uzyskanego jako tekst i renderowany w konsoli programu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]