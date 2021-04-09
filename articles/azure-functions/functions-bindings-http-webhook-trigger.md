---
title: Azure Functions wyzwalacza HTTP
description: Dowiedz się, jak wywołać funkcję platformy Azure za pośrednictwem protokołu HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4550be7ae8c543eea1bdfa085db6f23fe668a121
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025700"
---
# <a name="azure-functions-http-trigger"></a>Azure Functions wyzwalacza HTTP

Wyzwalacz HTTP umożliwia wywoływanie funkcji za pomocą żądania HTTP. Można użyć wyzwalacza HTTP do kompilowania bezserwerowych interfejsów API i reagowania na elementy webhook.

Domyślną wartością zwracaną dla funkcji wyzwalanej przez protokół HTTP jest:

- `HTTP 204 No Content` z pustą treścią w funkcjach 2. x i wyższych
- `HTTP 200 OK` z pustą treścią w funkcjach 1. x

Aby zmodyfikować odpowiedź HTTP, skonfiguruj [powiązanie danych wyjściowych](./functions-bindings-http-webhook-output.md).

Aby uzyskać więcej informacji na temat powiązań HTTP, zobacz [Omówienie](./functions-bindings-http-webhook.md) i [informacje wyjściowe powiązania](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która szuka `name` parametru w ciągu zapytania lub treści żądania HTTP. Zwróć uwagę, że wartość zwracana jest używana dla powiązania danych wyjściowych, ale atrybut wartości zwracanej nie jest wymagany.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład pokazuje powiązanie wyzwalacza w *function.jsw* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto *function.js* pliku:

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

Oto kod skryptu w języku C#, który jest powiązany z `HttpRequest` :

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
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Można powiązać z obiektem niestandardowym, a nie `HttpRequest` . Ten obiekt jest tworzony na podstawie treści żądania i przeanalizowany jako kod JSON. Analogicznie, typ może być przekazywane do powiązania danych wyjściowych odpowiedzi HTTP i zwracany jako treść odpowiedzi wraz z `200` kodem stanu.

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

# <a name="java"></a>[Java](#tab/java)

* [Odczytaj parametr z ciągu zapytania](#read-parameter-from-the-query-string)
* [Odczytaj treść z żądania POST](#read-body-from-a-post-request)
* [Odczyt parametru z trasy](#read-parameter-from-a-route)
* [Odczytaj treść POJO z żądania POST](#read-pojo-body-from-a-post-request)

W poniższych przykładach pokazano powiązanie wyzwalacza HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Odczytaj parametr z ciągu zapytania

Ten przykład odczytuje parametr o nazwie `id` , z ciągu zapytania i używa go do skompilowania dokumentu JSON zwróconego do klienta z typem zawartości `application/json` .

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

Ten przykład odczytuje treść żądania POST jako `String` i używa go do skompilowania dokumentu JSON zwróconego do klienta z typem zawartości `application/json` .

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

Ten przykład odczytuje obowiązkowy parametr o nazwie `id` i opcjonalny parametr `name` ze ścieżki trasy, a następnie używa ich do kompilowania dokumentu JSON zwróconego do klienta z typem zawartości `application/json` . T

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

Ten przykład odczytuje treść żądania POST. Treść żądania jest automatycznie deserializowana do `ToDoItem` obiektu i jest zwracana do klienta z typem zawartości `application/json` . `ToDoItem`Parametr jest serializowany przez środowisko uruchomieniowe funkcji w miarę ich przypisywania do `body` właściwości `HttpMessageResponse.Builder` klasy.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza w *function.jsw* pliku oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto *function.js* pliku:

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

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład pokazuje powiązanie wyzwalacza w *function.jsw* pliku i [funkcji programu PowerShell](functions-reference-node.md). Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

Poniższy przykład pokazuje powiązanie wyzwalacza w *function.jsw* pliku i [funkcji języka Python](functions-reference-python.md) , która używa powiązania. Funkcja szuka `name` parametru w ciągu zapytania lub treści żądania HTTP.

Oto *function.js* pliku:

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
            "name": "$return"
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

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

W [bibliotekach klas języka C#](functions-dotnet-class-library.md) i Java `HttpTrigger` atrybut jest dostępny w celu skonfigurowania funkcji.

Można ustawić poziom autoryzacji i dozwolone metody HTTP w parametrach konstruktora atrybutów, typie elementu webhook i szablonie trasy. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Konfiguracja](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `HttpTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Wprowadź** | n/d| Wymagane — musi być ustawiony na wartość `httpTrigger` . |
| **wskazywa** | n/d| Wymagane — musi być ustawiony na wartość `in` . |
| **Nazwij** | n/d| Wymagane — nazwa zmiennej używana w kodzie funkcji dla żądania lub treści żądania. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Określa, jakie klucze (jeśli istnieją) muszą być obecne w żądaniu w celu wywołania funkcji. Poziom autoryzacji może być jedną z następujących wartości: <ul><li><code>anonymous</code>&mdash;Nie jest wymagany żaden klucz interfejsu API.</li><li><code>function</code>&mdash;Wymagany jest klucz interfejsu API specyficzny dla funkcji. Jest to wartość domyślna, jeśli nie podano żadnej z nich.</li><li><code>admin</code>&mdash;Klucz główny jest wymagany.</li></ul> Aby uzyskać więcej informacji, zapoznaj się z sekcją dotyczącej [kluczy autoryzacji](#authorization-keys). |
| **form** |**Metody** | Tablica metod HTTP, do których funkcja reaguje. Jeśli nie zostanie określony, funkcja reaguje na wszystkie metody HTTP. Zobacz [Dostosowywanie punktu końcowego http](#customize-the-http-endpoint). |
| **route** | **Szlak** | Definiuje szablon trasy, który kontroluje adresy URL żądań, które odpowiada funkcja. Wartość domyślna, jeśli nie jest podano `<functionname>` . Aby uzyskać więcej informacji, zobacz [Dostosowywanie punktu końcowego http](#customize-the-http-endpoint). |
| **Element webhooktype** | **Element webhooktype** | _Obsługiwane tylko dla środowiska uruchomieniowego w wersji 1. x._<br/><br/>Konfiguruje wyzwalacz HTTP do działania jako odbiornik [elementu webhook](https://en.wikipedia.org/wiki/Webhook) dla określonego dostawcy. Nie ustawiaj `methods` właściwości w przypadku ustawienia tej właściwości. Typ elementu webhook może być jedną z następujących wartości:<ul><li><code>genericJson</code>&mdash;Punkt końcowy elementu webhook ogólnego przeznaczenia bez logiki dla określonego dostawcy. To ustawienie ogranicza żądania tylko do tych, które używają protokołu HTTP POST i z `application/json` typem zawartości.</li><li><code>github</code>&mdash;Funkcja reaguje na elementy [webhook usługi GitHub](https://developer.github.com/webhooks/). Nie należy używać właściwości  _authLevel_ z elementami webhook usługi GitHub. Aby uzyskać więcej informacji, zobacz sekcję elementy webhook w usłudze GitHub w dalszej części tego artykułu.</li><li><code>slack</code>&mdash;Funkcja reaguje na elementy [webhook zapasowych](https://api.slack.com/outgoing-webhooks). Nie należy używać właściwości _authLevel_ z elementami webhook zapasowych. Aby uzyskać więcej informacji, zobacz sekcję elementy webhooks w dalszej części tego artykułu.</li></ul>|

## <a name="payload"></a>Ładunku

Typ danych wejściowych wyzwalacza jest zadeklarowany jako `HttpRequest` lub jako typ niestandardowy. W przypadku wybrania `HttpRequest` tej opcji uzyskasz pełny dostęp do obiektu żądania. W przypadku typu niestandardowego środowisko uruchomieniowe próbuje przeanalizować treści żądania JSON w celu ustawienia właściwości obiektu.

## <a name="customize-the-http-endpoint"></a>Dostosowywanie punktu końcowego HTTP

Domyślnie podczas tworzenia funkcji dla wyzwalacza HTTP funkcja jest adresowana z trasą w postaci:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

Można dostosować tę trasę przy użyciu właściwości opcjonalnej `route` w powiązaniu danych wejściowych wyzwalacza http. Przykładowo następujące *function.jsw* pliku definiuje `route` Właściwość wyzwalacza http:

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

Ta konfiguracja umożliwia obsługę dwóch parametrów w adresie, _kategorii_ i _identyfikatorze_ funkcji. Aby uzyskać więcej informacji na temat sposobu, w jaki parametry trasy są tokenami w adresie URL, zobacz [Routing w ASP.NET Core](/aspnet/core/fundamentals/routing#route-constraint-reference).

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W węźle środowisko uruchomieniowe Functions dostarcza treści żądania z `context` obiektu. Aby uzyskać więcej informacji, zobacz [przykład wyzwalacza JavaScript](#example).

Poniższy przykład pokazuje, jak odczytywać parametry trasy z programu `context.bindingData` .

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

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Parametry trasy zadeklarowane w *function.jsw* pliku są dostępne jako właściwość `$Request.Params` obiektu.

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

Kontekst wykonywania funkcji jest udostępniany przez parametr zadeklarowany jako `func.HttpRequest` . To wystąpienie umożliwia funkcji dostęp do parametrów trasy danych, wartości ciągu zapytania i metod, które umożliwiają zwracanie odpowiedzi HTTP.

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

---

Domyślnie wszystkie trasy funkcji są poprzedzone *interfejsem API*. Można również dostosować lub usunąć prefiks przy użyciu `extensions.http.routePrefix` właściwości w [host.js](functions-host-json.md) pliku. Poniższy przykład usuwa prefiks trasy *interfejsu API* za pomocą pustego ciągu dla prefiksu w *host.js* pliku.

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Korzystanie z parametrów trasy

Parametry trasy, które zdefiniowano wzorzec funkcji, `route` są dostępne dla każdego powiązania. Na przykład jeśli trasa jest zdefiniowana jako `"route": "products/{id}"` powiązanie magazynu tabel, można użyć wartości `{id}` parametru w konfiguracji powiązania.

Poniższa konfiguracja przedstawia sposób `{id}` przekazywania parametru do powiązania `rowKey` .

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

W przypadku używania parametrów trasy `invoke_URL_template` jest automatycznie tworzony dla funkcji. Klienci mogą używać szablonu adresu URL, aby zrozumieć parametry, które muszą zostać przekazane w adresie URL podczas wywoływania funkcji przy użyciu adresu URL. Przejdź do jednej z funkcji wyzwalanych przez protokół HTTP w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Pobierz adres URL funkcji**.

Można programowo uzyskać dostęp do programu przy `invoke_URL_template` użyciu interfejsów api Azure Resource Manager dla [funkcji list](/rest/api/appservice/webapps/listfunctions) lub [funkcji Get](/rest/api/appservice/webapps/getfunction).

## <a name="working-with-client-identities"></a>Praca z tożsamościami klientów

Jeśli aplikacja funkcji używa [App Service uwierzytelniania/autoryzacji](../app-service/overview-authentication-authorization.md), można wyświetlić informacje o uwierzytelnionych klientach w kodzie. Te informacje są dostępne jako [nagłówki żądań wstrzykiwane przez platformę](../app-service/app-service-authentication-how-to.md#access-user-claims).

Te informacje można również odczytać z poziomu powiązań danych. Ta funkcja jest dostępna tylko dla środowiska uruchomieniowego Functions w wersji 2. x lub nowszej. Jest on również obecnie dostępny tylko dla języków .NET.

# <a name="c"></a>[C#](#tab/csharp)

Informacje dotyczące uwierzytelnionych klientów są dostępne jako [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal jest dostępny jako część kontekstu żądania, jak pokazano w następującym przykładzie:

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

Informacje dotyczące uwierzytelnionych klientów są dostępne jako [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal jest dostępny jako część kontekstu żądania, jak pokazano w następującym przykładzie:

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

# <a name="java"></a>[Java](#tab/java)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

Uwierzytelniony użytkownik jest dostępny za pośrednictwem [nagłówków HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>Klucze dostępu do funkcji

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Uzyskiwanie kluczy

Klucze są przechowywane jako część aplikacji funkcji na platformie Azure i są szyfrowane w stanie spoczynku. Aby wyświetlić klucze, utworzyć nowe lub przerzucić klucze do nowych wartości, przejdź do jednej z funkcji wyzwalanych przez protokół HTTP w [Azure Portal](https://portal.azure.com) i wybierz pozycję **klucze funkcji**.

Można również zarządzać Kluczami hosta. Przejdź do aplikacji funkcji w [Azure Portal](https://portal.azure.com) i wybierz pozycję **klucze aplikacji**.

Można programowo uzyskać klucze funkcji i hosta za pomocą interfejsów API Azure Resource Manager. Istnieją interfejsy API umożliwiające [wyświetlenie listy kluczy funkcji](/rest/api/appservice/webapps/listfunctionkeys) i [wyświetlenie kluczy hosta](/rest/api/appservice/webapps/listhostkeys), a w przypadku korzystania z miejsc wdrożenia równoważne interfejsy API to gniazda [kluczy funkcji list](/rest/api/appservice/webapps/listfunctionkeysslot) i [listę gniazd kluczy hosta](/rest/api/appservice/webapps/listhostkeysslot).

Możesz również programowo utworzyć nowe funkcje i klucze hosta przy użyciu klucza tajnego [funkcji Utwórz lub zaktualizuj](/rest/api/appservice/webapps/createorupdatefunctionsecret), [Utwórz lub zaktualizuj gniazdo tajne](/rest/api/appservice/webapps/createorupdatefunctionsecretslot)funkcji, [Utwórz lub zaktualizuj klucz tajny hosta](/rest/api/appservice/webapps/createorupdatehostsecret) oraz [Utwórz lub zaktualizuj interfejsy API miejsca na wpis tajny hosta](/rest/api/appservice/webapps/createorupdatehostsecretslot) .

Klucze funkcji i hosta można usuwać programowo przy użyciu [klucza tajnego funkcji Delete](/rest/api/appservice/webapps/deletefunctionsecret), [funkcji Delete Secret miejsca](/rest/api/appservice/webapps/deletefunctionsecretslot), [usuwania wpisu tajnego hosta](/rest/api/appservice/webapps/deletehostsecret)i [usuwania interfejsów Secret hosta](/rest/api/appservice/webapps/deletehostsecretslot) .

Możesz również użyć [starszych interfejsów API zarządzania kluczami, aby uzyskać klucze funkcji](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), ale zamiast tego zaleca się używanie Azure Resource Manager interfejsów API.

## <a name="api-key-authorization"></a>Autoryzacja klucza interfejsu API

Większość szablonów wyzwalacza HTTP wymaga klucza interfejsu API w żądaniu. Dlatego żądanie HTTP zwykle wygląda następująco:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

Klucz może być uwzględniony w zmiennej ciągu zapytania o nazwie `code` , jak powyżej. Może być również uwzględniony w `x-functions-key` nagłówku HTTP. Wartość klucza może być dowolnym kluczem funkcji zdefiniowanym dla funkcji lub dowolnym kluczem hosta.

Można zezwolić na żądania anonimowe, które nie wymagają kluczy. Możesz również wymagać użycia klucza głównego. Domyślny poziom autoryzacji można zmienić przy użyciu `authLevel` właściwości w kodzie JSON powiązania. Aby uzyskać więcej informacji, zobacz [wyzwalacz-konfiguracja](#configuration).

> [!NOTE]
> Podczas lokalnego uruchamiania funkcji Autoryzacja jest wyłączona niezależnie od określonego ustawienia poziomu autoryzacji. Po opublikowaniu na platformie Azure `authLevel` wymuszane jest ustawienie w wyzwalaczu. Klucze są nadal wymagane w przypadku uruchamiania [lokalnego w kontenerze](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Zabezpieczanie punktu końcowego HTTP w środowisku produkcyjnym

Aby w pełni zabezpieczyć punkty końcowe funkcji w środowisku produkcyjnym, należy rozważyć implementację jednej z następujących opcji zabezpieczeń na poziomie aplikacji. Korzystając z jednej z tych metod zabezpieczeń na poziomie aplikacji, należy ustawić poziom autoryzacji funkcji wyzwalanej przez protokół HTTP na `anonymous` .

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>Wdróż aplikację funkcji w izolacji

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Elementy webhook

> [!NOTE]
> Tryb elementu webhook jest dostępny tylko dla wersji 1. x środowiska uruchomieniowego funkcji. Ta zmiana została wprowadzona w celu zwiększenia wydajności wyzwalaczy HTTP w wersji 2. x lub nowszej.

W wersji 1. x szablony elementów webhook zapewniają dodatkową weryfikację ładunków elementu webhook. W wersji 2. x i wyższych, podstawowy wyzwalacz HTTP nadal działa i jest zalecanym rozwiązaniem dla elementów webhook. 

### <a name="github-webhooks"></a>Elementy webhook usługi GitHub

Aby odpowiedzieć na elementy webhook usługi GitHub, najpierw Utwórz funkcję z wyzwalaczem HTTP i ustaw właściwość **Webhooktype** na `github` . Następnie skopiuj swój adres URL i klucz interfejsu API na stronę **Dodawanie elementu webhook** w repozytorium GitHub. 

![Zrzut ekranu pokazujący sposób dodawania elementu webhook dla funkcji.](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Elementy webhook zapasu

Element webhook zapasu generuje token dla Ciebie, a nie pozwala na jego określenie, dlatego należy skonfigurować klucz specyficzny dla funkcji z tokenem ze zapasu czasu. Zobacz [klucze autoryzacji](#authorization-keys).

## <a name="webhooks-and-keys"></a>Elementy webhook i klucze

Autoryzacja elementu webhook jest obsługiwana przez składnik odbiornika elementu webhook, część wyzwalacza HTTP, a mechanizm zmienia się w zależności od typu elementu webhook. Każdy mechanizm polega na kluczu. Domyślnie jest używany klucz funkcji o nazwie "default". Aby użyć innego klucza, należy skonfigurować dostawcę elementu webhook w celu wysłania nazwy klucza z żądaniem w jeden z następujących sposobów:

* **Ciąg zapytania**: dostawca przekazuje nazwę klucza w `clientid` parametrze ciągu zapytania, na przykład `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` .
* **Nagłówek żądania**: dostawca przekazuje nazwę klucza w `x-functions-clientid` nagłówku.

## <a name="content-types"></a>Typy zawartości

Przekazywanie danych binarnych i formularzy do funkcji innej niż C wymaga użycia odpowiedniego nagłówka Content-Type. Obsługiwane typy zawartości obejmują `octet-stream` dane binarne i [typy wieloczęściowe](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart).

### <a name="known-issues"></a>Znane problemy

W funkcjach innych niż języka C żądania wysyłane z typem zawartości dają `image/jpeg` w wyniku `string` wartość przekazaną do funkcji. W takich przypadkach można ręcznie skonwertować `string` wartość na tablicę bajtową, aby uzyskać dostęp do nieprzetworzonych danych binarnych.

## <a name="limits"></a>Limity

Długość żądania HTTP jest ograniczona do 100 MB (104 857 600 bajtów), a długość adresu URL jest ograniczona do 4 KB (4 096 bajtów). Limity te są określane przez `httpRuntime` element [ plikuWeb.config](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config)środowiska uruchomieniowego.

Jeśli funkcja, która używa wyzwalacza HTTP, nie zostanie zakończona w ciągu 230 sekund, [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) przekroczy limit czasu i zwróci błąd http 502. Funkcja będzie kontynuowała działanie, ale nie będzie mogła zwracać odpowiedzi HTTP. W przypadku długotrwałych funkcji zalecamy wykonanie wzorców asynchronicznych i zwrócenie lokalizacji, w której można wysłać polecenie ping do stanu żądania. Informacje o tym, jak długo funkcja może być uruchamiana, znajduje się w artykule [skalowanie i planowanie zużycia](functions-scale.md#timeout).


## <a name="next-steps"></a>Następne kroki

- [Zwracanie odpowiedzi HTTP z funkcji](./functions-bindings-http-webhook-output.md)