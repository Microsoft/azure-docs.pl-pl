---
title: Dodawanie pętli do powtarzających się akcji
description: Tworzenie pętli, które powtarzają akcje przepływu pracy lub tablice procesów w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 88f1c88e721419bf944207b9c748b9250a25f428
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348070"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Tworzenie pętli, które powtarzają akcje przepływu pracy lub tablice procesów w usłudze Azure Logic Apps

Aby przetworzyć tablicę w aplikacji logiki, można utworzyć [pętlę "foreach"](#foreach-loop). Ta pętla powtarza jedną lub więcej akcji dla każdego elementu w tablicy. Aby ograniczyć liczbę elementów tablicy, które może przetworzyć pętlę "foreach", zobacz [limity współbieżności, zapętlania i departii](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Aby powtarzać akcje do momentu spełnienia warunku lub zmiany stanu, można utworzyć [pętlę "until"](#until-loop). Aplikacja logiki najpierw uruchamia wszystkie akcje wewnątrz pętli, a następnie sprawdza warunek lub stan. Jeśli warunek jest spełniony, pętla zostanie zatrzymana. W przeciwnym razie pętla powtarza się. Dla limitu liczby pętli "until", które mogą być uruchamiane przez aplikację logiki, zobacz [limity współbieżności, zapętlowania i departii](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

> [!TIP]
> Jeśli masz wyzwalacz, który odbiera tablicę i chcesz uruchomić przepływ pracy dla każdego elementu tablicy, możesz *departia* tę tablicę za pomocą [Właściwości wyzwalacza **SplitOn**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Pętla "foreach"

Pętla "foreach" powtarza jedną lub więcej akcji dla każdego elementu tablicy i działa tylko w tablicach. Poniżej przedstawiono niektóre zagadnienia dotyczące użycia pętli "foreach":

* Pętla "foreach" może przetwarzać ograniczoną liczbę elementów tablicy. W przypadku tego limitu należy zapoznać się z [limitami współbieżności, zapętlania i departii](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* Domyślnie iteracje w pętli "foreach" są uruchamiane w tym samym czasie lub równolegle. To zachowanie różni [się od automatyzowania w **celu zastosowania do każdej** pętli](/power-automate/apply-to-each) , w której iteracje są uruchamiane pojedynczo lub sekwencyjnie. Można jednak [skonfigurować sekwencyjne iteracje pętli "foreach"](#sequential-foreach-loop). Na przykład jeśli chcesz wstrzymać następną iterację w pętli "foreach" przy użyciu [akcji Opóźnij](../connectors/connectors-native-delay.md), musisz ustawić pętlę, aby uruchamiała się sekwencyjnie.

  Wyjątkiem zachowania domyślnego są zagnieżdżone pętle, w których iteracje są zawsze uruchamiane sekwencyjnie, a nie równolegle. Aby wykonać operacje równolegle dla elementów w pętli zagnieżdżonej, Utwórz i [Wywołaj podrzędną aplikację logiki](../logic-apps/logic-apps-http-endpoint.md).

* Aby uzyskać przewidywalne wyniki operacji na zmiennych w każdej iteracji pętli, Uruchom te pętle sekwencyjnie. Na przykład gdy zakończona współbieżnie uruchomiona pętla, zwiększanie, zmniejszanie i dołączanie do zmiennych operacji zwraca przewidywalne wyniki. Jednak podczas każdej iteracji w pętli współbieżnie działającej operacje te mogą zwracać nieprzewidywalne wyniki. 

* Akcje w pętli "foreach" używają [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
wyrażenie, aby odwoływać się do każdego elementu w tablicy i przetworzyć je. Jeśli określisz dane, które nie są w tablicy, przepływ pracy aplikacji logiki zakończy się niepowodzeniem. 

Ta przykładowa aplikacja logiki wysyła dzienne Podsumowanie dla kanału informacyjnego RSS witryny sieci Web. Aplikacja używa pętli "foreach", która wysyła wiadomość e-mail dla każdego nowego elementu.

1. [Utwórz tę przykładową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) przy użyciu konta Outlook.com lub konta służbowego.

2. Między wyzwalaczem RSS i akcją Wyślij wiadomość e-mail Dodaj pętlę "foreach". 

   1. Aby dodać pętlę między krokami, przesuń wskaźnik myszy nad strzałkę między tymi krokami. 
   Wybierz wyświetlony znak **Plus** ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

      ![Wybierz pozycję "Dodaj akcję"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. W polu wyszukiwania wybierz pozycję **Wszystko**. W polu wyszukiwania wpisz ciąg "for each" jako filtr. Z listy Akcje wybierz tę akcję: **dla każdej kontrolki**

      ![Dodaj pętlę "for each"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Teraz Skompiluj pętlę. W obszarze **Wybierz dane wyjściowe z poprzednich kroków** po wyświetleniu listy **Dodaj zawartość dynamiczną** wybierz tablicę **linki kanału informacyjnego** , która jest wyjściem z wyzwalacza RSS. 

   ![Wybierz z listy zawartości dynamicznej](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Można wybrać *tylko* dane wyjściowe tablicy z poprzedniego kroku.

   Wybrana tablica teraz zostanie wyświetlona tutaj:

   ![Wybierz tablicę](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Aby uruchomić akcję dla każdego elementu tablicy, przeciągnij akcję **Wyślij wiadomość e-mail** do pętli. 

   Aplikacja logiki może wyglądać podobnie do tego przykładu:

   ![Dodaj kroki do pętli "foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definicja pętli "foreach" (JSON)

Jeśli pracujesz w widoku kodu dla aplikacji logiki, możesz zdefiniować `Foreach` pętlę w definicji JSON aplikacji logiki, na przykład:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Pętla "foreach": sekwencyjne

Domyślnie cykle w pętli "foreach" są uruchamiane równolegle. Aby uruchomić każdy cykl sekwencyjnie, ustaw opcję **Sekwencyjna** pętla. Pętle "foreach" muszą być uruchamiane sekwencyjnie, gdy istnieją zagnieżdżone pętle lub zmienne wewnątrz pętli, w których oczekiwano przewidywalnych wyników. 

1. W prawym górnym rogu pętli wybierz pozycję **wielokropek** ( **...** ) > **Ustawienia**.

   ![W pętli "foreach" Wybierz pozycję "..." > "Ustawienia"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. W obszarze **Kontrola współbieżności** Zmień ustawienie **Kontrola współbieżności** **na włączone**. Przenieś suwak **stopień równoległości** na **1** , a następnie wybierz pozycję **gotowe**.

   ![Włącz kontrolę współbieżności](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Jeśli pracujesz z definicją JSON aplikacji logiki, możesz użyć `Sequential` opcji, dodając `operationOptions` parametr, na przykład:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Pętla "until"
  
Aby uruchamiać i powtarzać akcje do momentu spełnienia warunku lub zmiany stanu, należy umieścić te akcje w pętli "until". Aplikacja logiki najpierw uruchamia dowolną akcję i wszystkie akcje wewnątrz pętli, a następnie sprawdza warunek lub stan. Jeśli warunek jest spełniony, pętla zostanie zatrzymana. W przeciwnym razie pętla powtarza się. Dla limitu liczby pętli "until", które mogą być uruchamiane przez aplikację logiki, zobacz [limity współbieżności, zapętlowania i departii](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Poniżej przedstawiono kilka typowych scenariuszy, w których można użyć pętli "until":

* Wywoływanie punktu końcowego do momentu uzyskania odpowiedzi.

* Utwórz rekord w bazie danych. Zaczekaj, aż określone pole w tym rekordzie zostanie zatwierdzone. Kontynuuj przetwarzanie. 

Począwszy od 8:00 każdego dnia, Ta przykładowa aplikacja logiki zwiększa zmienną do momentu, gdy wartość zmiennej jest równa 10. Następnie aplikacja logiki wyśle wiadomość e-mail, która potwierdzi bieżącą wartość. 

> [!NOTE]
> W tych krokach jest używany pakiet Office 365 Outlook, ale można użyć dowolnego dostawcy poczty e-mail obsługiwanego przez Logic Apps. 
> [Sprawdź tutaj listę łączników](/connectors/). Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale interfejs użytkownika może wyglądać nieco inaczej. 

1. Tworzenia pustej aplikacji logiki. W Projektancie aplikacji logiki w polu wyszukiwania wybierz pozycję **wszystkie**. Wyszukaj ciąg "cykl". 
   Z listy Wyzwalacze wybierz pozycję Ten wyzwalacz: **cykliczny harmonogram**

   ![Dodaj wyzwalacz "cykl harmonogramu"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Określ, kiedy wyzwalany jest wyzwalacz, ustawiając interwał, częstotliwość i godzinę dnia. Aby ustawić godzinę, wybierz pozycję **Pokaż opcje zaawansowane**.

   ![Skonfiguruj harmonogram cyklu](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Właściwość | Wartość |
   | -------- | ----- |
   | **Interwał** | 1 | 
   | **Częstotliwość** | Dzień |
   | **W tych godzinach** | 8 |
   ||| 

1. W obszarze wyzwalacza wybierz pozycję **nowy krok**. 
   Wyszukaj frazę "zmienne" i wybierz tę akcję: **zainicjuj zmienne** zmiennych

   ![Dodaj akcję "Inicjuj zmienne-Variables"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Skonfiguruj swoją zmienną za pomocą następujących wartości:

   ![Ustaw właściwości zmiennej](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Właściwość | Wartość | Opis |
   | -------- | ----- | ----------- |
   | **Nazwa** | Limit | Nazwa zmiennej | 
   | **Typ** | Liczba całkowita | Typ danych zmiennej | 
   | **Wartość** | 0 | Wartość początkowa zmiennej | 
   |||| 

1. W akcji **Inicjuj zmienną** wybierz pozycję **nowy krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. Wyszukaj "until" i wybierz tę akcję: **until-Control**

   ![Dodaj pętlę "until"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Skompiluj warunek zakończenia pętli, wybierając zmienną **limitu** i operator **is równa się** . 
   Wprowadź **10** jako wartość porównania.

   ![Warunek wyjścia kompilacji dla pętli zatrzymania](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Wewnątrz pętli wybierz pozycję **Dodaj akcję**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. Wyszukaj frazę "zmienne" i wybierz tę akcję: **Zwiększ zmienne zmiennych**

   ![Dodaj akcję zwiększającą zmienną](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. W polu **Nazwa** wybierz zmienną **Limit** . Dla **wartości wprowadź wartość** "1". 

     ![Zwiększ wartość "limit" o 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Na zewnątrz i poniżej pętli wybierz pozycję **nowy krok**. 

1. W polu wyszukiwania wybierz pozycję **Wszystko**. 
     Znajdź i Dodaj akcję, która wysyła wiadomość e-mail, na przykład: 

     ![Dodaj akcję, która wysyła wiadomość e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. W razie potrzeby zaloguj się do swojego konta e-mail.

1. Ustaw właściwości akcji poczty e-mail. Dodaj zmienną **limitu** do tematu. Dzięki temu można potwierdzić, że bieżąca wartość zmiennej spełnia określony warunek, na przykład:

      ![Konfigurowanie właściwości poczty e-mail](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Właściwość | Wartość | Opis |
      | -------- | ----- | ----------- | 
      | **Do** | *\<email-address\@domain>* | Adres e-mail adresata. Na potrzeby testowania użyj własnego adresu e-mail. | 
      | **Temat** | Bieżąca wartość **limitu** | Określ temat wiadomości e-mail. Na potrzeby tego przykładu upewnij się, że dołączysz zmienną **limitu** . | 
      | **Treść** | <*Poczta e-mail — zawartość*> | Określ zawartość wiadomości e-mail, która ma zostać wysłana. Na potrzeby tego przykładu wpisz dowolny tekst. | 
      |||| 

1. Zapisz aplikację logiki. Aby ręcznie przetestować aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

      Po rozpoczęciu działania logiki otrzymujesz wiadomość e-mail z określoną zawartością:

      ![Odebrana wiadomość e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zapobiegaj pętlom nieskończonym

Pętla "until" ma domyślne limity, które przerywają wykonywanie w przypadku wystąpienia dowolnego z następujących warunków:

| Właściwość | Wartość domyślna | Opis | 
| -------- | ------------- | ----------- | 
| **Liczbą** | 60 | Największa liczba pętli, które są uruchamiane przed wyjściem z pętli. Wartość domyślna to 60 cykli. | 
| **Limit czasu** | PT1H | Najwięcej czasu na uruchomienie pętli przed wyjściem z pętli. Wartość domyślna to jedna godzina i jest określona w formacie ISO 8601. <p>Wartość limitu czasu jest obliczana dla każdego cyklu pętli. Jeśli jakakolwiek akcja w pętli trwa dłużej niż limit czasu, bieżący cykl nie zostanie zatrzymany. Jednak następny cykl nie zostanie uruchomiony, ponieważ warunek limitu nie jest spełniony. | 
|||| 

Aby zmienić te domyślne limity, wybierz pozycję **Pokaż opcje zaawansowane** w kształcie akcja pętla.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definicja "until" (JSON)

Jeśli pracujesz w widoku kodu dla aplikacji logiki, możesz zdefiniować `Until` pętlę w definicji JSON aplikacji logiki, na przykład:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

Ta przykładowa pętla "until" wywołuje punkt końcowy HTTP, który tworzy zasób. Pętla jest zatrzymywana, gdy treść odpowiedzi HTTP zostanie zwrócona ze `Completed` stanem. Aby zapobiec nieskończonym pętlom, pętla również zostaje zatrzymana w przypadku wystąpienia dowolnego z następujących warunków:

* Pętla przeprowadziła 10 razy w sposób określony przez `count` atrybut. Wartość domyślna to 60 razy. 

* Pętla była uruchamiana przez dwie godziny, zgodnie z definicją `timeout` atrybutu w formacie ISO 8601. Wartość domyślna to jedna godzina.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać lub zagłosować na temat funkcji i sugestii, [Azure Logic Apps witrynie opinii użytkowników](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (przełącznik instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie pogrupowanego stanu akcji (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
