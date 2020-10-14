---
title: 'Szybki Start: pierwsze zapytanie interfejsu API REST'
description: W tym przewodniku szybki start wykonaj kroki w celu wywołania punktu końcowego grafu zasobów dla interfejsu API REST i uruchomienia pierwszego zapytania.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 8be0556b6aa2fd234c0f3e25d83c0239c8f674bb
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057233"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu interfejsu API REST

Pierwszym krokiem do korzystania z usługi Azure Resource Graph przy użyciu interfejsu API REST jest sprawdzenie, czy masz dostępne narzędzie do wywoływania interfejsów API REST. Ten przewodnik Szybki Start przeprowadzi Cię przez proces uruchamiania zapytania i pobiera wyniki, wywołując punkt końcowy interfejsu API REST usługi Azure Resource Graph.

Po zakończeniu tego procesu będziesz mieć narzędzia do wywoływania punktów końcowych interfejsu API REST i uruchamiania pierwszego zapytania grafu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Wprowadzenie do interfejsu API REST

Jeśli jeszcze nie znasz interfejsu API REST, zacznij od przejrzenia [dokumentacji interfejsu API REST platformy Azure](/rest/api/azure/), aby uzyskać ogólny opis interfejsu API REST, a w szczególności identyfikatora URI żądania i treści żądania. W tym artykule wykorzystano te koncepcje w celu uzyskania wskazówek dotyczących pracy z usługą Azure Resource Graph i założono, że będzie ona pracowała nad nimi. Narzędzia, między innymi takie jak [ARMClient](https://github.com/projectkudu/ARMClient), mogą automatycznie obsługiwać autoryzację i są zalecane dla początkujących.

Aby uzyskać dane dotyczące specyfikacji grafu zasobów platformy Azure, zobacz [interfejs API REST usługi Azure Resource Graph](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>Interfejs API REST i program PowerShell

Jeśli jeszcze nie masz narzędzia do wykonywania wywołań interfejsu API REST, rozważ wykonywanie tych instrukcji w programie PowerShell. Poniższy przykładowy kod pobiera nagłówek do uwierzytelniania za pomocą platformy Azure. Wygeneruj nagłówek uwierzytelniania, czasami nazywany **tokenem elementu nośnego**, i podaj identyfikator URI interfejsu API REST, z którym chcesz nawiązać połączenie, za pomocą dowolnych parametrów lub **treści żądania**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Zamień `{subscriptionId}` na `$restUri` zmienną, aby uzyskać informacje o subskrypcji.
`$response`Zmienna przechowuje wynik `Invoke-RestMethod` polecenia cmdlet, które można analizować za pomocą poleceń cmdlet, takich jak [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json). Jeśli punkt końcowy usługi interfejsu API REST oczekuje **treści żądania**, podaj zmienną w formacie JSON dla parametru `-Body` polecenia `Invoke-RestMethod`.

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Za pomocą narzędzi interfejsu API REST dodanych do wybranego środowiska można wypróbować prostą kwerendę grafu zasobów. Zapytanie zwraca pierwsze pięć zasobów platformy Azure o **nazwie** i **typie zasobu** każdego zasobu.

W treści żądania każdego wywołania interfejsu API REST istnieje zmienna, która jest używana, aby zastąpić własną wartość:

- `{subscriptionID}` — zastąp swoim identyfikatorem subskrypcji

1. Uruchom pierwsze zapytanie dotyczące wykresu zasobów platformy Azure przy użyciu interfejsu API REST i `resources` punktu końcowego:

   - Identyfikator URI interfejsu API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Treść żądania

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
         }
     }
     ```

   > [!NOTE]
   > Ponieważ w tym przykładzie zapytania nie określono modyfikatora sortowania, takiego jak `order by` uruchomienie tego zapytania wiele razy, prawdopodobnie zostanie wyznaczony inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj wywołanie do `resouces` punktu końcowego i Zmień **zapytanie** na `order by` Właściwość **name** :

   - Identyfikator URI interfejsu API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Treść żądania

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
         }
     }
     ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zaktualizuj wywołanie do `resources` punktu końcowego i Zmień **zapytanie** , tak aby najpierw miało `order by` Właściwość **name** , a następnie `limit` na pięć pierwszych wyników:

   - Identyfikator URI interfejsu API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Treść żądania

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
         }
     }
     ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

Aby uzyskać więcej przykładów wywołań interfejsu API REST dla usługi Azure Resource Graph, zobacz [przykłady użycia wykresu zasobów platformy Azure](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Interfejs API REST nie ma bibliotek ani modułów do odinstalowania. Jeśli zainstalowano narzędzie, takie jak _ARMClient_ lub _Poster_ , aby wykonać wywołania i nie będzie już potrzebne, możesz odinstalować narzędzie teraz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wywołano punkt końcowy interfejsu API REST grafu zasobów i uruchomiono pierwsze zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
