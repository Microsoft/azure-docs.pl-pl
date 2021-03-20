---
title: Grupuj i uruchamiaj akcje według zakresu
description: Tworzenie akcji w zakresie, które są uruchamiane na podstawie stanu grupy w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 95b5cc191ac6857bf8e1b09e70b22d928473fe03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92314839"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Uruchamianie akcji na podstawie stanu grupy przy użyciu zakresów w Azure Logic Apps

Aby uruchamiać akcje dopiero po pomyślnym lub niepowodzeniu innej grupy akcji, należy zgrupować te akcje w *zakresie*. Ta struktura jest przydatna, gdy chcesz organizować akcje jako grupę logiczną, oszacować stan grupy i wykonywać akcje, które są oparte na stanie zakresu. Gdy wszystkie akcje w zakresie zakończą działanie, zakres pobiera również własny stan. Można na przykład użyć zakresów, gdy chcesz zaimplementować [obsługę wyjątków i błędów](../logic-apps/logic-apps-exception-handling.md#scopes). 

Aby sprawdzić stan zakresu, można użyć tych samych kryteriów, które są używane do określania stanu uruchomienia aplikacji logiki, **takich jak powodzenie**, **Niepowodzenie**, **Anulowanie** i tak dalej. Domyślnie, gdy wszystkie akcje tego zakresu zakończyły się powodzeniem, stan zakresu jest oznaczony jako **zakończony powodzeniem**. Ale jeśli jakakolwiek akcja w zakresie nie powiedzie się lub zostanie anulowana, stan zakresu jest oznaczony jako **Niepowodzenie**. Aby uzyskać ograniczenia dotyczące zakresów, zobacz [limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md). 

Na przykład poniżej znajduje się aplikacja logiki wysokiego poziomu, która używa zakresu do uruchamiania określonych akcji i warunku do sprawdzenia stanu zakresu. Jeśli jakiekolwiek akcje w zakresie zakończą się niepowodzeniem lub kończą się nieoczekiwanie, zakres jest oznaczony jako **Niepowodzenie** lub **przerwane** odpowiednio, a aplikacja logiki wysyła komunikat "zakres nie powiódł się". Jeśli wszystkie akcje w zakresie zostały wykonane pomyślnie, aplikacja logiki wyśle komunikat "zakres został pomyślnie".

![Diagram przedstawia przepływ zakresu aplikacji logiki z przykładami "zakres nie powiodło się" i "zakres zakończony powodzeniem".](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z przykładem w tym artykule, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Konto e-mail od dowolnego dostawcy poczty e-mail obsługiwanego przez Logic Apps. W tym przykładzie używa Outlook.com. Jeśli używasz innego dostawcy, przepływ ogólny pozostaje taki sam, ale interfejs użytkownika wygląda inaczej.

* Klucz mapy usługi Bing. Aby uzyskać ten klucz, zobacz sekcję <a href="/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key" target="_blank">Pobieranie klucza usługi mapy Bing</a>.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Tworzenie przykładowej aplikacji logiki

Najpierw Utwórz tę przykładową aplikację logiki, aby można było dodać zakres później:

![Tworzenie przykładowej aplikacji logiki](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Wyzwalacz **harmonogramu cyklicznego** , który sprawdza usługę mapy Bing w określonym interwale
* **Mapy Bing — Pobierz akcję trasy** , która sprawdza czas podróży między dwiema lokalizacjami
* Instrukcja warunkowa, która sprawdza, czy czas podróży przekracza określony czas podróży
* Akcja, która wysyła wiadomość e-mail, że bieżący czas podróży przekracza określony czas

Aplikację logiki można zapisać w dowolnym momencie, dlatego Zapisz swoją służbę często.

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, jeśli jeszcze tego nie zrobiono. Tworzenia pustej aplikacji logiki.

1. Dodaj wyzwalacz **harmonogram-cykl** z tymi ustawieniami: **Interval** = "1" i **częstotliwość** = "minuta"

   ![Konfigurowanie wyzwalacza "harmonogram-cykl"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Aby wizualnie uprościć widok i ukryć szczegóły każdej akcji w projektancie, Zwiń każdy kształt akcji w trakcie wykonywania tych kroków.

1. Dodaj akcję **mapy Bing — Pobierz trasę** . 

   1. Jeśli nie masz jeszcze połączenia z usługą mapy Bing, zostanie wyświetlony monit o utworzenie połączenia.

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Nazwa połączenia** | BingMapsConnection | Podaj nazwę połączenia. | 
      | **Klucz interfejsu API** | <*Twoje-Bing-Maps-Key*> | Wprowadź uzyskany wcześniej klucz usługi Mapy Bing. | 
      ||||  

   1. Skonfiguruj akcję **Pobierz trasę** , tak jak pokazano w poniższej tabeli:

      ![Skonfiguruj akcję "mapy Bing — Pobierz trasę"](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Aby uzyskać więcej informacji na temat tych parametrów, zobacz [Calculate route (Obliczanie trasy)](/bingmaps/rest-services/routes/calculate-a-route).

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Punkt nawigacyjny 1** | <*Start*> | Wprowadź Początek trasy. | 
      | **Punkt nawigacyjny 2** | <*punktów*> | Wprowadź lokalizację docelową trasy. | 
      | **Uniknięcie** | Brak | Wprowadź elementy, które mają być unikane na trasie, takie jak Autostrade, opłaty i tak dalej. Aby uzyskać możliwe wartości, zobacz [Obliczanie trasy](/bingmaps/rest-services/routes/calculate-a-route). | 
      | **Zoptymalizować** | timeWithTraffic | Wybierz parametr, aby zoptymalizować trasę, na przykład odległość, czas z bieżącymi informacjami o ruchu itd. Ten przykład używa tej wartości: "timeWithTraffic" | 
      | **Jednostka odległości** | <*Twoje preferencje*> | Wprowadź jednostkę odległości do obliczenia trasy. Ten przykład używa tej wartości: "mila" | 
      | **Tryb podróży** | Jazda samochodem | Wprowadź tryb podróży dla trasy. W tym przykładzie jest stosowana ta wartość "kierowanie" | 
      | **Transport publiczny — data i godzina** | Brak | Dotyczy tylko trybu tranzytowego. | 
      | **Typ Date-Type tranzytu** | Brak | Dotyczy tylko trybu tranzytowego. | 
      ||||  

1. [Dodaj warunek](../logic-apps/logic-apps-control-flow-conditional-statement.md) , który sprawdza, czy bieżący czas podróży z ruchem przekracza określony czas. 
   W tym przykładzie wykonaj następujące kroki:

   1. Zmień nazwę warunku na ten opis: **Jeśli czas ruchu jest większy niż określony czas**

   1. W kolumnie z lewej strony kliknij wewnątrz pola **Wybierz wartość** , aby wyświetlić listę zawartość dynamiczna. Z tej listy wybierz pole **ruch czasu trwania podróży** (w sekundach). 

      ![Kompilowanie warunku](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. W środkowym polu Wybierz pozycję Ten operator: **jest większe niż**

   1. W kolumnie po prawej stronie Wprowadź tę wartość porównania, która jest w sekundach i równa 10 minut: **600**

      Gdy wszystko będzie gotowe, warunek będzie wyglądać następująco:

      ![Ukończony warunek](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. W gałęzi w **przypadku wartości true** Dodaj akcję "Wyślij wiadomość e-mail" dla dostawcy poczty e-mail. 
   Skonfiguruj tę akcję, wykonując czynności opisane w tym obrazie:

   ![Dodaj akcję "Wyślij wiadomość e-mail" do gałęzi "w przypadku wartości true"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. W polu **do** wprowadź swój adres e-mail na potrzeby testowania.

   1. W polu **podmiot** wprowadź następujący tekst:

      ```Time to leave: Traffic more than 10 minutes```

   1. W polu **treść** wprowadź ten tekst z końcowym miejscem: 

      ```Travel time:```

      Gdy kursor pojawi się w polu **treść** , lista zawartości dynamicznej pozostaje otwarta, aby można było wybrać dowolne parametry, które są dostępne w tym punkcie.

   1. Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**.

   1. Znajdź i wybierz funkcję **DIV ()** . 
      Umieść kursor wewnątrz nawiasów funkcji.

   1. Gdy kursor znajduje się wewnątrz nawiasów funkcji, wybierz **zawartość dynamiczną** , aby wyświetlić listę zawartości dynamicznej. 
   
   1. W sekcji **pobieranie trasy** zaznacz pole ruch związany z ruchem **czasowym** .

      ![Wybierz pozycję "ruch w czasie trwania ruchu"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Gdy pole jest rozpoznawane jako format JSON, Dodaj **przecinek** ( ```,``` ), a następnie liczbę, ```60``` Aby przekonwertować wartość w polu **ruch związany z ruchem** z sekund na minuty. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Wyrażenie wygląda teraz tak, jak w poniższym przykładzie:

      ![Wyrażenie zakończenia](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Po rozpoczęciu wyrażenia Dodaj ten tekst z wiodącym miejscem: ``` minutes```
  
       Pole **treści** wygląda teraz następująco:

       ![Zakończono pole "Body"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Zapisz aplikację logiki.

Następnie Dodaj zakres, aby można było grupować określone akcje i oszacować ich stan.

## <a name="add-a-scope"></a>Dodawanie zakresu

1. Jeśli jeszcze tego nie zrobiono, Otwórz aplikację logiki w Projektancie aplikacji logiki. 

1. Dodaj zakres w pożądanej lokalizacji przepływu pracy. Na przykład aby dodać zakres między istniejącymi krokami przepływu pracy aplikacji logiki, wykonaj następujące kroki: 

   1. Przesuń wskaźnik myszy nad strzałkę, w której chcesz dodać zakres. 
   Wybierz znak **Plus** ( **+** ), > **dodać akcję**.

      ![Dodawanie zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. W polu wyszukiwania wprowadź "Scope" jako filtr. 
   Wybierz akcję **zakresu** .

## <a name="add-steps-to-scope"></a>Dodaj kroki do zakresu

1. Teraz Dodaj kroki lub przeciągnij istniejące kroki, które chcesz uruchomić w zakresie. W tym przykładzie przeciągnij te akcje do zakresu:
      
   * **Pobierz trasę**
   * **Jeśli czas ruchu przekracza określony czas**, który obejmuje gałęzie **prawdziwe** i **fałszywe**

   Aplikacja logiki będzie teraz wyglądać następująco:

   ![Dodano zakres](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. W obszarze Zakres Dodaj warunek, który sprawdza stan zakresu. Zmień nazwę warunku na ten opis: **Jeśli zakres nie powiódł się**

   ![Dodaj warunek, aby sprawdzić stan zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. W warunku Dodaj te wyrażenia, które sprawdzają, czy stan zakresu jest równy "Niepowodzenie" lub "przerwane". 

   1. Aby dodać kolejny wiersz, wybierz pozycję **Dodaj**. 

   1. W każdym wierszu kliknij wewnątrz lewej strony, aby wyświetlić listę zawartości dynamicznej. 
   Z listy zawartość dynamiczna wybierz pozycję **wyrażenie**. W polu edycji wprowadź wyrażenie, a następnie wybierz przycisk **OK**: 
   
      `result('Scope')[0]['status']`

      ![Zrzut ekranu pokazujący pole "wyrażenie" z wyróżnionym wyrażeniem wynikowym.](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Dla obu wierszy wybierz opcję **jest równa** operatorowi. 
   
   1. Dla wartości porównania w pierwszym wierszu wprowadź `Failed` . 
   W drugim wierszu wprowadź `Aborted` . 

      Gdy wszystko będzie gotowe, warunek będzie wyglądać następująco:

      ![Dodaj wyrażenie, które sprawdza stan zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Teraz ustaw właściwość warunek, `runAfter` tak aby warunek sprawdzał stan zakresu i uruchomił akcję dopasowywania zdefiniowaną w dalszych krokach.

   1. W warunku **Jeśli warunek nie powiódł się** , wybierz przycisk **wielokropka** (...), a następnie wybierz pozycję **Skonfiguruj przebieg po**.

      ![Skonfiguruj Właściwość "runAfter"](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Wybierz wszystkie te Stany zakresu: **powodzenie**, zakończone **niepowodzeniem**, **pominięto** i przekroczono **limit czasu**

      ![Wybierz Stany zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Po zakończeniu wybierz pozycję **Gotowe**. 
   W tym warunku zostanie wyświetlona ikona "informacje".

1. W gałęziach **if true** i **Jeśli false** Dodaj akcje, które chcesz wykonać na podstawie każdego stanu zakresu, na przykład Wyślij wiadomość e-mail lub wiadomość.

   ![Dodaj akcje do wykonania na podstawie stanu zakresu](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Zapisz aplikację logiki.

Gotowa aplikacja logiki będzie teraz wyglądać następująco:

![Zakończono działanie aplikacji logiki z zakresem](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Przetestuj swoją służbę

Na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Jeśli wszystkie akcje w zakresie zostały wykonane pomyślnie, zostanie wyświetlony komunikat "zakres został pomyślnie". Jeśli jakiekolwiek akcje w zakresie nie powiedzie się, zostanie wyświetlony komunikat "zakres nie powiódł się". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definicja JSON

Jeśli pracujesz w widoku kodu, możesz zdefiniować w zamian strukturę zakresu w definicji JSON aplikacji logiki. Na przykład Oto definicja JSON dla wyzwalacza i akcji w poprzedniej aplikacji logiki:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać funkcje i sugestie lub zagłosować na nie, odwiedź [witrynę opinii o Azure Logic Apps użytkownika](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (przełącznik instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)