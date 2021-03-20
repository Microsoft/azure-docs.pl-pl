---
title: 'Jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK C #'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku dowiesz się, jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK dla języka C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 93a3adf00203e317be912e3e72de7a3f7ca666c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001108"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK dla języka C #

[Zestaw SDK mowy](speech-sdk.md) Cognitive Services integruje się z [usługą Language Understanding (Luis)](https://www.luis.ai/home) w celu zapewnienia **rozpoznawania intencji**. Intencja to coś, co użytkownik chce zrobić: zarezerwować lot, sprawdzić prognozę pogody lub zadzwonić. Następnie może on użyć terminów, które wydają się naturalne. Za pomocą uczenia maszynowego LUIS mapuje żądania użytkowników do zdefiniowanych przez siebie intencji.

> [!NOTE]
> Aplikacja usługi LUIS definiuje intencje i jednostki, które chcesz rozpoznawać. Jest ona używana oddzielnie od aplikacji języka C#, która korzysta z usługi rozpoznawania mowy. W tym artykule „aplikacja” oznacza aplikację usługi LUIS, a „zastosowanie” oznacza kod języka C#.

W tym przewodniku używasz zestawu Speech SDK do tworzenia aplikacji konsolowej C#, która jest pochodząca z wyrażenia długości użytkownika za pośrednictwem mikrofonu urządzenia. Omawiane tematy:

> [!div class="checklist"]
>
> - Tworzenie projektu programu Visual Studio odwołującego się do pakietu NuGet zestawu Speech SDK
> - Utwórz konfigurację mowy i uzyskaj aparat rozpoznawania intencji
> - Uzyskiwanie modelu dla aplikacji usługi LUIS i dodawanie potrzebnych intencji
> - Określanie języka funkcji rozpoznawania mowy
> - Rozpoznawanie mowy z pliku
> - Używanie asynchronicznego, opartego na zdarzeniach rozpoznawania ciągłego

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego przewodnika upewnij się, że masz następujące elementy:

- Konto usługi LUIS. Możesz uzyskać je bezpłatnie za pośrednictwem [portalu usługi LUIS](https://www.luis.ai/home).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (dowolna wersja).

## <a name="luis-and-speech"></a>Usługa LUIS i mowa

Usługa LUIS integruje się z usługą rozpoznawania mowy, aby rozpoznawać intencje z mowy. Nie potrzebujesz subskrypcji usługi rozpoznawania mowy, tylko usługi LUIS.

LUIS używa trzech rodzajów kluczy:

| Typ klucza  | Przeznaczenie                                               |
| --------- | ----------------------------------------------------- |
| Tworzenie | Umożliwia programowe tworzenie i modyfikowanie aplikacji LUIS |
| Starter (początkowy)   | Umożliwia testowanie aplikacji LUIS przy użyciu tylko tekstu   |
| Punkt końcowy  | Autoryzuje dostęp do określonej aplikacji LUIS            |

W tym przewodniku potrzebny jest typ klucza punktu końcowego. W tym przewodniku użyto przykładowej aplikacji LUIS Automation, którą można utworzyć, korzystając z przewodnika Szybki Start dla [aplikacji do automatyzacji domowej](../luis/luis-get-started-create-app.md) . Jeśli utworzono własną aplikację LUIS, można jej użyć zamiast niej.

Gdy tworzysz aplikację LUIS, LUIS automatycznie generuje klucz początkowy, aby można było przetestować aplikację przy użyciu zapytań tekstowych. Ten klucz nie umożliwia integracji usługi mowy i nie współpracuje z tym przewodnikiem. Utwórz zasób LUIS na pulpicie nawigacyjnym platformy Azure i przypisz go do aplikacji LUIS. W tym przewodniku możesz użyć bezpłatnej warstwy subskrypcji.

Po utworzeniu zasobu LUIS na pulpicie nawigacyjnym platformy Azure Zaloguj się do [portalu Luis](https://www.luis.ai/home), wybierz aplikację na stronie **Moje aplikacje** , a następnie przejdź do strony **zarządzania** aplikacji. Na koniec wybierz pozycję **klucze i punkty końcowe** na pasku bocznym.

![Ustawienia kluczy i punktów końcowych usługi LUIS](media/sdk/luis-keys-endpoints-page.png)

Na stronie **Ustawienia kluczy i punktu końcowego** :

1. Przewiń w dół do sekcji **zasoby i klucze** , a następnie wybierz pozycję **Przypisz zasób**.
1. W oknie dialogowym **przypisywanie klucza do aplikacji** wprowadź następujące zmiany:

   - W obszarze **dzierżawca** wybierz pozycję **Microsoft**.
   - W obszarze **Nazwa subskrypcji** wybierz subskrypcję platformy Azure zawierającą zasób Luis, którego chcesz użyć.
   - W obszarze **klucz** wybierz zasób Luis, który ma być używany z aplikacją.

   Za chwilę w tabeli w dolnej części strony zostanie wyświetlona nowa subskrypcja.

1. Wybierz ikonę obok klawisza, aby skopiować go do Schowka. (Możesz użyć dowolnego klucza).

![Klucze subskrypcji aplikacji usługi LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Tworzenie projektu rozpoznawania mowy w programie Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Dodawanie kodu

Następnie Dodaj kod do projektu.

1. W **Eksplorator rozwiązań** Otwórz plik **program. cs**.

1. Zastąp blok `using` instrukcji na początku pliku następującym deklaracją:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Zastąp podaną `Main()` metodę następującym odpowiednikiem asynchronicznym:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Utwórz pustą metodę asynchroniczną `RecognizeIntentAsync()` , jak pokazano poniżej:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. W treści nowej metody Dodaj następujący kod:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Zastąp symbole zastępcze w tej metodzie kluczem subskrypcji, regionem i identyfikatorem aplikacji usługi LUIS w pokazany poniżej sposób.

   | Symbol zastępczy | Zamień na |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Klucz punktu końcowego usługi LUIS. Ponownie musisz pobrać ten element z pulpitu nawigacyjnego platformy Azure, a nie "klawisza początkowego". Można je znaleźć na stronie **klucze i punkty końcowe** aplikacji (w obszarze **Zarządzaj**) w [portalu Luis](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Krótki identyfikator regionu, w którym znajduje się Twoja subskrypcja usługi LUIS, na przykład `westus` dla regionu Zachodnie stany USA. Zobacz [Regions](regions.md) (Regiony). |
   | `YourLanguageUnderstandingAppId` | Identyfikator aplikacji usługi LUIS. Można je znaleźć na stronie **ustawień** aplikacji w [portalu Luis](https://www.luis.ai/home). |

Po wprowadzeniu tych zmian można skompilować (**Control + Shift + B**) i uruchomić (**F5**) aplikację. Po wyświetleniu monitu spróbuj wypowiedzieć "Wyłącz lampki" na mikrofon komputera. Aplikacja wyświetli wynik w oknie konsoli.

Poniższe sekcje zawierają omówienie kodu.

## <a name="create-an-intent-recognizer"></a>Tworzenie aparatu rozpoznawania intencji

Najpierw należy utworzyć konfigurację mowy z poziomu klucza i regionu punktu końcowego LUIS. Konfiguracje mowy umożliwiają tworzenie aparatów rozpoznawania dla różnych funkcji zestawu Speech SDK. Konfiguracja mowy ma wiele sposobów na określenie subskrypcji, której chcesz użyć; w tym miejscu używamy `FromSubscription` , który pobiera klucz subskrypcji i region.

> [!NOTE]
> Użyj klucza i regionu subskrypcji LUIS, a nie subskrypcji usługi mowy.

Następnie utwórz aparat rozpoznawania mowy przy użyciu elementu `new IntentRecognizer(config)`. Ponieważ konfiguracja wie już, której subskrypcji użyć, nie musisz ponownie podawać klucza subskrypcji i punktu końcowego podczas tworzenia aparatu rozpoznawania.

## <a name="import-a-luis-model-and-add-intents"></a>Importowanie modelu usługi LUIS i dodawanie intencji

Teraz zaimportuj model z aplikacji usługi LUIS przy użyciu elementu `LanguageUnderstandingModel.FromAppId()` i dodaj intencje usługi LUIS, które chcesz rozpoznawać za pośrednictwem metody `AddIntent()` aparatu rozpoznawania. Te dwa kroki zwiększają dokładność rozpoznawania mowy, wskazując słowa, których użytkownik prawdopodobnie użyje w swoich żądaniach. Nie musisz dodawać wszystkich intencji aplikacji, jeśli nie chcesz ich rozpoznać w aplikacji.

Aby dodać intencje, należy podać trzy argumenty: model LUIS (który został utworzony i nosi `model` nazwę), Nazwa celu i Identyfikator celu. Różnica między identyfikatorem i nazwą jest następująca.

| `AddIntent()`&nbsp;argument | Przeznaczenie |
| --------------------------- | ------- |
| `intentName` | Nazwa intencji zgodnie z definicją w aplikacji usługi LUIS. Ta wartość musi być zgodna z nazwą celu LUIS. |
| `intentID` | Identyfikator przypisany do rozpoznanej intencji przez zestaw SDK rozpoznawania mowy. Ta wartość może być dowolnie taka, jak chcesz. nie musi odpowiadać nazwie zamierzonej zdefiniowanej w aplikacji LUIS. Jeśli na przykład ten sam kod obsługuje wiele intencji, możesz dla nich użyć tego samego identyfikatora. |

Aplikacja LUIS Automation w domu ma dwa intencje: jeden do włączenia urządzenia i drugi do wyłączania urządzenia. Poniższe wiersze powodują dodanie tych intencji do aparatu rozpoznawania; zastąp trzy wiersze `AddIntent` w metodzie `RecognizeIntentAsync()` tym kodem.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Zamiast dodawać poszczególne osoby, można również użyć `AddAllIntents` metody, aby dodać wszystkie intencje w modelu do aparatu rozpoznawania.

## <a name="start-recognition"></a>Uruchamianie rozpoznawania

Po utworzeniu aparatu rozpoznawania i dodaniu intencji można rozpocząć rozpoznawanie. Zestaw Speech SDK obsługuje rozpoznawanie pojedyncze i ciągłe.

| Tryb rozpoznawania | Metody do wywołania | Wynik |
| ---------------- | --------------- | ------ |
| Pojedyncze | `RecognizeOnceAsync()` | Zwraca rozpoznaną intencję, jeśli istnieje, po jednej wypowiedzi. |
| Ciągłe | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Rozpoznaje wiele wyrażenia długości; emituje zdarzenia (na przykład `IntermediateResultReceived` ), gdy wyniki są dostępne. |

Aplikacja używa trybu pojedynczego zrzutu i dlatego wywołuje metodę `RecognizeOnceAsync()` rozpoczęcia rozpoznawania. Wynik to obiekt `IntentRecognitionResult` zawierający informacje o rozpoznanej intencji. Odpowiedź JSON LUIS można wyodrębnić przy użyciu następującego wyrażenia:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Aplikacja nie analizuje wyniku JSON. Wyświetla tekst JSON tylko w oknie konsoli.

![Wyniki rozpoznawania pojedynczej LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Określanie języka na potrzeby rozpoznawania

Domyślnie usługa LUIS rozpoznaje intencje w języku angielskim (Stany Zjednoczone) (`en-us`). Przypisanie kodu ustawień regionalnych do właściwości `SpeechRecognitionLanguage` konfiguracji mowy umożliwia rozpoznawanie intencji w innych językach. Na przykład Dodaj do `config.SpeechRecognitionLanguage = "de-de";` naszej aplikacji przed utworzeniem aparatu rozpoznawania, aby rozpoznawać intencje w języku niemieckim. Aby uzyskać więcej informacji, zobacz [Luis Language Support](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Ciągłe rozpoznawanie z pliku

Poniższy kod ilustruje dwie dodatkowe możliwości funkcji rozpoznawania intencji, które korzystają z zestawu SDK rozpoznawania mowy. Pierwsza z nich, wspomniana wcześniej, to rozpoznawanie ciągłe, w przypadku którego aparat rozpoznawania emituje zdarzenia, gdy wyniki są dostępne. Te zdarzenia mogą być następnie przetwarzane przez udostępnione programy obsługi zdarzeń. W przypadku ciągłego rozpoznawania należy wywołać metodę aparatu rozpoznawania, `StartContinuousRecognitionAsync()` Aby rozpocząć rozpoznawanie zamiast `RecognizeOnceAsync()` .

Inna możliwość to odczytywanie dźwięku zawierającego mowę w celu przetworzenia z pliku WAV. Implementacja obejmuje utworzenie konfiguracji audio, która może być używana podczas tworzenia aparatu rozpoznawania intencji. Plik musi być plikiem jednokanałowym (mono) z częstotliwością próbkowania wynoszącą 16 kHz.

Aby wypróbować te funkcje, Usuń lub Skomentuj treść `RecognizeIntentAsync()` metody i Dodaj do niej Poniższy kod.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Zmodyfikuj kod, aby uwzględnić identyfikator aplikacji, region i klucz punktu końcowego usługi LUIS oraz, tak jak poprzednio, dodać intencje usługi Home Automation. Zmień `whatstheweatherlike.wav` nazwę zapisanego pliku audio. Następnie Skompiluj, skopiuj plik audio do katalogu kompilacji i uruchom aplikację.

Na przykład w przypadku podania "wyłączenia sygnalizatorów", "Pause", a następnie "Włącz lampy" w zarejestrowanym pliku audio, mogą pojawić się dane wyjściowe konsoli podobne do następujących:

![Wyniki rozpoznawania LUIS pliku audio](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj kodu z tego artykułu w folderze **Samples/CSharp/sharedcontent/Console** .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: Rozpoznawanie mowy z mikrofonu](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)