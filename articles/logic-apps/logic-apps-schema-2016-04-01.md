---
title: Aktualizacje schematu Czerwiec-1-2016
description: Zaktualizowano wersję schematu 2016-06-01 dla definicji aplikacji logiki w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: ccc7df5bfac327fabf05f210764dbe10658b5015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86520719"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aktualizacje schematu dla Azure Logic Apps — 1 czerwca 2016

[Zaktualizowana wersja schematu](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) i interfejsu API dla Azure Logic Apps obejmuje kluczowe ulepszenia, dzięki którym Aplikacje logiki są bardziej niezawodne i łatwiejsze w użyciu:

* [Zakresy](#scopes) umożliwiają grupowanie lub zagnieżdżanie akcji jako kolekcji akcji.
* [Warunki i pętle](#conditions-loops) są teraz akcjami pierwszej klasy.
* Dokładniejsza kolejność uruchamiania akcji z `runAfter` właściwością, zastępując `dependsOn`

Aby uaktualnić Aplikacje logiki z 1 sierpnia 2015 schematu wersji zapoznawczej do schematu 1 czerwca 2016, [zapoznaj się z sekcją uaktualniania](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Zakresy

Ten schemat zawiera zakresy, które umożliwiają grupowanie akcji ze sobą lub zagnieżdżanie akcji wewnątrz siebie. Na przykład warunek może zawierać inny warunek. Dowiedz się więcej o [składni zakresu](./logic-apps-control-flow-loops.md)lub zapoznaj się z tym przykładem podstawowego zakresu:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Warunki i pętle zmiany

W poprzednich wersjach schematu warunki i pętle były parametrami skojarzonymi z pojedynczą akcją. Ten schemat dźwigu tego ograniczenia, więc warunki i pętle są teraz dostępne jako typy akcji. Dowiedz się więcej o [pętlach i zakresach](./logic-apps-control-flow-loops.md), [warunkach](../logic-apps/logic-apps-control-flow-conditional-statement.md)lub przejrzyj ten podstawowy przykład, który pokazuje akcję warunku:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Właściwość "runAfter"

`runAfter`Właściwość zastępuje `dependsOn` , co zapewnia większą precyzję podczas określania kolejności uruchamiania dla akcji na podstawie stanu poprzednich akcji. `dependsOn`Właściwość wskazuje, czy "akcja została uruchomiona i powiodła się", w zależności od tego, czy Poprzednia akcja zakończyła się powodzeniem, zakończyła się niepowodzeniem lub jako pominięcia — nie liczba uruchomień akcji. `runAfter`Właściwość zapewnia elastyczność jako obiekt, który określa wszystkie nazwy akcji, po których działa obiekt. Ta właściwość definiuje również tablicę Stanów, które są akceptowane jako wyzwalacze. Na przykład jeśli chcesz, aby akcja działała po pomyślnym wykonaniu akcji, a także po pomyślnym wykonaniu akcji B lub niepowodzeniem, skonfiguruj tę `runAfter` Właściwość:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uaktualnianie schematu

Aby uaktualnić do najnowszego [schematu](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), wystarczy wykonać kilka kroków. Proces uaktualniania obejmuje uruchomienie skryptu uaktualniania, zapisanie go jako nowej aplikacji logiki, a jeśli chcesz, prawdopodobnie zastępowanie poprzedniej aplikacji logiki.

1. W Azure Portal Otwórz aplikację logiki.

2. Przejdź do **omówienia**. Na pasku narzędzi aplikacji logiki wybierz pozycję **Aktualizuj schemat**.
   
   ![Wybierz pozycję Aktualizuj schemat][1]
   
   Zostanie zwrócona uaktualniona definicja, w razie potrzeby można ją skopiować i wkleić do definicji zasobu. 

   > [!IMPORTANT]
   > *Upewnij się, że* wybrano pozycję **Zapisz jako** , aby wszystkie odwołania do połączeń pozostawały prawidłowe w uaktualnionej aplikacji logiki.

3. Na pasku narzędzi bloku uaktualniania wybierz pozycję **Zapisz jako**.

4. Wprowadź nazwę i stan logiki. Aby wdrożyć uaktualnioną aplikację logiki, wybierz pozycję **Utwórz**.

5. Upewnij się, że uaktualniona aplikacja logiki działa zgodnie z oczekiwaniami.
   
   > [!NOTE]
   > Jeśli używasz wyzwalacza ręcznego lub żądania, adres URL wywołania zwrotnego zmieni się w nowej aplikacji logiki. Przetestuj nowy adres URL, aby upewnić się, że kompleksowe środowisko działa. Aby zachować poprzednie adresy URL, można sklonować za pośrednictwem istniejącej aplikacji logiki.

6. *Opcjonalne* Aby zastąpić poprzednią aplikację logiki nową wersją schematu, na pasku narzędzi wybierz pozycję **Klonuj**, a następnie pozycję **Aktualizuj schemat**. Ten krok jest niezbędny tylko wtedy, gdy chcesz zachować ten sam identyfikator zasobu lub adres URL wyzwalacza żądania aplikacji logiki.

## <a name="upgrade-tool-notes"></a>Uwagi dotyczące narzędzia do uaktualniania

### <a name="mapping-conditions"></a>Warunki mapowania

W uaktualnionej definicji Narzędzie to najlepiej sprawdza się w przypadku grupowania prawdy i fałszywych akcji rozgałęzień jednocześnie jako zakresu. W `@equals(actions('a').status, 'Skipped')` odniesieniu do wzorca projektanta pojawia się jako `else` Akcja. Jeśli jednak narzędzie wykryje nierozpoznawalne wzorce, narzędzie może utworzyć osobne warunki zarówno dla gałęzi true, jak i false. W razie potrzeby można ponownie mapować akcje po uaktualnieniu.

#### <a name="foreach-loop-with-condition"></a>Pętla "foreach" z warunkiem

W nowym schemacie można użyć akcji filtru, aby replikować wzorzec, który używa pętli **for each** z jednym warunkiem dla każdego elementu. Jednak zmiana jest wykonywana automatycznie podczas uaktualniania. Warunek stanie się akcją filtru, która pojawia się przed pętlą **for each** , zwracając tylko tablicę elementów pasujących do warunku i przekazując tę tablicę do **każdej** akcji. Aby zapoznać się z przykładem, zobacz [pętle i zakresy](./logic-apps-control-flow-loops.md).

### <a name="resource-tags"></a>Tagi zasobów

Po uaktualnieniu Tagi zasobów zostaną usunięte, dlatego należy je zresetować dla uaktualnionego przepływu pracy.

## <a name="other-changes"></a>Inne zmiany

### <a name="renamed-manual-trigger-to-request-trigger"></a>Zmieniono nazwę wyzwalacza "Manual" na wyzwalacz "Request"

`manual`Typ wyzwalacza został uznany za przestarzały, a jego nazwa została zmieniona na `request` Typ `http` . Ta zmiana powoduje utworzenie większej spójności dla rodzaju wzorca używanego przez wyzwalacz do kompilowania.

### <a name="new-filter-action"></a>Nowa akcja "filter"

Aby odfiltrować dużą tablicę w dół do mniejszego zestawu elementów, nowy `filter` Typ akceptuje tablicę i warunek, oblicza warunek dla każdego elementu i zwraca tablicę zawierającą elementy spełniające warunek.

### <a name="restrictions-for-foreach-and-until-actions"></a>Ograniczenia dotyczące akcji "foreach" i "until"

`foreach` `until` Pętla i jest ograniczona do pojedynczej akcji.

### <a name="new-trackedproperties-for-actions"></a>Nowe "trackedProperties" dla akcji

Akcje mogą teraz mieć dodatkową właściwość o nazwie `trackedProperties` , która jest elementem równorzędnym `runAfter` dla `type` właściwości i. Ten obiekt Określa pewne dane wejściowe lub wyjściowe akcji, które mają zostać dołączone do telemetrii diagnostyki platformy Azure, emitowane w ramach przepływu pracy. Na przykład:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie definicji przepływu pracy dla aplikacji logiki](../logic-apps/logic-apps-author-definitions.md)
* [Tworzenie wdrożenia aplikacji logiki](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
