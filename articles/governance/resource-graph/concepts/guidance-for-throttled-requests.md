---
title: Wskazówki dotyczące ograniczonych żądań
description: Zapoznaj się z równoległym grupowaniem, rozłożeniem, stronicowaniem i wykonywaniem zapytań, aby uniknąć ograniczania żądań przez usługę Azure Resource Graph.
ms.date: 10/14/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a8ba991d13b9be221e67f2ff1e393fb01f8a2d4
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056178"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Wskazówki dotyczące żądań z ograniczeniami na wykresie zasobów platformy Azure

Podczas tworzenia programistycznego i częstego używania danych grafu zasobów platformy Azure należy zwrócić uwagę na to, jak ograniczanie wpływa na wyniki zapytań. Zmiana sposobu, w jaki dane są żądane, może pomóc Ci w tym, że Twoja organizacja nie ma ograniczenia i utrzymuje przepływ danych na temat zasobów platformy Azure.

W tym artykule omówiono cztery obszary i wzorce związane z tworzeniem zapytań w usłudze Azure Resource Graph:

- Omówienie nagłówków ograniczeń
- Grupowanie zapytań
- Rozkładanie zapytań
- Wpływ na podział na strony

## <a name="understand-throttling-headers"></a>Omówienie nagłówków ograniczeń

Wykres zasobów platformy Azure przydziela numer przydziału dla każdego użytkownika na podstawie przedziału czasu. Na przykład użytkownik może wysyłać maksymalnie 15 zapytań w każdym 5-drugim oknie bez ograniczania przepustowości. Wartość przydziału jest określana na podstawie wielu czynników i może ulec zmianie.

W każdej odpowiedzi na zapytanie wykres zasobów platformy Azure dodaje dwa nagłówki ograniczania:

- `x-ms-user-quota-remaining` (int): pozostały przydział zasobów dla użytkownika. Ta wartość jest mapowana na liczbę zapytań.
- `x-ms-user-quota-resets-after` (hh: mm: SS): czas trwania do momentu zresetowania zużycia przydziału użytkownika.

Gdy podmiot zabezpieczeń ma dostęp do ponad 5000 subskrypcji w ramach [zakresu zapytania](./query-language.md#query-scope)dzierżawy lub grupy zarządzania, odpowiedź jest ograniczona do pierwszych subskrypcji 5000, a `x-ms-tenant-subscription-limit-hit` nagłówek jest zwracany jako `true` .

Aby zilustrować, jak działają nagłówki, przyjrzyjmy się odpowiedzi kwerendy zawierającej nagłówek i wartości `x-ms-user-quota-remaining: 10` i `x-ms-user-quota-resets-after: 00:00:03` .

- W ciągu następnych 3 sekund można przesłać maksymalnie 10 zapytań bez ograniczenia przepustowości.
- W ciągu 3 sekund wartości `x-ms-user-quota-remaining` i `x-ms-user-quota-resets-after` zostaną zresetowane do `15` i `00:00:05` odpowiednio.

Aby zobaczyć przykład użycia nagłówków do _wycofywania_ na żądaniach zapytań, zobacz test in [Query in Parallel](#query-in-parallel).

## <a name="grouping-queries"></a>Grupowanie zapytań

Grupowanie zapytań według subskrypcji, grupy zasobów lub pojedynczego zasobu jest wydajniejsze niż zapytania przekształcają. Koszt przydziału dla większego zapytania jest często mniejszy niż koszt przydziału dla wielu małych i przeznaczonych zapytań. Zaleca się, aby rozmiar grupy był mniejszy niż _300_.

- Przykład niezoptymalizowanego podejścia

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Przykład #1 zoptymalizowanego podejścia grupowania

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Przykład #2 zoptymalizowanego podejścia grupowania do pobierania wielu zasobów w jednym zapytaniu

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Rozkładanie zapytań

Ze względu na sposób wymuszonego ograniczania przepustowości zalecamy użycie zapytań. Oznacza to, że zamiast wysyłania zapytań 60 w tym samym czasie, należy rozłożyć zapytania na 4 5-sekundowe okna:

- Nierozkładany harmonogram zapytań

  | Liczba zapytań         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Przedział czasu (s) | 0-5 | 5-10 | 10-15 | 15-20 |

- Harmonogram zapytania rozłożonego

  | Liczba zapytań         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Przedział czasu (s) | 0-5 | 5-10 | 10-15 | 15-20 |

Oto przykład przestrzegania nagłówków ograniczania przy wysyłaniu zapytań do grafu zasobów platformy Azure:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Równoległe zapytanie

Mimo że grupowanie jest zalecane w porównaniu z przetwarzanie równoległe, istnieją przypadki, w których zapytania nie mogą być łatwo pogrupowane. W takich przypadkach możesz chcieć zbadać Wykres zasobów platformy Azure, wysyłając jednocześnie wiele zapytań. Oto przykład sposobu _wycofywania_ na podstawie nagłówków ograniczania w takich scenariuszach:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Dzielenie na strony

Ponieważ wykres zasobów platformy Azure zwraca co najwyżej 1000 wpisów w pojedynczej odpowiedzi na zapytanie, może być konieczne podział [zapytań na strony](./work-with-data.md#paging-results) , aby uzyskać kompletny zestaw danych, którego szukasz. Niektórzy klienci grafu zasobów platformy Azure obsługują jednak stronicowanie w inny sposób niż inne.

- Zestaw SDK języka C#

  W przypadku korzystania z zestawu SDK ResourceGraph należy obsługiwać stronicowanie, przekazując token pomijania zwracanego z poprzedniej odpowiedzi zapytania do następnej kwerendy z podziałem na strony. Ten projekt oznacza, że należy zebrać wyniki ze wszystkich wywołań z podziałem na strony i połączyć je razem na końcu. W takim przypadku każde wysłane zapytanie z podziałem na strony ma jeden przydział zapytania:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Interfejs wiersza polecenia platformy Azure/Azure PowerShell

  W przypadku korzystania z interfejsu wiersza polecenia platformy Azure lub Azure PowerShell zapytania do usługi Azure Resource Graph są automatycznie podzielone na strony, aby pobrać maksymalnie 5000 wpisów. Wyniki zapytania zwracają łączną listę wpisów ze wszystkich wywołań z podziałem na strony. W tym przypadku, w zależności od liczby wpisów w wyniku zapytania, pojedyncze zapytanie z podziałem na strony może zużywać więcej niż jeden przydział zapytania. Na przykład w poniższych przykładach pojedynczy przebieg zapytania może zużywać do pięciu zasobów zapytania:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Nadal masz ograniczone ograniczenia?

Jeśli podczas wykonywania powyższych zaleceń masz ograniczone ograniczenia, skontaktuj się z zespołem pod adresem [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com) .

Podaj następujące informacje:

- W przypadku konkretnego limitu ograniczania przepustowości wymagane są wymagania dotyczące przypadków użycia i współpracy.
- Ile zasobów masz dostęp? Ile z nich jest zwracanych z pojedynczego zapytania?
- Jakie typy zasobów interesują Cię?
- Co to jest Twój wzorzec zapytania? X zapytań na sekundę i tak dalej.

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [zapytaniach początkowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](../samples/advanced.md).
- Dowiedz się więcej o sposobach [eksplorowania zasobów](explore-resources.md).