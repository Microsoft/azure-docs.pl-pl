---
title: Utwórz konto usługi kontrolą przy użyciu zestawu SDK platformy .NET
description: Utwórz konto usługi Azure kontrolą za pomocą zestawu SDK platformy .NET.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: 04ed5cef351c81355a2390dd0b983c162f2b9532
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387516"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Szybki Start: Tworzenie konta usługi kontrolą przy użyciu zestawu SDK platformy .NET

W tym przewodniku szybki start opisano, jak utworzyć konto usługi Azure kontrolą za pomocą zestawu SDK platformy .NET 

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Własna [dzierżawa usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Twoje konto musi mieć uprawnienia do tworzenia zasobów w ramach subskrypcji

* Jeśli masz **Azure Policy** blokujące wszystkie aplikacje z tworzenia **konta magazynu** i **przestrzeni nazw EventHub**, musisz utworzyć wyjątek zasad przy użyciu tagu, który można wprowadzić podczas procesu tworzenia konta kontrolą. Głównym powodem jest to, że dla każdego utworzonego konta kontrolą należy utworzyć zarządzaną grupę zasobów i w ramach tej grupy zasobów, konto magazynu i przestrzeń nazw EventHub. Aby uzyskać więcej informacji, zobacz [Portal tworzenia katalogu](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

W przewodniku w tym artykule jest wykorzystywany program Visual Studio 2019. Procedury dotyczące Visual Studio 2013, 2015 lub 2017 różnią się nieznacznie.

### <a name="azure-net-sdk"></a>Zestaw Azure .NET SDK

Pobierz i zainstaluj zestaw [Azure .NET SDK](https://azure.microsoft.com/downloads/) na maszynie.

## <a name="create-an-application-in-azure-active-directory"></a>Utworzenie aplikacji w usłudze Azure Active Directory

W sekcjach w sekcji *jak: korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów*, postępuj zgodnie z instrukcjami, aby wykonać następujące zadania:

1. W obszarze [Tworzenie aplikacji Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)Utwórz aplikację reprezentującą aplikację platformy .NET, którą tworzysz w tym samouczku. W przypadku adresu URL logowania możesz podać fikcyjny adres URL, jak pokazano w artykule (`https://contoso.org/exampleapp`).
2. W polu [Pobierz wartości do logowania](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Pobierz **Identyfikator aplikacji** i **Identyfikator dzierżawy**, a następnie zanotuj te wartości, które są używane w dalszej części tego samouczka. 
3. W obszarze [Certyfikaty i wpisy tajne](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)Pobierz **klucz uwierzytelniania** i zanotuj tę wartość, która jest używana w dalszej części tego samouczka.
4. W polu [Przypisz aplikację do roli](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) **współautor** na poziomie subskrypcji, aby aplikacja mogła tworzyć fabryki danych w subskrypcji.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Następnie Utwórz aplikację konsolową .NET C# w programie Visual Studio:

1. Uruchom **program Visual Studio**.
2. W oknie uruchamiania wybierz pozycję **Utwórz nową**  >  **aplikację konsolową projektu (.NET Framework)**. Wymagana jest platforma .NET w wersji 4.5.2 lub nowszej.
3. W polu **Nazwa projektu** wprowadź **ADFv2QuickStart**.
4. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

1. Wybierz pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.
2. W okienku **konsoli Menedżera pakietów** Uruchom następujące polecenia, aby zainstalować pakiety. Aby uzyskać więcej informacji, zobacz [pakiet NuGet Microsoft. Azure. Management. kontrolą](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Tworzenie klienta kontrolą

1. Otwórz plik **Program.cs**, a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Dodaj do metody **Main** następujący kod, który określa zmienne. Zastąp symbole zastępcze własnymi wartościami. Aby zapoznać się z listą regionów świadczenia usługi Azure, w których usługa kontrolą jest obecnie dostępna, Wyszukaj w **usłudze Azure kontrolą** i wybierz odpowiednie regiony na następującej stronie: [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/).

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Dodaj do metody **Main** następujący kod, który tworzy wystąpienie klasy **PurviewManagementClient** . Ten obiekt jest używany do tworzenia konta kontrolą.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Utwórz konto kontrolą

Dodaj do metody **Main** następujący kod, który tworzy **konto kontrolą**. 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj i uruchom aplikację, a następnie sprawdź wykonanie.

Konsola drukuje postęp tworzenia konta kontrolą.

### <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

Przejdź do strony **kont kontrolą** w [Azure Portal](https://portal.azure.com) i Sprawdź konto utworzone przy użyciu powyższego kodu. 

## <a name="delete-purview-account"></a>Usuń konto kontrolą

Aby programowo usunąć konto kontrolą, Dodaj następujące wiersze kodu do programu: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Sprawdź, czy nazwa konta kontrolą jest dostępna

Aby sprawdzić dostępność konta kontrolą, użyj następującego kodu: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

Powyższy kod z opcją Print "true", jeśli nazwa jest dostępna i wartość "false", jeśli nazwa jest niedostępna.


## <a name="next-steps"></a>Następne kroki

Kod w tym samouczku tworzy konto kontrolą, usuwa konto kontrolą i sprawdza dostępność nazwy konta kontrolą. Teraz można pobrać zestaw .NET SDK i poznać inne akcje dostawcy zasobów, które można wykonać dla konta kontrolą.

Przejdź do następnego artykułu, aby dowiedzieć się, jak umożliwić użytkownikom dostęp do konta usługi Azure kontrolą. 

> [!div class="nextstepaction"]
> [Dodawanie użytkowników do konta usługi Azure kontrolą](catalog-permissions.md)
