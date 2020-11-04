---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305891"
---
Najpierw załaduj plik modelu słowa kluczowego przy użyciu `FromFile()` funkcji statycznej, która zwraca `KeywordRecognitionModel` . Użyj ścieżki do `.table` pliku pobranego z programu Speech Studio. Ponadto tworzysz `AudioConfig` przy użyciu domyślnego mikrofonu, a następnie tworzymy nowe wystąpienie `KeywordRecognizer` przy użyciu konfiguracji audio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Następnie uruchamianie rozpoznawania słów kluczowych odbywa się przy użyciu jednego wywołania do `RecognizeOnceAsync()` przez przekazanie obiektu modelu. Spowoduje to uruchomienie sesji rozpoznawania słowa kluczowego, która obowiązuje do momentu rozpoznania słowa kluczowego. Z tego względu Wzorzec projektowy jest używany w aplikacjach wielowątkowych lub w przypadkach użycia, w których można oczekiwać na zakończenie wyciągania.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> W poniższym przykładzie zastosowano funkcję lokalnego rozpoznawania słów kluczowych, ponieważ nie wymaga ona `SpeechConfig` obiektu dla kontekstu uwierzytelniania i nie kontaktuje się z zapleczem. Można jednak uruchomić rozpoznawanie słów kluczowych i weryfikację przy [użyciu ciągłego połączenia zaplecza](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).