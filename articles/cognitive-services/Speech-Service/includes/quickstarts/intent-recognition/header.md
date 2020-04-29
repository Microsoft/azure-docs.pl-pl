---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422068"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) i usługi Language UNDERSTANDING (Luis) do rozpoznawania intencji z danych audio przechwytywanych z mikrofonu. W tym celu należy użyć zestawu Speech SDK do przechwytywania mowy i prekompilowanej domeny z LUIS, aby identyfikować intencje dla automatyzacji domowej, takie jak Włączanie i wyłączanie oświetlenia. 

Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy i identyfikowanie intencji z mikrofonu trwa tylko kilka kroków:

> [!div class="checklist"]
>
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `IntentRecognizer` Obiekt przy użyciu `SpeechConfig` obiektu powyższego.
> * Za pomocą `IntentRecognizer` obiektu, Rozpocznij proces rozpoznawania dla pojedynczego wypowiedź.
> * Sprawdź zwracaną wartość `IntentRecognitionResult` .
