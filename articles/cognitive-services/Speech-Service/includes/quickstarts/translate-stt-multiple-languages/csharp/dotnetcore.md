---
title: 'Szybki Start: Tłumaczenie mowy na wiele języków, C# (.NET Core Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: erhopf
ms.openlocfilehash: bc1805202696fda2d57374e08e269045e1fe044c
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226375"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=dotnetcore&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz **program.cs**i Zastąp cały kod następującym kodem.

   ```Csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Translation;

   namespace helloworld
   {
       class Program
       {
           public static async Task TranslateSpeechToText()
           {
               // Creates an instance of a speech translation config with specified subscription key and service region.
               // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
               var config = SpeechTranslationConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

               // Sets source and target languages.
               // Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
               string fromLanguage = "en-US";
               config.SpeechRecognitionLanguage = fromLanguage;
               config.AddTargetLanguage("de");
               config.AddTargetLanguage("fr");

               // Creates a translation recognizer using the default microphone audio input device.
               using (var recognizer = new TranslationRecognizer(config))
               {
                   // Starts translation, and returns after a single utterance is recognized. The end of a
                   // single utterance is determined by listening for silence at the end or until a maximum of 15
                   // seconds of audio is processed. The task returns the recognized text as well as the translation.
                   // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
                   // shot recognition like command or query.
                   // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
                   Console.WriteLine("Say something...");
                   var result = await recognizer.RecognizeOnceAsync();

                   // Checks result.
                   if (result.Reason == ResultReason.TranslatedSpeech)
                   {
                       Console.WriteLine($"RECOGNIZED '{fromLanguage}': {result.Text}");
                       foreach (var element in result.Translations)
                       {
                           Console.WriteLine($"TRANSLATED into '{element.Key}': {element.Value}");
                       }
                   }
                   else if (result.Reason == ResultReason.RecognizedSpeech)
                   {
                       Console.WriteLine($"RECOGNIZED '{fromLanguage}': {result.Text} (text could not be translated)");
                   }
                   else if (result.Reason == ResultReason.NoMatch)
                   {
                       Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                   }
                   else if (result.Reason == ResultReason.Canceled)
                   {
                       var cancellation = CancellationDetails.FromResult(result);
                       Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                       if (cancellation.Reason == CancellationReason.Error)
                       {
                           Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                           Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                           Console.WriteLine($"CANCELED: Did you update the subscription info?");
                       }
                   }
               }
           }

           static void Main(string[] args)
           {
               TranslateSpeechToText().Wait();
           }
       }
   }
   ```

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zamień ciąg na `YourServiceRegion` [region](~/articles/cognitive-services/Speech-Service/regions.md) skojarzony z subskrypcją.

1. Na pasku menu wybierz kolejno opcje **plik**  >  **Zapisz wszystko**.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **Kompiluj**kompilacje  >  **rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **Debuguj**  >  **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Wypowiedz zwrot lub zdanie w języku angielskim. Aplikacja przesyła mowę do usługi mowy, która tłumaczy i przekształca na tekst (w tym przypadku w języku francuskim i niemieckim). Usługa mowy wysyła następnie tekst z powrotem do aplikacji do wyświetlenia.

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
TRANSLATED into 'fr': Quel temps fait-il à Seattle ?
````

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
