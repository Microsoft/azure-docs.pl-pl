---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052879"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) i usługi Language UNDERSTANDING (Luis) do rozpoznawania intencji z danych audio przechwytywanych z mikrofonu. W tym celu należy użyć zestawu Speech SDK do przechwytywania mowy i prekompilowanej domeny z LUIS, aby identyfikować intencje dla automatyzacji domowej, takie jak Włączanie i wyłączanie oświetlenia. 

Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy i identyfikowanie intencji z mikrofonu trwa tylko kilka kroków:

> [!div class="checklist"]
>
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `IntentRecognizer` Obiekt przy użyciu `SpeechConfig` obiektu powyższego.
> * Za pomocą `IntentRecognizer` obiektu, Rozpocznij proces rozpoznawania dla pojedynczego wypowiedź.
> * Sprawdź `IntentRecognitionResult` zwracaną wartość.

