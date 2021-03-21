---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: a0f8744061853e8bd81d3435c1f007e96a7d5783
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495331"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Najnowsza wersja [programu .NET Core Client Library](https://dotnet.microsoft.com/download/dotnet-core) dla danego systemu operacyjnego.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `AccessTokensQuickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: **program. cs**.

```console
dotnet new console -o AccessTokensQuickstart
```

Zmień katalog na nowo utworzony folder aplikacji i Użyj `dotnet build` polecenia, aby skompilować aplikację.

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę tożsamości usług Azure Communication Services dla platformy .NET za pomocą `dotnet add package` polecenia.

```console
dotnet add package Azure.Communication.Identity --version 1.0.0-beta.5
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz plik **program. cs** w edytorze tekstu
1. Dodaj `using` dyrektywę, aby uwzględnić `Azure.Communication.Identity` przestrzeń nazw
1. Aktualizowanie `Main` deklaracji metody do obsługi kodu asynchronicznego

Użyj następującego kodu, aby rozpocząć:

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Zainicjuj `CommunicationIdentityClient` przy użyciu parametrów połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../create-communication-resource.md#store-your-connection-string).

Dodaj następujący kod do metody `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

Alternatywnie możesz oddzielić punkt końcowy i klucz dostępu.
```csharp
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
string endpoint = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ENDPOINT");
string accessKey = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ACCESSKEY");
var client = new CommunicationIdentityClient(new Uri(endpoint), new AzureKeyCredential(accessKey));
```

Jeśli masz skonfigurowaną tożsamość zarządzaną, zobacz [Korzystanie z tożsamości zarządzanych](../managed-identity.md), ale także uwierzytelnianie przy użyciu tożsamości zarządzanej.
```csharp
TokenCredential tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Tworzenie tożsamości

Usługi komunikacyjne Azure obsługują uproszczony katalog tożsamości. Użyj `createUser` metody, aby utworzyć nowy wpis w katalogu z unikatowym `Id` . Przechowywanie otrzymanej tożsamości z mapowaniem do użytkowników aplikacji. Można na przykład przechowywać je w bazie danych serwera aplikacji. Tożsamość jest wymagana później, aby można było wystawiać tokeny dostępu.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Wystawianie tokenów dostępu do tożsamości

Użyj `GetToken` metody, aby wystawić token dostępu dla istniejącej tożsamości usług komunikacyjnych. Parametr `scopes` definiuje zestaw elementów pierwotnych, który będzie autoryzować ten token dostępu. Zapoznaj się z [listą obsługiwanych akcji](../../concepts/authentication.md). Nowe wystąpienie parametru `communicationUser` można utworzyć na podstawie ciągu reprezentującego tożsamość usługi komunikacyjnej platformy Azure.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Tokeny dostępu to krótkoterminowe poświadczenia, które należy ponownie wydać. Nie może to spowodować zakłócenia środowiska użytkownika aplikacji. `expiresOn`Właściwość Response wskazuje okres istnienia tokenu dostępu.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Tworzenie tożsamości i wystawianie tokenu dostępu w ramach tego samego żądania

Użyj `CreateUserAndTokenAsync` metody, aby utworzyć tożsamość usługi komunikacyjnej i wydać dla niej token dostępu. Parametr `scopes` definiuje zestaw elementów pierwotnych, który będzie autoryzować ten token dostępu. Zapoznaj się z [listą obsługiwanych akcji](../../concepts/authentication.md).

```csharp
// Issue an identity and an access token with the "voip" scope for the new identity
var identityAndTokenResponse = await client.CreateUserAndTokenAsync(scopes: new[] { CommunicationTokenScope.VoIP });
var identity = identityAndTokenResponse.Value.User;
var token = identityAndTokenResponse.Value.AccessToken.Token;
var expiresOn = identityAndTokenResponse.Value.AccessToken.ExpiresOn;

Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

## <a name="refresh-access-tokens"></a>Odświeżenie tokenów dostępu

Aby odświeżyć token dostępu, Przekaż wystąpienie `CommunicationUserIdentifier` obiektu do `GetTokenAsync` . Jeśli zostały one zapisane `Id` i trzeba utworzyć nowe, można to `CommunicationUserIdentifier` zrobić, przechodząc `Id` do konstruktora przechowywanego w `CommunicationUserIdentifier` następujący sposób:

```csharp
// In this example, userId is a string containing the Id property of a previously-created CommunicationUser
var identityToRefresh = new CommunicationUserIdentifier(userId);
var tokenResponse = await client.GetTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>Odwołaj tokeny dostępu

W niektórych przypadkach można jawnie odwołać tokeny dostępu. Na przykład, gdy użytkownik aplikacji zmieni hasło używane do uwierzytelniania w usłudze. Metoda `RevokeTokensAsync` unieważnia wszystkie aktywne tokeny dostępu, które zostały wystawione dla tożsamości.

```csharp
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>Usuwanie tożsamości

Usunięcie tożsamości odwołuje wszystkie aktywne tokeny dostępu i uniemożliwia wystawianie tokenów dostępu dla tożsamości. Usuwa również całą zawartość utrwaloną skojarzoną z tożsamością.

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```console
dotnet run
```
