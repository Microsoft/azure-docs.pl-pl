---
title: Tworzenie i dołączanie równoległych gałęzi dla akcji w przepływach pracy
description: Dowiedz się, jak tworzyć lub scalać równoległe gałęzie działające dla niezależnych działań przepływu pracy w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 3514ce966e1de9af1741de6b966964aca2599610
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269237"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Utwórz lub Przyłącz gałęzie równoległe dla akcji przepływu pracy w Azure Logic Apps

Domyślnie akcje w przepływach pracy aplikacji logiki działają sekwencyjnie. Aby wykonać niezależne akcje w tym samym czasie, można utworzyć [gałęzie równoległe](#parallel-branches), a następnie [dołączyć te gałęzie](#join-branches) później w przepływie. 

> [!TIP] 
> Jeśli masz wyzwalacz, który odbiera tablicę i chcesz uruchomić przepływ pracy dla każdego elementu tablicy, możesz *departia* tę tablicę za pomocą [Właściwości wyzwalacza **SplitOn** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Dodaj gałąź równoległą

Aby uruchomić niezależne kroki w tym samym czasie, można dodać gałęzie równoległe obok istniejącego kroku. 

![Równoległe wykonywanie kroków](media/logic-apps-control-flow-branches/parallel.png)

Aplikacja logiki czeka na zakończenie wszystkich gałęzi przed kontynuowaniem przepływu pracy. Gałęzie równoległe są uruchamiane tylko wtedy, gdy ich `runAfter` wartości właściwości pasują do stanu gotowego kroku nadrzędnego. Na przykład oba `branchAction1` i `branchAction2` są ustawione do uruchamiania tylko po `parentAction` zakończeniu z `Succeeded` stanem.

> [!NOTE]
> Przed rozpoczęciem aplikacja logiki musi już mieć krok, w którym można dodać gałęzie równoległe.

1. W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Umieść wskaźnik myszy nad strzałką powyżej kroku, w którym chcesz dodać gałęzie równoległe. Wybierz wyświetlony znak **Plus** ( **+** ), a następnie wybierz pozycję **Dodaj gałąź równoległą**. 

   ![Dodaj gałąź równoległą](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. W polu wyszukiwania Znajdź i wybierz żądaną akcję.

   ![Zrzut ekranu przedstawiający okno "Wybieranie akcji" w Projektancie aplikacji logiki.](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Wybrana akcja będzie teraz widoczna w gałęzi równoległej, na przykład:

   ![Znajdź i wybierz żądaną akcję](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Teraz w każdej gałęzi równoległej Dodaj odpowiednie kroki. Aby dodać kolejną akcję do gałęzi, Przenieś wskaźnik pod akcję, w której chcesz dodać akcję sekwencyjną. Wybierz wyświetlony znak **Plus** ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

   ![Dodawanie sekwencyjnej akcji do gałęzi równoległej](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. W polu wyszukiwania Znajdź i wybierz żądaną akcję.

   ![Zrzut ekranu przedstawiający okno dialogowe "Wybieranie akcji" i pole wyszukiwania w Projektancie aplikacji logiki.](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Wybrana akcja będzie teraz wyświetlana w bieżącej gałęzi, na przykład:

   ![Znajdź i wybierz akcję sekwencyjną](media/logic-apps-control-flow-branches/added-sequential-action.png)

Aby scalić gałęzie ze sobą, [Dołącz do gałęzi równoległych](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Parallel — definicja gałęzi (JSON)

Jeśli pracujesz w widoku kodu, możesz zdefiniować strukturę równoległą w definicji JSON aplikacji logiki, na przykład:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Dołącz gałęzie równoległe

Aby scalić rozgałęzienia równoległe, wystarczy dodać krok u dołu w obszarze wszystkich gałęzi. Ten krok jest uruchamiany po zakończeniu wszystkich gałęzi równoległych.

![Dołącz gałęzie równoległe](media/logic-apps-control-flow-branches/join.png)

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz aplikację logiki w Projektancie aplikacji logiki. 

1. W obszarze gałęzie równoległe, które chcesz dołączyć, wybierz pozycję **nowy krok**. 

   ![Dodaj krok do przyłączenia](media/logic-apps-control-flow-branches/add-join-step.png)

1. W polu wyszukiwania Znajdź i wybierz żądaną akcję jako krok dołączania gałęzi.

   ![Znajdź i wybierz akcję sprzęgającą gałęzie równoległe](media/logic-apps-control-flow-branches/join-steps.png)

   Gałęzie równoległe są teraz scalone.

   ![Przyłączone gałęzie](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Join — definicja (JSON)

Jeśli pracujesz w widoku kodu, możesz zdefiniować strukturę sprzężenia w definicji JSON aplikacji logiki, na przykład:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać funkcje i sugestie lub zagłosować na nie, odwiedź [witrynę opinii o Azure Logic Apps użytkownika](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (przełącznik instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Wykonaj kroki na podstawie pogrupowanego stanu akcji (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
