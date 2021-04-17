---
title: Tworzenie konta Programu Purview przy użyciu zestawu SDK platformy .NET
description: Tworzenie konta usługi Azure Purview przy użyciu zestawu SDK platformy .NET.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: b3dc7bf8ac7650a7219c15a09a31d4dcf84a40bf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587803"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Szybki start: tworzenie konta Purview przy użyciu zestawu SDK platformy .NET

W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Purview przy użyciu zestawu SDK platformy .NET 

> [!IMPORTANT]
> Usługa Azure Purview jest obecnie dostępna w wersji zapoznawczej. Dodatkowe warunki użytkowania wersji [zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) programu Microsoft Azure zawierają dodatkowe postanowienia prawne dotyczące funkcji platformy Azure, które są dostępne w wersji beta, wersji zapoznawczej lub w inny sposób nie są jeszcze ogólnie dostępne.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Własna [dzierżawa usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Twoje konto musi mieć uprawnienia do tworzenia zasobów w subskrypcji

* Jeśli nie  masz Azure Policy tworzenia konta  magazynu i przestrzeni nazw **usługi EventHub,** musisz wprowadzić wyjątek zasad przy użyciu tagu , który można wprowadzić podczas tworzenia konta usługi Purview. Główną przyczyną jest to, że dla każdego utworzonego konta programu Purview musi ona utworzyć zarządzaną grupę zasobów w ramach tej grupy zasobów, konta magazynu i przestrzeni nazw usługi EventHub. Aby uzyskać więcej informacji, zobacz [Create Catalog Portal (Tworzenie portalu katalogu)](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

W przewodniku w tym artykule Visual Studio 2019 r. Procedury dotyczące Visual Studio 2013, 2015 lub 2017 różnią się nieco.

### <a name="azure-net-sdk"></a>Zestaw Azure .NET SDK

Pobierz i zainstaluj zestaw [Azure .NET SDK](https://azure.microsoft.com/downloads/) na maszynie.

## <a name="create-an-application-in-azure-active-directory"></a>Utworzenie aplikacji w usłudze Azure Active Directory

W sekcjach *Instrukcje: używanie* portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi, które mogą uzyskać dostęp do zasobów, postępuj zgodnie z instrukcjami, aby wykonać następujące zadania:

1. W [części Tworzenie Azure Active Directory utwórz](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)aplikację reprezentującą aplikację .NET, która jest tworzymy w tym samouczku. W przypadku adresu URL logowania możesz podać fikcyjny adres URL, jak pokazano w artykule (`https://contoso.org/exampleapp`).
2. Na [stronie Pobierz wartości](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)logowania  pobierz identyfikator aplikacji i identyfikator dzierżawy, a następnie zanotuj te wartości, których użyjemy w dalszej części tego samouczka.  
3. W [części Certyfikaty i wpisy tajne](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)pobierz klucz **uwierzytelniania** i zanotuj tę wartość, która będzie późniejsza w tym samouczku.
4. W [skrypcie](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)Przypisywanie aplikacji do roli przypisz ją do roli **Współautor** na poziomie subskrypcji, aby aplikacja może tworzyć fabryki danych w ramach subskrypcji.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Następnie utwórz aplikację konsolowa .NET w języku C# w Visual Studio:

1. Uruchom **Visual Studio**.
2. W oknie Start wybierz pozycję **Utwórz nowy projekt** Aplikacja  >  **konsolowa (.NET Framework).** Wymagana jest platforma .NET w wersji 4.5.2 lub nowszej.
3. W **nazwa projektu,** wprowadź **PurviewQuickStart**.
4. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

1. Wybierz pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.
2. W **okienku Menedżer pakietów Konsoli** programu uruchom następujące polecenia, aby zainstalować pakiety. Aby uzyskać więcej informacji, zobacz pakiet [NuGet Microsoft.Azure.Management.Purview.](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/)

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Tworzenie klienta programu Purview

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

2. Dodaj do metody **Main** następujący kod, który określa zmienne. Zastąp symbole zastępcze własnymi wartościami. Aby uzyskać listę regionów platformy Azure, w których usługa Purview jest obecnie dostępna, wyszukaj usługę **Azure Purview** i wybierz regiony, które Cię interesują, na następującej stronie: Produkty [dostępne według regionów.](https://azure.microsoft.com/global-infrastructure/services/)

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

3. Dodaj do metody **Main** następujący kod, który tworzy wystąpienie klasy **PurviewManagementClient.** Ten obiekt umożliwia utworzenie konta Programu Purview.

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

## <a name="create-a-purview-account"></a>Tworzenie konta programu Purview

Dodaj do metody **Main** następujący kod, który tworzy **konto Programu Purview.** 

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

Skompilowanie i uruchomienie aplikacji, a następnie zweryfikowanie wykonania.

Konsola drukuje postęp tworzenia konta Programu Purview.

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

Przejdź do strony **Konta programu Purview** w [Azure Portal](https://portal.azure.com) i sprawdź konto utworzone przy użyciu powyższego kodu. 

## <a name="delete-purview-account"></a>Usuwanie konta programu Purview

Aby programowo usunąć konto programu Purview, dodaj do programu następujące wiersze kodu: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Sprawdź, czy nazwa konta programu Purview jest dostępna

Aby sprawdzić dostępność konta purview, użyj następującego kodu: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

Powyższy kod z drukowaniem "True", jeśli nazwa jest dostępna, i "False", jeśli nazwa jest niedostępny.


## <a name="next-steps"></a>Następne kroki

Kod w tym samouczku tworzy konto purview, usuwa konto purview i sprawdza dostępność nazwy konta purview. Teraz możesz pobrać zestaw SDK platformy .NET i dowiedzieć się więcej o innych akcjach dostawcy zasobów, które można wykonać dla konta programu Purview.

W następnym artykule dowiesz się, jak zezwolić użytkownikom na dostęp do konta usługi Azure Purview. 

> [!div class="nextstepaction"]
> [Dodawanie użytkowników do konta usługi Azure Purview](catalog-permissions.md)
