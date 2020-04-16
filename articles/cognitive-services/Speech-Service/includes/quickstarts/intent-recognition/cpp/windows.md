---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 770e037641ac8fbf75989dc94b66fd1df0689e50
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422144"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">Zainstaluj zestaw SDK mowy dla środowiska programistycznego i utwórz pusty przykładowy projekt<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji usługi LUIS do rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Następnie otwórz projekt w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj `helloworld.cpp`projekt i otwórz .

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu. Należy pamiętać, że utworzono metodę `recognizeIntent()`asynchronizową o nazwie .

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `IntentRecognizer` należy utworzyć konfigurację, która używa klucza i lokalizacji zasobu przewidywania usługi LUIS.

> [!IMPORTANT]
> Klucz startowy i klucze autora nie będą działać. Należy użyć klucza przewidywania i lokalizacji, które zostały utworzone wcześniej. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji usługi LUIS w celu rozpoznania intencji](#create-a-luis-app-for-intent-recognition).

Wstaw ten `recognizeIntent()` kod w metodzie. Upewnij się, że aktualizujesz następujące wartości:

* Zamień `"YourLanguageUnderstandingSubscriptionKey"` klucz przewidywania usługi LUIS.
* Zamień `"YourLanguageUnderstandingServiceRegion"` lokalizację usługi LUIS.  Użyj **identyfikatora regionu** z [regionu](https://aka.ms/speech/sdkregion).

>[!TIP]
> Jeśli potrzebujesz pomocy w znajdowaniu tych wartości, zobacz [Tworzenie aplikacji usługi LUIS w celu rozpoznania intencji](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

W tym przykładzie `FromSubscription()` użyto `SpeechConfig`metody do utworzenia pliku . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

SDK mowy domyślnie rozpoznaje przy użyciu en-us dla języka, zobacz [Określanie języka źródłowego mowy do tekstu, aby](../../../../how-to-specify-source-language.md) uzyskać informacje na temat wybierania języka źródłowego.

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie funkcji IntentRecognizer

Teraz utwórzmy plik `IntentRecognizer`. Wstaw ten `recognizeIntent()` kod w metodzie, tuż poniżej konfiguracji mowy.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodawanie languageUnderstandingModel i intencje

Należy skojarzyć `LanguageUnderstandingModel` z aparatem rozpoznawania intencji i dodać intencje, które chcesz rozpoznać. Będziemy używać intencji z wstępnie utworzonej domeny do automatyzacji domu.

Wstaw ten `IntentRecognizer`kod poniżej swojego . Upewnij się, `"YourLanguageUnderstandingAppId"` że zastąpisz identyfikator aplikacji usługi LUIS.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji usługi LUIS w celu rozpoznania intencji](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>Rozpoznawanie intencji

Z `IntentRecognizer` obiektu, masz zamiar wywołać `RecognizeOnceAsync()` metodę. Ta metoda pozwala usługi mowy wiedzieć, że wysyłasz jedną frazę do rozpoznawania i że po zidentyfikowaniu frazy, aby zatrzymać rozpoznawanie mowy. Dla uproszczenia poczekamy na przyszłość, która powróci do końca.

Wstaw ten kod poniżej modelu:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy wynik rozpoznawania jest zwracany przez usługę mowy, należy coś z nim zrobić. Będziemy to proste i wydrukować wynik do konsoli.

Wstaw ten `auto result = recognizer->RecognizeOnceAsync().get();`kod poniżej:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

> [!NOTE]
> Dodaliśmy kilka komentarzy do tej wersji.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia aplikacji i testowania rozpoznawania mowy za pomocą usługi Mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz pozycję **Build** > **Build Solution**.
2. **Uruchom aplikację** — na pasku menu wybierz pozycję **Debugowanie** > **rozpocznij debugowanie** lub naciśnij <kbd>klawisz F5</kbd>.
3. **Rozpocznij rozpoznawanie** - poprosi Cię o wymówienia frazy w języku angielskim. Mowa jest wysyłana do usługi Mowy, transkrybowana jako tekst i renderowana w konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
