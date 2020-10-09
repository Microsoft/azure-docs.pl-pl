---
title: Definiowanie przepływów pracy moderowania za pomocą konsoli interfejsu API REST — Content Moderator
titleSuffix: Azure Cognitive Services
description: Korzystając z interfejsów API przeglądu Content Moderator platformy Azure, można definiować niestandardowe przepływy pracy i progi na podstawie zasad dotyczących zawartości.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72754179"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definiowanie przepływów pracy moderowania (REST) i korzystanie z nich

Przepływy pracy to dostosowane filtry oparte na chmurze, które umożliwiają efektywniejsze obsługiwanie zawartości. Przepływy pracy mogą łączyć się z różnymi usługami, aby filtrować zawartość na różne sposoby, a następnie podejmować odpowiednie działania. W tym przewodniku pokazano, jak używać interfejsów API REST przepływu pracy za pomocą konsoli interfejsu API, aby tworzyć przepływy pracy i korzystać z nich. Po zrozumieniu struktury interfejsów API można łatwo przenieść te wywołania na dowolną platformę zgodną z usługą REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w witrynie Content Moderator [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="create-a-workflow"></a>Tworzenie przepływu pracy

Aby utworzyć lub zaktualizować przepływ pracy, przejdź do strony **[przepływy pracy — tworzenie lub aktualizowanie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** odwołania do interfejsu API i wybierz przycisk dla regionu klucza (można go znaleźć w adresie URL punktu końcowego na stronie **poświadczenia** [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/)). Spowoduje to uruchomienie konsoli interfejsu API, w której można łatwo tworzyć i uruchamiać wywołania interfejsu API REST.

![Przepływ pracy — Utwórz lub zaktualizuj wybór regionu strony](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź wartości dla **zespołu**, **przepływu pracy**i **OCP-APIM-Subscription-Key**:

- **zespół**: Identyfikator zespołu, który został utworzony podczas konfigurowania konta narzędzia do [przeglądania](https://contentmoderator.cognitive.microsoft.com/) (znajdującego się w polu **Identyfikator** na ekranie poświadczeń narzędzia przeglądu).
- **workflowname**: Nazwa nowego przepływu pracy do dodania (lub istniejącej nazwy, jeśli chcesz zaktualizować istniejący przepływ pracy).
- **OCP-APIM-Subscription-Key**: klucz Content Moderator. Można to znaleźć na karcie **Ustawienia** w [narzędziu do przeglądu](https://contentmoderator.cognitive.microsoft.com).

![Przepływ pracy — tworzenie lub aktualizowanie parametrów i nagłówków zapytań konsoli](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Wprowadź definicję przepływu pracy

1. Edytuj pole **treści żądania** , aby wprowadzić żądanie JSON zawierające szczegóły **opisu** i **typu** (albo `Image` `Text` ).
2. W polu **wyrażenie**Skopiuj domyślne wyrażenie JSON przepływu pracy. Końcowy ciąg JSON powinien wyglądać następująco:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Można definiować proste, złożone i nawet zagnieżdżone wyrażenia dla przepływów pracy przy użyciu tego interfejsu API. Dokumentacja [przepływu pracy — tworzenie lub aktualizacja](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) zawiera przykłady bardziej złożonej logiki.

### <a name="submit-your-request"></a>Prześlij żądanie
  
Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** to `200 OK` i zostanie wyświetlone pole **zawartość odpowiedzi** `true` .

### <a name="examine-the-new-workflow"></a>Badanie nowego przepływu pracy

W [narzędziu do przeglądu](https://contentmoderator.cognitive.microsoft.com/)wybierz pozycję **Ustawienia**  >  **przepływy pracy**. Nowy przepływ pracy powinien pojawić się na liście.

![Lista narzędzi do przeglądu przepływów pracy](images/workflow-console-new-workflow.PNG)

Wybierz opcję **Edytuj** dla przepływu pracy i przejdź do karty **Projektant** . W tym miejscu można zobaczyć intuicyjną reprezentację logiki JSON.

![Karta projektanta dla wybranego przepływu pracy](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Pobierz szczegóły przepływu pracy

Aby pobrać szczegółowe informacje o istniejącym przepływie pracy, przejdź do strony **[przepływ pracy — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** odwołanie do interfejsu API i wybierz przycisk dla regionu (region, w którym jest zarządzany klucz).

![Przepływ pracy — dostęp do wyboru regionów](images/test-drive-region.png)

Wprowadź parametry wywołania REST zgodnie z powyższą sekcją. Upewnij się, że ten czas, **workflowname** jest nazwą istniejącego przepływu pracy.

![Pobierz parametry i nagłówki zapytania](images/workflow-get-default.PNG)

Wybierz pozycję **Wyślij**. Jeśli operacja powiedzie się, **stan odpowiedzi** to `200 OK` , a w polu **zawartość odpowiedzi** zostanie wyświetlony przepływ pracy w formacie JSON, podobny do następującego:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać przepływów pracy z [zadaniami moderowania zawartości](try-review-api-job.md).