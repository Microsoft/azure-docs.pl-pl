---
title: Wykonywanie operacji na danych
description: Konwertowanie i formatowanie danych wyjściowych i formatów oraz zarządzanie nimi w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710375"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Wykonywanie operacji na danych w Azure Logic Apps

W tym artykule przedstawiono sposób pracy z danymi w aplikacjach logiki przez dodanie akcji dla tych zadań i nie tylko:

* Utwórz tabele na podstawie tablic.
* Utwórz tablice z innych tablic na podstawie warunku.
* Utwórz przyjazne dla użytkownika tokeny z właściwości obiektu JavaScript Object Notation (JSON), aby można było łatwo korzystać z tych właściwości w przepływie pracy.

Jeśli nie znajdziesz akcji, którą chcesz wykonać, spróbuj przeglądać wiele różnych [funkcji manipulowania danymi](../logic-apps/workflow-definition-language-functions-reference.md) , które udostępnia Azure Logic Apps.

Te tabele podsumowują operacje na danych, których można użyć i są zorganizowane na podstawie typów danych źródłowych, na których działają operacje, ale każdy opis pojawia się alfabetycznie.

**Akcje tablicy** 

Te akcje pomagają w pracy z danymi w tablicach.

| Akcja | Opis |
|--------|-------------|
| [**Utwórz tabelę CSV**](#create-csv-table-action) | Utwórz tabelę wartości rozdzielanych przecinkami (CSV) z tablicy. |
| [**Utwórz tabelę HTML**](#create-html-table-action) | Utwórz tabelę HTML z tablicy. |
| [**Filtruj tablicę**](#filter-array-action) | Utwórz podzestaw tablicy z tablicy na podstawie określonego filtru lub warunku. |
| [**Join**](#join-action) | Utwórz ciąg ze wszystkich elementów w tablicy i oddziel każdy element z określonym znakiem. |
| [**Wybierz**](#select-action) | Utwórz tablicę na podstawie określonych właściwości dla wszystkich elementów w innej tablicy. |
||| 

**Akcje JSON**

Te akcje pomagają w pracy z danymi w formacie JavaScript Object Notation (JSON).

| Akcja | Opis |
|--------|-------------|
| [**Redagowanie**](#compose-action) | Utwórz wiadomość lub ciąg z wielu wejść, które mogą mieć różne typy danych. Następnie można użyć tego ciągu jako pojedynczej wartości wejściowej, zamiast wielokrotnie wprowadzać te same dane wejściowe. Na przykład można utworzyć pojedynczy komunikat JSON z różnych danych wejściowych. |
| [**Analiza JSON**](#parse-json-action) | Utwórz przyjazne dla użytkownika tokeny danych dla właściwości w zawartości JSON, aby łatwiej używać właściwości w aplikacjach logiki. |
|||

Aby utworzyć bardziej złożone przekształcenia JSON, zobacz [wykonywanie zaawansowanych transformacji JSON przy użyciu szablonów płynnych](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której jest potrzebna operacja do pracy z danymi

  Jeśli dopiero zaczynasz tworzyć aplikacje Logic Apps, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki 

  Operacje na danych są dostępne tylko jako akcje, więc zanim będzie można użyć tych akcji, uruchom aplikację logiki z wyzwalaczem i Uwzględnij inne akcje wymagane do utworzenia żądanych danych wyjściowych.

<a name="compose-action"></a>

## <a name="compose-action"></a>Akcja redagowania

Aby skonstruować pojedyncze dane wyjściowe, takie jak obiekt JSON z wielu danych wejściowych, można **użyć akcji tworzenia** . Dane wejściowe mogą mieć różne typy, takie jak liczby całkowite, wartości logiczne, tablice, obiekty JSON, oraz każdy inny typ natywny, który Azure Logic Apps obsługuje, na przykład Binary i XML. Następnie można użyć danych wyjściowych w akcjach, które po akcji **Utwórz** . Akcja **redagowania** umożliwia także zapisanie danych przez wielokrotne wprowadzanie tych samych danych wejściowych podczas tworzenia przepływu pracy aplikacji logiki.

Na przykład można skonstruować komunikat JSON z wielu zmiennych, takich jak zmienne ciągów, które przechowują imiona i nazwiska osób, a także zmienną całkowitą przechowującą wiek osób. W tym miejscu Akcja **redagowania** akceptuje następujące dane wejściowe:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

i tworzy następujące dane wyjściowe:

`{"age":35,"fullName":"Owens,Sophie"}`

Aby wypróbować przykład, wykonaj następujące kroki przy użyciu projektanta aplikacji logiki. Lub, jeśli wolisz pracować w edytorze widoku kodu, możesz **skopiować przykładowe definicje** akcji tworzenia i **inicjowania zmiennych** z tego artykułu do własnej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — Tworzenie](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie zastosowano Azure Portal i aplikację logiki z wyzwalaczem **cyklu** oraz kilka akcji **inicjowania zmiennych** . Te akcje są konfigurowane do tworzenia dwóch zmiennych ciągów i zmiennej typu Integer. Podczas późniejszego testowania aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Zredaguj"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. W aplikacji logiki, w której chcesz utworzyć dane wyjściowe, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **nowy krok**.

     ![Wybierz pozycję "nowy krok" dla akcji "Zredaguj"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `compose` jako filtr. Z listy Akcje wybierz akcję **Zredaguj** .

   ![Wybierz akcję "Zredaguj"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. W polu **dane wejściowe** podaj dane wejściowe do utworzenia danych wyjściowych.

   Na potrzeby tego przykładu po kliknięciu wewnątrz pola **dane wejściowe** zostanie wyświetlona lista zawartość dynamiczna, aby można było wybrać wcześniej utworzone zmienne:

   ![Wybierz dane wejściowe do użycia dla akcji "Zredaguj"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Oto zakończono Przykładowa Akcja **redagowania** : 

   ![Zakończono przykład akcji "Zredaguj"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w źródłowej definicji przepływu pracy, zobacz [Akcja redagowania](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy Akcja **redagowania** powoduje utworzenie oczekiwanych wyników, Wyślij do siebie powiadomienie zawierające dane wyjściowe akcji **Utwórz** .

1. W aplikacji logiki Dodaj akcję, która może wysyłać wyniki z akcji **redagowania** .

1. W tej akcji kliknij w dowolnym miejscu, w którym mają być wyświetlane wyniki. Gdy zostanie otwarta lista zawartości dynamicznej, w obszarze Akcja **redagowanie** wybierz pozycję **dane wyjściowe**.

   W tym przykładzie użyta zostanie Akcja **Wyślij wiadomość e-mail** i uwzględniono pola **wyjściowe** w treści i temacie wiadomości e-mail:

   ![Pola "output" dla akcji "Zredaguj"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   W oparciu o używany łącznik poczty e-mail znajdują się następujące wyniki:

   ![Wyślij wiadomość e-mail z wynikami akcji "redagowania"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Akcja tworzenia tabeli CSV

Aby utworzyć tabelę wartości rozdzielanych przecinkami (CSV), która zawiera właściwości i wartości z obiektów JavaScript Object Notation (JSON) w tablicy, użyj akcji **Utwórz tabelę CSV** . Następnie można użyć tabeli w akcjach, które są zgodne z akcją **Utwórz tabelę CSV** .

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować przykłady **Tworzenie tabeli CSV** i **zainicjuj zmienne** definicje akcji z tego artykułu do własnej definicji przepływu pracy aplikacji logiki. [przykłady kodu operacji danych — Tworzenie tabeli CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie zastosowano Azure Portal i aplikację logiki z wyzwalaczem **cyklu** i akcją **inicjowania zmiennej** . Akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest tablicą z właściwościami i wartościami w formacie JSON. Podczas późniejszego testowania aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Utwórz tabelę CSV"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. W aplikacji logiki, w której chcesz utworzyć tabelę CSV, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **nowy krok**.

     ![Wybierz pozycję "nowy krok" dla akcji "Utwórz tabelę CSV"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `create csv table` jako filtr. Z listy Akcje wybierz akcję **Utwórz tabelę CSV** .

   ![Wybierz akcję "Utwórz tabelę CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. W polu **od** Podaj tablicę lub wyrażenie, dla których chcesz utworzyć tabelę.

   Na potrzeby tego przykładu, gdy klikniesz **wewnątrz pola,** zostanie wyświetlona lista zawartości dynamicznej, aby można było wybrać wcześniej utworzoną zmienną:

   ![Wybierz dane wyjściowe macierzy do tworzenia tabeli CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Aby utworzyć przyjazne dla użytkownika tokeny dla właściwości w obiektach JSON, aby można było wybrać te właściwości jako dane wejściowe, przed wywołaniem akcji **Utwórz tabelę CSV** Użyj [składni JSON analizy](#parse-json-action) .

   Oto przykład gotowej akcji **Utwórz tabelę CSV** : 

   ![Zakończono przykład akcji "Utwórz tabelę CSV"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="customize-table-format"></a>Dostosuj Format tabeli

Domyślnie właściwość **Columns** jest ustawiona na automatyczne tworzenie kolumn tabeli na podstawie elementów tablicy. Aby określić niestandardowe nagłówki i wartości, wykonaj następujące kroki:

1. Otwórz listę **kolumny** i wybierz pozycję **niestandardowe**.

1. We właściwości **nagłówka** Określ niestandardowy tekst nagłówka, który ma być używany.

1. We właściwości **Value (wartość** ) Określ wartość niestandardową, która ma zostać użyta.

Aby zwrócić wartości z tablicy, można użyć [ `item()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#item) z akcją **Utwórz tabelę CSV** . W `For_each` pętli można użyć [ `items()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#items).

Załóżmy na przykład, że chcesz, aby kolumny tabeli miały tylko wartości właściwości, a nie nazwy właściwości z tablicy. Aby zwrócić tylko te wartości, wykonaj następujące kroki w widoku projektanta lub w widoku kodu. Oto wynik zwracany przez ten przykład:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Pracuj w widoku projektanta

W akcji Zachowaj pustą kolumnę **nagłówka** . W każdym wierszu w kolumnie **Value** odwołuje się do każdej potrzebnej właściwości tablicy. Każdy wiersz w obszarze **wartość** zwraca wszystkie wartości dla określonej właściwości tablicy i zostaje kolumną w tabeli.

1. W obszarze **wartość**w każdym wierszu, który chcesz, kliknij wewnątrz pola edycji, aby wyświetlić listę zawartości dynamicznej.

1. Z listy zawartość dynamiczna wybierz pozycję **wyrażenie**.

1. W edytorze wyrażeń wprowadź to wyrażenie, które określa żądaną wartość właściwości tablicy, a następnie wybierz **przycisk OK**.

   `item()?['<array-property-name>']`

   Na przykład:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Dereferencja "Description" dla "Create CSV Table"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Powtórz poprzednie kroki dla każdej potrzebnej właściwości tablicy. Gdy skończysz, Twoja akcja będzie wyglądać następująco:

   ![Funkcja "Item ()" w temacie "Create CSV Table"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Aby rozwiązać wyrażenia do bardziej opisowych wersji, przełącz się do widoku kodu i wróć do widoku projektanta, a następnie ponownie otwórz zwiniętej akcji:

   Akcja **Utwórz tabelę CSV** będzie teraz wyglądać następująco:

   !["Create CSV Table" — rozpoznane wyrażenia, brak nagłówków](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Pracuj w widoku kodu

W definicji JSON akcji w `columns` tablicy Ustaw `header` Właściwość na pusty ciąg. Dla każdej `value` właściwości należy odwoływać się do każdej właściwości tablicy, która ma zostać wybrana.

1. Na pasku narzędzi projektanta wybierz **Widok kod**.

1. W edytorze kodu w `columns` tablicy akcji Dodaj pustą `header` Właściwość i to `value` wyrażenie dla każdej kolumny wartości tablicy, które chcesz:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Na przykład:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Przełącz się z powrotem do widoku projektanta i ponownie otwórz zwiniętej akcji.

   Akcja **Utwórz tabelę CSV** będzie teraz wyświetlana jak w tym przykładzie, a wyrażenia zostały rozwiązane z bardziej opisowymi wersjami:

   !["Create CSV Table" — rozpoznane wyrażenia i brak nagłówków](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Aby uzyskać więcej informacji na temat tej akcji w źródłowej definicji przepływu pracy, zobacz [Akcja tabeli](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby sprawdzić, czy Akcja **Utwórz tabelę CSV** tworzy oczekiwane wyniki, Wyślij do siebie powiadomienie zawierające dane wyjściowe akcji **Utwórz tabelę CSV** .

1. W aplikacji logiki Dodaj akcję, która może wysyłać wyniki z akcji **Utwórz tabelę CSV** .

1. W tej akcji kliknij w dowolnym miejscu, w którym mają być wyświetlane wyniki. Gdy zostanie otwarta lista zawartości dynamicznej, w obszarze Akcja **Utwórz tabelę CSV** wybierz pozycję **dane wyjściowe**. 

   W tym przykładzie zostanie użyta akcja **Wyślij wiadomość e-mail** w programie Outlook 365 i zawiera pole **dane wyjściowe** w treści wiadomości e-mail:

   ![Pola "output" dla akcji "Utwórz tabelę CSV"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   W oparciu o używany łącznik poczty e-mail znajdują się następujące wyniki:

   ![Wyślij wiadomość e-mail z wynikami akcji "Utwórz tabelę CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Akcja tworzenia tabeli HTML

Aby utworzyć tabelę HTML zawierającą właściwości i wartości z obiektów JavaScript Object Notation (JSON) w tablicy, użyj akcji **Utwórz tabelę HTML** . Następnie można użyć tabeli w akcjach, które są zgodne z akcją **Tworzenie tabeli HTML** .

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować przykładowe definicje akcji **Create HTML** i **Initialize Variable** z tego artykułu do własnej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — Tworzenie tabeli HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie zastosowano Azure Portal i aplikację logiki z wyzwalaczem **cyklu** i akcją **inicjowania zmiennej** . Akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest tablicą z właściwościami i wartościami w formacie JSON. Podczas późniejszego testowania aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla "Create HTML Table"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. W aplikacji logiki, w której chcesz utworzyć tabelę HTML, wykonaj jedną z następujących czynności:

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **nowy krok**.

     ![Wybierz pozycję "nowy krok" dla akcji "Utwórz tabelę HTML"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `create html table` jako filtr. Z listy Akcje wybierz akcję **Utwórz tabelę HTML** .

   ![Wybierz akcję "Utwórz tabelę HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. W polu **od** Podaj tablicę lub wyrażenie, dla których chcesz utworzyć tabelę.

   Na potrzeby tego przykładu, gdy klikniesz **wewnątrz pola,** zostanie wyświetlona lista zawartości dynamicznej, aby można było wybrać wcześniej utworzoną zmienną:

   ![Wybierz dane wyjściowe macierzy do tworzenia tabeli HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Aby utworzyć przyjazne dla użytkownika tokeny dla właściwości w obiektach JSON, aby można było wybrać te właściwości jako dane wejściowe, przed wywołaniem akcji **Utwórz tabelę HTML** Użyj [składni JSON analizy](#parse-json-action) .

   Oto przykład gotowej akcji **Create HTML Table** :

   ![Zakończono przykład dla "Create HTML Table"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="customize-table-format"></a>Dostosuj Format tabeli

Domyślnie właściwość **Columns** jest ustawiona na automatyczne tworzenie kolumn tabeli na podstawie elementów tablicy. Aby określić niestandardowe nagłówki i wartości, wykonaj następujące kroki:

1. Otwórz listę **kolumny** i wybierz pozycję **niestandardowe**.

1. We właściwości **nagłówka** Określ niestandardowy tekst nagłówka, który ma być używany.

1. We właściwości **Value (wartość** ) Określ wartość niestandardową, która ma zostać użyta.

Aby zwrócić wartości z tablicy, można użyć [ `item()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#item) z akcją **Utwórz tabelę HTML** . W `For_each` pętli można użyć [ `items()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#items).

Załóżmy na przykład, że chcesz, aby kolumny tabeli miały tylko wartości właściwości, a nie nazwy właściwości z tablicy. Aby zwrócić tylko te wartości, wykonaj następujące kroki w widoku projektanta lub w widoku kodu. Oto wynik zwracany przez ten przykład:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Pracuj w widoku projektanta

W akcji Zachowaj pustą kolumnę **nagłówka** . W każdym wierszu w kolumnie **Value** odwołuje się do każdej potrzebnej właściwości tablicy. Każdy wiersz w obszarze **wartość** zwraca wszystkie wartości dla określonej właściwości i zostaje kolumną w tabeli.

1. W obszarze **wartość**w każdym wierszu, który chcesz, kliknij wewnątrz pola edycji, aby wyświetlić listę zawartości dynamicznej.

1. Z listy zawartość dynamiczna wybierz pozycję **wyrażenie**.

1. W edytorze wyrażeń wprowadź to wyrażenie, które określa żądaną wartość właściwości tablicy, a następnie wybierz **przycisk OK**.

   `item()?['<array-property-name>']`

   Na przykład:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Właściwość dereference w akcji "Create HTML Table"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Powtórz poprzednie kroki dla każdej potrzebnej właściwości tablicy. Gdy skończysz, Twoja akcja będzie wyglądać następująco:

   ![Funkcja "Item ()" w temacie "Create HTML Table"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Aby rozwiązać wyrażenia do bardziej opisowych wersji, przełącz się do widoku kodu i wróć do widoku projektanta, a następnie ponownie otwórz zwiniętej akcji:

   Akcja **Utwórz tabelę HTML** będzie teraz wyglądać następująco:

   !["Tworzenie tabeli HTML" — rozpoznane wyrażenia, brak nagłówków](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Pracuj w widoku kodu

W definicji JSON akcji w `columns` tablicy Ustaw `header` Właściwość na pusty ciąg. Dla każdej `value` właściwości należy odwoływać się do każdej właściwości tablicy, która ma zostać wybrana.

1. Na pasku narzędzi projektanta wybierz **Widok kod**.

1. W edytorze kodu w `columns` tablicy akcji Dodaj pustą `header` Właściwość i to `value` wyrażenie dla każdej kolumny wartości tablicy, które chcesz:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Na przykład:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Przełącz się z powrotem do widoku projektanta i ponownie otwórz zwiniętej akcji.

   Akcja **Utwórz tabelę HTML** będzie teraz wyświetlana jak w tym przykładzie, a wyrażenia zostały rozwiązane z bardziej opisowymi wersjami:

   !["Tworzenie tabeli HTML" — rozpoznane wyrażenia i brak nagłówków](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Aby uzyskać więcej informacji na temat tej akcji w źródłowej definicji przepływu pracy, zobacz [Akcja tabeli](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby sprawdzić, czy Akcja **Utwórz tabelę HTML** tworzy oczekiwane wyniki, Wyślij do siebie powiadomienie zawierające dane wyjściowe akcji **Utwórz tabelę HTML** .

1. W aplikacji logiki Dodaj akcję, która może wysyłać wyniki z akcji **tworzenia tabeli HTML** .

1. W tej akcji kliknij w dowolnym miejscu, w którym mają być wyświetlane wyniki. Po otwarciu listy zawartości dynamicznej w obszarze akcji **Utwórz tabelę HTML** wybierz pozycję **dane wyjściowe**. 

   W tym przykładzie zostanie użyta akcja **Wyślij wiadomość e-mail** w programie Outlook 365 i zawiera pole **dane wyjściowe** w treści wiadomości e-mail:

   ![Pola "output" dla "Create HTML Table"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > W przypadku dołączania danych wyjściowych tabeli HTML do akcji poczty e-mail upewnij się, że właściwość **is HTML została** ustawiona na **wartość tak** w opcjach zaawansowanych akcji poczty e-mail. Dzięki temu akcja e-mail prawidłowo sformatuje tabelę HTML.

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   W oparciu o używany łącznik poczty e-mail znajdują się następujące wyniki:

   ![Wiadomość e-mail z wynikami "Tworzenie tabeli HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Akcja filtrowania tablicy

Aby utworzyć mniejszą tablicę zawierającą elementy, które spełniają określone kryteria, z istniejącej tablicy, użyj akcji **filtrowania tablicy** . Następnie można użyć filtrowanej tablicy w akcjach, które po akcji **Filtruj tablicę** .

> [!NOTE]
> W każdym tekście filtru używanym w warunku jest uwzględniana wielkość liter. Ponadto ta akcja nie może zmienić formatu ani składników elementów w tablicy. 
> 
> Aby akcje używały danych wyjściowych macierzy z akcji **filtrowania tablicy** , te akcje muszą akceptować tablice jako dane wejściowe lub trzeba przekształcić tablicę wyjściową w inny zgodny format.
> 
> Jeśli wywołasz punkt końcowy HTTP i otrzymasz odpowiedź JSON, użyj akcji **Analizuj dane JSON** , aby przetworzyć odpowiedź JSON. 
> W przeciwnym razie Akcja **Filtruj tablicę** może odczytać tylko treść odpowiedzi, a nie strukturę ładunku JSON.

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować przykładową **tablicę filtrów** i **zainicjuj zmienne** definicje akcji z tego artykułu do podstawowej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — tablica filtrów](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie zastosowano Azure Portal i aplikację logiki z wyzwalaczem **cyklu** i akcją **inicjowania zmiennej** . Akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest tablicą zawierającą Przykładowe liczby całkowite. Podczas późniejszego testowania aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   > [!NOTE]
   > Chociaż w tym przykładzie używana jest prosta tablica liczb całkowitych, ta akcja jest szczególnie przydatna w przypadku tablic obiektów JSON, w których można filtrować na podstawie właściwości i wartości obiektów.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Filtruj tablicę"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. W aplikacji logiki, w której chcesz utworzyć przefiltrowaną tablicę, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **nowy krok**.

     ![Wybierz pozycję "nowy krok" dla akcji "Filtruj tablicę"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź `filter array` jako filtr. Z listy Akcje wybierz akcję **Filtruj tablicę** .

   ![Wybierz akcję "Filtruj tablicę"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. W polu **od** Podaj tablicę lub wyrażenie, które chcesz filtrować.

   Na potrzeby tego przykładu, gdy klikniesz **wewnątrz pola,** zostanie wyświetlona lista zawartości dynamicznej, aby można było wybrać wcześniej utworzoną zmienną:

   ![Wybierz tablicę wyjściową dla tworzenia filtrowanej tablicy](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Dla warunku Określ elementy tablicy do porównania, wybierz operator porównania i określ wartość porównania.

   W tym przykładzie funkcja jest wykorzystywana `item()` do uzyskiwania dostępu do każdego elementu w tablicy, podczas gdy akcja **Filtruj tablicę** wyszukuje elementy tablicy, których wartość jest większa niż jeden:

   ![Zakończono przykład akcji "Filtruj tablicę"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w źródłowej definicji przepływu pracy, zobacz [Akcja zapytania](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby potwierdzić, czy Akcja **Filtruj tablicę** tworzy oczekiwane wyniki, Wyślij do siebie powiadomienie zawierające dane wyjściowe akcji **Filtruj tablicę** .

1. W aplikacji logiki Dodaj akcję, która może wysyłać wyniki z akcji **filtrowania tablicy** .

1. W tej akcji kliknij w dowolnym miejscu, w którym mają być wyświetlane wyniki. Po otwarciu listy zawartości dynamicznej wybierz pozycję **wyrażenie**. Aby uzyskać dane wyjściowe macierzy z akcji **filtrowania tablicy** , wprowadź to wyrażenie, które zawiera nazwę akcji **filtrowania tablicy** :

   `@actionBody('Filter_array')`

   W tym przykładzie zostanie użyta akcja **Wyślij wiadomość e-mail** w programie Outlook 365, która zawiera dane wyjściowe z wyrażenia **actionBody ("Filter_array")** w treści wiadomości e-mail:

   ![Wyniki akcji z akcji "Filtruj tablicę"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   W oparciu o używany łącznik poczty e-mail znajdują się następujące wyniki:

   ![Wyślij wiadomość e-mail z wynikami akcji "Filtruj tablicę"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Akcja łączenia

Aby utworzyć ciąg zawierający wszystkie elementy z tablicy i oddzielić te elementy o określonym znaku ogranicznika, użyj akcji **sprzężenia** . Następnie można użyć ciągu w akcjach, które po akcji **Join** .

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować przykładowe operacje **Join** i **Initialize** z tego artykułu do odpowiedniej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — sprzężenie](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie zastosowano Azure Portal i aplikację logiki z wyzwalaczem **cyklu** i akcją **inicjowania zmiennej** . Ta akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest tablicą zawierającą Przykładowe liczby całkowite. Po przetestowaniu aplikacji logiki później można uruchomić aplikację ręcznie bez czekania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "join"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. W aplikacji logiki, w której chcesz utworzyć ciąg z tablicy, wykonaj jedną z następujących czynności:

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **nowy krok**.

     ![SSelect "nowy krok" dla akcji "join"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź `join` jako filtr. Z listy Akcje wybierz pozycję Ta akcja: **Dołącz**

   ![Wybierz akcję "Dołącz"](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. W polu **od** Podaj tablicę zawierającą elementy, które chcesz dołączyć jako ciąg.

   Na potrzeby tego przykładu po kliknięciu wewnątrz pola **z** listy zawartość dynamiczna zostanie wyświetlona, aby można było wybrać wcześniej utworzoną zmienną:  

   ![Wybierz dane wyjściowe macierzy do utworzenia ciągu](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. W polu **Dołącz do** wprowadź znak, który ma zostać oddzielone dla każdego elementu tablicy. 

   W tym przykładzie zastosowano dwukropek (:) jako separator.

   ![Podaj znak separatora](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w źródłowej definicji przepływu pracy, zobacz [akcję Join](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby sprawdzić, czy Akcja **łączenia** tworzy oczekiwane wyniki, Wyślij do siebie powiadomienie zawierające dane wyjściowe akcji **sprzężenia** .

1. W aplikacji logiki Dodaj akcję, która może wysyłać wyniki z akcji **Dołącz** .

1. W tej akcji kliknij w dowolnym miejscu, w którym mają być wyświetlane wyniki. Po otwarciu listy zawartości dynamicznej w obszarze akcji **sprzężenia** wybierz pozycję **dane wyjściowe**. 

   W tym przykładzie zostanie użyta akcja **Wyślij wiadomość e-mail** w programie Outlook 365 i zawiera pole **dane wyjściowe** w treści wiadomości e-mail:

   ![Pola "output" dla akcji "join"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   W oparciu o używany łącznik poczty e-mail znajdują się następujące wyniki:

   ![Wyślij wiadomość e-mail z wynikami akcji "join"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Analiza akcji JSON

Aby uzyskać odwołanie lub uzyskać dostęp do właściwości w zawartości JavaScript Object Notation (JSON), możesz utworzyć przyjazne dla użytkownika pola lub tokeny dla tych właściwości przy użyciu akcji **Przeanalizuj dane JSON** . Dzięki temu można wybrać te właściwości z listy zawartości dynamicznej po określeniu danych wejściowych dla aplikacji logiki. Dla tej akcji można dostarczyć schemat JSON lub wygenerować schemat JSON z przykładowej zawartości lub ładunku JSON.

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować przykładowe definicje akcji **Przeanalizuj dane JSON** i **zainicjuj zmienne** z tego artykułu do własnej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — Analiza JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie zastosowano Azure Portal i aplikację logiki z wyzwalaczem **cyklu** i akcją **inicjowania zmiennej** . Akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest obiektem JSON, który ma właściwości i wartości. Podczas późniejszego testowania aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Analizuj dane JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. W aplikacji logiki, w której chcesz analizować zawartość JSON, wykonaj jedną z następujących czynności:

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **nowy krok**.

     ![Wybierz pozycję "nowy krok" dla akcji "Analiza JSON"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź `parse json` jako filtr. Z listy Akcje wybierz akcję **Przeanalizuj dane JSON** .

   ![Wybierz akcję "Analizuj dane JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. W polu **zawartość** Podaj zawartość JSON, którą chcesz przeanalizować.

   Na potrzeby tego przykładu po kliknięciu wewnątrz pola **zawartość** zostanie wyświetlona lista zawartość dynamiczna, aby można było wybrać wcześniej utworzoną zmienną:

   ![Wybierz obiekt JSON dla akcji Przeanalizuj dane JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Wprowadź schemat JSON, który opisuje przeanalizowany zawartość JSON.

   W tym przykładzie przedstawiono schemat JSON:

   ![Podaj schemat JSON dla obiektu JSON, który chcesz przeanalizować](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Jeśli nie masz schematu, możesz wygenerować ten schemat z zawartości lub *ładunku*JSON. 
   
   1. W akcji **Przeanalizuj dane JSON** wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W obszarze **wprowadzanie lub wklejanie przykładowego ładunku JSON**Podaj zawartość w formacie JSON, a następnie wybierz pozycję **gotowe**.

      ![Wprowadź zawartość JSON w celu wygenerowania schematu](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w źródłowej definicji przepływu pracy, zobacz [Analiza JSON akcja](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby sprawdzić, czy Akcja **Przeanalizuj dane JSON** powoduje utworzenie oczekiwanych wyników, Wyślij do siebie powiadomienie zawierające dane wyjściowe akcji **analizy JSON** .

1. W aplikacji logiki Dodaj akcję, która może wysyłać wyniki z akcji **Przeanalizuj dane JSON** .

1. W tej akcji kliknij w dowolnym miejscu, w którym mają być wyświetlane wyniki. Po otwarciu listy zawartości dynamicznej w obszarze akcji **Przeanalizuj dane JSON** możesz teraz wybrać właściwości z przeanalizowanej zawartości w formacie JSON.

   W tym przykładzie zostanie użyta akcja **Wyślij wiadomość e-mail w usłudze** Office 365 Outlook, która zawiera pola **FirstName**, **LastName**i **email** w treści wiadomości e-mail:

   ![Właściwości JSON w akcji "Wyślij wiadomość e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Oto gotowa akcja dla wiadomości e-mail:

   ![Zakończono przykład akcji poczty e-mail](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**. 

   W oparciu o używany łącznik poczty e-mail znajdują się następujące wyniki:

   ![Wyślij wiadomość e-mail z wynikami akcji "Analizuj dane JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Wybierz akcję

Aby utworzyć tablicę z obiektami JSON utworzonymi z wartości w istniejącej tablicy, użyj akcji **SELECT** . Na przykład można utworzyć obiekt JSON dla każdej wartości w tablicy liczb całkowitych, określając właściwości, które każdy obiekt JSON musi mieć i jak mapować wartości w tablicy źródłowej na te właściwości. Chociaż można zmienić składniki w tych obiektach JSON, tablica wyjściowa zawsze ma taką samą liczbę elementów jak tablica źródłowa.

> [!NOTE]
> Aby akcje używały danych wyjściowych macierzy z akcji **SELECT** , te akcje muszą akceptować tablice jako dane wejściowe lub być może trzeba przekształcić tablicę wyjściową w inny zgodny format. 

Jeśli wolisz pracować w edytorze widoku kodu, możesz skopiować przykład **wyboru** i zainicjować definicje akcji **zmiennych** z tego artykułu do własnej definicji przepływu pracy aplikacji logiki: [przykłady kodu operacji danych — wybierz](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie zastosowano Azure Portal i aplikację logiki z wyzwalaczem **cyklu** i akcją **inicjowania zmiennej** . Akcja jest skonfigurowana do tworzenia zmiennej, której wartość początkowa jest tablicą zawierającą Przykładowe liczby całkowite. Podczas późniejszego testowania aplikacji logiki można ręcznie uruchomić aplikację bez oczekiwania na uruchomienie wyzwalacza.

   ![Uruchamianie przykładowej aplikacji logiki dla akcji "Select"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. W aplikacji logiki, w której chcesz utworzyć tablicę, wykonaj jedną z następujących czynności: 

   * Aby dodać akcję w ostatnim kroku, wybierz pozycję **nowy krok**.

     ![Wybierz pozycję "nowy krok" dla akcji "Select"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę łączącą, aby pojawił się znak plus ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `select` jako filtr. Z listy Akcje wybierz akcję **Wybierz** .

   ![Wybierz akcję "Wybierz"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. W polu **od** Określ tablicę źródłową, którą chcesz.

   Na potrzeby tego przykładu, gdy klikniesz **wewnątrz pola,** zostanie wyświetlona lista zawartości dynamicznej, aby można było wybrać wcześniej utworzoną zmienną:

   ![Wybierz tablicę źródłową dla akcji SELECT](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. W kolumnie po lewej stronie pola **mapy** Podaj nazwę właściwości, która ma zostać przypisana do każdej wartości w tablicy źródłowej. W kolumnie po prawej stronie Określ wyrażenie reprezentujące wartość, do której chcesz przypisać właściwość.

   W tym przykładzie określono "Product_ID" jako nazwę właściwości, aby przypisać każdą wartość w tablicy liczb całkowitych przy użyciu `item()` funkcji w wyrażeniu, która uzyskuje dostęp do każdego elementu tablicy. 

   ![Określ właściwość i wartości obiektu JSON, aby utworzyć tablicę](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Oto ukończona akcja:

   ![Zakończono przykład akcji "Select"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat tej akcji w źródłowej definicji przepływu pracy, zobacz [Wybieranie akcji](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby sprawdzić, czy Akcja **Wybierz** powoduje utworzenie oczekiwanych wyników, Wyślij do siebie powiadomienie zawierające dane wyjściowe z akcji **Wybierz** .

1. W aplikacji logiki Dodaj akcję, która może wysyłać wyniki z akcji **Wybierz** .

1. W tej akcji kliknij w dowolnym miejscu, w którym mają być wyświetlane wyniki. Po otwarciu listy zawartości dynamicznej wybierz pozycję **wyrażenie**. Aby uzyskać dane wyjściowe macierzy z akcji **SELECT** , wprowadź to wyrażenie zawierające nazwę **wybranej** akcji:

   `@actionBody('Select')`

   W tym przykładzie zostanie użyta akcja **Wyślij wiadomość e-mail** w programie Outlook 365, która zawiera dane wyjściowe z `@actionBody('Select')` wyrażenia w treści wiadomości e-mail:

   ![Wyniki akcji z akcji "Select"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Teraz ręcznie uruchom aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   W oparciu o używany łącznik poczty e-mail znajdują się następujące wyniki:

   ![Wyślij wiadomość e-mail z wynikami akcji "Select"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat [łączników Logic Apps](../connectors/apis-list.md)
