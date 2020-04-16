---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400387"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do konwersji tekstu na syntetyzowaną mowę. Usługa zamiany tekstu na mowę oferuje wiele opcji syntetyzowanych głosów, w obszarze [obsługa języka zamiany tekstu na mowę.](../../../language-support.md#text-to-speech) Po spełnieniu kilku wymagań wstępnych renderowanie syntetyzowanej mowy do domyślnych głośników wymaga tylko czterech kroków:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `SpeechSynthesizer` obiekt `SpeechConfig` przy użyciu obiektu z góry.
> * Używanie `SpeechSynthesizer` obiektu do mówienia tekstu.
> * Sprawdź `SpeechSynthesisResult` zwrócone błędy.
