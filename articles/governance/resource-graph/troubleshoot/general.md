---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z różnymi zestawami SDK podczas badania zasobów platformy Azure za pomocą usługi Azure Resource Graph.
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 0d783b9cb4fa30e3c3e0ff82536b4878c29c7a0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916706"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Rozwiązywanie problemów z błędami przy użyciu grafu zasobów platformy Azure

Podczas wykonywania zapytania dotyczącego zasobów platformy Azure za pomocą usługi Azure Resource Graph mogą wystąpić błędy. W tym artykule opisano różne błędy, które mogą wystąpić i sposoby ich rozwiązywania.

## <a name="finding-error-details"></a>Znajdowanie szczegółów błędu

Większość błędów jest wynikiem problemu podczas wykonywania zapytania przy użyciu grafu zasobów platformy Azure. Gdy zapytanie nie powiedzie się, zestaw SDK zawiera szczegółowe informacje o zapytaniu zakończonym niepowodzeniem. Te informacje wskazują, że problem może zostać naprawiony, a późniejsza kwerenda powiedzie się.

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>Scenariusz: ograniczone żądania

#### <a name="issue"></a>Problem

Klienci tworzący duże lub częste zapytania o zasoby mają ograniczone żądania.

#### <a name="cause"></a>Przyczyna

Wykres zasobów platformy Azure przydziela numer przydziału dla każdego użytkownika na podstawie przedziału czasu. Na przykład użytkownik może wysyłać maksymalnie 15 zapytań w każdym 5-drugim oknie bez ograniczania przepustowości. Wartość przydziału jest określana na podstawie wielu czynników i może ulec zmianie. Aby uzyskać więcej informacji, zobacz [ograniczanie przepływności na wykresie zasobów platformy Azure](../overview.md#throttling).

#### <a name="resolution"></a>Rozwiązanie

Istnieje kilka metod postępowania z ograniczonymi żądaniami:

- [Grupowanie zapytań](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [Rozkładanie zapytań](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [Równoległe zapytanie](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [Dzielenia na strony](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scenariusz: zbyt wiele subskrypcji

#### <a name="issue"></a>Problem

Klienci mający dostęp do ponad 1000 subskrypcji, w tym subskrypcji między dzierżawcami i [usługi Azure Lighthouse](../../../lighthouse/overview.md), nie mogą pobierać danych ze wszystkich subskrypcji w ramach pojedynczego wywołania do grafu zasobów platformy Azure.

#### <a name="cause"></a>Przyczyna

Interfejs wiersza polecenia platformy Azure i program PowerShell przesyłają dalej tylko pierwsze subskrypcje 1000 do grafu zasobów platformy Azure. Interfejs API REST dla usługi Azure Resource Graph akceptuje maksymalną liczbę subskrypcji, na które należy wykonać zapytanie.

#### <a name="resolution"></a>Rozwiązanie

Żądania usługi Batch dotyczące zapytania z podzbiorem subskrypcji w ramach limitu subskrypcji 1000. Rozwiązanie korzysta z parametru **subskrypcji** w programie PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Scenariusz: nieobsługiwany nagłówek REST typu Content-Type

#### <a name="issue"></a>Problem

Klienci wysyłający zapytanie do interfejsu API REST usługi Azure Resource Graph otrzymują odpowiedź _500_ (błąd wewnętrzny serwera).

#### <a name="cause"></a>Przyczyna

Interfejs API REST usługi Azure Resource Graph obsługuje tylko `Content-Type` **aplikację/plik JSON**. Niektóre narzędzia lub agenci REST są domyślnie **tekstem/zwykłym**, co nie jest obsługiwane przez interfejs API REST.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy narzędzie lub Agent używany do wysyłania zapytań do grafu zasobów platformy Azure ma nagłówek interfejsu API REST `Content-Type` skonfigurowany dla **aplikacji/JSON**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scenariusz: Brak uprawnień do odczytu do wszystkich subskrypcji na liście

#### <a name="issue"></a>Problem

Klienci, którzy jawnie przekazują listę subskrypcji za pomocą zapytania grafu zasobów platformy Azure, uzyskują odpowiedź _403_ (dostęp zabroniony).

#### <a name="cause"></a>Przyczyna

Jeśli klient nie ma uprawnienia do odczytu wszystkich podanych subskrypcji, żądanie jest odrzucane z powodu braku odpowiednich praw zabezpieczeń.

#### <a name="resolution"></a>Rozwiązanie

Uwzględnij co najmniej jedną subskrypcję na liście subskrypcji, do której klient uruchamia kwerendę ma co najmniej dostęp do odczytu. Aby uzyskać więcej informacji, zobacz [uprawnienia na wykresie zasobów platformy Azure](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
- Connect with [@AzureSupport](https://twitter.com/azuresupport) — oficjalne konto Microsoft Azure, aby usprawnić obsługę klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.