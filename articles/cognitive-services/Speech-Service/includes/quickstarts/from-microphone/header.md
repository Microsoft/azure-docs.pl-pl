---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400745"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie rozpoznać mowę przy użyciu danych wejściowych mikrofonu i uzyskać transkrypcję tekstu z przechwyconego dźwięku. Tę funkcję można łatwo zintegrować z aplikacjami lub urządzeniami w celu wykonywania typowych zadań rozpoznawania, takich jak Konwersacje jego przepisywania. Można go również użyć w celu uzyskania bardziej złożonej integracji, na przykład przy użyciu platformy bot z zestawem SDK mowy do tworzenia asystentów głosowych.

Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z mikrofonu obejmuje tylko cztery kroki:

> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `SpeechRecognizer` Obiekt przy użyciu `SpeechConfig` obiektu powyższego.
> * Za pomocą `SpeechRecognizer` obiektu, Rozpocznij proces rozpoznawania dla pojedynczego wypowiedź.
> * Sprawdź zwracaną wartość `SpeechRecognitionResult` .
