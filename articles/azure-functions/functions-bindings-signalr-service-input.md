---
title: Powiązanie danych wejściowych usługi sygnałów Azure Functions
description: Zapoznaj się z tematem zwracanie adresu URL punktu końcowego usługi sygnalizującego i tokenu dostępu w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77530265"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Powiązanie danych wejściowych usługi sygnalizującego dla Azure Functions

Aby klient mógł nawiązać połączenie z usługą Azure Signal, musi pobrać adres URL punktu końcowego usługi i prawidłowy token dostępu. Powiązanie danych wejściowych *SignalRConnectionInfo* generuje adres URL punktu końcowego usługi sygnalizującego oraz prawidłowy token, który jest używany do nawiązywania połączenia z usługą. Ponieważ token jest ograniczony do czasu i może służyć do uwierzytelniania określonego użytkownika w połączeniu, nie należy buforować tokenu ani udostępniać go między klientami. Wyzwalacz HTTP korzystający z tego powiązania może być używany przez klientów do pobierania informacji o połączeniu.

Aby uzyskać więcej informacji na temat tego powiązania do tworzenia funkcji "Negotiate", która może być używana przez zestaw SDK klienta sygnalizującego, zapoznaj się z [artykułem opracowywanie i konfigurowanie Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) w dokumentacji dotyczącej pojęć dotyczących usługi sygnalizującej.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-signalr-service.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[S #](#tab/csharp)

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

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizujące w pliku *Function. JSON* i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

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

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizującego w pliku *Function. JSON* i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

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

Poniższy przykład przedstawia powiązanie danych wejściowych informacji o połączeniu sygnalizującego w pliku *Function. JSON* i funkcji języka [Python](functions-reference-python.md) , która używa powiązania do zwrócenia informacji o połączeniu.

Tutaj podano dane powiązań w pliku *Function. JSON* :

Przykład Function. JSON:

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

App Service Authentication ustawia nagłówki HTTP o `x-ms-client-principal-id` nazwach i `x-ms-client-principal-name` , które zawierają odpowiednio Identyfikator podmiotu zabezpieczeń klienta uwierzytelnionego i nazwę.

# <a name="c"></a>[S #](#tab/csharp)

Można ustawić `UserId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub. `{headers.x-ms-client-principal-name}`

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

Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub. `{headers.x-ms-client-principal-name}`

Przykład Function. JSON:

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

Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub. `{headers.x-ms-client-principal-name}`

Przykład Function. JSON:

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

Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub. `{headers.x-ms-client-principal-name}`

Przykład Function. JSON:

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
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Można ustawić `userId` właściwość powiązania na wartość z dowolnego nagłówka przy użyciu [wyrażenia powiązania](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` lub. `{headers.x-ms-client-principal-name}`

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

- [Komunikaty usługi wysyłania sygnałów (powiązanie danych wyjściowych)](./functions-bindings-signalr-service-output.md) 
