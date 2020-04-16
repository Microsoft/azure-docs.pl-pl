---
title: 'Jak rozpoznać intencje z mowy przy użyciu SDK mowy C #'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku dowiesz się, jak rozpoznać intencje z mowy przy użyciu SDK mowy dla języka C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: 41ebcb7b44ea88af06a30a611960fd8bb0ceddee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402219"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Jak rozpoznać intencje z mowy przy użyciu SDK mowy dla języka C #

Pakiet [SDK mowy](speech-sdk.md) usług Cognitive Services integruje się z [usługą rozpoznawania języka (LUIS)](https://www.luis.ai/home) w celu **zapewnienia rozpoznawania intencji.** Intencja to coś, co użytkownik chce zrobić: zarezerwować lot, sprawdzić prognozę pogody lub zadzwonić. Następnie może on użyć terminów, które wydają się naturalne. Korzystając z uczenia maszynowego usługa LUIS mapuje żądania użytkowników do intencji zdefiniowanych.

> [!NOTE]
> Aplikacja usługi LUIS definiuje intencje i jednostki, które chcesz rozpoznawać. Jest ona używana oddzielnie od aplikacji języka C#, która korzysta z usługi rozpoznawania mowy. W tym artykule „aplikacja” oznacza aplikację usługi LUIS, a „zastosowanie” oznacza kod języka C#.

W tym przewodniku można użyć zestawu SDK mowy do opracowania aplikacji konsoli Języka C#, która wywodzi intencje z wypowiedzi użytkownika za pośrednictwem mikrofonu urządzenia. Omawiane tematy:

> [!div class="checklist"]
>
> - Tworzenie projektu programu Visual Studio odwołującego się do pakietu NuGet zestawu Speech SDK
> - Tworzenie konfiguracji mowy i uzyskanie aparatu rozpoznawania intencji
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

Usługa LUIS używa trzech rodzajów kluczy:

| Typ klucza  | Przeznaczenie                                               |
| --------- | ----------------------------------------------------- |
| Tworzenie | Umożliwia programowe tworzenie i modyfikowanie aplikacji usługi LUIS |
| Starter (początkowy)   | Umożliwia testowanie aplikacji usługi LUIS przy użyciu tylko tekstu   |
| Endpoint  | Autoryzuje dostęp do określonej aplikacji usługi LUIS            |

W tym przewodniku potrzebny jest typ klucza punktu końcowego. W tym przewodniku użyto przykładowej aplikacji usługi LUIS automatyzacji domowej, którą można utworzyć, wykonując przewodnik Szybki start [aplikacji Użyj wstępnie utworzonej aplikacji automatyki](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) domowej. Jeśli utworzono własną aplikację usługi LUIS, możesz jej użyć.

Podczas tworzenia aplikacji usługi LUIS usługa LUIS automatycznie generuje klucz startowy, dzięki czemu można przetestować aplikację przy użyciu zapytań tekstowych. Ten klucz nie włącza integracji usługi mowy i nie będzie działać z tym przewodnikiem. Utwórz zasób usługi LUIS na pulpicie nawigacyjnym platformy Azure i przypisz go do aplikacji usługi LUIS. W tym przewodniku można skorzystać z bezpłatnej warstwy subskrypcji.

Po utworzeniu zasobu usługi LUIS na pulpicie nawigacyjnym platformy Azure zaloguj się do [portalu usługi LUIS](https://www.luis.ai/home), wybierz aplikację na stronie Moje **aplikacje,** a następnie przełącz się do strony **Zarządzanie aplikacją.** Na koniec wybierz **klawisze i punkty końcowe** na pasku bocznym.

![Ustawienia kluczy i punktów końcowych usługi LUIS](media/sdk/luis-keys-endpoints-page.png)

Na stronie **Ustawienia kluczy i punktu końcowego:**

1. Przewiń w dół do sekcji **Zasoby i klucze** i wybierz pozycję **Przypisz zasób**.
1. W oknie **dialogowym Przypisywanie klucza do aplikacji** wykonuj następujące zmiany:

   - W obszarze **Dzierżawa**wybierz pozycję **Microsoft**.
   - W obszarze **Nazwa subskrypcji**wybierz subskrypcję platformy Azure zawierającą zasób usługi LUIS, którego chcesz użyć.
   - W obszarze **Klucz**wybierz zasób usługi LUIS, którego chcesz użyć z aplikacją.

   Za chwilę w tabeli w dolnej części strony zostanie wyświetlona nowa subskrypcja.

1. Wybierz ikonę obok klawisza, aby skopiować ją do schowka. (Możesz użyć dowolnego klucza).

![Klucze subskrypcji aplikacji usługi LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Tworzenie projektu rozpoznawania mowy w programie Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Dodawanie kodu

Następnie należy dodać kod do projektu.

1. W **Eksploratorze rozwiązań**otwórz plik **Program.cs**.

1. Zastąp `using` blok instrukcji na początku pliku następującymi deklaracjami:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Zastąp podaną `Main()` metodę następującym ekwiwalentem asynchronicznem:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Utwórz pustą metodę asynchronizacjową, `RecognizeIntentAsync()`jak pokazano poniżej:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. W treści tej nowej metody dodaj ten kod:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Zastąp symbole zastępcze w tej metodzie kluczem subskrypcji, regionem i identyfikatorem aplikacji usługi LUIS w pokazany poniżej sposób.

   | Symbol zastępczy | Zamień na |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Klucz punktu końcowego usługi LUIS. Ponownie należy uzyskać ten element z pulpitu nawigacyjnego platformy Azure, a nie "klucz startowy". Można go znaleźć na stronie **Klucze i punkty końcowe** aplikacji (w obszarze **Zarządzanie)** w [portalu usługi LUIS](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Krótki identyfikator regionu, w którym znajduje się Twoja subskrypcja usługi LUIS, na przykład `westus` dla regionu Zachodnie stany USA. Zobacz [Regions](regions.md) (Regiony). |
   | `YourLanguageUnderstandingAppId` | Identyfikator aplikacji usługi LUIS. Można go znaleźć na stronie **Ustawienia** aplikacji w [portalu usługi LUIS](https://www.luis.ai/home). |

Po tych wprowadzonych zmianach można tworzyć **(Control+Shift+B**) i uruchamiać (**F5**) aplikację. Gdy pojawi się monit, spróbuj w mikrofonie komputera wyświadczyć "Wyłącz światła". Aplikacja wyświetla wynik w oknie konsoli.

Poniższe sekcje zawierają omówienie kodu.

## <a name="create-an-intent-recognizer"></a>Tworzenie aparatu rozpoznawania intencji

Najpierw należy utworzyć konfigurację mowy z klucza końcowego usługi LUIS i regionu. Konfiguracje mowy można używać do tworzenia aparatów rozpoznawania dla różnych możliwości SDK mowy. Konfiguracja mowy ma wiele sposobów, aby określić subskrypcję, której chcesz użyć; tutaj używamy `FromSubscription`, który ma klucz subskrypcji i regionu.

> [!NOTE]
> Użyj klucza i regionu subskrypcji usługi LUIS, a nie subskrypcji usługi mowy.

Następnie utwórz aparat rozpoznawania mowy przy użyciu elementu `new IntentRecognizer(config)`. Ponieważ konfiguracja już wie, która subskrypcja do użycia, nie trzeba określić klucz subskrypcji i punktu końcowego ponownie podczas tworzenia aparatu rozpoznawania.

## <a name="import-a-luis-model-and-add-intents"></a>Importowanie modelu usługi LUIS i dodawanie intencji

Teraz zaimportuj model z aplikacji usługi LUIS przy użyciu elementu `LanguageUnderstandingModel.FromAppId()` i dodaj intencje usługi LUIS, które chcesz rozpoznawać za pośrednictwem metody `AddIntent()` aparatu rozpoznawania. Te dwa kroki zwiększają dokładność rozpoznawania mowy, wskazując słowa, których użytkownik prawdopodobnie użyje w swoich żądaniach. Nie musisz dodawać wszystkich intencji aplikacji, jeśli nie musisz rozpoznawać ich wszystkich w aplikacji.

Aby dodać intencje, należy podać trzy argumenty: model usługi LUIS `model`(który został utworzony i nosi nazwę), nazwę intencji i identyfikator intencji. Różnica między identyfikatorem i nazwą jest następująca.

| `AddIntent()`&nbsp;Argument | Przeznaczenie |
| --------------------------- | ------- |
| `intentName` | Nazwa intencji zgodnie z definicją w aplikacji usługi LUIS. Ta wartość musi dokładnie odpowiadać nazwie intencji usługi LUIS. |
| `intentID` | Identyfikator przypisany do rozpoznanej intencji przez zestaw SDK rozpoznawania mowy. Ta wartość może być, co chcesz; nie musi odpowiadać nazwie intencji zdefiniowanej w aplikacji usługi LUIS. Jeśli na przykład ten sam kod obsługuje wiele intencji, możesz dla nich użyć tego samego identyfikatora. |

Aplikacja usługi HOME Automation LUIS ma dwie intencje: jeden do włączania urządzenia, a drugi do wyłączania urządzenia. Poniższe wiersze powodują dodanie tych intencji do aparatu rozpoznawania; zastąp trzy wiersze `AddIntent` w metodzie `RecognizeIntentAsync()` tym kodem.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Zamiast dodawać poszczególne intencje, można `AddAllIntents` również użyć metody, aby dodać wszystkie intencje w modelu do aparatu rozpoznawania.

## <a name="start-recognition"></a>Uruchamianie rozpoznawania

Po utworzeniu aparatu rozpoznawania i dodaniu intencji można rozpocząć rozpoznawanie. Zestaw Speech SDK obsługuje rozpoznawanie pojedyncze i ciągłe.

| Tryb rozpoznawania | Metody do wywołania | Wynik |
| ---------------- | --------------- | ------ |
| Pojedyncze | `RecognizeOnceAsync()` | Zwraca rozpoznaną intencję, jeśli istnieje, po jednej wypowiedzi. |
| Ciągłe | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Rozpoznaje wiele wypowiedzi; emituje zdarzenia (na `IntermediateResultReceived`przykład), gdy wyniki są dostępne. |

Aplikacja używa trybu pojedynczego strzału `RecognizeOnceAsync()` i tak wywołuje, aby rozpocząć rozpoznawanie. Wynik to obiekt `IntentRecognitionResult` zawierający informacje o rozpoznanej intencji. Odpowiedź usługi LUIS JSON można wyodrębnić przy użyciu następującego wyrażenia:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Aplikacja nie analizuje wyniku JSON. Wyświetla tylko tekst JSON w oknie konsoli.

![Pojedyncze wyniki rozpoznawania usługi LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Określanie języka na potrzeby rozpoznawania

Domyślnie usługa LUIS rozpoznaje intencje w języku angielskim (Stany Zjednoczone) (`en-us`). Przypisanie kodu ustawień regionalnych do właściwości `SpeechRecognitionLanguage` konfiguracji mowy umożliwia rozpoznawanie intencji w innych językach. Na przykład `config.SpeechRecognitionLanguage = "de-de";` dodaj w naszej aplikacji przed utworzeniem aparat rozpoznawania rozpoznać intencje w języku niemieckim. Aby uzyskać więcej informacji, zobacz [obsługa języka usługi LUIS](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Ciągłe rozpoznawanie z pliku

Poniższy kod ilustruje dwie dodatkowe możliwości funkcji rozpoznawania intencji, które korzystają z zestawu SDK rozpoznawania mowy. Pierwsza z nich, wspomniana wcześniej, to rozpoznawanie ciągłe, w przypadku którego aparat rozpoznawania emituje zdarzenia, gdy wyniki są dostępne. Te zdarzenia mogą być następnie przetwarzane przez udostępnione programy obsługi zdarzeń. Z ciągłym rozpoznawaniem, można `StartContinuousRecognitionAsync()` wywołać metodę aparatu `RecognizeOnceAsync()`rozpoznawania, aby rozpocząć rozpoznawanie zamiast .

Inna możliwość to odczytywanie dźwięku zawierającego mowę w celu przetworzenia z pliku WAV. Implementacja polega na utworzeniu konfiguracji audio, który może służyć podczas tworzenia aparat rozpoznawania intencji. Plik musi być plikiem jednokanałowym (mono) z częstotliwością próbkowania wynoszącą 16 kHz.

Aby wypróbować te funkcje, usuń lub `RecognizeIntentAsync()` skomentuj treść metody i dodaj w jego miejsce następujący kod.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Zmodyfikuj kod, aby uwzględnić identyfikator aplikacji, region i klucz punktu końcowego usługi LUIS oraz, tak jak poprzednio, dodać intencje usługi Home Automation. Zmień `whatstheweatherlike.wav` nazwę zarejestrowanego pliku audio. Następnie skompiluj, skopiuj plik audio do katalogu kompilacji i uruchom aplikację.

Jeśli na przykład powiesz "Wyłącz światła", wstrzymaj, a następnie powiedz "Włącz światła" w nagranym pliku audio, może pojawić się wyjście konsoli podobne do następującego:

![Wyniki rozpoznawania usługi LUIS w pliku audio](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj kodu z tego artykułu w folderze **samples/csharp/sharedcontent/console.**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki start: rozpoznawanie mowy z mikrofonu](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
