---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 54b5e3f0ed2fd10fa04305fdefbec725143f62c5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421882"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Zainstaluj zestaw SDK mowy dla środowiska programistycznego jako projekt utworzony i pusty przykładowy .<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji usługi LUIS do rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Następnie otwórz projekt w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj `Program.cs`projekt i otwórz .

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu. Należy pamiętać, że utworzono metodę `RecognizeIntentAsync()`asynchronizową o nazwie .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `IntentRecognizer` należy utworzyć konfigurację, która używa klucza i lokalizacji zasobu przewidywania usługi LUIS.

> [!IMPORTANT]
> Klucz startowy i klucze autora nie będą działać. Należy użyć klucza przewidywania i lokalizacji, które zostały utworzone wcześniej. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji usługi LUIS w celu rozpoznania intencji](#create-a-luis-app-for-intent-recognition).

Wstaw ten `RecognizeIntentAsync()` kod w metodzie. Upewnij się, że aktualizujesz następujące wartości:

* Zamień `"YourLanguageUnderstandingSubscriptionKey"` klucz przewidywania usługi LUIS.
* Zamień `"YourLanguageUnderstandingServiceRegion"` lokalizację usługi LUIS. Użyj **identyfikatora regionu** z [regionu](https://aka.ms/speech/sdkregion).

>[!TIP]
> Jeśli potrzebujesz pomocy w znajdowaniu tych wartości, zobacz [Tworzenie aplikacji usługi LUIS w celu rozpoznania intencji](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

W tym przykładzie `FromSubscription()` użyto `SpeechConfig`metody do utworzenia pliku . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

SDK mowy domyślnie rozpoznaje przy użyciu en-us dla języka, zobacz [Określanie języka źródłowego mowy do tekstu, aby](../../../../how-to-specify-source-language.md) uzyskać informacje na temat wybierania języka źródłowego.

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie funkcji IntentRecognizer

Teraz utwórzmy plik `IntentRecognizer`. Ten obiekt jest tworzony wewnątrz using instrukcji, aby zapewnić prawidłowe uwalnianie zasobów niezarządzanych. Wstaw ten `RecognizeIntentAsync()` kod w metodzie, tuż poniżej konfiguracji mowy.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodawanie languageunderstandingModel i intencje

Należy skojarzyć `LanguageUnderstandingModel` z aparatem rozpoznawania intencji i dodać intencje, które mają być rozpoznawane. Będziemy używać intencji z wstępnie utworzonej domeny do automatyzacji domu. Wstaw ten kod w using instrukcji z poprzedniej sekcji. Upewnij się, `"YourLanguageUnderstandingAppId"` że zastąpisz identyfikator aplikacji usługi LUIS.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji usługi LUIS w celu rozpoznania intencji](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

## <a name="recognize-an-intent"></a>Rozpoznawanie intencji

Z `IntentRecognizer` obiektu, masz zamiar wywołać `RecognizeOnceAsync()` metodę. Ta metoda pozwala usługi mowy wiedzieć, że wysyłasz jedną frazę do rozpoznawania i że po zidentyfikowaniu frazy, aby zatrzymać rozpoznawanie mowy.

Wewnątrz using instrukcji, dodaj ten kod poniżej modelu.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy wynik rozpoznawania jest zwracany przez usługę mowy, należy coś z nim zrobić. Będziemy to proste i wydrukować wyniki do konsoli.

Wewnątrz using instrukcji, `RecognizeOnceAsync()`poniżej, dodaj ten kod:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

> [!NOTE]
> Dodaliśmy kilka komentarzy do tej wersji.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia aplikacji i testowania rozpoznawania mowy za pomocą usługi Mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz pozycję **Build** > **Build Solution**.
2. **Uruchom aplikację** — na pasku menu wybierz pozycję **Debugowanie** > **rozpocznij debugowanie** lub naciśnij <kbd>klawisz F5</kbd>.
3. **Rozpocznij rozpoznawanie** - poprosi Cię o wymówienia frazy w języku angielskim. Mowa jest wysyłana do usługi Mowy, transkrybowana jako tekst i renderowana w konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
