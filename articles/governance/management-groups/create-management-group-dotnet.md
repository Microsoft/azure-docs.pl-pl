---
title: 'Szybki Start: Tworzenie grupy zarządzania przy użyciu platformy .NET Core'
description: W tym przewodniku szybki start użyjesz platformy .NET Core do utworzenia grupy zarządzania w celu zorganizowania zasobów w hierarchii zasobów.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592578"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Szybki Start: Tworzenie grupy zarządzania przy użyciu platformy .NET Core

Grupy zarządzania to kontenery ułatwiające zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z [Azure Policy](../policy/overview.md) i [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Wykonanie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy, które są uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkowa konfiguracja grup zarządzania](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Nazwa główna usługi platformy Azure, w tym _clientId_ i _clientSecret_. Jeśli nie masz nazwy głównej usługi do użycia z usługą Azure Policy lub chcesz utworzyć nową, zobacz [biblioteki zarządzania platformy Azure dla uwierzytelniania platformy .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Pomiń ten krok, aby zainstalować pakiety .NET Core, jak opisano w następnych krokach.

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez uprawnienia do zapisu grupy zarządzania przypisanej do tego użytkownika, jeśli [Ochrona hierarchii](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nie jest włączona. Ta nowa grupa zarządzania jest elementem podrzędnym głównej grupy zarządzania lub [domyślną grupą zarządzania](./how-to/protect-resource-hierarchy.md#setting---default-management-group) , a twórca otrzymuje przypisanie roli "właściciel". Usługa Grupa zarządzania umożliwia taką możliwość, aby przypisania ról nie były potrzebne na poziomie głównym. Brak dostępu użytkowników do głównej grupy zarządzania podczas jej tworzenia. Aby uniknąć niestosowania progów wyszukiwania administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Konfiguracja aplikacji

Aby włączyć platformę .NET Core do zarządzania grupami zarządzania, Utwórz nową aplikację konsolową i zainstaluj wymagane pakiety.

1. Sprawdź, czy jest zainstalowany najnowszy program .NET Core (co najmniej **3.1.8**). Jeśli nie jest jeszcze zainstalowany, Pobierz go pod adresem [dotnet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Zainicjuj nową aplikację konsolową .NET Core o nazwie "mgCreate":

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Zmień katalogi na folder nowego projektu i zainstaluj wymagane pakiety dla Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Zastąp wartość domyślną `program.cs` następującym kodem i Zapisz zaktualizowany plik:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Kompilowanie i publikowanie `mgCreate` aplikacji konsolowej:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>Tworzenie grupy zarządzania

W tym przewodniku szybki start utworzysz nową grupę zarządzania w głównej grupie zarządzania.

1. Zmień katalogi na `{run-folder}` zdefiniowane za pomocą poprzedniego `dotnet publish` polecenia.

1. Wprowadź następujące polecenie w terminalu:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

Poprzednie polecenia korzystają z następujących informacji:

- `{tenantId}` — zastąp identyfikatorem swojej dzierżawy
- `{clientId}` -Zamień na identyfikator klienta nazwy głównej usługi
- `{clientSecret}` -Zamień na klucz tajny klienta jednostki usługi
- `{groupID}` -Zamień na identyfikator nowej grupy zarządzania
- `{displayName}` -Zamień na przyjazną nazwę nowej grupy zarządzania

Wynik jest nową grupą zarządzania w głównej grupie zarządzania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

- Usuń nową grupę zarządzania za pomocą portalu.

- Jeśli chcesz usunąć aplikację konsolową .NET Core i zainstalowane pakiety, Usuń `mgCreate` folder projektu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami za pomocą grup zarządzania](./manage.md)