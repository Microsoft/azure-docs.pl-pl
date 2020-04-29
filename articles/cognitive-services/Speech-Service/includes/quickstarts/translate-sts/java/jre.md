---
title: 'Szybki Start: Tłumaczenie mowy na mowę, Java (Windows, Linux) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 8208f6bd4e41063f514d6008166bd64ce7b1cfca
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78925362"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=jre)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Aby dodać nową pustą klasę do projektu Java, wybierz pozycję **plik** > **Nowa** > **Klasa**.

1. W oknie **Nowa klasa Java** wprowadź ciąg **speechsdk.quickstart** w polu **Pakiet** i **Main** w polu **Nazwa**.

   ![Zrzut ekranu okna Nowa klasa Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Zastąp cały kod w pliku `Main.java` następującym fragmentem kodu:

   ```Java
   package quickstart;

   import java.io.IOException;
   import java.util.concurrent.Future;
   import java.util.concurrent.ExecutionException;
   import com.microsoft.cognitiveservices.speech.*;
   import com.microsoft.cognitiveservices.speech.translation.*;

   public class Main {

       public static void translateSpeechToSpeech() throws InterruptedException, ExecutionException, IOException
       {
           // Creates an instance of a speech translation config with specified
           // subscription key and service region. Replace with your own subscription key
           // and region identifier from here: https://aka.ms/speech/sdkregion

           int exitCode = 1;
           SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey",  "YourServiceRegion");
           assert(config != null);

           // Sets source and target languages.
           String fromLanguage = "en-US";
           String toLanguage = "de";
           config.setSpeechRecognitionLanguage(fromLanguage);
           config.addTargetLanguage(toLanguage);

           // Sets the synthesis output voice name.
           // Replace with the languages of your choice, from list found here: https://aka.ms/speech/tts-languages
           config.setVoiceName("de-DE-Hedda");

           // Creates a translation recognizer using the default microphone audio input device.
           TranslationRecognizer recognizer = new TranslationRecognizer(config);
           assert(recognizer != null);

           // Prepare to handle the synthesized audio data.
           recognizer.synthesizing.addEventListener((s, e) -> {
               int size = e.getResult().getAudio().length;
               System.out.println(size != 0
                   ? "AUDIO SYNTHESIZED: " + size + " byte(s)"
                   : "AUDIO SYNTHESIZED: " + size + " byte(s) (COMPLETE)");
           });

           System.out.println("Say something...");

           // Starts translation, and returns after a single utterance is recognized. The end of a
           // single utterance is determined by listening for silence at the end or until a maximum of 15
           // seconds of audio is processed. The task returns the recognized text as well as the translation.
           // Note: Since recognizeOnceAsync() returns only a single utterance, it is suitable only for single
           // shot recognition like command or query.
           // For long-running multi-utterance recognition, use startContinuousRecognitionAsync() instead.
           Future<TranslationRecognitionResult> task = recognizer.recognizeOnceAsync();
           assert(task != null);

           TranslationRecognitionResult result = task.get();
           assert(result != null);

           if (result.getReason() == ResultReason.TranslatedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText());
               System.out.println("TRANSLATED into '" + toLanguage + "': " + result.getTranslations().get(toLanguage));
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.RecognizedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText() + "(text could not be  translated)");
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.NoMatch) {
               System.out.println("NOMATCH: Speech could not be recognized.");
           }
           else if (result.getReason() == ResultReason.Canceled) {
               CancellationDetails cancellation = CancellationDetails.fromResult(result);
               System.out.println("CANCELED: Reason=" + cancellation.getReason());

               if (cancellation.getReason() == CancellationReason.Error) {
                   System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                   System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                   System.out.println("CANCELED: Did you update the subscription info?");
               }
           }

           recognizer.close();

           System.exit(exitCode);
       }

       public static void main(String[] args) {
           try {
               translateSpeechToSpeech();
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());
               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany projektu.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz F11 lub wybierz polecenie **Uruchom** > **debugowanie**.

1. Wypowiedz zwrot lub zdanie w języku angielskim. Aplikacja przesyła mowę do usługi mowy, która tłumaczy i przekształca na tekst (w tym przypadku do języka niemieckiego). Usługa mowy wysyła następnie dźwięk, a następnie tekst z powrotem do aplikacji do wyświetlenia.

````
Say something...
AUDIO SYNTHESIZED: 76784 byte(s)
AUDIO SYNTHESIZED: 0 byte(s)(COMPLETE)
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
````

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
