---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 8bcc3e41f87570822f48a111fe98d1fdaf72679f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470857"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">Zainstaluj zestaw Speech SDK dla środowiska programistycznego i Utwórz pusty przykładowy projekt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji LUIS na potrzeby rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otwórz projekt

1. Otwórz preferowany IDE.
2. Załaduj projekt i otwórz go `Main.java` .

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby można było zainicjować `IntentRecognizer` obiekt, należy utworzyć konfigurację, która używa klucza i lokalizacji dla zasobu przewidywania Luis.  

Wstaw ten kod w bloku try/catch w `main()` . Upewnij się, że te wartości są aktualizowane:

* Zamień `"YourLanguageUnderstandingSubscriptionKey"` na klucz predykcyjny Luis.
* Zamień `"YourLanguageUnderstandingServiceRegion"` na lokalizację Luis. Użyj **identyfikatora regionu** z [regionu](https://aka.ms/speech/sdkregion)

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tych wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Ten przykład używa `FromSubscription()` metody do skompilowania `SpeechConfig` . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true).

Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer` . Wstaw ten kod bezpośrednio poniżej konfiguracji mowy.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Musisz skojarzyć `LanguageUnderstandingModel` z aparatem rozpoznawania intencji i dodać żądane intencje. Będziemy używać intencji z prekompilowanej domeny dla automatyzacji domowej.

Wstaw ten kod poniżej `IntentRecognizer` . Upewnij się, że zastąpisz `"YourLanguageUnderstandingAppId"` za pomocą identyfikatora aplikacji Luis.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

Ten przykład używa `addIntent()` funkcji, aby indywidualnie dodawać intencje. Jeśli chcesz dodać wszystkie intencje z modelu, użyj `addAllIntents(model)` i przekaż model.

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z `IntentRecognizer` obiektu jest wywoływana `recognizeOnceAsync()` Metoda. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy.

Wstaw ten kod pod modelem:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do konsoli.

Wstaw ten kod poniżej rozmowy do `recognizeOnceAsync()` .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Zasoby wydania

Ważne jest, aby zwolnić zasoby mowy po ich zakończeniu. Wstaw ten kod na końcu bloku try/catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

> [!NOTE]
> Dodaliśmy Komentarze do tej wersji.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz <kbd>F11</kbd>lub wybierz polecenie **Uruchom**  >  **debugowanie**.
Następne 15 sekund mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
