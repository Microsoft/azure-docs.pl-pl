---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/15/2020
ms.author: trbye
ms.openlocfilehash: 7603a5aa138e719d7ffe3db7a4c4484466efc1e3
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98147342"
---
Jedną z podstawowych funkcji usługi mowy jest możliwość rozpoznawania i transkrypcja mowy (często nazywanej zamianą mowy na tekst). W tym przewodniku szybki start dowiesz się, jak używać zestawu Speech SDK w aplikacjach i produktach do wykonywania konwersji wysokiej jakości zamiany mowy na tekst.

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/recognizer) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować [zestaw Speech SDK dla języka go](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser).

## <a name="speech-to-text-from-microphone"></a>Zamiana mowy na tekst z mikrofonu

Użyj następującego przykładowego kodu, aby uruchomić rozpoznawanie mowy z domyślnego mikrofonu urządzenia. Zastąp zmienne `subscription` i `region` kluczami subskrypcji i regionu. Uruchomienie skryptu spowoduje rozpoczęcie sesji rozpoznawania na domyślnym mikrofonie i tekście wyjściowym.

```go
package main

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func sessionStartedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
}

func sessionStoppedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
}

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
    speechRecognizer.SessionStarted(sessionStartedHandler)
    speechRecognizer.SessionStopped(sessionStoppedHandler)
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Uruchom następujące polecenia, aby utworzyć plik go. mod, który łączy się ze składnikami hostowanymi w witrynie GitHub.

```cmd
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Teraz Kompiluj i uruchamiaj kod.

```cmd
go build
go run quickstart
```

Aby uzyskać szczegółowe informacje na temat klas i, zobacz dokumentację referencyjną [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechConfig) [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechRecognizer) .

## <a name="speech-to-text-from-audio-file"></a>Zamiana mowy na tekst z pliku audio

Użyj poniższego przykładu, aby uruchomić rozpoznawanie mowy z pliku dźwiękowego. Zastąp zmienne `subscription` i `region` kluczami subskrypcji i regionu. Ponadto Zastąp zmienną `file` ścieżką do pliku. wav. Uruchomienie skryptu spowoduje rozpoznanie mowy z pliku i wyjście wyniku tekstu.

```go
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"
    file := "path/to/file.wav"

    audioConfig, err := audio.NewAudioConfigFromWavFileInput(file)
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
    speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Started (ID=", event.SessionID, ")")
    })
    speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Stopped (ID=", event.SessionID, ")")
    })

    task := speechRecognizer.RecognizeOnceAsync()
    var outcome speech.SpeechRecognitionOutcome
    select {
    case outcome = <-task:
    case <-time.After(5 * time.Second):
        fmt.Println("Timed out")
        return
    }
    defer outcome.Close()
    if outcome.Error != nil {
        fmt.Println("Got an error: ", outcome.Error)
    }
    fmt.Println("Got a recognition!")
    fmt.Println(outcome.Result.Text)
}
```

Uruchom następujące polecenia, aby utworzyć plik go. mod, który łączy się ze składnikami hostowanymi w witrynie GitHub.

```cmd
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Teraz Kompiluj i uruchamiaj kod.

```cmd
go build
go run quickstart
```

Aby uzyskać szczegółowe informacje na temat klas i, zobacz dokumentację referencyjną [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechConfig) [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechRecognizer) .