---
title: Dodawanie instrukcji switch do przepływów pracy
description: Sposób tworzenia instrukcji switch kontrolujących akcje przepływu pracy na podstawie określonych wartości w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 62f147104cf9e0e1605b02a420cb6d20190361b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657452"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Utwórz instrukcje Switch, które uruchamiają akcje przepływu pracy na podstawie określonych wartości w Azure Logic Apps

Aby uruchamiać określone akcje na podstawie wartości obiektów, wyrażeń lub tokenów, Dodaj instrukcję *Switch* . Ta struktura szacuje obiekt, wyrażenie lub token, wybiera wielkość liter pasującą do wyniku i uruchamia określone akcje tylko dla tego przypadku. Po uruchomieniu instrukcji switch tylko jeden przypadek powinien być zgodny z wynikami.

Załóżmy na przykład, że chcesz, aby aplikacja logiki, która wykonuje różne kroki, w oparciu o opcję wybraną w wiadomości e-mail. W tym przykładzie aplikacja logiki sprawdza kanał informacyjny RSS witryny internetowej pod kątem nowej zawartości. Po pojawieniu się nowego elementu w kanale informacyjnym RSS aplikacja logiki wysyła wiadomość e-mail do osoby zatwierdzającej. W zależności od tego, czy osoba zatwierdzająca wybierze opcję "Zatwierdź" czy "Odrzuć", aplikacja logiki postępuje zgodnie z różnymi krokami.

> [!TIP]
> Podobnie jak w przypadku wszystkich języków programowania, instrukcje Switch obsługują tylko operatory równości. Jeśli potrzebujesz innych operatorów relacyjnych, takich jak "większe niż", użyj [instrukcji warunkowej](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Aby zapewnić jednoznaczne zachowanie wykonywania, przypadki muszą zawierać unikatową i statyczną wartość zamiast tokenów dynamicznych lub wyrażeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aby postępować zgodnie z przykładem w tym artykule, [Utwórz tę przykładową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta Outlook.com lub konta służbowego.

  1. Po dodaniu akcji do wysyłania wiadomości e-mail Znajdź i wybierz tę akcję: **Wyślij wiadomość e-mail z zatwierdzeniem**

     ![Wybierz pozycję "Wyślij wiadomość e-mail dotyczącą zatwierdzenia"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Podaj wymagane pola, takie jak adres e-mail osoby, która otrzymuje wiadomość e-mail dotyczącą zatwierdzenia. 
  W obszarze **Opcje użytkownika**wprowadź wartość "Zatwierdź, Odrzuć".

     ![Wprowadź szczegóły wiadomości e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Dodaj instrukcję Switch

1. Na potrzeby tego przykładu Dodaj instrukcję Switch na końcu przykładowego przepływu pracy. Po ostatnim kroku wybierz pozycję **nowy krok**.

   Aby dodać instrukcję Switch między krokami, przesuń wskaźnik myszy nad strzałkę, w której chcesz dodać instrukcję Switch. Wybierz wyświetlony znak **Plus** ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź ciąg "switch" jako filtr. Wybierz tę akcję: **formant przełącznika**

   ![Dodaj przełącznik](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Instrukcja Switch występuje z jednym przypadkiem i domyślnym przypadkiem. 
   Domyślnie Instrukcja Switch wymaga co najmniej jednego przypadku oraz domyślnego przypadku. 

   ![Pusta Instrukcja default Switch](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Kliknij wewnątrz pola **na** , aby wyświetlić listę zawartości dynamicznej. Z tej listy wybierz pole **SelectedOption** , którego dane wyjściowe określają akcję do wykonania. 

   ![Wybierz pozycję "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Aby obsłużyć przypadki, w których osoba zatwierdzająca wybiera `Approve` lub `Reject` Dodaj inną wielkość liter między **wielkością liter** a **wartością domyślną**. 

   ![Dodaj inny przypadek](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Dodaj te akcje do odpowiednich przypadków:

   | Spraw # | **SelectedOption** | Akcja |
   |--------|--------------------|--------|
   | Przypadek 1 | **Zatwierdzenie** | Dodaj akcję **Wyślij wiadomość e-mail** do programu Outlook w celu wysłania szczegółowych informacji o elemencie RSS tylko wtedy, gdy osoba zatwierdzająca zabrała **zatwierdzenie**. |
   | Przypadek 2 | **Reject** | Dodaj akcję **Wyślij wiadomość e-mail** do programu Outlook, aby poinformować inne osoby zatwierdzające o odrzuceniu elementu RSS. |
   | Domyślne | Brak | Nie trzeba podejmować żadnych działań. W tym przykładzie **domyślnym** przypadkiem jest pusty, ponieważ **SelectedOption** ma tylko dwie opcje. |
   |||

   ![Zakończono przełączanie instrukcji](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Zapisz aplikację logiki. 

   Aby ręcznie przetestować ten przykład, wybierz pozycję **Uruchom** , dopóki aplikacja logiki nie odnajdzie nowego elementu RSS i wyśle wiadomość e-mail z potwierdzeniem. 
   Wybierz pozycję **Zatwierdź** , aby obserwować wyniki.

## <a name="json-definition"></a>Definicja JSON

Teraz, po utworzeniu aplikacji logiki przy użyciu instrukcji switch, przyjrzyjmy się definicji kodu wysokiego poziomu za instrukcją Switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Etykieta | Opis |
|-------|-------------|
| `"Switch"`         | Nazwa instrukcji switch, którą można zmienić nazwy na potrzeby czytelności |
| `"type": "Switch"` | Określa, że akcja jest instrukcją Switch |
| `"expression"`     | W tym przykładzie określa opcję wybraną przez osobę zatwierdzającą, która jest szacowana dla każdego przypadku, zgodnie z deklaracją w dalszej części definicji |
| `"cases"` | Definiuje dowolną liczbę przypadków. W każdym przypadku `"Case_*"` jest to domyślna nazwa dla tego przypadku, którą można zmienić na czytelność |
| `"case"` | Określa wartość przypadku, która musi być stałą i unikatową wartością, którą instrukcja SWITCH używa do porównania. Jeśli żadne przypadki nie pasują do wyniku wyrażenia przełącznika, akcje w `"default"` sekcji są uruchamiane. | 
| | | 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać lub zagłosować na temat funkcji lub sugestii, odwiedź [witrynę opinii o Azure Logic Apps użytkownika](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie pogrupowanego stanu akcji (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
