---
title: Azure Functions wyzwalacza HTTP
description: Dowiedz się, jak wywołać funkcję platformy Azure za pośrednictwem protokołu HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277598"
---
# <a name="azure-functions-http-trigger"></a>Azure Functions wyzwalacza HTTP

Wyzwalacz HTTP umożliwia wywoływanie funkcji za pomocą żądania HTTP. Można użyć wyzwalacza HTTP do kompilowania bezserwerowych interfejsów API i reagowania na elementy webhook.

Domyślną wartością zwracaną dla funkcji wyzwalanej przez protokół HTTP jest:

- `HTTP 204 No Content`z pustą treścią w funkcjach 2. x i wyższych
- `HTTP 200 OK`z pustą treścią w funkcjach 1. x

Aby zmodyfikować odpowiedź HTTP, skonfiguruj [powiązanie danych wyjściowych](./functions-bindings-http-webhook-output.md).

Aby uzyskać więcej informacji na temat powiązań HTTP, zobacz [Omówienie](./functions-bindings-http-webhook.md) i [informacje wyjściowe powiązania](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Przykład

# <a name="c"></a>[S #](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która szuka `name` parametru w ciągu zapytania lub treści żądania HTTP. Zwróć uwagę, że wartość zwracana jest używana dla powiązania danych wyjściowych, ale atrybut wartości zwracanej nie jest wymagany.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład pokazuje powiązanie wyzwalacza w pliku *Function. JSON* oraz [funkcję skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto plik *Function. JSON* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#, który jest `HttpRequest`powiązany z:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Można powiązać z obiektem niestandardowym, `HttpRequest`a nie. Ten obiekt jest tworzony na podstawie treści żądania i przeanalizowany jako kod JSON. Analogicznie, typ może być przekazywane do powiązania danych wyjściowych odpowiedzi HTTP i zwracany jako treść odpowiedzi wraz z kodem `200` stanu.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto plik *Function. JSON* :

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład pokazuje powiązanie wyzwalacza w pliku *Function. JSON* i [funkcji języka Python](functions-reference-python.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto plik *Function. JSON* :

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="java"></a>[Java](#tab/java)

* [Odczytaj parametr z ciągu zapytania](#read-parameter-from-the-query-string)
* [Odczytaj treść z żądania POST](#read-body-from-a-post-request)
* [Odczyt parametru z trasy](#read-parameter-from-a-route)
* [Odczytaj treść POJO z żądania POST](#read-pojo-body-from-a-post-request)

W poniższych przykładach pokazano powiązanie wyzwalacza HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Odczytaj parametr z ciągu zapytania

Ten przykład odczytuje parametr o nazwie `id`, z ciągu zapytania i używa go do skompilowania dokumentu JSON zwróconego do klienta z typem `application/json`zawartości.

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Odczytaj treść z żądania POST

Ten przykład odczytuje treść żądania POST jako `String`i używa go do skompilowania dokumentu JSON zwróconego do klienta z typem `application/json`zawartości.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Odczyt parametru z trasy

Ten przykład odczytuje obowiązkowy parametr o nazwie `id`i opcjonalny parametr `name` ze ścieżki trasy, a następnie używa ich do kompilowania dokumentu JSON zwróconego do klienta z typem `application/json`zawartości. T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>Odczytaj treść POJO z żądania POST

Oto kod `ToDoItem` klasy, do której odwołuje się w tym przykładzie:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Ten przykład odczytuje treść żądania POST. Treść żądania jest automatycznie deserializowana do `ToDoItem` obiektu i jest zwracana do klienta z typem `application/json`zawartości. `ToDoItem` Parametr jest serializowany przez środowisko uruchomieniowe funkcji w miarę ich przypisywania do `body` właściwości `HttpMessageResponse.Builder` klasy.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

W [bibliotekach klas języka C#](functions-dotnet-class-library.md) i Java `HttpTrigger` atrybut jest dostępny w celu skonfigurowania funkcji.

Można ustawić poziom autoryzacji i dozwolone metody HTTP w parametrach konstruktora atrybutów, typie elementu webhook i szablonie trasy. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Konfiguracja](#configuration).

# <a name="c"></a>[S #](#tab/csharp)

W tym przykładzie pokazano, jak używać atrybutu [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [przykład wyzwalacza](#example).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W tym przykładzie pokazano, jak używać atrybutu [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) .

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Aby uzyskać pełny przykład, zobacz [przykład wyzwalacza](#example).

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i w `HttpTrigger` atrybucie.

|Function. JSON — Właściwość | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Wprowadź** | n/d| Wymagane — musi być ustawiony na `httpTrigger`wartość. |
| **wskazywa** | n/d| Wymagane — musi być ustawiony na `in`wartość. |
| **Nazwij** | n/d| Wymagane — nazwa zmiennej używana w kodzie funkcji dla żądania lub treści żądania. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Określa, jakie klucze (jeśli istnieją) muszą być obecne w żądaniu w celu wywołania funkcji. Poziom autoryzacji może być jedną z następujących wartości: <ul><li><code>anonymous</code>&mdash;Nie jest wymagany żaden klucz interfejsu API.</li><li><code>function</code>&mdash;Wymagany jest klucz interfejsu API specyficzny dla funkcji. Jest to wartość domyślna, jeśli nie podano żadnej z nich.</li><li><code>admin</code>&mdash;Klucz główny jest wymagany.</li></ul> Aby uzyskać więcej informacji, zapoznaj się z sekcją dotyczącej [kluczy autoryzacji](#authorization-keys). |
| **form** |**Metody** | Tablica metod HTTP, do których funkcja reaguje. Jeśli nie zostanie określony, funkcja reaguje na wszystkie metody HTTP. Zobacz [Dostosowywanie punktu końcowego http](#customize-the-http-endpoint). |
| **Szlak** | **Trasa** | Definiuje szablon trasy, który kontroluje adresy URL żądań, które odpowiada funkcja. Wartość domyślna, jeśli nie jest podano `<functionname>`. Aby uzyskać więcej informacji, zobacz [Dostosowywanie punktu końcowego http](#customize-the-http-endpoint). |
| **Element webhooktype** | **Element webhooktype** | _Obsługiwane tylko dla środowiska uruchomieniowego w wersji 1. x._<br/><br/>Konfiguruje wyzwalacz HTTP do działania jako odbiornik [elementu webhook](https://en.wikipedia.org/wiki/Webhook) dla określonego dostawcy. Nie ustawiaj `methods` właściwości w przypadku ustawienia tej właściwości. Typ elementu webhook może być jedną z następujących wartości:<ul><li><code>genericJson</code>&mdash;Punkt końcowy elementu webhook ogólnego przeznaczenia bez logiki dla określonego dostawcy. To ustawienie ogranicza żądania tylko do tych, które używają protokołu HTTP POST i `application/json` z typem zawartości.</li><li><code>github</code>&mdash;Funkcja reaguje na elementy [webhook usługi GitHub](https://developer.github.com/webhooks/). Nie należy używać właściwości _authLevel_ z elementami webhook usługi GitHub. Aby uzyskać więcej informacji, zobacz sekcję elementy webhook w usłudze GitHub w dalszej części tego artykułu.</li><li><code>slack</code>&mdash;Funkcja reaguje na elementy [webhook zapasowych](https://api.slack.com/outgoing-webhooks). Nie należy używać właściwości _authLevel_ z elementami webhook zapasowych. Aby uzyskać więcej informacji, zobacz sekcję elementy webhooks w dalszej części tego artykułu.</li></ul>|

## <a name="payload"></a>Ładunku

Typ danych wejściowych wyzwalacza jest zadeklarowany `HttpRequest` jako lub jako typ niestandardowy. W przypadku wybrania tej opcji `HttpRequest`uzyskasz pełny dostęp do obiektu żądania. W przypadku typu niestandardowego środowisko uruchomieniowe próbuje przeanalizować treści żądania JSON w celu ustawienia właściwości obiektu.

## <a name="customize-the-http-endpoint"></a>Dostosowywanie punktu końcowego HTTP

Domyślnie podczas tworzenia funkcji dla wyzwalacza HTTP funkcja jest adresowana z trasą w postaci:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Można dostosować tę trasę przy użyciu właściwości opcjonalnej `route` w powiązaniu danych wejściowych wyzwalacza http. Przykładowo następujący plik *Function. JSON* definiuje `route` Właściwość wyzwalacza http:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Korzystając z tej konfiguracji, funkcja jest teraz adresowana do następującej trasy zamiast oryginalnej trasy.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Ta konfiguracja umożliwia obsługę dwóch parametrów w adresie, _kategorii_ i _identyfikatorze_funkcji.

# <a name="c"></a>[S #](#tab/csharp)

Możesz użyć dowolnego [ograniczenia trasy interfejsu API sieci Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) z parametrami. Poniższy kod funkcji w języku C# korzysta z obu parametrów.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Możesz użyć dowolnego [ograniczenia trasy interfejsu API sieci Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) z parametrami. Poniższy kod funkcji w języku C# korzysta z obu parametrów.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W węźle środowisko uruchomieniowe Functions dostarcza treści żądania z `context` obiektu. Aby uzyskać więcej informacji, zobacz [przykład wyzwalacza JavaScript](#example).

Poniższy przykład pokazuje, jak odczytywać parametry trasy z `context.bindingData`programu.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

Kontekst wykonywania funkcji jest udostępniany przez parametr zadeklarowany jako `func.HttpRequest`. To wystąpienie umożliwia funkcji dostęp do parametrów trasy danych, wartości ciągu zapytania i metod, które umożliwiają zwracanie odpowiedzi HTTP.

Po zdefiniowaniu parametrów trasy są dostępne dla funkcji przez wywołanie `route_params` metody.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

Kontekst wykonywania funkcji jest właściwościami zadeklarowanymi w `HttpTrigger` atrybucie. Ten atrybut umożliwia zdefiniowanie parametrów trasy, poziomów autoryzacji, czasowników HTTP i wystąpienia żądania przychodzącego.

Parametry trasy są definiowane za pośrednictwem `HttpTrigger` atrybutu.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Domyślnie wszystkie trasy funkcji są poprzedzone *interfejsem API*. Można również dostosować lub usunąć prefiks przy użyciu `http.routePrefix` właściwości w pliku [host. JSON](functions-host-json.md) . Poniższy przykład usuwa prefiks trasy *interfejsu API* za pomocą pustego ciągu dla prefiksu w pliku *host. JSON* .

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Korzystanie z parametrów trasy

Parametry trasy, które zdefiniowano `route` wzorzec funkcji, są dostępne dla każdego powiązania. Na przykład jeśli trasa jest zdefiniowana jako `"route": "products/{id}"` powiązanie magazynu tabel, można użyć wartości `{id}` parametru w konfiguracji powiązania.

Poniższa konfiguracja przedstawia sposób przekazywania `{id}` parametru do powiązania `rowKey`.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Praca z tożsamościami klientów

Jeśli aplikacja funkcji używa [App Service uwierzytelniania/autoryzacji](../app-service/overview-authentication-authorization.md), można wyświetlić informacje o uwierzytelnionych klientach w kodzie. Te informacje są dostępne jako [nagłówki żądań wstrzykiwane przez platformę](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Te informacje można również odczytać z poziomu powiązań danych. Ta funkcja jest dostępna tylko dla środowiska uruchomieniowego Functions w wersji 2. x lub nowszej. Jest on również obecnie dostępny tylko dla języków .NET.

# <a name="c"></a>[S #](#tab/csharp)

Informacje dotyczące uwierzytelnionych klientów są dostępne jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal jest dostępny jako część kontekstu żądania, jak pokazano w następującym przykładzie:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternatywnie, ClaimsPrincipal można po prostu uwzględnić jako dodatkowy parametr w sygnaturze funkcji:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Informacje dotyczące uwierzytelnionych klientów są dostępne jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal jest dostępny jako część kontekstu żądania, jak pokazano w następującym przykładzie:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternatywnie, ClaimsPrincipal można po prostu uwzględnić jako dodatkowy parametr w sygnaturze funkcji:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="java"></a>[Java](#tab/java)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

## <a name="authorization-keys"></a>Klucze autoryzacji

Funkcje umożliwiają korzystanie z kluczy, aby utrudnić dostęp do punktów końcowych funkcji HTTP podczas opracowywania.  Jeśli poziom autoryzacji HTTP w funkcji wyzwalanej przez protokół HTTP nie jest `anonymous`ustawiony na, żądania muszą zawierać klucz interfejsu API w żądaniu. 

> [!IMPORTANT]
> Chociaż klucze mogą pomóc w zablokowaniu punktów końcowych HTTP podczas opracowywania, nie są one przeznaczone do zabezpieczania wyzwalacza HTTP w środowisku produkcyjnym. Aby dowiedzieć się więcej, zobacz temat [Zabezpieczanie punktu końcowego HTTP w środowisku produkcyjnym](#secure-an-http-endpoint-in-production).

> [!NOTE]
> W środowisku uruchomieniowym Functions 1. x dostawcy elementów webhook mogą używać kluczy do autoryzowania żądań na różne sposoby, w zależności od tego, co obsługuje dostawca. Dotyczy to elementów [webhook i kluczy](#webhooks-and-keys). Środowisko uruchomieniowe funkcji w wersji 2. x i nowszych nie obejmuje wbudowanej obsługi dostawców elementu webhook.

#### <a name="authorization-scopes-function-level"></a>Zakresy autoryzacji (na poziomie funkcji)

Istnieją dwa zakresy autoryzacji dla kluczy poziomu funkcji:

* **Funkcja**: te klucze dotyczą tylko określonych funkcji, w których są zdefiniowane. Gdy jest używany jako klucz interfejsu API, zezwala na dostęp tylko do tej funkcji.

* **Host**: klucze z zakresem hosta mogą służyć do uzyskiwania dostępu do wszystkich funkcji w aplikacji funkcji. Gdy jest używany jako klucz interfejsu API, zezwalają na dostęp do dowolnej funkcji w aplikacji funkcji. 

Każdy klucz ma nazwę dla odwołania i istnieje klucz domyślny (o nazwie "domyślny") na poziomie funkcji i hosta. Klucze funkcji mają pierwszeństwo przed Kluczami hosta. Gdy dwa klucze są zdefiniowane z tą samą nazwą, klucz funkcji jest zawsze używany.

#### <a name="master-key-admin-level"></a>Klucz główny (poziom administratora) 

Każda aplikacja funkcji ma również klucz hosta na poziomie administratora o nazwie `_master`. Oprócz zapewniania dostępu na poziomie hosta do wszystkich funkcji w aplikacji klucz główny zapewnia również dostęp administracyjny do interfejsów API REST środowiska uruchomieniowego. Nie można odwołać tego klucza. W przypadku ustawienia poziomu autoryzacji programu `admin`żądania muszą używać klucza głównego. wszystkie inne kluczowe wyniki w przypadku niepowodzenia autoryzacji.

> [!CAUTION]  
> Ze względu na podwyższony poziom uprawnień w aplikacji funkcji udzielanej przez klucz główny nie należy udostępniać tego klucza innym podmiotom lub rozpowszechniać go w natywnych aplikacjach klienckich. Należy zachować ostrożność podczas wybierania poziomu autoryzacji administratora.

## <a name="obtaining-keys"></a>Uzyskiwanie kluczy

Klucze są przechowywane jako część aplikacji funkcji na platformie Azure i są szyfrowane w stanie spoczynku. Aby wyświetlić klucze, utworzyć nowe lub przerzucić klucze do nowych wartości, przejdź do jednej z funkcji wyzwalanych przez protokół HTTP w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Zarządzaj**.

![Zarządzaj kluczami funkcji w portalu.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Klucze funkcji można uzyskać programowo przy użyciu [interfejsów API zarządzania kluczami](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

## <a name="api-key-authorization"></a>Autoryzacja klucza interfejsu API

Większość szablonów wyzwalacza HTTP wymaga klucza interfejsu API w żądaniu. Dlatego żądanie HTTP zwykle wygląda następująco:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Klucz może być uwzględniony w zmiennej ciągu zapytania o nazwie `code`, jak powyżej. Może być również uwzględniony w nagłówku `x-functions-key` http. Wartość klucza może być dowolnym kluczem funkcji zdefiniowanym dla funkcji lub dowolnym kluczem hosta.

Można zezwolić na żądania anonimowe, które nie wymagają kluczy. Możesz również wymagać użycia klucza głównego. Domyślny poziom autoryzacji można zmienić przy użyciu `authLevel` właściwości w kodzie JSON powiązania. Aby uzyskać więcej informacji, zobacz [wyzwalacz-konfiguracja](#configuration).

> [!NOTE]
> Podczas lokalnego uruchamiania funkcji Autoryzacja jest wyłączona niezależnie od określonego ustawienia poziomu autoryzacji. Po opublikowaniu na platformie Azure `authLevel` wymuszane jest ustawienie w wyzwalaczu. Klucze są nadal wymagane w przypadku uruchamiania [lokalnego w kontenerze](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Zabezpieczanie punktu końcowego HTTP w środowisku produkcyjnym

Aby w pełni zabezpieczyć punkty końcowe funkcji w środowisku produkcyjnym, należy rozważyć implementację jednej z następujących opcji zabezpieczeń na poziomie aplikacji:

* Włącz App Service uwierzytelnianie/autoryzację dla aplikacji funkcji. Platforma App Service umożliwia korzystanie z usługi Azure Active Directory (AAD) i wielu dostawców tożsamości innych firm do uwierzytelniania klientów. Ta strategia służy do implementowania niestandardowych reguł autoryzacji dla funkcji i można korzystać z informacji o użytkowniku z kodu funkcji. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie i autoryzacja w Azure App Service](../app-service/overview-authentication-authorization.md) i [Praca z tożsamościami klientów](#working-with-client-identities).

* Uwierzytelnianie żądań odbywa się przy użyciu usługi Azure API Management (APIM). APIM zapewnia różne opcje zabezpieczeń interfejsu API dla żądań przychodzących. Aby dowiedzieć się więcej, zobacz [API Management zasady uwierzytelniania](../api-management/api-management-authentication-policies.md). Mając APIM na miejscu, można skonfigurować aplikację funkcji do akceptowania żądań tylko z adresu IP wystąpienia usługi APIM. Aby dowiedzieć się więcej, zobacz temat [Ograniczenia adresów IP](ip-addresses.md#ip-address-restrictions).

* Wdróż aplikację funkcji w Azure App Service Environment (ASE). Środowisko ASE zapewnia dedykowane środowiska hostingu, w ramach którego można uruchamiać funkcje. Środowisko ASE umożliwia skonfigurowanie pojedynczej bramy frontonu, za pomocą której można uwierzytelniać wszystkie żądania przychodzące. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory aplikacji sieci Web (WAF) dla App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Korzystając z jednej z tych metod zabezpieczeń na poziomie aplikacji, należy ustawić poziom autoryzacji funkcji wyzwalanej przez protokół HTTP na `anonymous`.

## <a name="webhooks"></a>Elementy webhook

> [!NOTE]
> Tryb elementu webhook jest dostępny tylko dla wersji 1. x środowiska uruchomieniowego funkcji. Ta zmiana została wprowadzona w celu zwiększenia wydajności wyzwalaczy HTTP w wersji 2. x lub nowszej.

W wersji 1. x szablony elementów webhook zapewniają dodatkową weryfikację ładunków elementu webhook. W wersji 2. x i wyższych, podstawowy wyzwalacz HTTP nadal działa i jest zalecanym rozwiązaniem dla elementów webhook. 

### <a name="github-webhooks"></a>Elementy webhook usługi GitHub

Aby odpowiedzieć na elementy webhook usługi GitHub, najpierw Utwórz funkcję z wyzwalaczem HTTP i ustaw właściwość **Webhooktype** na `github`. Następnie skopiuj swój adres URL i klucz interfejsu API na stronę **Dodawanie elementu webhook** w repozytorium GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Elementy webhook zapasu

Element webhook zapasu generuje token dla Ciebie, a nie pozwala na jego określenie, dlatego należy skonfigurować klucz specyficzny dla funkcji z tokenem ze zapasu czasu. Zobacz [klucze autoryzacji](#authorization-keys).

## <a name="webhooks-and-keys"></a>Elementy webhook i klucze

Autoryzacja elementu webhook jest obsługiwana przez składnik odbiornika elementu webhook, część wyzwalacza HTTP, a mechanizm zmienia się w zależności od typu elementu webhook. Każdy mechanizm polega na kluczu. Domyślnie jest używany klucz funkcji o nazwie "default". Aby użyć innego klucza, należy skonfigurować dostawcę elementu webhook w celu wysłania nazwy klucza z żądaniem w jeden z następujących sposobów:

* **Ciąg zapytania**: dostawca przekazuje nazwę klucza w parametrze ciągu `clientid` zapytania, na przykład `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Nagłówek żądania**: dostawca przekazuje nazwę klucza w `x-functions-clientid` nagłówku.

## <a name="limits"></a>Limity

Długość żądania HTTP jest ograniczona do 100 MB (104 857 600 bajtów), a długość adresu URL jest ograniczona do 4 KB (4 096 bajtów). Limity te są określane przez `httpRuntime` element [pliku Web. config](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config)środowiska uruchomieniowego.

Jeśli funkcja, która używa wyzwalacza HTTP, nie zostanie zakończona w ciągu 230 sekund, [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) przekroczy limit czasu i zwróci błąd http 502. Funkcja będzie kontynuowała działanie, ale nie będzie mogła zwracać odpowiedzi HTTP. W przypadku długotrwałych funkcji zalecamy wykonanie wzorców asynchronicznych i zwrócenie lokalizacji, w której można wysłać polecenie ping do stanu żądania. Informacje o tym, jak długo funkcja może być uruchamiana, znajduje się w artykule [skalowanie i planowanie zużycia](functions-scale.md#timeout).


## <a name="next-steps"></a>Następne kroki

- [Zwracanie odpowiedzi HTTP z funkcji](./functions-bindings-http-webhook-output.md)
