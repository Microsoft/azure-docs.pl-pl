---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422068"
---
W tym przewodniku Szybki start użyjesz [zestawu SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) i usługi rozumienie języka (LUIS) do rozpoznawania intencji z danych audio przechwyconych z mikrofonu. W szczególności użyjesz zestawu SDK mowy do przechwytywania mowy, a wstępnie utworzonej domeny z usługi LUIS do identyfikowania intencji automatyzacji domowej, takich jak włączanie i wyłączanie światła. 

Po spełnieniu kilku wymagań wstępnych rozpoznawanie mowy i identyfikowanie intencji z mikrofonu wymaga tylko kilku kroków:

> [!div class="checklist"]
>
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `IntentRecognizer` obiekt `SpeechConfig` przy użyciu obiektu z góry.
> * Za `IntentRecognizer` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź `IntentRecognitionResult` zwrócony.
