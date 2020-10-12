---
title: Twórz zmienne do przechowywania i przekazywania wartości oraz zarządzaj nimi
description: Dowiedz się, jak przechowywać, zarządzać, używać i przekazywać wartości przy użyciu zmiennych w zautomatyzowanych zadaniach i przepływie pracy, które tworzysz przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333906"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Przechowywanie wartości i zarządzanie nimi przy użyciu zmiennych w usłudze Azure Logic Apps

W tym artykule przedstawiono sposób tworzenia i pracy ze zmiennymi używanymi do przechowywania wartości w aplikacji logiki. Na przykład zmienne mogą pomóc w śledzeniu, ile razy działa pętla. Aby wykonać iterację tablicy lub sprawdzić tablicę dla określonego elementu, można użyć zmiennej, aby odwołać się do numeru indeksu dla każdego elementu tablicy.

Można tworzyć zmienne dla typów danych, takich jak liczba całkowita, zmiennoprzecinkowa, wartość logiczna, ciąg, tablica i obiekt. Po utworzeniu zmiennej można wykonać inne zadania, na przykład:

* Pobierz lub odwołuje się do wartości zmiennej.
* Zwiększ lub Zmniejsz zmienną przez wartość stałą, znaną również jako *Zwiększ* i *Zmniejsz*.
* Przypisz inną wartość do zmiennej.
* Wstaw lub *Dołącz* wartość zmiennej jako ostatni element w ciągu lub tablicy.

Zmienne istnieją i są globalne tylko w ramach wystąpienia aplikacji logiki, które je tworzy. Ponadto zachowują się one między iteracjami pętli wewnątrz wystąpienia aplikacji logiki. Podczas odwoływania się do zmiennej, użyj nazwy zmiennej jako tokenu, a nie nazwy akcji, która jest zwykłym sposobem odwoływania się do danych wyjściowych akcji.

> [!IMPORTANT]
> Domyślnie cykle w pętli "for each" są uruchamiane równolegle. W przypadku używania zmiennych w pętlach Uruchom pętlę [sekwencyjnie](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) , aby zmienne zwracały przewidywalne wyniki.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której ma zostać utworzona zmienna

  Jeśli dopiero zaczynasz tworzyć aplikacje Logic Apps, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki

  Aby można było dodać akcje do tworzenia i pracy ze zmiennymi, aplikacja logiki musi rozpoczynać się od wyzwalacza.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Zainicjuj zmienną

Można utworzyć zmienną i zadeklarować jej typ danych i wartość początkową — wszystko w ramach jednej akcji w aplikacji logiki. Można zadeklarować zmienne tylko na poziomie globalnym, a nie w obrębie zakresów, warunków i pętli.

