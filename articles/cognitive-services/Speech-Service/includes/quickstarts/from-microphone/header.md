---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400745"
---
W tym przewodniku Szybki start można użyć [zestawu SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do interaktywnego rozpoznawania mowy z wejścia mikrofonu i uzyskania transkrypcji tekstu z przechwyconego dźwięku. Łatwo jest zintegrować tę funkcję z aplikacjami lub urządzeniami w celu wykonywania typowych zadań rozpoznawania, takich jak transkrypcja konwersacji. Może być również używany do bardziej złożonych integracji, takich jak za pomocą bot framework z SDK mowy do tworzenia asystentów głosowych.

Po spełnieniu kilku wymagań wstępnych rozpoznawanie mowy z mikrofonu wymaga tylko czterech kroków:

> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `SpeechRecognizer` obiekt `SpeechConfig` przy użyciu obiektu z góry.
> * Za `SpeechRecognizer` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź `SpeechRecognitionResult` zwrócony.
