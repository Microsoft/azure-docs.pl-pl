---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a752b723980c45e1488f1471ee8139190853b54c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97966566"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) i usługi Language UNDERSTANDING (Luis) do rozpoznawania intencji z danych audio przechwytywanych z mikrofonu. W tym celu należy użyć zestawu Speech SDK do przechwytywania mowy i prekompilowanej domeny z LUIS, aby identyfikować intencje dla automatyzacji domowej, takie jak Włączanie i wyłączanie oświetlenia. 

Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy i identyfikowanie intencji z mikrofonu trwa tylko kilka kroków:

> [!div class="checklist"]
>
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `IntentRecognizer` Obiekt przy użyciu `SpeechConfig` obiektu powyższego.
> * Za pomocą `IntentRecognizer` obiektu, Rozpocznij proces rozpoznawania dla pojedynczego wypowiedź.
> * Sprawdź `IntentRecognitionResult` zwracaną wartość.

> [!NOTE]
> Można utworzyć LanguageUnderstandingModel, przekazując adres URL punktu końcowego do metody FromEndpoint.
> Zestaw Speech SDK obsługuje tylko punkty końcowe LUIS v 2.0, a punkty końcowe LUIS v 2.0 zawsze obserwują jeden z tych dwóch wzorców:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
