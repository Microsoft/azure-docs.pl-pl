---
title: Powiązanie danych wejściowych usługi sygnałów Azure Functions
description: Zapoznaj się z tematem zwracanie adresu URL punktu końcowego usługi sygnalizującego i tokenu dostępu w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 3f3a99c83d4a18f3085419b91be947dd67f8eec4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763323"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Powiązanie danych wejściowych usługi sygnalizującego dla Azure Functions

Aby klient mógł nawiązać połączenie z usługą Azure Signal, musi pobrać adres URL punktu końcowego usługi i prawidłowy token dostępu. Powiązanie danych wejściowych *SignalRConnectionInfo* generuje adres URL punktu końcowego usługi sygnalizującego oraz prawidłowy token, który jest używany do nawiązywania połączenia z usługą. Ponieważ token jest ograniczony do czasu i może służyć do uwierzytelniania określonego użytkownika w połączeniu, nie należy buforować tokenu ani udostępniać go między klientami. Wyzwalacz HTTP korzystający z tego powiązania może być używany przez klientów do pobierania informacji o połączeniu.

Aby uzyskać więcej informacji na temat tego powiązania do tworzenia funkcji "Negotiate", która może być używana przez zestaw SDK klienta sygnalizującego, zapoznaj się z [artykułem opracowywanie i konfigurowanie Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) w dokumentacji dotyczącej pojęć dotyczących usługi sygnalizującej.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-signalr-service.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która uzyskuje informacje o połączeniu sygnalizujące przy użyciu powiązania wejściowego i zwraca je za pośrednictwem protokołu HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizujące w *function.jsw* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Poniżej przedstawiono powiązania danych w *function.js* pliku:

Przykład function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod skryptu w języku C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizującego w *function.jsw* pliku i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Poniżej przedstawiono powiązania danych w *function.js* pliku:

Przykład function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizujące w *function.jsw* pliku i funkcji języka [Python](functions-reference-python.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Poniżej przedstawiono powiązania danych w *function.js* pliku:

Przykład function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod języka Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje [funkcję języka Java](functions-reference-java.md) , która uzyskuje informacje o połączeniu sygnalizujące przy użyciu powiązania wejściowego i zwraca je za pośrednictwem protokołu HTTP.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Tokeny uwierzytelnione

Jeśli funkcja jest wyzwalana przez uwierzytelnionego klienta, można dodać do wygenerowanego tokenu identyfikator użytkownika. Uwierzytelnianie w aplikacji funkcji można łatwo dodać przy użyciu [uwierzytelniania App Service](../app-service/overview-authentication-authorization.md).

App Service Authentication ustawia nagłówki HTTP o nazwach `x-ms-client-principal-id` i, `x-ms-client-principal-name` które zawierają odpowiednio Identyfikator podmiotu zabezpieczeń klienta uwierzytelnionego i nazwę.

# <a name="c"></a>[C#](#tab/csharp)

Można ustawić `UserId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}` .

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}` .

Przykład function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod skryptu w języku C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}` .

Przykład function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}` .

Przykład function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Oto kod języka Python:

```python
def main(req: func.HttpRequest, connectionInfo: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfo,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub `{headers.x-ms-client-principal-name}` .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Następne kroki

- [Obsługa komunikatów z usługi sygnalizującego (powiązanie wyzwalacza)](./functions-bindings-signalr-service-trigger.md)
- [Komunikaty usługi wysyłania sygnałów (powiązanie danych wyjściowych)](./functions-bindings-signalr-service-output.md) 
