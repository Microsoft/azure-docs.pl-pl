---
title: Używanie zadań moderowania z konsolą interfejsu API REST — Content Moderator
titleSuffix: Azure Cognitive Services
description: Korzystając z operacji Przejrzyj zadania w interfejsie API, można inicjować zadania kompleksowego moderowania zawartości dla zawartości obrazu lub tekstu w usłudze Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 924c21037a464770fac13c9b45ddcf261ff5a058
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905178"
---
# <a name="define-and-use-moderation-jobs-api-console"></a>Definiowanie i używanie zadań moderowania (konsola interfejsu API)

Zadanie moderowania służy jako rodzaj otoki dla funkcji moderowania zawartości, przepływów pracy i przeglądów. W tym przewodniku pokazano, jak za pomocą interfejsów API REST zadania inicjować i sprawdzać zadania związane z moderowaniem zawartości. Po zrozumieniu struktury interfejsów API można łatwo przenieść te wywołania na dowolną platformę zgodną z usługą REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w witrynie Content Moderator [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/) .
- Obowiązkowe [Zdefiniuj niestandardowy przepływ pracy](./Review-Tool-User-Guide/Workflows.md) , który ma być używany z Twoim zadaniem; można również użyć domyślnego przepływu pracy.

## <a name="create-a-job"></a>Tworzenie zadania

Aby utworzyć zadanie moderowania, przejdź do strony Tworzenie odwołania do interfejsu API [zadania](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) i wybierz przycisk dla regionu subskrypcji. Region można znaleźć w adresie URL punktu końcowego na stronie **poświadczenia** [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/). Spowoduje to uruchomienie konsoli interfejsu API, w której można łatwo tworzyć i uruchamiać wywołania interfejsu API REST.

![Zadanie — tworzenie wyboru regionu strony](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź następujące wartości, aby skonstruować wywołanie REST:

- **TeamName**: Identyfikator zespołu, który został utworzony podczas konfigurowania konta narzędzia do [przeglądania](https://contentmoderator.cognitive.microsoft.com/) (znajdującego się w polu **Identyfikator** na ekranie poświadczeń narzędzia przeglądu).
- **ContentType**: może to być "Image", "text" lub "Video".
- **Identyfikatorze**: niestandardowy ciąg identyfikatora. Ten ciąg jest przesyłany do interfejsu API i zwracany przez wywołanie zwrotne. Jest to przydatne w przypadku kojarzenia wewnętrznych identyfikatorów lub metadanych z wynikami zadania moderowania.
- **Workflowname**: Nazwa utworzonego wcześniej przepływu pracy (lub wartość domyślna dla domyślnego przepływu pracy).
- **CallbackEndpoint**: (opcjonalnie) adres URL do odbierania informacji zwrotnych po zakończeniu przeglądu.
- **OCP-APIM-Subscription-Key**: klucz Content Moderator. Ten klucz można znaleźć na karcie **Ustawienia** [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Wypełnij treść żądania

Treść wywołania REST zawiera jedno pole, **ContentValue**. Wklej w nieprzetworzonej zawartości tekstowej w przypadku moderowania tekstu lub wprowadź obraz lub adres URL wideo w przypadku moderowania obrazu lub wideo. Możesz użyć następującego przykładowego adresu URL obrazu: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Zadanie — Tworzenie parametrów zapytania konsoli, nagłówków i pola treści żądania](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Prześlij żądanie

Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** to `200 OK` , a w polu **zawartość odpowiedzi** zostanie wyświetlony Identyfikator zadania. Skopiuj ten identyfikator, aby użyć go w poniższych krokach.

![Przegląd — Tworzenie pola zawartość odpowiedzi konsoli wyświetla identyfikator przeglądu](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Pobierz stan zadania

Aby uzyskać informacje o stanie i szczegóły zadania uruchomionego lub ukończonego, przejdź do strony [zadanie — Pobierz](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) odwołanie do interfejsu API i wybierz przycisk dla regionu (region, w którym jest zarządzany klucz).

![Zadanie — pobieranie wybranego regionu](images/test-drive-region.png)

Wprowadź parametry wywołania REST zgodnie z powyższą sekcją. W tym kroku **JobID** jest unikatowym ciągiem identyfikatora, który został otrzymany podczas tworzenia zadania. Wybierz pozycję **Wyślij**. Jeśli operacja powiedzie się, **stan odpowiedzi** to `200 OK` , a w polu **zawartość odpowiedzi** zostanie wyświetlone zadanie w formacie JSON, na przykład:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Zadanie — Pobieranie odpowiedzi na wywołanie REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Zbadaj nowe przeglądy

Jeśli zadanie zawartości spowodowało utworzenie przeglądu, możesz je wyświetlić w [narzędziu do przeglądu](https://contentmoderator.cognitive.microsoft.com). Wybierz pozycję **Przeglądaj**  >  **Image** / **Text** / **wideo** tekst obrazu (w zależności od użytej zawartości). Zawartość powinna zostać wyświetlona, gotowa do przeglądu przez ludzi. Gdy moderator ludzki przegląda wstępnie przypisane znaczniki i dane prognoz i przesyła ostateczną decyzję o moderowaniu, interfejs API zadań przesyła wszystkie te informacje do wyznaczonego punktu końcowego wywołania zwrotnego.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób tworzenia i wykonywania zapytań dotyczących zadań moderowania zawartości przy użyciu interfejsu API REST. Następnie należy zintegrować zadania w scenariuszu kompleksowego łagodzenia, na przykład w samouczku [moderowania handlu elektronicznego](./ecommerce-retail-catalog-moderation.md) .