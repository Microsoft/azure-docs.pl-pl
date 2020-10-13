---
title: Funkcje HTTP w Durable Functions-Azure Functions
description: Dowiedz się więcej na temat zintegrowanych funkcji protokołu HTTP w rozszerzeniu Durable Functions Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 4f84ccbddc6f5244ac8f4334b716d770e0ed4afc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328925"
---
# <a name="http-features"></a>Funkcje HTTP

Durable Functions ma kilka funkcji, które ułatwiają dołączanie trwałych aranżacji i jednostek do przepływów pracy protokołu HTTP. W tym artykule szczegółowo przedstawiono niektóre z tych funkcji.

## <a name="exposing-http-apis"></a>Uwidacznianie interfejsów API protokołu HTTP

Aranżacje i jednostki mogą być wywoływane i zarządzane przy użyciu żądań HTTP. Rozszerzenie Durable Functions uwidacznia wbudowane interfejsy API protokołu HTTP. Udostępnia również interfejsy API do współdziałania z aranżacjami i jednostkami z poziomu funkcji wyzwalanych przez protokół HTTP.

### <a name="built-in-http-apis"></a>Wbudowane interfejsy API protokołu HTTP

Rozszerzenie Durable Functions automatycznie dodaje zestaw interfejsów API protokołu HTTP do hosta Azure Functions. Za pomocą tych interfejsów API można korzystać z aranżacji i jednostek oraz zarządzać nimi bez konieczności pisania kodu.

Obsługiwane są następujące wbudowane interfejsy API protokołu HTTP.

