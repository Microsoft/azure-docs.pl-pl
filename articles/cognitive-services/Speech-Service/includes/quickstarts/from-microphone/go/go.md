---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: a18ce7b7c28b99967668bc33c5a94cbb58bfbc34
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376494"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Konfigurowanie środowiska deweloperskiego i Tworzenie pustego projektu](../../../../quickstarts/setup-platform.md)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

## <a name="setup-your-environment"></a>Konfigurowanie środowiska

Zaktualizuj plik go. mod przy użyciu najnowszej wersji zestawu SDK, dodając ten wiersz
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego
1. Zastąp zawartość pliku źródłowego (np. `sr-quickstart.go` ) poniższym, który obejmuje następujące elementy:

- Definicja pakietu "Main"
- Importowanie niezbędnych modułów z zestawu Speech SDK
- zmienne do przechowywania informacji o subskrypcji, które zostaną zastąpione w dalszej części tego przewodnika Szybki Start
- prosta implementacja przy użyciu mikrofonu do wejścia audio
- programy obsługi zdarzeń dla różnych zdarzeń, które mają miejsce podczas rozpoznawania mowy

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Zastąp `YOUR_SUBSCRIPTION_KEY` wartości i wartościami `YOUR_SUBSCRIPTIONKEY_REGION` rzeczywistymi z zasobu Speech.

- Przejdź do Azure Portal i Otwórz zasób mowy
- W obszarze **klucze** po lewej stronie są dostępne dwa klucze subskrypcji
    - Użyj jednej z nich jako `YOUR_SUBSCRIPTION_KEY` zastąpienia wartości
- W obszarze **Przegląd** po lewej stronie Zanotuj region i zamapuj go na identyfikator regionu
- Użyj identyfikatora regionu jako `YOUR_SUBSCRIPTIONKEY_REGION` zastąpienia wartości, na przykład: dla regionu `"westus"` **zachodnie stany USA**

## <a name="code-explanation"></a>Wyjaśnienie kodu
Klucz subskrypcji mowy i region są wymagane do utworzenia obiektu konfiguracji mowy. Obiekt konfiguracji jest wymagany do utworzenia wystąpienia obiektu aparatu rozpoznawania mowy.

Wystąpienie aparatu rozpoznawania udostępnia wiele metod rozpoznawania mowy. W tym przykładzie rozpoznawanie mowy jest ciągle rozpoznawane. Ta funkcja pozwala usłudze mowy wiedzieć, że wysyłasz wiele fraz do rozpoznawania, i kiedy program kończy działanie w celu zatrzymania rozpoznawania mowy. Gdy zostaną uzyskane wyniki, kod zapisze je w konsoli programu.

## <a name="build-and-run"></a>Kompilowanie i uruchamianie
Teraz skonfigurujesz Tworzenie projektu i testowanie rozpoznawania mowy przy użyciu usługi Speech.
1. Kompiluj projekt, np. **"przejdź do kompilacji"**
2. Uruchom moduł i zacznij mówić do mikrofonu na urządzeniu. Twoja Zamiana jest przekazywana do usługi mowy i uzyskanego do tekstu, który pojawia się w danych wyjściowych.


> [!NOTE]
> Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)


## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
