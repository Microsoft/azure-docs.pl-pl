---
title: 'Szybki Start: synteza mowy, Java (Android) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy w języku Java w systemie Android przy użyciu zestawu Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yulili
ms.openlocfilehash: 8f3a6c808cec28d3c2184bfd99aa2f5b1f6f88a3
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86226441"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)

## <a name="create-user-interface"></a>Tworzenie interfejsu użytkownika

Utworzymy podstawowy interfejs użytkownika dla aplikacji. Edytuj układ dla głównego działania, `activity_main.xml`. Początkowo układ zawiera pasek tytułu z nazwą aplikacji i element TextView zawierający tekst „Hello World!”.

1. Kliknij element TextView. Zmień jego atrybut ID w prawym górnym rogu `outputMessage` , a następnie przeciągnij go do dolnego ekranu. Usuń jego tekst.

1. Z palety w lewym górnym rogu okna `activity_main.xml` przeciągnij przycisk do pustego miejsca nad tekstem.

1. W atrybutach przycisku po prawej stronie w polu wartości atrybutu `onClick` wprowadź `onSpeechButtonClicked`. Napiszemy metodę o tej nazwie do obsługi zdarzenia przycisku.  Zmień atrybut ID w prawym górnym rogu na `button`.

1. Przeciągnij zwykły tekst do obszaru powyżej przycisku; Zmień jego atrybut ID na `speakText` , a następnie Zmień atrybut tekstu na `Hi there!` .

1. Użyj ikony różdżki u góry projektanta na potrzeby wnioskowania ograniczeń układu.


    ![Zrzut ekranu przedstawiający ikonę różdżki](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Tekst i graficzna reprezentacja interfejsu użytkownika powinny teraz wyglądać następująco:

![Zrzut ekranu przedstawiający sposób, w jaki interfejs użytkownika powinien wyglądać.](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy `MainActivity.java`. Zastąp cały zawarty w tym pliku kod poniższym kodem.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onSpeechButtonClicked` oznacza, jak wspomniano wcześniej, procedurę obsługi naciskania przycisku. Naciśnięcie przycisku uruchamia funkcję synteza mowy.

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp także ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-and-run-the-app"></a>Skompiluj i uruchom aplikację

1. Połącz urządzenie z systemem Android do komputera projektowego. Upewnij się, że na urządzeniu włączono [tryb projektowania i debugowanie USB](https://developer.android.com/studio/debug/dev-options). Alternatywnie możesz utworzyć [emulator systemu Android](https://developer.android.com/studio/run/emulator).

1. Aby skompilować aplikację, naciśnij klawisze CTRL + F9 lub wybierz opcję **Kompiluj**  >  **Utwórz projekt** z paska menu.

1. Aby uruchomić aplikację, naciśnij klawisze Shift + F10 **lub wybierz polecenie Uruchom**  >  **przebieg "App"**.

1. W wyświetlonym oknie cel wdrożenia wybierz urządzenie z systemem Android lub emulator.

   ![Zrzut ekranu okna Deployment Target (Cel wdrożenia)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Wprowadź tekst i naciśnij przycisk w aplikacji, aby rozpocząć sekcję synteza mowy. Słychać dźwięk z głośników domyślnego i zobaczysz `speech synthesis succeeded` informacje na ekranie.

![Zrzut ekranu aplikacji Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
