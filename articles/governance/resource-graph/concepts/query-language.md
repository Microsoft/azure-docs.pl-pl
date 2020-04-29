---
title: Opis języka zapytań
description: Opisuje tabele grafu zasobów i dostępne typy danych Kusto, operatory i funkcje możliwe do użycia w usłudze Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78927495"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informacje o języku zapytań grafu zasobów platformy Azure

Język zapytań dla wykresu zasobów platformy Azure obsługuje wiele operatorów i funkcji. Każda służbowa i działania oparta na [Kusto Query Language (KQL)](/azure/kusto/query/index). Aby dowiedzieć się więcej o języku zapytań używanym przez Graf zasobów, Zacznij od [samouczka dla KQL](/azure/kusto/query/tutorial).

W tym artykule omówiono składniki językowe obsługiwane przez program Resource Graph:

- [Tabele grafu zasobów](#resource-graph-tables)
- [Obsługiwane elementy języka KQL](#supported-kql-language-elements)
- [Znaki ucieczki](#escape-characters)

## <a name="resource-graph-tables"></a>Tabele grafu zasobów

Wykres zasobów zawiera kilka tabel służących do przechowywania danych o typach zasobów Menedżer zasobów i ich właściwościach. Te tabele mogą być używane z `join` operatorami or lub `union` do pobierania właściwości z powiązanych typów zasobów. Poniżej znajduje się lista tabel dostępnych na wykresie zasobów:

|Tabele grafu zasobów |Opis |
|---|---|
|Zasoby |Domyślna tabela, jeśli żadna nie została zdefiniowana w zapytaniu. Większość Menedżer zasobów typów zasobów i właściwości jest tutaj. |
|ResourceContainers |Obejmuje subskrypcję (w wersji zapoznawczej-- `Microsoft.Resources/subscriptions`)`Microsoft.Resources/subscriptions/resourcegroups`i grupy zasobów () oraz typy zasobów i dane. |
|AdvisorResources |Obejmuje zasoby _związane_ z `Microsoft.Advisor`programem. |
|AlertsManagementResources |Obejmuje zasoby _związane_ z `Microsoft.AlertsManagement`programem. |
|MaintenanceResources |Obejmuje zasoby _związane_ z `Microsoft.Maintenance`programem. |
|SecurityResources |Obejmuje zasoby _związane_ z `Microsoft.Security`programem. |

Pełną listę zawierającą typy zasobów można znaleźć w temacie [Reference: obsługiwane tabele i typy zasobów](../reference/supported-tables-resources.md).

> [!NOTE]
> _Zasoby_ są tabelami domyślnymi. Podczas wykonywania zapytania względem tabeli _zasobów_ nie jest wymagane podanie nazwy tabeli, chyba że ani `join` `union` nie są używane. Jednak zalecanym sposobem jest zawsze dołączenie początkowej tabeli do zapytania.

Użyj Eksploratora grafów zasobów w portalu, aby dowiedzieć się, jakie typy zasobów są dostępne w każdej tabeli. Alternatywnie należy użyć zapytania, `<tableName> | distinct type` na przykład w celu uzyskania listy typów zasobów, których dana tabela grafu zasobów obsługuje istniejące w danym środowisku.

Poniższe zapytanie pokazuje prostą `join`. Wyniki zapytania powodują mieszanie kolumn wraz ze wszystkimi zduplikowanymi nazwami kolumn z sprzężonej tabeli, _ResourceContainers_ w tym przykładzie, są dołączane z **1**. Ponieważ tabela _ResourceContainers_ zawiera typy dla subskrypcji i grup zasobów, do przyłączenia do zasobu z tabeli _zasobów_ można użyć dowolnego typu.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Poniższe zapytanie pokazuje bardziej złożone użycie programu `join`. `project` Zapytanie ogranicza przyłączoną tabelę do zasobów subskrypcji i z w celu uwzględnienia tylko oryginalnego identyfikatora _subskrypcji_ pola i _nazwy pola Nazwa_ z _nazwą._ Zmiana nazwy pola pozwala uniknąć `join` dodawania go jako _Name1_ , ponieważ pole już istnieje w obszarze _zasoby_. Oryginalna tabela jest filtrowana z `where` i poniżej `project` zawiera kolumny z obu tabel. Wynikiem zapytania jest pojedynczy magazyn kluczy zawierający typ, nazwę magazynu kluczy oraz nazwę subskrypcji, w której znajduje się.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> W przypadku `join` ograniczania wyników `project`przy użyciu właściwości, która `join` jest używana przez program do powiązania dwóch tabel, identyfikator _subskrypcji_ w powyższym przykładzie `project`, musi być uwzględniona w.

## <a name="supported-kql-language-elements"></a>Obsługiwane elementy języka KQL

Wykres zasobów obsługuje wszystkie [typy danych](/azure/kusto/query/scalar-data-types/)KQL, [funkcje skalarne](/azure/kusto/query/scalarfunctions), [Operatory skalarne](/azure/kusto/query/binoperators)i [funkcje agregujące](/azure/kusto/query/any-aggfunction). Określone [Operatory tabelaryczne](/azure/kusto/query/queries) są obsługiwane przez Graf zasobów, a niektóre z nich mają różne zachowania.

### <a name="supported-tabulartop-level-operators"></a>Obsługiwane operatory tabelaryczne/najwyższego poziomu

Poniżej znajduje się lista operatorów tabelarycznych KQL obsługiwanych przez Graf zasobów z określonymi przykładami:

|KQL |Zapytanie przykładowe grafu zasobów |Uwagi |
|---|---|---|
|[liczbą](/azure/kusto/query/countoperator) |[Liczenie magazynów kluczy](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Pokaż różne wartości dla określonego aliasu](../samples/starter.md#distinct-alias-values) | |
|[sunąć](/azure/kusto/query/extendoperator) |[Liczba maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-os) | |
|[Złącza](/azure/kusto/query/joinoperator) |[Magazyn kluczy z nazwą subskrypcji](../samples/advanced.md#join) |Obsługiwane typy sprzężeń: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [wewnętrzne](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limit 3 `join` w pojedynczym zapytaniu. Niestandardowe strategie dołączania, takie jak sprzężenie emisji, nie są dozwolone. Może być używany w jednej tabeli lub między tabelami _zasobów_ i _ResourceContainers_ . |
|[granice](/azure/kusto/query/limitoperator) |[Lista wszystkich publicznych adresów IP](../samples/starter.md#list-publicip) |Synonim`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Starszy operator, zamiast `mv-expand` tego użyj. _RowLimit_ max z 400. Wartość domyślna to 128. |
|[MV — rozwiń](/azure/kusto/query/mvexpandoperator) |[Wyświetlanie listy Cosmos DB z określonymi lokalizacjami zapisu](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max z 400. Wartość domyślna to 128. |
|[porządek](/azure/kusto/query/orderoperator) |[Wyświetl listę zasobów posortowanych według nazwy](../samples/starter.md#list-resources) |Synonim`sort` |
|[projektu](/azure/kusto/query/projectoperator) |[Wyświetl listę zasobów posortowanych według nazwy](../samples/starter.md#list-resources) | |
|[projekt — poza](/azure/kusto/query/projectawayoperator) |[Usuń kolumny z wyników](../samples/advanced.md#remove-column) | |
|[porządku](/azure/kusto/query/sortoperator) |[Wyświetl listę zasobów posortowanych według nazwy](../samples/starter.md#list-resources) |Synonim`order` |
|[Podsumuj](/azure/kusto/query/summarizeoperator) |[Liczba zasobów platformy Azure](../samples/starter.md#count-resources) |Uproszczona tylko pierwsza strona |
|[czasochłonn](/azure/kusto/query/takeoperator) |[Lista wszystkich publicznych adresów IP](../samples/starter.md#list-publicip) |Synonim`limit` |
|[Do góry](/azure/kusto/query/topoperator) |[Pokaż pięć pierwszych maszyn wirtualnych według nazwy i ich typu systemu operacyjnego](../samples/starter.md#show-sorted) | |
|[Unii](/azure/kusto/query/unionoperator) |[Łączenie wyników z dwóch zapytań w jeden wynik](../samples/advanced.md#unionresults) |`| union` \[ `kind=` `inner` \| `outer` Dozwolona pojedyncza _tabela:_ _T_ `withsource=` _ColumnName_ ColumnName\] . \] \[ Limit 3 `union` etapów w pojedynczej kwerendzie. Rozpoznawanie rozmyte tabel `union` nogi nie jest dozwolone. Może być używany w jednej tabeli lub między tabelami _zasobów_ i _ResourceContainers_ . |
|[miejscu](/azure/kusto/query/whereoperator) |[Pokaż zasoby zawierające magazyn](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Znaki ucieczki

Niektóre nazwy właściwości, takie jak te, które zawierają `.` lub `$`, muszą być opakowane lub wyprowadzane w zapytaniu lub nazwa właściwości jest interpretowane niepoprawnie i nie zapewniają oczekiwanych wyników.

- `.`— Zawiń nazwę właściwości w taki sposób, aby:`['propertyname.withaperiod']`
  
  Przykładowe zapytanie, które zawija Właściwość _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`— Znak ucieczki w nazwie właściwości. Używany znak ucieczki zależy od wykresu zasobów powłoki jest uruchamiany z.

  - **bash** - `\`

    Przykładowe zapytanie, które wyprowadza _ \$typ_ właściwości w bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** — nie `$` wyznaczania znaku ucieczki.

  - **Narzędzia** - ``` ` ```

    Przykładowe zapytanie, które określa _ \$typ_ właściwości w programie PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [zapytaniach początkowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](../samples/advanced.md).
- Dowiedz się więcej o sposobach [eksplorowania zasobów](explore-resources.md).