---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, Java (Android)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku Java w systemie Android przy użyciu zestawu Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 3a3799fc1e931993c00ba497765f4cd3e60d3493
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376531"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

## <a name="create-a-user-interface"></a>Tworzenie interfejsu użytkownika

Teraz utworzysz podstawowy interfejs użytkownika dla aplikacji. Edytuj układ dla głównego działania, `activity_main.xml`. Początkowo układ obejmuje pasek tytułu z nazwą aplikacji oraz TextView, który zawiera tekst "Hello world!".

* Wybierz element TextView. Zmień atrybut ID w prawym górnym rogu na `hello`.

* Z palety w lewym górnym rogu `activity_main.xml` okna przeciągnij przycisk do pustego miejsca powyżej tekstu.

* W atrybutach przycisku po prawej stronie w polu wartości atrybutu `onClick` wprowadź `onSpeechButtonClicked`. Napiszemy metodę o tej nazwie do obsługi zdarzenia przycisku. Zmień atrybut ID w prawym górnym rogu na `button`.

* Użyj ikony różdżki u góry projektanta na potrzeby wnioskowania ograniczeń układu.

  ![Zrzut ekranu przedstawiający ikonę różdżki](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Tekst i graficzna reprezentacja interfejsu użytkownika powinny teraz wyglądać następująco:

![Interfejs użytkownika](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy `MainActivity.java`. Zastąp cały kod w tym pliku następującym:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onCreate` zawiera kod, który żąda uprawnień do mikrofonu i Internetu oraz inicjuje powiązanie z platformą natywną. Konfigurowanie powiązań z platformą natywną jest wymagane tylko raz. Należy to zrobić na początku, podczas inicjowania aplikacji.

   * Metoda `onSpeechButtonClicked` oznacza, jak wspomniano wcześniej, procedurę obsługi naciskania przycisku. Naciśnięcie przycisku powoduje wyzwolenie transkrypcji zamiany mowy na tekst.

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp również ciąg `YourServiceRegion` **identyfikatorem regionu** z [regionu](https://aka.ms/speech/sdkregion) skojarzonego z subskrypcją.

## <a name="build-and-run-the-app"></a>Skompiluj i uruchom aplikację

1. Połącz urządzenie z systemem Android do komputera projektowego. Upewnij się, że włączono [tryb programowania i debugowanie USB](https://developer.android.com/studio/debug/dev-options) na urządzeniu.

1. Aby skompilować aplikację, wybierz kombinację klawiszy CTRL + F9 lub wybierz opcję **Kompiluj**  >  **Utwórz projekt** z paska menu.

1. Aby uruchomić aplikację, wybierz polecenie SHIFT + F10 lub wybierz polecenie **Uruchom**  >  **przebieg "App"**.

1. W wyświetlonym oknie cel wdrożenia wybierz urządzenie z systemem Android.

   ![Zrzut ekranu okna Deployment Target (Cel wdrożenia)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Wybierz przycisk w aplikacji, aby rozpocząć sekcję rozpoznawania mowy. Kolejne 15 sekund mowy w języku angielskim zostanie wysłanych do usługi rozpoznawania mowy i poddanych transkrypcji. Wynik zostanie wyświetlony w aplikacji Android i w oknie programu logcat w programie Android Studio.

![Zrzut ekranu aplikacji Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
