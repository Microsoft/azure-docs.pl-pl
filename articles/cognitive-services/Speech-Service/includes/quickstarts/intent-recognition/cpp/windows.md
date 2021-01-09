---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: e81d1f03caa11771be51bf74cdbd0d51c1de8c1d
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052890"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">Zainstaluj zestaw Speech SDK dla środowiska programistycznego i Utwórz pusty przykładowy projekt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji LUIS na potrzeby rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Następnie otwórz projekt w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj projekt i otwórz go `helloworld.cpp` .

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu. Należy pamiętać, że utworzono metodę asynchroniczną o nazwie `recognizeIntent()` .

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby można było zainicjować `IntentRecognizer` obiekt, należy utworzyć konfigurację, która używa klucza i lokalizacji dla zasobu przewidywania Luis.

> [!IMPORTANT]
> Klucz początkowy i klucze tworzenia nie będą działały. Musisz użyć utworzonego wcześniej klucza predykcyjnego i lokalizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

Wstaw ten kod w `recognizeIntent()` metodzie. Upewnij się, że te wartości są aktualizowane:

* Zamień `"YourLanguageUnderstandingSubscriptionKey"` na klucz predykcyjny Luis.
* Zamień `"YourLanguageUnderstandingServiceRegion"` na lokalizację Luis.  Użyj **identyfikatora regionu** z [regionu](../../../../regions.md).

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tych wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

Ten przykład używa `FromSubscription()` metody do skompilowania `SpeechConfig` . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](/cpp/cognitive-services/speech/speechconfig).

Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer` . Wstaw ten kod w `recognizeIntent()` metodzie bezpośrednio poniżej konfiguracji mowy.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Musisz skojarzyć `LanguageUnderstandingModel` z aparatem rozpoznawania intencji i dodać żądane intencje. Będziemy używać intencji z prekompilowanej domeny dla automatyzacji domowej.

Wstaw ten kod poniżej `IntentRecognizer` . Upewnij się, że zastąpisz `"YourLanguageUnderstandingAppId"` za pomocą identyfikatora aplikacji Luis.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

Ten przykład używa `AddIntent()` funkcji, aby indywidualnie dodawać intencje. Jeśli chcesz dodać wszystkie intencje z modelu, użyj `AddAllIntents(model)` i przekaż model.

> [!NOTE]
> Można utworzyć LanguageUnderstandingModel, przekazując adres URL punktu końcowego do metody FromEndpoint.
> Zestaw Speech SDK obsługuje tylko punkty końcowe LUIS v 2.0, a punkty końcowe LUIS v 2.0 zawsze obserwują jeden z tych dwóch wzorców:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z `IntentRecognizer` obiektu jest wywoływana `RecognizeOnceAsync()` Metoda. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy. Dla uproszczenia poczekamy na przyszłe zakończenie działania.

Wstaw ten kod pod modelem:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do konsoli.

Wstaw poniższy kod `auto result = recognizer->RecognizeOnceAsync().get();` :

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

> [!NOTE]
> Dodaliśmy Komentarze do tej wersji.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz polecenie **Kompiluj**  >  **kompilację rozwiązania**.
2. **Uruchom aplikację** — z poziomu paska menu wybierz **Debuguj**  >  **Rozpocznij debugowanie** lub naciśnij klawisz <kbd>F5</kbd>.
3. **Rozpocznij rozpoznawanie** — spowoduje to wyświetlenie monitu o rozmowę w języku angielskim. Twoja mowa jest wysyłana do usługi mowy, uzyskanego jako tekst i renderowany w konsoli programu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]