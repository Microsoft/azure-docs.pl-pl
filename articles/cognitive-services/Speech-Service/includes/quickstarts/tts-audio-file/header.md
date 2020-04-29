---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400330"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na polecenie "Speech" w pliku audio. Usługa zamiany tekstu na mowę oferuje liczne opcje dla wydawanych głosów, w ramach [pomocy technicznej języka zamiany tekstu na mowę](../../../language-support.md#text-to-speech). Po spełnieniu kilku wymagań wstępnych, synteza mowy do pliku obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz obiekt konfiguracji audio, który określa. Nazwa pliku WAV.
> * Utwórz `SpeechSynthesizer` Obiekt przy użyciu obiektów konfiguracji z powyższych.
> * Używając `SpeechSynthesizer` obiektu, przekonwertuj swój tekst na mowę, zapisując go w określonym pliku audio.
> * Zbadaj `SpeechSynthesizer` zwrócone błędy.
