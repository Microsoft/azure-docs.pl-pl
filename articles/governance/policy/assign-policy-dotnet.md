---
title: 'Szybki Start: nowe przypisanie zasad z platformą .NET Core'
description: W tym przewodniku szybki start użyjesz platformy .NET Core do utworzenia przypisania Azure Policy, aby zidentyfikować niezgodne zasoby.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91604692"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu platformy .NET Core

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. W tym przewodniku Szybki start utworzysz przypisanie zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu zidentyfikujesz maszyny wirtualne, które nie są _zgodne_.

Biblioteka .NET Core służy do zarządzania zasobami platformy Azure. W tym przewodniku wyjaśniono, jak za pomocą biblioteki .NET Core Azure Policy utworzyć przypisanie zasad.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- Nazwa główna usługi platformy Azure, w tym _clientId_ i _clientSecret_. Jeśli nie masz nazwy głównej usługi do użycia z usługą Azure Policy lub chcesz utworzyć nową, zobacz [biblioteki zarządzania platformy Azure dla uwierzytelniania platformy .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Pomiń ten krok, aby zainstalować pakiety .NET Core, jak opisano w następnych krokach.

## <a name="create-the-azure-policy-project"></a>Tworzenie projektu Azure Policy

Aby włączyć platformę .NET Core do zarządzania Azure Policy, Utwórz nową aplikację konsolową i zainstaluj wymagane pakiety.

1. Sprawdź, czy jest zainstalowany najnowszy program .NET Core (co najmniej **3.1.8**). Jeśli nie jest jeszcze zainstalowany, Pobierz go pod adresem [dotnet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Zainicjuj nową aplikację konsolową .NET Core o nazwie "policyAssignment":

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Zmień katalogi na folder nowego projektu i zainstaluj wymagane pakiety dla Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

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
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Kompilowanie i publikowanie `policyAssignment` aplikacji konsolowej:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz **maszyny wirtualne inspekcji, które nie używają definicji Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Ta definicja zasad identyfikuje zasoby, które nie spełniają warunków określonych w definicji zasad.

1. Zmień katalogi na `{run-folder}` zdefiniowane za pomocą poprzedniego `dotnet publish` polecenia.

1. Wprowadź następujące polecenie w terminalu:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

Poprzednie polecenia korzystają z następujących informacji:

- `{tenantId}` — zastąp identyfikatorem swojej dzierżawy
- `{clientId}` -Zamień na identyfikator klienta nazwy głównej usługi
- `{clientSecret}` -Zamień na klucz tajny klienta jednostki usługi
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji
- **Nazwa** — unikatowa nazwa obiektu przypisania zasad. Powyższy przykład używa _inspekcji-VM-manageddisks_.
- **DisplayName** — nazwa wyświetlana przypisania zasad. W takim przypadku używasz _maszyn wirtualnych inspekcji bez przypisania dysków zarządzanych_.
- **policyDefID** — ścieżka definicji zasad, na podstawie której tworzysz przypisanie. W takim przypadku jest IDENTYFIKATORem _maszyn wirtualnych inspekcji definicji zasad, które nie korzystają z dysków zarządzanych_.
- **Opis** — dokładniejsze wyjaśnienie tego, czym są zasady lub dlaczego są przypisane do tego zakresu.
- **zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może ona przedziały od grupy zarządzania do pojedynczego zasobu. Pamiętaj, aby zastąpić `{scope}` jednym z następujących wzorców:
  - Grupa zarządzania: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Subskrypcje: `/subscriptions/{subscriptionId}`
  - Grupa zasobów: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Zasoby `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Teraz możesz zidentyfikować niezgodne zasoby, aby zrozumieć stan zgodności środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Po utworzeniu przypisania zasad można zidentyfikować niezgodne zasoby.

1. Zainicjuj nową aplikację konsolową .NET Core o nazwie "policyCompliance":

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Zmień katalogi na folder nowego projektu i zainstaluj wymagane pakiety dla Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

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
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Kompilowanie i publikowanie `policyAssignment` aplikacji konsolowej:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Zmień katalogi na `{run-folder}` zdefiniowane za pomocą poprzedniego `dotnet publish` polecenia.

1. Wprowadź następujące polecenie w terminalu:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

Poprzednie polecenia korzystają z następujących informacji:

- `{tenantId}` — zastąp identyfikatorem swojej dzierżawy
- `{clientId}` -Zamień na identyfikator klienta nazwy głównej usługi
- `{clientSecret}` -Zamień na klucz tajny klienta jednostki usługi
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji
- **Nazwa** — unikatowa nazwa obiektu przypisania zasad. Powyższy przykład używa _inspekcji-VM-manageddisks_.

Wyniki `response` są zgodne z informacjami wyświetlanymi na karcie **zgodność zasobów** w przypisaniu zasad w widoku Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

- Usuń _maszyny wirtualne inspekcji przypisania zasad bez przypisywania dysków zarządzanych_ za pośrednictwem portalu. Definicja zasad jest wbudowana, dlatego nie istnieje definicja do usunięcia.

- Jeśli chcesz usunąć aplikacje konsolowe platformy .NET Core i zainstalowane pakiety, Usuń `policyAssignment` `policyCompliance` foldery programu i.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej o przypisywaniu definicji zasad w celu sprawdzenia, czy nowe zasoby są zgodne, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
