---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 44c4427540e1b94ebcaf00e6875723e0a654e9ef
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425283"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Zainstaluj zestaw Speech SDK dla środowiska programistycznego i Utwórz pusty przykładowy projekt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji LUIS na potrzeby rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otwórz projekt

1. Otwórz preferowany IDE.
2. Utwórz nowy projekt i Utwórz plik o nazwie `quickstart.py` , a następnie otwórz go.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby można było zainicjować `IntentRecognizer` obiekt, należy utworzyć konfigurację, która używa klucza i lokalizacji dla zasobu przewidywania Luis.

Wstaw ten kod w `quickstart.py` . Upewnij się, że te wartości są aktualizowane:

* Zamień `"YourLanguageUnderstandingSubscriptionKey"` na klucz predykcyjny Luis.
* Zamień `"YourLanguageUnderstandingServiceRegion"` na lokalizację Luis. Użyj **identyfikatora regionu** z [regionu](../../../../regions.md)

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tych wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Ten przykład konstruuje `SpeechConfig` Obiekt przy użyciu klucza Luis i regionu. Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer` . Wstaw ten kod bezpośrednio poniżej konfiguracji mowy.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Musisz skojarzyć `LanguageUnderstandingModel` z aparatem zamierzania i dodać żądane intencje. Będziemy używać intencji z prekompilowanej domeny dla automatyzacji domowej.

Wstaw ten kod poniżej `IntentRecognizer` . Upewnij się, że zastąpisz `"YourLanguageUnderstandingAppId"` za pomocą identyfikatora aplikacji Luis. 

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

Ten przykład używa `add_intents()` funkcji, aby dodać listę jawnie zdefiniowanych intencji. Jeśli chcesz dodać wszystkie intencje z modelu, użyj `add_all_intents(model)` i przekaż model.

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z `IntentRecognizer` obiektu jest wywoływana `recognize_once()` Metoda. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy.

Wstaw ten kod pod modelem.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do konsoli.

Poniżej Zadzwoń do `recognize_once()` , Dodaj ten kod.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco.

> [!NOTE]
> Dodaliśmy Komentarze do tej wersji.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Uruchom próbkę z konsoli programu lub w środowisku IDE:

```
python quickstart.py
```

Następne 15 sekund mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]