* [Rozpocznij nową aranżację](durable-functions-http-api.md#start-orchestration)
* [Wystąpienie aranżacji zapytania](durable-functions-http-api.md#get-instance-status)
* [Przerwij wystąpienie aranżacji](durable-functions-http-api.md#terminate-instance)
* [Wyślij zdarzenie zewnętrzne do aranżacji](durable-functions-http-api.md#raise-event)
* [Przeczyść historię aranżacji](durable-functions-http-api.md#purge-single-instance-history)
* [Wyślij zdarzenie operacji do jednostki](durable-functions-http-api.md#signal-entity)
* [Pobieranie stanu jednostki](durable-functions-http-api.md#get-entity)
* [Zapytanie dotyczące listy jednostek](durable-functions-http-api.md#list-entities)

Zobacz artykuł dotyczący [interfejsów API protokołu HTTP](durable-functions-http-api.md) , aby uzyskać pełny opis wszystkich wbudowanych interfejsów API protokołu HTTP uwidocznionych przez rozszerzenie Durable Functions.

### <a name="http-api-url-discovery"></a>Odnajdowanie adresów URL interfejsu API protokołu HTTP

[Powiązanie klienta aranżacji](durable-functions-bindings.md#orchestration-client) udostępnia interfejsy API, które mogą generować wygodne ładunki odpowiedzi HTTP. Na przykład może utworzyć odpowiedź zawierającą linki do interfejsów API zarządzania dla określonego wystąpienia aranżacji. W poniższych przykładach pokazano funkcję wyzwalacza HTTP, która pokazuje, jak używać tego interfejsu API dla nowego wystąpienia aranżacji:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

# <a name="python"></a>[Python](#tab/python)

**__init__. PR**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    function_name = req.route_params['functionName']
    event_data = req.get_body()

    instance_id = await client.start_new(function_name, instance_id, event_data)
    
    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)
```

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

---

Uruchamianie funkcji programu Orchestrator za pomocą funkcji wyzwalacza protokołu HTTP pokazanych wcześniej można wykonać przy użyciu dowolnego klienta HTTP. Następujące polecenie zwinięcie uruchamia funkcję programu Orchestrator o nazwie `DoWork` :

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Dalej to Przykładowa odpowiedź dla aranżacji, która ma `abc123` jako identyfikator. Niektóre szczegóły zostały usunięte w celu przejrzystości.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

W poprzednim przykładzie każde z pól kończące się `Uri` odnosi się do wbudowanego interfejsu API protokołu HTTP. Za pomocą tych interfejsów API można zarządzać docelowym wystąpieniem aranżacji.

> [!NOTE]
> Format adresów URL elementu webhook zależy od używanej wersji hosta Azure Functions. Poprzedni przykład dotyczy hosta Azure Functions 2,0.

Aby uzyskać opis wszystkich wbudowanych interfejsów API protokołu HTTP, zobacz [Dokumentacja interfejsu API protokołu HTTP](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Śledzenie operacji asynchronicznych

Wspomniana wcześniej odpowiedź HTTP została zaprojektowana w celu ułatwienia wdrożenia długotrwałych asynchronicznych interfejsów API protokołu HTTP przy użyciu Durable Functions. Ten wzorzec jest czasami nazywany *wzorcem klienta sondowania*. Przepływ klient/serwer działa w następujący sposób:

1. Klient wysyła żądanie HTTP w celu uruchomienia długotrwałego procesu, takiego jak funkcja programu Orchestrator.
1. Docelowy wyzwalacz HTTP zwraca odpowiedź HTTP 202 z nagłówkiem lokalizacji, który ma wartość "statusQueryGetUri".
1. Klient sonduje adres URL w nagłówku lokalizacji. Klient nadal widzi odpowiedzi HTTP 202 z nagłówkiem lokalizacji.
1. Gdy wystąpienie zostanie zakończone lub nie powiedzie się, punkt końcowy w nagłówku lokalizacji zwraca HTTP 200.

Ten protokół umożliwia koordynację długotrwałych procesów z zewnętrznymi klientami lub usługami, które mogą sondować punkt końcowy HTTP i podążać za nagłówkiem lokalizacji. Zarówno implementacja klienta, jak i serwera tego wzorca jest wbudowana w Durable Functions interfejsów API protokołu HTTP.

> [!NOTE]
> Domyślnie wszystkie działania oparte na protokole HTTP zapewniane przez [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) obsługują standardowy wzorzec operacji asynchronicznej. Ta funkcja umożliwia osadzenie długotrwałej funkcji trwałej w ramach przepływu pracy Logic Apps. Więcej szczegółowych informacji na temat Logic Apps obsługi asynchronicznych wzorców HTTP można znaleźć w temacie [Azure Logic Apps akcje przepływu pracy i wyzwalacze](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interakcje z aranżacjami mogą być wykonywane z dowolnego typu funkcji, a nie tylko funkcji wyzwalanych przez protokół HTTP.

Aby uzyskać więcej informacji na temat zarządzania aranżacjami i jednostkami przy użyciu interfejsów API klienta, zobacz artykuł dotyczący [zarządzania wystąpieniem](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Używanie interfejsów API protokołu HTTP

Zgodnie z opisem w temacie [ograniczenia kodu funkcji programu Orchestrator](durable-functions-code-constraints.md)funkcje programu Orchestrator nie mogą bezpośrednio wykonywać operacji we/wy. Zamiast tego zwykle wywołują [funkcje działania](durable-functions-types-features-overview.md#activity-functions) , które wykonują operacje we/wy.

Począwszy od Durable Functions 2,0, aranżacje mogą natywnie korzystać z interfejsów API protokołu HTTP przy użyciu [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger).

Poniższy przykładowy kod przedstawia funkcję programu Orchestrator wykonującą wychodzące żądanie HTTP:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    response = yield context.call_http('GET', url)
    
    if response["statusCode"] >= 400:
        # handling of error codes goes here

main = df.Orchestrator.create(orchestrator_function)
```

---

Za pomocą akcji "Call HTTP" można wykonywać następujące akcje w funkcjach programu Orchestrator:

* Wywołaj interfejsy API protokołu HTTP bezpośrednio z funkcji aranżacji, z pewnymi ograniczeniami wymienionymi w dalszej części.
* Automatycznie obsługuje wzorce sondowania stanu HTTP 202 po stronie klienta.
* Użyj [tożsamości zarządzanych przez platformę Azure](../../active-directory/managed-identities-azure-resources/overview.md) , aby uzyskać autoryzowane wywołania http do innych punktów końcowych platformy Azure.

Możliwość korzystania z interfejsów API protokołu HTTP bezpośrednio z funkcji programu Orchestrator jest zaprojektowana jako wygoda dla pewnego zestawu typowych scenariuszy. Wszystkie te funkcje można zaimplementować samodzielnie za pomocą funkcji działania. W wielu przypadkach funkcje działania mogą zapewnić większą elastyczność.

### <a name="http-202-handling"></a>Obsługa protokołu HTTP 202

Interfejs API "Call HTTP" może automatycznie implementować stronę klienta wzorca sondowania. Jeśli interfejs API o nazwie zwróci odpowiedź HTTP 202 z nagłówkiem lokalizacji, funkcja programu Orchestrator automatycznie sonduje zasób lokalizacji do momentu otrzymania odpowiedzi innej niż 202. Ta odpowiedź będzie odpowiedzią na kod funkcji programu Orchestrator.

> [!NOTE]
> Program Orchestrator Functions również natywnie obsługuje wzorzec klienta sondowania po stronie serwera, zgodnie z opisem w temacie [śledzenie operacji asynchronicznych](#async-operation-tracking). Ta pomoc techniczna oznacza, że aranżacje w jednej aplikacji funkcji mogą łatwo koordynować funkcje programu Orchestrator w innych aplikacjach funkcji. Jest to podobne do koncepcji [aranżacji podrzędnej](durable-functions-sub-orchestrations.md) , ale z obsługą komunikacji między aplikacjami. Ta obsługa jest szczególnie przydatna w przypadku tworzenia aplikacji w stylu mikrousług.

### <a name="managed-identities"></a>Tożsamości zarządzane

Durable Functions natywnie obsługuje wywołania interfejsów API, które akceptują tokeny Azure Active Directory (Azure AD) na potrzeby autoryzacji. Ta obsługa używa [tożsamości zarządzanych przez platformę Azure](../../active-directory/managed-identities-azure-resources/overview.md) do uzyskiwania tych tokenów.

Poniższy kod stanowi przykład funkcji programu .NET Orchestrator. Funkcja wykonuje uwierzytelnione wywołania do ponownego uruchomienia maszyny wirtualnej za pomocą [interfejsu API REST maszyn wirtualnych](/rest/api/compute/virtualmachines)Azure Resource Manager.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net/.default
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net/.default"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net/.default");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    subscription_id = "mySubId"
    resource_group = "myRg"
    vm_name = "myVM"
    api_version = "2019-03-01"
    token_source = df.ManagedIdentityTokenSource("https://management.core.windows.net/.default")

    # get a list of the Azure subscriptions that I have access to
    restart_response = yield context.call_http("POST", 
        f"https://management.azure.com/subscriptions/${subscription_id}/resourceGroups/${resource_group}/providers/Microsoft.Compute/virtualMachines/${vm_name}/restart?api-version=${api_version}",
        None,
        None,
        token_source)
    return restart_response

main = df.Orchestrator.create(orchestrator_function)
```

---

W poprzednim przykładzie `tokenSource` parametr jest skonfigurowany do uzyskiwania tokenów usługi Azure AD dla [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Tokeny są identyfikowane przez identyfikator URI zasobu `https://management.core.windows.net/.default` . W przykładzie przyjęto założenie, że bieżąca aplikacja funkcji działa lokalnie lub została wdrożona jako aplikacja funkcji z tożsamością zarządzaną. Przyjęto założenie, że lokalna tożsamość lub zarządzana tożsamość ma uprawnienia do zarządzania maszynami wirtualnymi w określonej grupie zasobów `myRG` .

W czasie wykonywania skonfigurowane Źródło tokenu automatycznie zwraca token dostępu OAuth 2,0. Źródło dodaje token jako token okaziciela do nagłówka autoryzacji żądania wychodzącego. Ten model to poprawa ręcznego dodawania nagłówków autoryzacji do żądań HTTP z następujących powodów:

* Odświeżanie tokenu jest obsługiwane automatycznie. Nie musisz martwić się o wygasłe tokeny.
* Tokeny nigdy nie są przechowywane w stanie nietrwałej aranżacji.
* Nie musisz pisać żadnego kodu, aby zarządzać pozyskiwaniem tokenów.

Możesz znaleźć bardziej kompletny przykład w [prekompilowanym przykładzie RestartVMs języka C#](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs).

Tożsamości zarządzane nie są ograniczone do zarządzania zasobami platformy Azure. Przy użyciu tożsamości zarządzanych można uzyskiwać dostęp do dowolnych interfejsów API, które akceptują tokeny okaziciela usługi Azure AD, w tym usługi platformy Azure firmy Microsoft i aplikacje sieci Web od partnerów. Aplikacja internetowa partnera może nawet być inną aplikacją funkcji. Aby uzyskać listę usług platformy Azure firmy Microsoft, które obsługują uwierzytelnianie w usłudze Azure AD, zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Ograniczenia

Wbudowana obsługa wywoływania interfejsów API protokołu HTTP jest wygodną funkcją. Nie jest ona odpowiednia dla wszystkich scenariuszy.

Żądania HTTP wysyłane przez funkcje programu Orchestrator i ich odpowiedzi są serializowane i trwałe jako komunikaty w kolejce. To zachowanie w kolejce zapewnia niezawodne i bezpieczne wywoływanie połączeń HTTP [w celu aranżacji](durable-functions-orchestrations.md#reliability). Jednak zachowanie kolejkowania ma także ograniczenia:

* Każde żądanie HTTP wymaga dodatkowego opóźnienia w porównaniu do macierzystego klienta HTTP.
* Duże komunikaty żądania lub odpowiedzi, które nie pasują do komunikatu kolejki, mogą znacząco obniżyć wydajność aranżacji. Obciążenie odciążania ładunków komunikatów do magazynu obiektów BLOB może spowodować potencjalne obniżenie wydajności.
* Przesyłanie strumieniowe, fragmenty i ładunki binarne nie są obsługiwane.
* Możliwość dostosowywania zachowania klienta HTTP jest ograniczona.

Jeśli którykolwiek z tych ograniczeń ma wpływ na przypadek użycia, rozważ użycie funkcji działania i bibliotek klienckich protokołu HTTP specyficznych dla języka, aby wychodzące wywołania HTTP.

> [!NOTE]
> Jeśli jesteś deweloperem platformy .NET, możesz zastanawiać się, dlaczego ta funkcja używa typów **DurableHttpRequest** i **DurableHttpResponse** zamiast wbudowanych typów .NET **HttpRequestMessage** i **HttpResponseMessage** .
>
> Ten wybór projektu jest zamierzony. Główną przyczyną jest to, że typy niestandardowe pomagają zapewnić, że użytkownicy nie będą wprowadzać nieprawidłowych założeń dotyczących obsługiwanych zachowań wewnętrznego klienta HTTP. Typy specyficzne dla Durable Functions umożliwiają również uproszczenie projektowania interfejsu API. Mogą również łatwiej udostępnić dostępne specjalne funkcje, takie jak [integracja tożsamości zarządzanej](#managed-identities) i [wzorzec klienta sondowania](#http-202-handling). 

### <a name="extensibility-net-only"></a>Rozszerzalność (tylko platforma .NET)

Dostosowanie zachowania wewnętrznego klienta protokołu HTTP aranżacji jest możliwe przy użyciu [Azure Functions iniekcja zależności .NET](../functions-dotnet-dependency-injection.md). Ta możliwość może być przydatna do wprowadzania małych zmian w zachowaniu. Może być również przydatna do testowania jednostkowego klienta HTTP przez wstrzyknięcie obiektów makiety.

Poniższy przykład demonstruje użycie iniekcji zależności, aby wyłączyć weryfikację certyfikatu TLS/SSL dla funkcji programu Orchestrator, które wywołują zewnętrzne punkty końcowe HTTP.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o jednostkach trwałych](durable-functions-entities.md)
