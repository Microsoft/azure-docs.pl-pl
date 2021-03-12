---
title: 'Podpisz żądanie HTTP przy użyciu języka C #'
description: W tym samouczku wyjaśniono, jak wersja języka C# podpisująca żądanie HTTP za pomocą sygnatury HMAC dla usług Azure Communications Services.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: c8cf2eb091aa7ab70fa6dba1a8b1f56bea1a00bf
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631352"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zainstaluj [program Visual Studio](https://visualstudio.microsoft.com/downloads/).
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu usługi Azure Communications Services](../../quickstarts/create-communication-resource.md). W tym samouczku należy zarejestrować **resourceEndpoint** i **resourceAccessKey** .

## <a name="sign-an-http-request-with-c"></a>Podpisz żądanie HTTP przy użyciu języka C #

W celu wygenerowania sygnatury HMAC dla każdego żądania HTTP do uwierzytelniania klucza dostępu jest stosowany wspólny klucz tajny. Ta sygnatura jest generowana przy użyciu algorytmu SHA256 i jest wysyłana w `Authorization` nagłówku przy użyciu `HMAC-SHA256` schematu. Na przykład:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature`Składa się z:

- Czasownik HTTP (na przykład `GET` lub `PUT` )
- Ścieżka żądania HTTP
- Date (Data)
- Host
- x-MS-Content-SHA256

## <a name="setup"></a>Konfigurowanie

Poniższe kroki opisują sposób konstruowania nagłówka autoryzacji.

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

W oknie konsoli, takim jak cmd, PowerShell lub bash, użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `SignHmacTutorial` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: **program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Zmień katalog na nowo utworzony folder aplikacji. Użyj `dotnet build` polecenia, aby skompilować aplikację.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Zainstaluj pakiet

Zainstaluj pakiet `Newtonsoft.Json` , który jest używany do serializacji treści.

```console
dotnet add package Newtonsoft.Json
```

Zaktualizuj `Main` deklarację metody, aby obsługiwała kod asynchroniczny. Użyj poniższego kodu, aby rozpocząć.

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>Utwórz komunikat żądania

Na potrzeby tego przykładu zostanie podpisywany wniosek o utworzenie nowej tożsamości przy użyciu interfejsu API uwierzytelniania usług komunikacyjnych (wersja `2021-03-07` ).

Dodaj następujący kod do metody `Main`:

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create a uri you are going to call.
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Zamień `resourceEndpoint` na rzeczywistą wartość punktu końcowego zasobu.

## <a name="create-a-content-hash"></a>Utwórz skrót zawartości

Skrót zawartości jest częścią podpisu HMAC. Użyj poniższego kodu, aby obliczyć skrót zawartości. Tę metodę można dodać do `Progam.cs` `Main` metody.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Obliczanie sygnatury

Użyj poniższego kodu, aby utworzyć metodę przetwarzania sygnatury HMAC.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Zamień `resourceAccessKey` na klucz dostępu rzeczywistego zasobu usług komunikacyjnych.

## <a name="create-an-authorization-header-string"></a>Tworzenie ciągu nagłówka autoryzacji

Teraz tworzymy ciąg, który dodamy do naszego nagłówka autoryzacji.

1. Oblicz skrót zawartości.
1. Określ sygnaturę czasową skoordynowanego czasu uniwersalnego (UTC).
1. Przygotuj ciąg do podpisania.
1. Oblicz sygnaturę.
1. Połącz ciąg, który będzie używany w nagłówku autoryzacji.
 
Dodaj następujący kod do metody `Main`:

```csharp
// Compute a content hash.
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp.
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature.
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Dodaj nagłówki do requestMessage

Użyj poniższego kodu, aby dodać wymagane nagłówki do `requestMessage` .

```csharp
//Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//Add a date header.
requestMessage.Headers.Add("Date", date);
//Add an authorization header.
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Testowanie klienta

Wywołaj punkt końcowy za pomocą polecenia `HttpClient` , a następnie sprawdź odpowiedź.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
