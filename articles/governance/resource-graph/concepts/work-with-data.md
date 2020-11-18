---
title: Praca z dużymi zestawami danych
description: Informacje na temat pobierania, formatowania, wyświetlania i pomijania rekordów w dużych zestawach danych podczas pracy z wykresem zasobów platformy Azure.
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 6054d2cd2cf012c21f451ece87db672897fa0398
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843353"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Praca z dużymi zestawami danych zasobów platformy Azure

Usługa Azure Resource Graph została zaprojektowana do pracy z i uzyskiwania informacji o zasobach w środowisku platformy Azure. Wykres zasobów umożliwia szybkie pobieranie danych, nawet w przypadku wykonywania zapytań o tysiące rekordów. Wykres zasobów zawiera kilka opcji pracy z tymi dużymi zestawami danych.

Aby uzyskać wskazówki dotyczące pracy z kwerendami z dużą częstotliwością, zobacz [wskazówki dotyczące żądań ograniczających](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Rozmiar wyniku zestawu danych

Domyślnie wykres zasobów ogranicza wszelkie zapytania, aby zwracać tylko **100** rekordów. Ta kontrolka chroni zarówno użytkownika, jak i usługę przed niezamierzonymi zapytaniami, które spowodują powstanie dużych zestawów danych. To zdarzenie najczęściej występuje, gdy klient próbuje znaleźć i filtrować zasoby w taki sposób, aby odpowiadały one konkretnym potrzebom. Ta kontrolka różni się od użycia [górnego](/azure/kusto/query/topoperator) lub [ograniczającego](/azure/kusto/query/limitoperator) operatory języka Azure Eksplorator danych, aby ograniczyć wyniki.

> [!NOTE]
> Przy **pierwszym** użyciu zaleca się kolejność wyników według co najmniej jednej kolumny z `asc` lub `desc` . Bez sortowania, zwracane wyniki są losowe i nie można ich powtarzać.

Domyślny limit można zastąpić wszystkimi metodami współpracy z wykresem zasobów. W poniższych przykładach pokazano, jak zmienić limit rozmiaru zestawu danych na _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

W [interfejsie API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)formant jest **$Top** i jest częścią **QueryRequestOptions**.

Formant, który jest _najbardziej restrykcyjny_ , zostanie wygrany. Na przykład jeśli w zapytaniu jest używany operator **Top** lub **Limit** , a wynikiem będzie więcej rekordów niż **pierwsze**, Maksymalna liczba zwracanych rekordów będzie równa **pierwsze**. Podobnie, jeśli **górny** lub **Limit** jest mniejszy niż **pierwszy**, zwracany zestaw rekordów będzie mniejszą wartością skonfigurowaną przez wartość **Top** lub **Limit**.

**Pierwszy** obecnie ma maksymalną dozwoloną wartość _5000_, która osiąga przez [stronicowanie wyników](#paging-results) _1000_ rekordów jednocześnie.

> [!IMPORTANT]
> Gdy **Pierwsza** wartość jest skonfigurowana tak, aby była większa niż _1000_ rekordów, **zapytanie musi zawierać** wartość pola **ID** , aby podział na strony ma działać. Jeśli brakuje tego zapytania, odpowiedź nie zostanie [stronicowana](#paging-results) i wyniki są ograniczone do _1000_ rekordów.

## <a name="skipping-records"></a>Pomijanie rekordów

Następną opcją pracy z dużymi zestawami danych jest kontrolka **pomijania** . Ta kontrolka umożliwia kwerendy przeskoczenie lub pominięcie zdefiniowanej liczby rekordów przed zwróceniem wyników. **Pomijanie** jest przydatne w przypadku zapytań, które sortują wyniki w zrozumiały sposób, gdy celem jest uzyskanie rekordów w środku zestawu wyników. Jeśli wyniki są konieczne na końcu zwracanego zestawu danych, bardziej wydajne jest użycie innej konfiguracji sortowania i pobranie wyników z góry zestawu danych.

> [!NOTE]
> W przypadku korzystania z funkcji **Skip** zaleca się kolejność wyników według co najmniej jednej kolumny z `asc` lub `desc` . Bez sortowania, zwracane wyniki są losowe i nie można ich powtarzać. Jeśli `limit` lub `take` są używane w zapytaniu, **Skip** jest ignorowane.

W poniższych przykładach pokazano, jak pominąć pierwsze _10_ rekordów, a zamiast tego zostanie wyświetlony zwrócony zestaw wyników z 11 rekordu:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

W [interfejsie API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)formant jest **$Skip** i jest częścią **QueryRequestOptions**.

## <a name="paging-results"></a>Stronicowanie wyników

Gdy konieczne jest przerwanie zestawu wyników w mniejszych zestawach rekordów do przetworzenia lub ponieważ zestaw wyników będzie przekroczyć maksymalną dozwoloną wartość _1000_ zwracanych rekordów, należy użyć stronicowania. [Interfejs API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) 
 **QueryResponse** udostępnia wartości wskazujące, że zestaw wyników został podzielony: **resultTruncated** i **$skipToken**. **resultTruncated** jest wartością logiczną, która informuje odbiorcę, jeśli w odpowiedzi nie zostały zwrócone dodatkowe rekordy. Ten stan można również zidentyfikować, gdy właściwość **Count** jest mniejsza niż Właściwość **totalRecords** . **totalRecords** definiuje liczbę rekordów, które pasują do zapytania.

 **resultTruncated** ma **wartość true** , Jeśli stronicowanie jest wyłączone lub niemożliwe, ponieważ nie ma żadnej `id` kolumny lub gdy nie są dostępne mniej zasobów niż żądanie zapytania. Gdy **resultTruncated** ma **wartość true**, właściwość **$skipToken** nie jest ustawiona.

W poniższych przykładach pokazano, jak **pominąć** pierwsze 3000 rekordów i zwrócić **pierwsze** 1000 rekordów po pominięciu tych rekordów przy użyciu interfejsu wiersza polecenia platformy Azure i Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Zapytanie musi uwzględniać wartość pola **ID** **, aby** można było rozpocząć stronicowanie. Jeśli brakuje tego zapytania, odpowiedź nie będzie zawierać **$skipToken**.

Aby zapoznać się z przykładem, zobacz [następne zapytanie na stronie](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#next-page-query) w dokumentacji interfejsu API REST.

## <a name="formatting-results"></a>Formatowanie wyników

Wyniki zapytania grafu zasobów są dostępne w dwóch formatach, _Table_ i _ObjectArray_. Format jest konfigurowany przy użyciu parametru **resultFormat** jako części opcji żądania. Format _tabeli_ jest wartością domyślną dla **resultFormat**.

Wyniki z interfejsu wiersza polecenia platformy Azure są domyślnie dostępne w formacie JSON. Wyniki w Azure PowerShell są domyślnie **parametr PSCustomObject** , ale można je szybko przekonwertować na format JSON przy użyciu `ConvertTo-Json` polecenia cmdlet. W przypadku innych zestawów SDK wyniki zapytania można skonfigurować w celu wygenerowania danych wyjściowych w formacie _ObjectArray_ .

### <a name="format---table"></a>Format — tabela

Format domyślny _tabeli_ zwraca wyniki w formacie JSON zaprojektowanym do wyróżnienia wartości projektu kolumny i wiersza właściwości zwracanych przez zapytanie. Ten format ściśle przypomina dane zdefiniowane w tabeli strukturalnej lub arkuszu kalkulacyjnym z określonymi kolumnami, a następnie każdy wiersz reprezentuje dane wyrównane do tych kolumn.

Oto przykład wyniku zapytania z formatowaniem _tabeli_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Format — ObjectArray

Format _ObjectArray_ zwraca również wyniki w formacie JSON. Jednak ten projekt jest wyrównany do pary klucz/wartość wspólnych w formacie JSON, gdzie kolumny i dane wierszy są dopasowane w grupach tablic.

Oto przykład wyniku zapytania z formatowaniem _ObjectArray_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Poniżej przedstawiono kilka przykładów ustawiania **resultFormat** do używania formatu _ObjectArray_ :

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [zapytaniach początkowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](../samples/advanced.md).
- Dowiedz się więcej o sposobach [eksplorowania zasobów](explore-resources.md).
