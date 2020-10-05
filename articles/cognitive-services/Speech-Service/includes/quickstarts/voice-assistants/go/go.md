---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 59e862fe04e7bf715e6e44c783f2cf9c0ecbc7c3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377513"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md)
> * Utwórz element bot połączony z [kanałem mowy linii bezpośredniej](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio
>
  > [!NOTE]
  > Zapoznaj się z [listą obsługiwanych regionów dla asystentów głosowych](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) i upewnij się, że zasoby zostały wdrożone w jednym z tych regionów.

## <a name="setup-your-environment"></a>Konfigurowanie środowiska

Zaktualizuj plik go. mod przy użyciu najnowszej wersji zestawu SDK, dodając ten wiersz
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego
Zastąp zawartość pliku źródłowego (np. `quickstart.go` ) poniższym, który obejmuje następujące elementy:

- Definicja pakietu "Main"
- Importowanie niezbędnych modułów z zestawu Speech SDK
- zmienne do przechowywania informacji bot, które zostaną zastąpione w dalszej części tego przewodnika Szybki Start
- prosta implementacja przy użyciu mikrofonu do wejścia audio
- programy obsługi zdarzeń dla różnych zdarzeń, które odbywają się podczas interakcji mowy

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Zastąp `YOUR_SUBSCRIPTION_KEY` wartości i wartościami `YOUR_BOT_REGION` rzeczywistymi z zasobu Speech.

- Przejdź do Azure Portal i Otwórz zasób mowy
- W obszarze **klucze i punkt końcowy** po lewej stronie są dostępne dwa klucze subskrypcji
    - Użyj jednej z nich jako `YOUR_SUBSCRIPTION_KEY` zastąpienia wartości
- W obszarze **Przegląd** po lewej stronie Zanotuj region i zamapuj go na identyfikator regionu
    - Użyj identyfikatora regionu jako `YOUR_BOT_REGION` zastąpienia wartości, na przykład: dla regionu `"westus"` **zachodnie stany USA**

   > [!NOTE]
   > Zapoznaj się z [listą obsługiwanych regionów dla asystentów głosowych](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) i upewnij się, że zasoby zostały wdrożone w jednym z tych regionów.

   > [!NOTE]
   > Aby uzyskać informacje na temat konfigurowania bot, zobacz dokumentację platformy bot dla [kanału Direct line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Wyjaśnienie kodu
Klucz subskrypcji mowy i region są wymagane do utworzenia obiektu konfiguracji mowy. Obiekt konfiguracji jest wymagany do utworzenia wystąpienia obiektu aparatu rozpoznawania mowy.

Wystąpienie aparatu rozpoznawania udostępnia wiele metod rozpoznawania mowy. W tym przykładzie rozpoznawanie mowy jest ciągle rozpoznawane. Ta funkcja pozwala usłudze mowy wiedzieć, że wysyłasz wiele fraz do rozpoznawania, i kiedy program kończy działanie w celu zatrzymania rozpoznawania mowy. Gdy zostaną uzyskane wyniki, kod zapisze je w konsoli programu.

## <a name="build-and-run"></a>Kompilowanie i uruchamianie
Jesteś teraz gotowy do kompilowania projektu i testowania niestandardowego asystenta głosowego przy użyciu usługi mowy.
1. Kompiluj projekt, np. **"przejdź do kompilacji"**
2. Uruchom moduł i zacznij mówić do mikrofonu na urządzeniu. Twoja Zamiana jest przesyłana do kanału mowy Direct line i uzyskanego do tekstu, który jest wyświetlany jako dane wyjściowe.


> [!NOTE]
> Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