1. W [Azure Portal](https://portal.azure.com) lub w programie Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie zastosowano Azure Portal i aplikację logiki z istniejącym wyzwalaczem.

1. W aplikacji logiki w kroku, w którym chcesz dodać zmienną, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **nowy krok**.

     ![Zrzut ekranu pokazujący akcję "nowy krok" wybraną na stronie "projektant aplikacji logiki".](./media/logic-apps-create-variables-store-values/add-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `variables` jako filtr. Z listy Akcje wybierz pozycję **zainicjuj zmienną**.

   ![Wybierz akcję](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Podaj te informacje o zmiennej, zgodnie z poniższym opisem:

   | Właściwość | Wymagany | Wartość |  Opis |
   |----------|----------|-------|--------------|
   | **Nazwa** | Tak | <*Nazwa zmiennej*> | Nazwa zmiennej do zwiększenia |
   | **Typ** | Tak | <*Typ zmiennej*> | Typ danych dla zmiennej. |
   | **Wartość** | Nie | <*wartość początkowa*> | Początkowa wartość zmiennej <p><p>**Porada**: Chociaż opcjonalna, ustaw tę wartość jako najlepsze rozwiązanie, aby zawsze znać wartość początkową dla zmiennej. |
   |||||

   Na przykład:

   ![Zainicjuj zmienną](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Teraz Kontynuuj dodawanie żądanych akcji. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Jeśli przełączysz projektanta z edytorem widoku kodu, w tym miejscu zostanie wyświetlona Akcja **zainicjuj zmienną** w definicji aplikacji logiki, która jest w formacie JavaScript Object Notation (JSON):

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> Chociaż Akcja **zainicjuj zmienną** ma `variables` sekcję, która jest strukturą jako tablicą, akcja może utworzyć tylko jedną zmienną jednocześnie. Każda nowa zmienna wymaga pojedynczej akcji **inicjowania zmiennej** .

Oto przykłady dla innych typów zmiennych:

*Zmienna ciągu*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Zmienna logiczna*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Tablica z liczbami całkowitymi*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Tablica z ciągami*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Pobierz wartość zmiennej

Aby pobrać lub odwołać się do zawartości zmiennej, można również użyć [funkcji zmienne ()](../logic-apps/workflow-definition-language-functions-reference.md#variables) w Projektancie aplikacji logiki i edytorze widoku kodu. W przypadku odwoływania się do zmiennej Użyj nazwy zmiennej jako tokenu, a nie nazwy akcji, która jest zwykłym sposobem odwoływania się do danych wyjściowych akcji.

Na przykład to wyrażenie Pobiera elementy z zmiennej tablicowej [utworzonej wcześniej w tym artykule](#append-value) przy użyciu `variables()` funkcji. `string()`Funkcja zwraca zawartość zmiennej w formacie ciągu:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Zwiększ zmienną 

Aby *zwiększyć lub zwiększyć* zmienną przez wartość stałą, Dodaj akcję **zwiększania zmiennej** do aplikacji logiki. Ta akcja działa tylko w przypadku zmiennych liczb całkowitych i zmiennoprzecinkowych.

1. W Projektancie aplikacji logiki w kroku, w którym chcesz zwiększyć istniejącą zmienną, wybierz pozycję **nowy krok**. 

   Na przykład ta aplikacja logiki ma już wyzwalacz i akcję, która utworzyła zmienną. Dodaj nową akcję w ramach następujących kroków:

   ![Dodawanie akcji](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus (+). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź wartość "zmienna przyrostowa" jako filtr. Na liście Akcje wybierz pozycję **Zwiększ wartość zmiennej**.

   ![Wybierz akcję "Zwiększ zmienną"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Podaj te informacje w celu zwiększenia wartości zmiennej:

   | Właściwość | Wymagany | Wartość |  Opis |
   |----------|----------|-------|--------------|
   | **Nazwa** | Tak | <*Nazwa zmiennej*> | Nazwa zmiennej do zwiększenia |
   | **Wartość** | Nie | <*wartość przyrostu*> | Wartość używana do zwiększania wartości zmiennej. Wartość domyślna to 1. <p><p>**Porada**: Chociaż opcjonalna, ustaw tę wartość jako najlepsze rozwiązanie, aby zawsze znać konkretną wartość zwiększania zmiennej. |
   ||||

   Na przykład:

   ![Przykład wartości przyrostu](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Jeśli przełączysz projektanta z edytorem widoku kodu, Oto sposób, w jaki Akcja **zmiennej przyrostowej** jest wyświetlana w definicji aplikacji logiki, która jest w formacie JSON:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Przykład: Tworzenie licznika pętli

Zmienne są często używane do zliczania, ile razy działa pętla. Ten przykład pokazuje, jak tworzyć i używać zmiennych dla tego zadania, tworząc pętlę, która zlicza załączniki w wiadomości e-mail.

1. W Azure Portal Utwórz pustą aplikację logiki. Dodaj wyzwalacz, który sprawdza, czy są nowe wiadomości e-mail i załączniki.

   W tym przykładzie do **momentu odebrania nowej wiadomości e-mail**zostaje użyty wyzwalacz Office 365 Outlook. Można skonfigurować ten wyzwalacz do uruchamiania tylko wtedy, gdy wiadomość e-mail ma załączniki. Można jednak użyć dowolnego łącznika, który wyszukuje nowe wiadomości e-mail z załącznikami, takich jak łącznik Outlook.com.

1. Aby sprawdzić załączniki i przekazać te załączniki do przepływu pracy aplikacji logiki, wybierz pozycję **tak** dla tych właściwości:

   * **Zawiera załącznik**
   * **Uwzględnij załączniki**

   ![Sprawdź, czy istnieją załączniki i Dołącz do nich](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Dodaj akcję [ **zainicjuj zmienną** ](#create-variable). Utwórz zmienną całkowitą o nazwie o `Count` zerowej wartości początkowej.

   ![Dodaj akcję dla "Initialize variable"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Aby przechodzić przez każdy załącznik, Dodaj pętlę *for each* .

   1. W akcji **Inicjuj zmienną** wybierz pozycję **nowy krok**.

   1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź wartość `for each` jako filtr wyszukiwania, a następnie wybierz pozycję **dla każdej z nich**.

      ![Dodaj pętlę "for each"](./media/logic-apps-create-variables-store-values/add-loop.png)

1. W pętli kliknij wewnątrz pola **Wybierz dane wyjściowe z poprzednich kroków** . Gdy zostanie wyświetlona lista zawartości dynamicznej, wybierz pozycję **załączniki**.

   ![Wybieranie pozycji „Załączniki”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Właściwość **załączniki** przekazuje tablicę, która zawiera załączniki wiadomości e-mail z danych wyjściowych wyzwalacza do pętli.

1. W pętli **for each** wybierz pozycję **Dodaj akcję**.

   ![Wybierz pozycję "Dodaj akcję"](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. W polu wyszukiwania wprowadź wartość "zmienna przyrostowa" jako filtr. Z listy Akcje wybierz pozycję **Zwiększ wartość zmiennej**.

   > [!NOTE]
   > Upewnij się, że w pętli występuje Akcja **zmiennej przyrostowej** . Jeśli akcja pojawia się poza pętlą, przeciągnij akcję do pętli.

1. W akcji **zmiennej przyrostowej** z listy **Nazwa** wybierz zmienną **Count** .

   ![Wybierz zmienną "Count"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. W obszarze pętla Dodaj wszystkie akcje, które wysyłają liczbę załączników. W akcji Uwzględnij wartość ze zmiennej **Count** , na przykład:

   ![Dodaj akcję, która wysyła wyniki](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Jeśli aplikacja logiki nie jest włączona, w menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Włącz**.

1. Na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Uruchom**. Ten krok polega na ręcznym uruchomieniu aplikacji logiki.

1. Wyślij wiadomość e-mail z co najmniej jednym załącznikiem do konta e-mail użytego w tym przykładzie.

   Ten krok wyzwala wyzwalacz aplikacji logiki, który tworzy i uruchamia wystąpienie dla przepływu pracy aplikacji logiki. W efekcie aplikacja logiki wyśle wiadomość e-mail z komunikatem o liczbie załączników wysłanych wiadomości e-mail.

Jeśli przełączysz projektanta z edytorem widoku kodu, Oto, jak zostanie wyświetlona pętla **for each** wraz z akcją **zwiększania zmiennej** wewnątrz definicji aplikacji logiki, która jest w formacie JSON.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Zmniejsz zmienną

Aby zmniejszyć lub *zmniejszyć* zmienną przez wartość stałą, wykonaj kroki w celu [zwiększenia zmiennej](#increment-value) , z wyjątkiem tego, że zamiast tego można znaleźć i wybrać akcję **Zmniejsz zmienną** . Ta akcja działa tylko w przypadku zmiennych liczb całkowitych i zmiennoprzecinkowych.

Poniżej przedstawiono właściwości akcji **zmniejszania zmiennej** :

| Właściwość | Wymagany | Wartość |  Opis |
|----------|----------|-------|--------------|
| **Nazwa** | Tak | <*Nazwa zmiennej*> | Nazwa zmiennej do zmniejszenia | 
| **Wartość** | Nie | <*wartość przyrostu*> | Wartość zmniejszania zmiennej. Wartość domyślna to 1. <p><p>**Porada**: Chociaż opcjonalna, ustaw tę wartość jako najlepsze rozwiązanie, aby zawsze znać konkretną wartość zmniejszenia wartości zmiennej. |
||||| 

Jeśli przełączysz projektanta z edytorem widoku kodu, w tym miejscu zostanie wyświetlona Akcja **Zmniejsz zmienną** w definicji aplikacji logiki, która jest w formacie JSON.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>Ustaw zmienną

Aby przypisać inną wartość do istniejącej zmiennej, postępuj zgodnie z instrukcjami, aby [zwiększyć zmienną](#increment-value) z wyjątkiem tego, że:

1. W zamian Znajdź i wybierz akcję **Ustaw zmienną** .

1. Podaj nazwę zmiennej i wartość, którą chcesz przypisać. Obie nowe wartości i zmienne muszą mieć ten sam typ danych. Wartość jest wymagana, ponieważ ta akcja nie ma wartości domyślnej.

Poniżej przedstawiono właściwości dla akcji **Ustaw zmienną** :

| Właściwość | Wymagany | Wartość |  Opis |
|----------|----------|-------|--------------|
| **Nazwa** | Tak | <*Nazwa zmiennej*> | Nazwa zmiennej do zmiany |
| **Wartość** | Tak | <*Nowa wartość*> | Wartość, do której ma zostać przypisana zmienna. Oba muszą mieć ten sam typ danych. |
||||| 

> [!NOTE]
> O ile nie są zwiększane ani zmniejszane zmienne, zmiana zmiennych wewnątrz pętli *może* spowodować nieoczekiwane wyniki, ponieważ pętle są uruchamiane równolegle lub współbieżnie, domyślnie. W takich przypadkach spróbuj ustawić pętlę, aby uruchamiała się sekwencyjnie. Na przykład, gdy chcesz odwołać się do wartości zmiennej wewnątrz pętli i spodziewać się na początku i na końcu tego wystąpienia pętli, wykonaj następujące kroki, aby zmienić sposób działania pętli: 
>
> 1. W prawym górnym rogu pętli wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.
> 
> 2. W obszarze **Kontrola współbieżności**Zmień ustawienie **domyślne Przesłoń** na **włączone**.
>
> 3. Przeciągnij suwak **stopień równoległości** na **1**.

Jeśli przełączysz projektanta z edytorem widoku kodu, Oto sposób, w jaki Akcja **Ustaw zmienną** pojawia się w definicji aplikacji logiki, która jest w formacie JSON. Ten przykład zmienia `Count` bieżącą wartość zmiennej na inną wartość.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Dołącz do zmiennej

W przypadku zmiennych, które przechowują ciągi lub tablice, można wstawić lub *dołączyć* wartość zmiennej jako ostatni element w tych ciągach lub tablicach. Można postępować zgodnie z instrukcjami, aby [zwiększyć zmienną](#increment-value) , z tą różnicą, że w zamian wykonaj następujące czynności: 

1. Znajdź i wybierz jedną z tych akcji w zależności od tego, czy zmienna jest ciągiem lub tablicą: 

   * **Dołącz do zmiennej ciągu**
   * **Dołącz do zmiennej tablicowej** 

1. Podaj wartość, która ma zostać dołączona jako ostatni element w ciągu lub tablicy. Ta wartość jest wymagana.

Poniżej przedstawiono właściwości **dołączania do...** akcje:

| Właściwość | Wymagany | Wartość |  Opis |
|----------|----------|-------|--------------|
| **Nazwa** | Tak | <*Nazwa zmiennej*> | Nazwa zmiennej do zmiany |
| **Wartość** | Tak | <*Dołącz wartość*> | Wartość, która ma zostać dołączona, która może mieć dowolny typ |
|||||

Jeśli przełączysz projektanta z edytorem widoku kodu, w tym miejscu zostanie wyświetlona Akcja **Dołącz do zmiennej tablicowej** w definicji aplikacji logiki, która jest w formacie JSON. Ten przykład tworzy zmienną tablicową i dodaje kolejną wartość jako ostatni element w tablicy. Wynik jest zaktualizowaną zmienną, która zawiera tę tablicę: `[1,2,3,"red"]`

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat [łączników Logic Apps](../connectors/apis-list.md)
