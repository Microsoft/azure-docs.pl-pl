---
title: Obsługa błędów i wyjątków w przepływach pracy
description: Dowiedz się, jak obsługiwać błędy i wyjątki, które występują w zautomatyzowanych zadaniach i przepływach pracy utworzonych przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: estfan, logicappspm, azla
ms.date: 02/18/2021
ms.topic: article
ms.openlocfilehash: fbe797937021763bb97ca09e1da792d9a7010f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702508"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Obsługa błędów i wyjątków w usłudze Azure Logic Apps

Sposób, w jaki każda architektura integracji odpowiednio obsługuje przestoje lub problemy spowodowane przez systemy zależne mogą stanowić wyzwanie. Aby ułatwić tworzenie niezawodnych i odpornych integracji, które bezpiecznie obsługują problemy i błędy, Logic Apps zapewnia środowisko pierwszej klasy do obsługi błędów i wyjątków.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Zasady ponawiania

Aby uzyskać najbardziej podstawowy wyjątek i obsługę błędów, można użyć *zasad ponawiania* w dowolnej akcji lub wyzwalaczu, jeśli jest to obsługiwane, na przykład w temacie [Akcja http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Zasady ponawiania określają, czy i w jaki sposób akcja lub wyzwalacz ponawiają próbę żądania, gdy pierwotne żądanie przekroczy limit czasu lub zakończy się niepowodzeniem, co oznacza każde żądanie, które powoduje wystąpienie odpowiedzi 408, 429 lub 5xx. Jeśli nie zostaną użyte żadne inne zasady ponawiania, zostaną użyte zasady domyślne.

Oto typy zasad ponawiania prób:

| Typ | Opis |
|------|-------------|
| **Wartooć** | Te zasady wysyłają do czterech ponownych prób w *wykładniczo rosnących* odstępach czasu, które są skalowane o 7,5 sekund, ale są ograniczone do zakresu od 5 do 45 sekund. |
| **Interwał wykładniczy**  | Te zasady czekają losowy interwał wybrany z wykładniczo rosnącego zakresu przed wysłaniem kolejnego żądania. |
| **Stały interwał**  | Te zasady czekają określony interwał przed wysłaniem kolejnego żądania. |
| **Brak**  | Nie wysyłaj ponownie żądania. |
|||

Aby uzyskać informacje na temat limitów ponowień zasad, zobacz [Logic Apps limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#http-limits).

### <a name="change-retry-policy"></a>Zmień zasady ponawiania

Aby wybrać inne zasady ponawiania, wykonaj następujące kroki:

1. Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Otwórz **Ustawienia** dla akcji lub wyzwalacza.

1. Jeśli akcja lub wyzwalacz obsługuje zasady ponawiania, w obszarze **zasady ponawiania** wybierz odpowiedni typ.

Można też ręcznie określić zasady ponawiania w `inputs` sekcji dla akcji lub wyzwalacza, który obsługuje zasady ponawiania. Jeśli nie określisz zasad ponawiania, akcja będzie używać zasad domyślnych.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*retry-typ zasad*> | Ciąg | Typ zasad ponawiania próby, którego chcesz użyć: `default` , `none` , `fixed` lub `exponential` |
| <*Ponawianie interwału*> | Ciąg | Interwał ponawiania, w którym wartość musi używać [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Domyślny interwał minimalny to `PT5S` i maksymalny interwał to `PT1D` . Korzystając z zasad interwału wykładniczego, można określić różne wartości minimalne i maksymalne. |
| <*Ponawianie prób*> | Liczba całkowita | Liczba ponownych prób, które muszą zawierać się w przedziale od 1 do 90 |
||||

*Opcjonalne*

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*minimalny interwał*> | Ciąg | Dla zasad interwału wykładniczego najmniejszy interwał dla losowo wybranego interwału w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*maksimum — interwał*> | Ciąg | Dla zasad interwałów wykładniczych największy interwał dla losowo wybranego interwału w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Poniżej znajduje się więcej informacji na temat różnych typów zasad.

<a name="default-retry"></a>

### <a name="default"></a>Domyślne

Jeśli nie określisz zasad ponawiania, akcja będzie używać zasad domyślnych, które są w rzeczywistości [zasadami interwału wykładniczego](#exponential-interval) , które wysyłają do czterech ponownych prób przy wykładniczym zwiększeniu interwałów, które są skalowane przez 7,5 sekund. Interwał jest limitem od 5 do 45 sekund.

Chociaż nie zostało to jawnie zdefiniowane w akcji lub wyzwalaczu, poniżej przedstawiono sposób zachowania zasad domyślnych w przykładowej akcji HTTP:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Brak

Aby określić, że akcja lub wyzwalacz nie ponowią prób żądań zakończonych niepowodzeniem, ustaw wartość opcji <*retry-Policy-type*> na `none` .

### <a name="fixed-interval"></a>Stały interwał

Aby określić, że akcja lub wyzwalacz czekają określony interwał przed wysłaniem kolejnego żądania, należy ustawić> <*retry-Policy-Type* `fixed` .

*Przykład*

Ta zasada ponawiania próbuje pobrać najnowsze wiadomości dwa razy po pierwszym niepomyślnym żądaniu z opóźnieniem 30-sekundowym między kolejnymi próbami:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Interwał wykładniczy

Aby określić, że akcja lub wyzwalacz czekają losowy interwał przed wysłaniem kolejnego żądania, należy ustawić> <*retry-Policy-Type* `exponential` . Interwał losowy jest wybierany z wykładniczo rosnącego zakresu. Opcjonalnie można również przesłonić domyślne minimalne i maksymalne interwały, określając własne interwały minimalne i maksymalne.

**Zakresy zmiennych losowych**

W tej tabeli przedstawiono sposób, w jaki Logic Apps generuje jednorodną zmienną losową w określonym zakresie dla każdego ponowienia próby, włącznie z liczbą ponownych prób:

| Liczba ponownych prób | Minimalny interwał | Maksymalny interwał |
|--------------|------------------|------------------|
| 1 | Max (0, <> *minimalnym interwałem* ) | min (interwał, <*Maksymalny interwał*>) |
| 2 | Max (interwał, <*minimalny interwał*>) | minimum (2 * interwał, <*Maksymalny interwał*>) |
| 3 | Max (2 * interwał, <*minimalny interwał*>) | minimum (4 * interwał, <*Maksymalny interwał*>) |
| 4 | Max (4 * interwał, <*minimalny interwał*>) | minimum (8 * interwał, <*Maksymalny interwał*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Błędy catch i Handle poprzez zmianę zachowania "Uruchom po"

Po dodaniu akcji w Projektancie aplikacji logiki niejawnie deklaruje zamówienie, które ma być używane do uruchamiania tych akcji. Gdy akcja zakończy się, ta akcja jest oznaczona za pomocą stanu, takiego jak `Succeeded` , `Failed` , `Skipped` lub `TimedOut` . W każdej definicji akcji `runAfter` Właściwość określa akcję poprzednika, która musi zostać zakończona, i Stany dozwolone dla tego poprzednika przed uruchomieniem akcji następnika. Domyślnie akcja dodana w Projektancie jest uruchamiana dopiero po zakończeniu poprzednika ze `Succeeded` stanem.

Gdy akcja zgłasza nieobsługiwany błąd lub wyjątek, akcja jest oznaczona `Failed` i wszystkie akcje następnika są oznaczone `Skipped` . Jeśli takie zachowanie ma miejsce w przypadku akcji, która ma gałęzie równoległe, aparat Logic Apps jest zgodny z innymi gałęziami w celu określenia ich stanu ukończenia. Na przykład jeśli rozgałęzienie zakończy się `Skipped` akcją, stan zakończenia tej gałęzi jest oparty na pominięciu stanu poprzednika działania. Po zakończeniu działania aplikacji logiki aparat określa stan całego przebiegu, oceniając wszystkie Stany gałęzi. Jeśli dowolna gałąź zostanie zakończona niepowodzeniem, cały przebieg aplikacji logiki zostanie oznaczony `Failed` .

![Przykłady pokazujące, jak są oceniane Stany uruchamiania](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Aby upewnić się, że akcja może być nadal uruchomiona pomimo stanu jego poprzednika, [Dostosuj działanie "Uruchom po"](#customize-run-after) , aby obsłużyć stan niepowodzeń poprzedników.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Dostosuj zachowanie "Uruchom po"

Można dostosować działanie "Uruchom po" akcji, aby akcja była uruchamiana, gdy stan poprzednika to,,, `Succeeded` `Failed` `Skipped` `TimedOut` lub dowolnego z tych stanów. Na przykład, aby wysłać wiadomość e-mail po `Add_a_row_into_a_table` oznaczeniu akcji poprzednika programu Excel Online `Failed` , należy `Succeeded` zmienić zachowanie "Uruchom po", wykonując następujące czynności:

* W widoku Projekt wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Skonfiguruj przebieg po**.

  ![Skonfiguruj zachowanie "Uruchom po" dla akcji](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  Kształt akcji pokazuje domyślny stan wymagany dla akcji poprzednika, która **dodaje wiersz do tabeli** w tym przykładzie:

  ![Domyślne zachowanie "Uruchom po" dla akcji](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Zmień zachowanie "Uruchom po" na żądany stan, którego **nie powiodło się** w tym przykładzie:

  ![Zmień zachowanie "Uruchom po" na "zakończone niepowodzeniem"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Aby określić, że akcja ma być uruchamiana, gdy akcja poprzednika jest oznaczona jako `Failed` `Skipped` lub `TimedOut` , wybierz inne stany:

  ![Zmień zachowanie "Uruchom po", aby mieć inne Stany](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* W widoku Kod w definicji JSON akcji Edytuj `runAfter` Właściwość, która jest zgodna z następującą składnią:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Na potrzeby tego przykładu Zmień `runAfter` Właściwość z `Succeeded` na `Failed` :

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Aby określić, że akcja ma być uruchamiana, gdy akcja poprzednika jest oznaczona jako `Failed` `Skipped` lub `TimedOut` , Dodaj inne stany:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Oceń akcje z zakresami i ich wynikami

Podobnie jak w przypadku wykonywania pojedynczych akcji z `runAfter` właściwością, można grupować akcje w obrębie [zakresu](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Zakresy można używać w celu logicznego grupowania akcji, oceny stanu zagregowanego zakresu i wykonywania akcji na podstawie tego stanu. Po zakończeniu wszystkich akcji w zakresie, sam zakres uzyskuje własny stan.

Aby sprawdzić stan zakresu, można użyć tych samych kryteriów, które są używane do sprawdzania stanu uruchomienia aplikacji logiki, takiej jak `Succeeded` , `Failed` , i tak dalej.

Domyślnie, gdy wszystkie akcje tego zakresu zakończyły się powodzeniem, stan zakresu jest oznaczony `Succeeded` . Jeśli Ostatnia akcja w zakresie `Failed` ma wartość lub `Aborted` , stan zakresu jest oznaczony `Failed` .

Aby przechwytywać wyjątki w `Failed` zakresie i uruchamiać akcje, które obsługują te błędy, można użyć `runAfter` właściwości dla tego `Failed` zakresu. Dzięki temu, jeśli *jakieś* akcje w zakresie zakończą się niepowodzeniem i używasz `runAfter` właściwości dla tego zakresu, można utworzyć pojedynczą akcję w celu przechwycenia błędów.

Aby uzyskać ograniczenia dotyczące zakresów, zobacz [limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Pobierz kontekst i wyniki dla niepowodzeń

Chociaż przechwytywanie błędów z zakresu jest przydatne, można również zastanowić się, że kontekst pomaga zrozumieć, które akcje nie powiodły się, oraz wszelkie zwrócone błędy lub kody stanu. [ `result()` Funkcja](../logic-apps/workflow-definition-language-functions-reference.md#result) zwraca wyniki z akcji najwyższego poziomu w akcji z określonym zakresem, akceptując pojedynczy parametr, który jest nazwą zakresu i zwracając tablicę zawierającą wyniki z tych akcji pierwszego poziomu. Te obiekty akcji obejmują te same atrybuty, co zwracane przez `actions()` funkcję, takie jak godzina rozpoczęcia akcji, godzina zakończenia, stan, dane wejściowe, identyfikatory korelacji i wyjścia. 

> [!NOTE]
> `result()`Funkcja zwraca wyniki *tylko* z akcji pierwszego poziomu, a nie przed bardziej zagnieżdżonymi akcjami, takimi jak akcje przełącznika lub warunku.

Aby uzyskać kontekst o akcjach, które zakończyły się niepowodzeniem w zakresie, można użyć `@result()` wyrażenia z nazwą zakresu i `runAfter` właściwością. Aby odfiltrować zwracaną tablicę do akcji mających `Failed` stan, można dodać [akcję **Filtruj tablicę**](logic-apps-perform-data-operations.md#filter-array-action). Aby uruchomić akcję dla zwróconej akcji zakończonej niepowodzeniem, weź zwrotną filtrowaną tablicę i Użyj [ **dla każdej** pętli](../logic-apps/logic-apps-control-flow-loops.md).

Oto przykład, a następnie szczegółowy opis, który wysyła żądanie HTTP POST z treścią odpowiedzi dla wszystkich akcji, które zakończyły się niepowodzeniem w ramach akcji zakresu o nazwie "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Oto szczegółowy przewodnik, w którym opisano, co się dzieje w tym przykładzie:

1. Aby uzyskać wynik ze wszystkich akcji w ramach elementu "My_Scope", Akcja **filtrowania tablicy** używa tego wyrażenia filtru: `@result('My_Scope')`

1. Warunek dla **tablicy filtru** to każdy `@result()` element o stanie równy `Failed` . Ten warunek filtruje tablicę, która ma wszystkie wyniki akcji z "My_Scope" w dół do tablicy z wynikami akcji zakończonych niepowodzeniem.

1. Wykonaj `For_each` akcję pętli dla *przefiltrowanych* danych wyjściowych tablicy. Ten krok wykonuje akcję dla każdego wyniku działania zakończonego niepowodzeniem, który został wcześniej przefiltrowany.

   Jeśli pojedyncza akcja w zakresie zakończy się niepowodzeniem, akcje w `For_each` pętli są wykonywane tylko raz. Wiele akcji zakończonych niepowodzeniem powoduje jedną akcję na awarię.

1. Wyślij wpis HTTP w `For_each` treści odpowiedzi elementu, która jest `@item()['outputs']['body']` wyrażeniem.

   `@result()`Kształt elementu jest taki sam jak `@actions()` kształt i można go analizować w taki sam sposób.

1. Uwzględnij dwa nagłówki niestandardowe z nazwą akcji zakończonej niepowodzeniem ( `@item()['name']` ) i identyfikatorem śledzenia klienta, który nie powiódł się `@item()['clientTrackingId']` .

Oto przykład pojedynczego `@result()` elementu, `name` `body` który pokazuje właściwości, i, `clientTrackingId` które są analizowane w poprzednim przykładzie. Poza `For_each` akcją `@result()` zwraca tablicę tych obiektów.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Aby wykonać różne wzorce obsługi wyjątków, można użyć wyrażeń opisanych wcześniej w tym artykule. Możesz wykonać akcję obejmującą pojedynczą obsługę wyjątków poza zakresem, który akceptuje całą przefiltrowaną tablicę błędów, i Usuń `For_each` akcję. Możesz również dołączyć inne przydatne właściwości z `\@result()` odpowiedzi, jak opisano wcześniej.

## <a name="set-up-azure-monitor-logs"></a>Konfigurowanie dzienników Azure Monitor

Poprzednie wzorce to doskonałe rozwiązanie do obsługi błędów i wyjątków w ramach przebiegu, ale można również identyfikować błędy niezależne od samego uruchomienia i odpowiadać na nie. [Azure monitor](../azure-monitor/overview.md) zapewnia prostą metodę wysyłania wszystkich zdarzeń przepływu pracy, w tym wszystkich stanów uruchamiania i akcji, do [obszaru roboczego log Analytics](../azure-monitor/logs/data-platform-logs.md), [konta usługi Azure Storage](../storage/blobs/storage-blobs-overview.md)lub [Event Hubs platformy Azure](../event-hubs/event-hubs-about.md).

Aby oszacować Stany uruchamiania, można monitorować dzienniki i metryki lub publikować je w dowolnym preferowanym narzędziu do monitorowania. Jedną z potencjalnych opcji jest przesyłanie strumieniowe wszystkich zdarzeń za pomocą Event Hubs do [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). W Stream Analytics można pisać zapytania na żywo na podstawie wszelkich anomalii, średnich lub błędów z dzienników diagnostycznych. Za pomocą Stream Analytics można wysyłać informacje do innych źródeł danych, takich jak kolejki, tematy, SQL, Azure Cosmos DB lub Power BI.

## <a name="next-steps"></a>Następne kroki

* [Zobacz, jak klient kompiluje obsługę błędów w Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Znajdź więcej Logic Apps przykładów i scenariuszy](../logic-apps/logic-apps-examples-and-scenarios.md)
