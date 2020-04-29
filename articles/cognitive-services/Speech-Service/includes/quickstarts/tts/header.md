---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400387"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do przekonwertowania tekstu na mowę. Usługa zamiany tekstu na mowę oferuje liczne opcje dla wydawanych głosów, w ramach [pomocy technicznej języka zamiany tekstu na mowę](../../../language-support.md#text-to-speech). Po spełnieniu kilku wymagań wstępnych, renderowanie mowy do domyślnych głośników obejmuje tylko cztery kroki:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `SpeechSynthesizer` Obiekt przy użyciu `SpeechConfig` obiektu powyższego.
> * Używanie `SpeechSynthesizer` obiektu do mówienia tekstu.
> * Sprawdź `SpeechSynthesisResult` zwrócone błędy.
