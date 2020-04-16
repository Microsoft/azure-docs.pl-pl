---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400330"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do konwersji tekstu na syntetyzowaną mowę w pliku audio. Usługa zamiany tekstu na mowę oferuje wiele opcji syntetyzowanych głosów, w obszarze [obsługa języka zamiany tekstu na mowę.](../../../language-support.md#text-to-speech) Po spełnieniu kilku wymagań wstępnych syntetyzowanie mowy w pliku wykonuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz obiekt Konfiguracja audio, który określa obiekt . Nazwa pliku WAV.
> * Utwórz `SpeechSynthesizer` obiekt przy użyciu obiektów konfiguracyjnych z góry.
> * Za `SpeechSynthesizer` pomocą obiektu przekonwertuj tekst na syntetyzowaną mowę, zapisując go w określonym pliku audio.
> * Sprawdź `SpeechSynthesizer` zwrócone błędy.
