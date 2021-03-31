---
title: Dodawanie instrukcji warunkowych do przepływów pracy
description: Jak utworzyć warunki sterujące akcjami w przepływach pracy w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 19f42270e37c42cf56bbde04f73c01027a56ff63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657394"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Tworzenie instrukcji warunkowych kontrolujących akcje przepływu pracy w Azure Logic Apps

Aby uruchamiać określone akcje w aplikacji logiki tylko po przekazaniu określonego warunku, Dodaj *instrukcję warunkową*. Ta struktura kontroli porównuje dane w przepływie pracy z określonymi wartościami lub polami. Następnie można określić różne akcje, które są uruchamiane w zależności od tego, czy dane spełniają warunek. Możesz zagnieżdżać warunki wewnątrz siebie.

Załóżmy na przykład, że masz aplikację logiki, która wysyła zbyt wiele wiadomości e-mail, gdy nowe elementy są widoczne w kanale informacyjnym RSS witryny sieci Web. Możesz dodać instrukcję warunkową, aby wysyłać wiadomości e-mail tylko wtedy, gdy nowy element zawiera określony ciąg. 

> [!TIP]
> Aby wykonać różne kroki na podstawie różnych konkretnych wartości, należy zamiast tego użyć [*instrukcji switch*](../logic-apps/logic-apps-control-flow-switch-statement.md) .

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aby postępować zgodnie z przykładem w tym artykule, [Utwórz tę przykładową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta Outlook.com lub konta służbowego.

## <a name="add-condition"></a>Dodawanie warunku

1. W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Dodaj warunek w pożądanej lokalizacji. 

   Aby dodać warunek między krokami, przesuń wskaźnik myszy nad strzałkę, w której chcesz dodać warunek. Wybierz wyświetlony znak **Plus** ( **+** ), a następnie wybierz pozycję **Dodaj akcję**. Na przykład:

   ![Dodaj akcję między krokami](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Jeśli chcesz dodać warunek na końcu przepływu pracy, w dolnej części aplikacji logiki wybierz pozycję  **nowy krok** > **Dodaj akcję**.

1. W polu wyszukiwania wprowadź wartość "warunek" jako filtr. Wybierz tę akcję: **formant warunku**

   ![Dodawanie warunku](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. W polu **warunek** Skompiluj swój warunek. 

   1. W polu po lewej stronie Określ dane lub pola, które chcesz porównać.

      Po kliknięciu w lewym polu zostanie wyświetlona lista zawartości dynamicznej, aby można było wybrać dane wyjściowe z poprzednich kroków w aplikacji logiki. 
      Na potrzeby tego przykładu wybierz podsumowanie kanału informacyjnego RSS.

      ![Kompilowanie warunku](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. W środkowym polu Wybierz operację do wykonania. 
   Na potrzeby tego przykładu wybierz pozycję "**Contains**". 

   1. W prawym polu Określ wartość lub pole jako kryterium. 
   W tym przykładzie określ następujący ciąg: **Microsoft**

   Oto pełen warunek:

   ![Gotowy warunek](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Aby dodać kolejny wiersz do warunku, wybierz pozycję **Dodaj**  >  **Dodaj wiersz**. 
   Aby dodać grupę z podwarunkami, wybierz pozycję **Dodaj**  >  **Dodaj grupę**. 
   Aby zgrupować istniejące wiersze, zaznacz pola wyboru dla tych wierszy, wybierz przycisk wielokropka (...) dla każdego wiersza, a następnie wybierz polecenie **Utwórz grupę**.

1. W obszarze **Jeśli prawda** i w **przypadku wartości false** Dodaj kroki do wykonania w zależności od tego, czy warunek jest spełniony. Na przykład:

   ![Warunek z ścieżkami "If true" i "If false"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Możesz przeciągnąć istniejące akcje do ścieżek **if true** i **if false** .

1. Zapisz aplikację logiki.

Ta aplikacja logiki wysyła wiadomość e-mail tylko wtedy, gdy nowe elementy kanału informacyjnego RSS spełnią warunek.

## <a name="json-definition"></a>Definicja JSON

Oto definicja kodu wysokiego poziomu za instrukcją warunkową:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać funkcje i sugestie lub zagłosować na nie, odwiedź [witrynę opinii o Azure Logic Apps użytkownika](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Wykonaj kroki na podstawie różnych wartości (przełącznik instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie pogrupowanego stanu akcji (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
