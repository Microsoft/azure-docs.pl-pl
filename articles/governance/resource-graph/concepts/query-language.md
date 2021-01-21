---
title: Opis języka zapytań
description: Opisuje tabele grafu zasobów i dostępne typy danych Kusto, operatory i funkcje możliwe do użycia w usłudze Azure Resource Graph.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: 137b5c40097d7de82e156b4a0869d7257d3e9964
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624762"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informacje o języku zapytań grafu zasobów platformy Azure

Język zapytań dla wykresu zasobów platformy Azure obsługuje wiele operatorów i funkcji. Każda służbowa i działania oparta na [Kusto Query Language (KQL)](/azure/kusto/query/index). Aby dowiedzieć się więcej o języku zapytań używanym przez Graf zasobów, Zacznij od [samouczka dla KQL](/azure/kusto/query/tutorial).

W tym artykule omówiono składniki językowe obsługiwane przez program Resource Graph:

- [Tabele grafu zasobów](#resource-graph-tables)
- [Niestandardowe elementy języka grafu zasobów](#resource-graph-custom-language-elements)
- [Obsługiwane elementy języka KQL](#supported-kql-language-elements)
- [Zakres zapytania](#query-scope)
- [Znaki ucieczki](#escape-characters)

## <a name="resource-graph-tables"></a>Tabele grafu zasobów

Wykres zasobów zawiera kilka tabel służących do przechowywania danych o typach zasobów Azure Resource Manager i ich właściwościach. Niektóre tabele mogą być używane z `join` `union` operatorami or lub do pobierania właściwości z powiązanych typów zasobów. Poniżej znajduje się lista tabel dostępnych na wykresie zasobów:

|Tabela grafu zasobów |Czy są `join` inne tabele? |Opis |
|---|---|---|
|Zasoby |Tak |Domyślna tabela, jeśli żadna nie została zdefiniowana w zapytaniu. Większość Menedżer zasobów typów zasobów i właściwości jest tutaj. |
|ResourceContainers |Tak |Obejmuje subskrypcję (w wersji zapoznawczej-- `Microsoft.Resources/subscriptions` ) i grupy zasobów ( `Microsoft.Resources/subscriptions/resourcegroups` ) oraz typy zasobów i dane. |
|AdvisorResources |Tak (wersja zapoznawcza) |Obejmuje zasoby _związane_ z programem `Microsoft.Advisor` . |
|AlertsManagementResources |Tak (wersja zapoznawcza) |Obejmuje zasoby _związane_ z programem `Microsoft.AlertsManagement` . |
|GuestConfigurationResources |Nie |Obejmuje zasoby _związane_ z programem `Microsoft.GuestConfiguration` . |
|MaintenanceResources |Częściowe, Dołącz tylko _do_ . (wersja zapoznawcza) |Obejmuje zasoby _związane_ z programem `Microsoft.Maintenance` . |
|PatchAssessmentResources|Nie |Obejmuje zasoby _związane_ z oceną poprawek Virtual Machines platformy Azure. |
|PatchInstallationResources|Nie |Obejmuje zasoby _związane_ z instalacją poprawek Virtual Machines platformy Azure. |
|PolicyResources |Nie |Obejmuje zasoby _związane_ z programem `Microsoft.PolicyInsights` . (**Wersja zapoznawcza**)|
|RecoveryServicesResources |Częściowe, Dołącz tylko _do_ . (wersja zapoznawcza) |Obejmuje zasoby _związane_ z `Microsoft.DataProtection` i `Microsoft.RecoveryServices` . |
|SecurityResources |Częściowe, Dołącz tylko _do_ . (wersja zapoznawcza) |Obejmuje zasoby _związane_ z programem `Microsoft.Security` . |
|ServiceHealthResources |Nie |Obejmuje zasoby _związane_ z programem `Microsoft.ResourceHealth` . |

Aby uzyskać pełną listę, w tym typy zasobów, zobacz temat [Reference: obsługiwane tabele i typy zasobów](../reference/supported-tables-resources.md).

> [!NOTE]
> _Zasoby_ są tabelami domyślnymi. Podczas wykonywania zapytania względem tabeli _zasobów_ nie jest wymagane podanie nazwy tabeli, chyba że `join` ani nie `union` są używane. Jednak zalecanym sposobem jest zawsze dołączenie początkowej tabeli do zapytania.

Użyj Eksploratora grafów zasobów w portalu, aby dowiedzieć się, jakie typy zasobów są dostępne w każdej tabeli. Alternatywnie należy użyć zapytania, `<tableName> | distinct type` na przykład w celu uzyskania listy typów zasobów, których dana tabela grafu zasobów obsługuje istniejące w danym środowisku.

Poniższe zapytanie pokazuje prostą `join` . Wyniki zapytania powodują mieszanie kolumn wraz ze wszystkimi zduplikowanymi nazwami kolumn z sprzężonej tabeli, _ResourceContainers_ w tym przykładzie, są dołączane z **1**. Ponieważ tabela _ResourceContainers_ zawiera typy dla subskrypcji i grup zasobów, do przyłączenia do zasobu z tabeli _zasobów_ można użyć dowolnego typu.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Poniższe zapytanie pokazuje bardziej złożone użycie programu `join` . Najpierw zapytanie jest wykorzystywane `project` do pobierania pól z _zasobów_ dla magazynu Azure Key Vault typ zasobu. Następny krok używa `join` do scalania wyników z _ResourceContainers_ , gdzie typ jest subskrypcją _dla_ właściwości, która jest zarówno w pierwszej tabeli `project` , jak i sprzężonej tabeli `project` . Zmiana nazwy pola pozwala uniknąć `join` dodawania go jako _Name1_ , ponieważ właściwość jest już rzutowana z _zasobów_. Wynikiem zapytania jest pojedynczy magazyn kluczy, który umożliwia wyświetlanie typu, nazwy, lokalizacji i grupy zasobów magazynu kluczy wraz z nazwą subskrypcji, w której znajduje się.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| project name, type, location, subscriptionId, resourceGroup
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, location, resourceGroup, SubName
| limit 1
```

> [!NOTE]
> W przypadku ograniczania `join` wyników przy użyciu `project` właściwości, która jest używana przez program `join` do powiązania dwóch tabel, identyfikator _subskrypcji_ w powyższym przykładzie, musi być uwzględniona w `project` .

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Właściwości rozszerzone (wersja zapoznawcza)

Jako funkcja w _wersji zapoznawczej_ niektóre typy zasobów na wykresie zasobów mają dodatkowe właściwości związane z typem, które są dostępne do wykonywania zapytań poza właściwościami dostarczonymi przez Azure Resource Manager. Ten zestaw wartości, znany jako _właściwości rozszerzone_, istnieje w obsługiwanym typie zasobów w `properties.extended` . Aby zobaczyć, które typy zasobów mają _Rozszerzone właściwości_, użyj następującego zapytania:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Przykład: Pobierz liczbę maszyn wirtualnych według `instanceView.powerState.code` :

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Niestandardowe elementy języka grafu zasobów

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Składnia zapytania udostępnionego (wersja zapoznawcza)

Jako funkcja w wersji zapoznawczej można uzyskać dostęp do [udostępnionego zapytania](../tutorials/create-share-query.md) bezpośrednio w zapytaniu wykresu zasobów. W tym scenariuszu można utworzyć standardowe zapytania jako zapytania udostępnione i użyć ich ponownie. Aby wywołać udostępnione zapytanie wewnątrz zapytania grafu zasobów, użyj `{{shared-query-uri}}` składni. Identyfikator URI zapytania udostępnionego jest adresem _zasobu_ zapytania udostępnionego na stronie **Ustawienia** dla tego zapytania. W tym przykładzie nasz udostępniony identyfikator URI zapytania to `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` .
Ten identyfikator URI wskazuje na subskrypcję, grupę zasobów i pełną nazwę zapytania udostępnionego, do którego chcemy się odwołać w innym zapytaniu. To zapytanie jest takie samo jak utworzone w [samouczku: Tworzenie i udostępnianie zapytania](../tutorials/create-share-query.md).

> [!NOTE]
> Nie można zapisać zapytania, które odwołuje się do zapytania udostępnionego jako zapytania udostępnionego.

Przykład 1: Użyj tylko zapytania udostępnionego

Wyniki tego zapytania grafu zasobów są takie same, jak zapytanie przechowywane w zapytaniu udostępnionym.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Przykład 2: Dołącz udostępnione zapytanie jako część większego zapytania

To zapytanie najpierw używa udostępnionej kwerendy, a następnie używa `limit` w celu ograniczenia wyników.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Obsługiwane elementy języka KQL

Wykres zasobów obsługuje podzbiór [typów danych](/azure/kusto/query/scalar-data-types/)KQL, [funkcje skalarne](/azure/kusto/query/scalarfunctions), [Operatory skalarne](/azure/kusto/query/binoperators)i [funkcje agregujące](/azure/kusto/query/any-aggfunction). Określone [Operatory tabelaryczne](/azure/kusto/query/queries) są obsługiwane przez Graf zasobów, a niektóre z nich mają różne zachowania.

### <a name="supported-tabulartop-level-operators"></a>Obsługiwane operatory tabelaryczne/najwyższego poziomu

Poniżej znajduje się lista operatorów tabelarycznych KQL obsługiwanych przez Graf zasobów z określonymi przykładami:

|KQL |Zapytanie przykładowe grafu zasobów |Uwagi |
|---|---|---|
|[liczbą](/azure/kusto/query/countoperator) |[Liczenie magazynów kluczy](../samples/starter.md#count-keyvaults) | |
|[itp](/azure/kusto/query/distinctoperator) |[Pokaż zasoby zawierające magazyn](../samples/starter.md#show-storage) | |
|[sunąć](/azure/kusto/query/extendoperator) |[Liczba maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-os) | |
|[Złącza](/azure/kusto/query/joinoperator) |[Magazyn kluczy z nazwą subskrypcji](../samples/advanced.md#join) |Obsługiwane typy sprzężeń: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [wewnętrzne](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limit 3 `join` w pojedynczym zapytaniu, z którym może być wiele tabel `join` . Jeśli wszystkie użycie między tabelami `join` jest między _zasobami_ i _ResourceContainers_, to 3 `join` . Niestandardowe strategie dołączania, takie jak sprzężenie emisji, nie są dozwolone. W których tabelach można używać `join` , zobacz [tabele grafów zasobów](#resource-graph-tables). |
|[limit](/azure/kusto/query/limitoperator) |[Lista wszystkich publicznych adresów IP](../samples/starter.md#list-publicip) |Synonim `take` . Nie działa z [pominięciem](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Starszy operator, `mv-expand` zamiast tego użyj. _RowLimit_ max z 400. Wartość domyślna to 128. |
|[MV — rozwiń](/azure/kusto/query/mvexpandoperator) |[Wyświetlanie listy Cosmos DB z określonymi lokalizacjami zapisu](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max z 400. Wartość domyślna to 128. Limit 3 `mv-expand` w pojedynczym zapytaniu.|
|[order](/azure/kusto/query/orderoperator) |[Wyświetl listę zasobów posortowanych według nazwy](../samples/starter.md#list-resources) |Synonim `sort` |
|[projektu](/azure/kusto/query/projectoperator) |[Wyświetl listę zasobów posortowanych według nazwy](../samples/starter.md#list-resources) | |
|[projekt — poza](/azure/kusto/query/projectawayoperator) |[Usuń kolumny z wyników](../samples/advanced.md#remove-column) | |
|[porządku](/azure/kusto/query/sortoperator) |[Wyświetl listę zasobów posortowanych według nazwy](../samples/starter.md#list-resources) |Synonim `order` |
|[Podsumuj](/azure/kusto/query/summarizeoperator) |[Liczba zasobów platformy Azure](../samples/starter.md#count-resources) |Uproszczona tylko pierwsza strona |
|[czasochłonn](/azure/kusto/query/takeoperator) |[Lista wszystkich publicznych adresów IP](../samples/starter.md#list-publicip) |Synonim `limit` . Nie działa z [pominięciem](./work-with-data.md#skipping-records). |
|[Do góry](/azure/kusto/query/topoperator) |[Pokaż pięć pierwszych maszyn wirtualnych według nazwy i ich typu systemu operacyjnego](../samples/starter.md#show-sorted) | |
|[Unii](/azure/kusto/query/unionoperator) |[Łączenie wyników z dwóch zapytań w jeden wynik](../samples/advanced.md#unionresults) |Dozwolona pojedyncza tabela: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _ColumnName_ \] . Limit 3 `union` etapów w pojedynczej kwerendzie. Rozpoznawanie rozmyte `union` tabel nogi nie jest dozwolone. Może być używany w jednej tabeli lub między tabelami _zasobów_ i _ResourceContainers_ . |
|[miejscu](/azure/kusto/query/whereoperator) |[Pokaż zasoby zawierające magazyn](../samples/starter.md#show-storage) | |

Istnieje domyślny limit 3 `join` i 3 `mv-expand` operatorów w jednym ZAPYTANIU zestawu SDK wykresu zasobów. Możesz zażądać zwiększenia limitów dla dzierżawy w ramach **pomocy i obsługi technicznej**.

Aby można było obsługiwać środowisko portalu "Open Query", Eksplorator zasobów platformy Azure ma wyższy limit globalny niż zestaw SDK grafu zasobów.

## <a name="query-scope"></a>Zakres zapytania

Zakres subskrypcji, z których zasoby są zwracane przez zapytanie, zależy od metody uzyskiwania dostępu do grafu zasobów. Interfejs wiersza polecenia platformy Azure i Azure PowerShell wypełnić listę subskrypcji do uwzględnienia w żądaniu na podstawie kontekstu autoryzowanego użytkownika. Listę subskrypcji można określić ręcznie dla każdej z użyciem odpowiednio **subskrypcji** i parametrów **subskrypcji** .
W interfejsie API REST i wszystkich innych zestawów SDK Lista subskrypcji do uwzględnienia zasobów musi być jawnie zdefiniowana w ramach żądania.

Wersja **zapoznawcza** interfejsu API REST `2020-04-01-preview` dodaje właściwość do zakresu zapytania do [grupy zarządzania](../../management-groups/overview.md). Ten interfejs API w wersji zapoznawczej powoduje również, że właściwość subskrypcji jest opcjonalna. Jeśli grupa zarządzania lub Lista subskrypcji nie jest zdefiniowana, zakres zapytania obejmuje wszystkie zasoby, w tym zasoby [Lighthouse platformy Azure](../../../lighthouse/concepts/azure-delegated-resource-management.md) , do których uwierzytelniony użytkownik może uzyskać dostęp. Nowa `managementGroupId` Właściwość przyjmuje identyfikator grupy zarządzania, który różni się od nazwy grupy zarządzania. Gdy `managementGroupId` jest określony, uwzględniane są zasoby z pierwszych 5000 subskrypcji w lub poniżej określonej hierarchii grupy zarządzania. `managementGroupId` nie mogą być używane w tym samym czasie co `subscriptions` .

Przykład: wykonywanie zapytania dotyczącego wszystkich zasobów w hierarchii grupy zarządzania o nazwie "moja grupa zarządzania" o IDENTYFIKATORze "myMG".

- Identyfikator URI interfejsu API REST

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Treść żądania

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Znaki ucieczki

Niektóre nazwy właściwości, takie jak te, które zawierają `.` lub `$` , muszą być opakowane lub wyprowadzane w zapytaniu lub nazwa właściwości jest interpretowane niepoprawnie i nie zapewniają oczekiwanych wyników.

- `.` — Zawiń nazwę właściwości w taki sposób, aby: `['propertyname.withaperiod']`
  
  Przykładowe zapytanie, które zawija Właściwość _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` — Znak ucieczki w nazwie właściwości. Używany znak ucieczki zależy od wykresu zasobów powłoki jest uruchamiany z.

  - **bash** - `\`

    Przykładowe zapytanie, które wyprowadza _\$ Typ_ właściwości w bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** — nie `$` wyznaczania znaku ucieczki.

  - **Narzędzia** - ``` ` ```

    Przykładowe zapytanie, które określa _\$ Typ_ właściwości w programie PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [zapytaniach początkowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](../samples/advanced.md).
- Dowiedz się więcej o sposobach [eksplorowania zasobów](explore-resources.md).
