---
title: plik dołączania
description: plik dołączania
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 5c9066f369183de3b4cfe19cc5635e8f1b4a94a2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779241"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Najnowsza wersja [programu .NET Core Client Library](https://dotnet.microsoft.com/download/dotnet-core) dla danego systemu operacyjnego.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `UserAccessTokensQuickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: **program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Zmień katalog na nowo utworzony folder aplikacji i Użyj `dotnet build` polecenia, aby skompilować aplikację.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę administracyjną usług Azure Communications Services dla platformy .NET za pomocą `dotnet add package` polecenia.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.2
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz plik **program.cs** w edytorze tekstu
1. Dodaj `using` dyrektywę, aby uwzględnić `Azure.Communication.Administration` przestrzeń nazw
1. Aktualizowanie `Main` deklaracji metody do obsługi kodu asynchronicznego

Użyj następującego kodu, aby rozpocząć:

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Zainicjuj `CommunicationIdentityClient` przy użyciu parametrów połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../create-communication-resource.md#store-your-connection-string).

Dodaj następujący kod do metody `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Tworzenie użytkownika

Usługi komunikacyjne Azure obsługują uproszczony katalog tożsamości. Użyj `createUser` metody, aby utworzyć nowy wpis w katalogu z unikatowym `Id` . Należy zachować mapowanie między użytkownikami aplikacji i wygenerowanymi tożsamościami usług komunikacyjnych (np. przechowując je w bazie danych serwera aplikacji).

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Wystaw tokeny dostępu użytkowników

Użyj `issueToken` metody, aby wystawić token dostępu dla użytkownika usług komunikacyjnych. Jeśli nie podasz opcjonalnego `user` parametru, zostanie utworzony nowy użytkownik, który zostanie zwrócony z tokenem.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Tokeny dostępu użytkowników to krótkoterminowe poświadczenia, które należy ponownie wydać, aby uniemożliwić użytkownikom zakłócenia działania usługi. `expiresOn`Właściwość Response wskazuje okres istnienia tokenu.

## <a name="revoke-user-access-tokens"></a>Odwołaj tokeny dostępu użytkowników

W niektórych przypadkach może być konieczne jawne odwołanie tokenów dostępu użytkowników, na przykład gdy użytkownik zmieni hasło używane do uwierzytelniania w usłudze. Ta funkcja jest dostępna za pośrednictwem biblioteki klienta Administracja usług Azure Communication Services.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Usuwanie użytkownika

Usunięcie tożsamości odwołuje wszystkie aktywne tokeny i uniemożliwia wystawianie kolejnych tokenów dla tożsamości. Usuwa również całą zawartość utrwaloną skojarzoną z użytkownikiem.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```console
dotnet run
```
