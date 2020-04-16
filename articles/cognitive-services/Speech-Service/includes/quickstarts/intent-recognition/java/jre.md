---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 58f771120c5c81e27751af66b64e146c79bb6508
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421875"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">Zainstaluj zestaw SDK mowy dla środowiska programistycznego i utwórz pusty przykładowy projekt<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji usługi LUIS do rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otwórz swój projekt

1. Otwórz preferowany IDE.
2. Załaduj `Main.java`projekt i otwórz .

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `IntentRecognizer` należy utworzyć konfigurację, która używa klucza i lokalizacji zasobu przewidywania usługi LUIS.  

Wstaw ten kod w bloku `main()`try / catch w . Upewnij się, że aktualizujesz następujące wartości:

* Zamień `"YourLanguageUnderstandingSubscriptionKey"` klucz przewidywania usługi LUIS.
* Zamień `"YourLanguageUnderstandingServiceRegion"` lokalizację usługi LUIS. Użyj **identyfikatora regionu** z [regionu](https://aka.ms/speech/sdkregion)

>[!TIP]
> Jeśli potrzebujesz pomocy w znajdowaniu tych wartości, zobacz [Tworzenie aplikacji usługi LUIS w celu rozpoznania intencji](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

W tym przykładzie `FromSubscription()` użyto `SpeechConfig`metody do utworzenia pliku . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

SDK mowy domyślnie rozpoznaje przy użyciu en-us dla języka, zobacz [Określanie języka źródłowego mowy do tekstu, aby](../../../../how-to-specify-source-language.md) uzyskać informacje na temat wybierania języka źródłowego.

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie funkcji IntentRecognizer

Teraz utwórzmy plik `IntentRecognizer`. Wstaw ten kod tuż poniżej konfiguracji mowy.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodawanie languageUnderstandingModel i intencje

Należy skojarzyć `LanguageUnderstandingModel` z aparatem rozpoznawania intencji i dodać intencje, które chcesz rozpoznać. Będziemy używać intencji z wstępnie utworzonej domeny do automatyzacji domu.

Wstaw ten `IntentRecognizer`kod poniżej swojego . Upewnij się, `"YourLanguageUnderstandingAppId"` że zastąpisz identyfikator aplikacji usługi LUIS.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji usługi LUIS w celu rozpoznania intencji](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Rozpoznawanie intencji

Z `IntentRecognizer` obiektu, masz zamiar wywołać `recognizeOnceAsync()` metodę. Ta metoda pozwala usługi mowy wiedzieć, że wysyłasz jedną frazę do rozpoznawania i że po zidentyfikowaniu frazy, aby zatrzymać rozpoznawanie mowy.

Wstaw ten kod poniżej modelu:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy wynik rozpoznawania jest zwracany przez usługę mowy, należy coś z nim zrobić. Będziemy to proste i wydrukować wynik do konsoli.

Wstaw ten kod `recognizeOnceAsync()`poniżej połączenia do .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Zwolnij zasoby

Ważne jest, aby zwolnić zasoby mowy, gdy skończysz z nich korzystać. Wstaw ten kod na końcu bloku try/ catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

> [!NOTE]
> Dodaliśmy kilka komentarzy do tej wersji.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Naciśnij <kbd>klawisz F11</kbd>lub wybierz pozycję **Uruchom** > **debugowanie**.
Następne 15 sekund mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
