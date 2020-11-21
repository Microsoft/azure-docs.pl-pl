---
title: Pojęcia dotyczące strumienia danych wejściowych audio zestawu Speech SDK
titleSuffix: Azure Cognitive Services
description: Przegląd możliwości interfejsu API wejściowego strumienia usługi Speech SDK.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.custom: devx-track-csharp
ms.openlocfilehash: 87fa97dafe9de4a23f5eaadfd4083cd1ca517cde
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026594"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Informacje o interfejsie API strumieniowego wejścia audio zestawu mowy SDK

Interfejs API **wejścia audio** zestawu mowy SDK umożliwia przesyłanie strumieniowe audio do aparatów rozpoznawania zamiast używania mikrofonu lub interfejsów API plików wejściowych.

Następujące kroki są wymagane w przypadku korzystania ze strumieni danych wejściowych audio:

- Określ format strumienia audio. Ten format musi być obsługiwany przez zestaw mowy SDK i usługę mowy. Obecnie obsługiwana jest tylko następująca konfiguracja:

  Próbki audio są w formacie PCM, jeden kanał, 16 bitów na próbkę, 8000 lub 16000 próbek na sekundę (16000 lub 32000 bajtów na sekundę), dwa bloki wyrównania (16 bitów włącznie z uzupełnieniem dla przykładu).

  Odpowiedni kod w zestawie SDK, aby utworzyć format audio wygląda następująco:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Upewnij się, że kod zawiera PIERWOTNe dane audio zgodnie z tymi specyfikacjami. Zapewnij również 16-bitowe przykłady w formacie little-endian. Są również obsługiwane przykłady ze znakiem. Jeśli dane źródłowe audio nie są zgodne z obsługiwanymi formatami, dźwięk musi być przekształcony w wymagany format.

- Utwórz własną klasę strumienia wejściowego audio pochodną `PullAudioInputStreamCallback` . Zaimplementuj elementy członkowskie `Read()` i `Close()`. Dokładna sygnatura funkcji jest zależna od języka, ale kod będzie wyglądać podobnie do tego przykładu kodu:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Utwórz konfigurację audio w oparciu o format audio i strumień wejściowy. Podczas tworzenia aparatu rozpoznawania należy przekazać zwykłą konfigurację mowy i konfigurację wejściową audio. Przykład:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Następne kroki

- [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Zobacz jak rozpoznać mowę w języku C #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)