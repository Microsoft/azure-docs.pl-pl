---
title: Koncepcje przeglądów, przepływów pracy i zadań — Content Moderator
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o podstawowych pojęciach dotyczących narzędzia do przeglądu. przeglądy, przepływy pracy i zadania.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91872055"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Przeglądy, przepływy pracy i zadania moderowania zawartości

Content Moderator łączy moderowane maszynowo z funkcjami w pętli, aby utworzyć optymalny proces moderowania dla rzeczywistych scenariuszy. Odbywa się to za pośrednictwem narzędzia do [przeglądu](https://contentmoderator.cognitive.microsoft.com)opartego na chmurze. W tym przewodniku omówiono podstawowe pojęcia narzędzia do przeglądu: przeglądy, przepływy pracy i zadania.

## <a name="reviews"></a>Przeglądy

W przeglądzie zawartość jest przekazywana do narzędzia przeglądu i pojawia się na karcie **Przegląd** . W tym miejscu użytkownicy mogą zmienić zastosowane znaczniki i zastosować własne niestandardowe znaczniki odpowiednio do potrzeb. Gdy użytkownik przesyła przegląd, wyniki są wysyłane do określonego punktu końcowego wywołania zwrotnego, a zawartość jest usuwana z lokacji.

![Przejrzyj witrynę internetową narzędzia Otwórz w przeglądarce, na karcie Przegląd](./Review-Tool-user-Guide/images/image-workflow-review.png)

Zapoznaj się z [przewodnikiem po narzędziu przeglądowym](./review-tool-user-guide/review-moderated-images.md) , aby rozpocząć tworzenie przeglądów, lub zobacz [Przewodnik po interfejsie API REST](./try-review-api-review.md) , aby dowiedzieć się, jak to zrobić programowo

## <a name="workflows"></a>Przepływy

Przepływ pracy to dostosowany do chmury filtr dla zawartości. Przepływy pracy mogą łączyć się z różnymi usługami, aby filtrować zawartość na różne sposoby, a następnie podejmować odpowiednie działania. Za pomocą łącznika Content Moderator przepływ pracy może automatycznie stosować Tagi moderowania i tworzyć przeglądy z przesłaną zawartością.

### <a name="view-workflows"></a>Wyświetl przepływy pracy

Aby wyświetlić istniejące przepływy pracy, przejdź do [narzędzia przeglądu](https://contentmoderator.cognitive.microsoft.com/) i wybierz pozycję **Ustawienia**  >  **przepływy pracy**.

![Domyślny przepływ pracy](images/default-workflow-listed.PNG)

Przepływy pracy można całkowicie opisać jako ciągi JSON, co sprawia, że są one dostępne programowo. Jeśli wybierzesz opcję **Edytuj** dla przepływu pracy, a następnie wybierzesz kartę **JSON** , zobaczysz wyrażenie JSON podobne do następujących:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Aby rozpocząć tworzenie i używanie przepływów pracy, [Zobacz przewodnik po](./review-tool-user-guide/workflows.md) [interfejsie API REST](./try-review-api-workflow.md) , aby dowiedzieć się, jak to zrobić programowo.

## <a name="jobs"></a>Stanowiska

Zadanie moderowania służy jako rodzaj otoki dla funkcji moderowania zawartości, przepływów pracy i przeglądów. Zadanie skanuje zawartość przy użyciu interfejsu API moderowania obrazu Content Moderator lub interfejsu API moderowania tekstu, a następnie sprawdza go pod kątem określonego przepływu pracy. W oparciu o wyniki przepływu pracy może to spowodować, że nie można utworzyć przeglądu zawartości w [narzędziu do przeglądu](./review-tool-user-guide/human-in-the-loop.md). Podczas gdy zarówno przeglądy, jak i przepływy pracy można tworzyć i konfigurować przy użyciu odpowiednich interfejsów API, interfejs API zadań pozwala uzyskać szczegółowy raport dotyczący całego procesu (który można wysłać do określonego punktu końcowego wywołania zwrotnego).

Zobacz [Przewodnik po interfejsie API REST](./try-review-api-job.md) , aby rozpocząć pracę z zadaniami.

## <a name="next-steps"></a>Następne kroki

* Przetestuj [konsolę interfejsu API zadania](try-review-api-job.md)i użyj przykładów kodu interfejsu API REST. Jeśli znasz program Visual Studio i C#, zapoznaj się również z [przewodnikiem Szybki Start dla programu .NET](moderation-jobs-quickstart-dotnet.md). 
* Aby zapoznać się z przeglądami, Rozpocznij pracę z [konsolą interfejsu API przeglądu](try-review-api-review.md)i Skorzystaj z przykładów kodu interfejsu API REST. Następnie zobacz sekcję przeglądy [przewodnika Szybki Start dla platformy .NET](dotnet-sdk-quickstart.md).
* Aby poznać Recenzje wideo, Skorzystaj z [przewodnika Szybki Start](video-reviews-quickstart-dotnet.md)dotyczącego przeglądu filmów wideo i Dowiedz się, jak [dodać transkrypcje do przeglądu wideo](video-transcript-reviews-quickstart-dotnet.md).
