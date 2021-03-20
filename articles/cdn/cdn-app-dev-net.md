---
title: Wprowadzenie do biblioteki Azure CDN dla platformy .NET | Microsoft Docs
description: Dowiedz się, jak napisać aplikacje .NET do zarządzania Azure CDN przy użyciu programu Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: a812704c42a4da5ddf89fe6c5ba0c9a684047f75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88919492"
---
# <a name="get-started-with-the-azure-cdn-library-for-net"></a>Wprowadzenie do biblioteki Azure CDN dla platformy .NET
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
>
>

Za pomocą [biblioteki Azure CDN dla platformy .NET](/dotnet/api/overview/azure/cdn) można zautomatyzować tworzenie profilów i punktów końcowych usługi CDN oraz zarządzanie nimi.  Ten samouczek przeprowadzi Cię przez proces tworzenia prostej aplikacji konsolowej .NET, która pokazuje kilka dostępnych operacji.  Ten samouczek nie jest przeznaczony do szczegółowego opisywania wszystkich aspektów biblioteki Azure CDN dla platformy .NET.

Aby ukończyć ten samouczek, potrzebujesz programu Visual Studio 2015.  [Program Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) jest dostępny bezpłatnie do pobrania.

> [!TIP]
> [Ukończony projekt z tego samouczka](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) jest dostępny do pobrania w witrynie MSDN.
>
>

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Tworzenie projektu i dodawanie pakietów NuGet
Po utworzeniu grupy zasobów dla naszych profilów usługi CDN i udostępnieniu naszej aplikacji usługi Azure AD uprawnienia do zarządzania profilami i punktami końcowymi sieci CDN w tej grupie możemy rozpocząć tworzenie aplikacji.

W programie Visual Studio 2015 kliknij kolejno pozycje **plik**, **Nowy**, **projekt...** , aby otworzyć okno dialogowe Nowy projekt.  Rozwiń pozycję **Visual C#**, a następnie w okienku po lewej stronie wybierz pozycję **Windows** .  Kliknij pozycję **Aplikacja konsolowa** w środkowym okienku.  Nazwij projekt, a następnie kliknij przycisk **OK**.

![Nowy projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Nasz projekt będzie używać niektórych bibliotek platformy Azure zawartych w pakietach NuGet.  Dodajmy je do projektu.

1. Kliknij menu **Narzędzia** , **Menedżer pakietów NuGet**, a następnie **konsolę Menedżera pakietów**.

    ![Zarządzanie pakietami NuGet](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. W konsoli Menedżera pakietów wykonaj następujące polecenie, aby zainstalować **Active Directory Authentication Library (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Wykonaj następujące czynności, aby zainstalować **bibliotekę zarządzania Azure CDN**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Dyrektywy, stałe, Metoda Main i metody pomocnika
Uzyskajmy podstawową strukturę naszego zapisaną przez program.

1. Po powrocie na kartę program. cs Zastąp `using` dyrektywy w górnej części:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Musimy zdefiniować niektóre stałe nasze metody będą używane.  W `Program` klasie, ale przed `Main` metodą, Dodaj następujące elementy.  Pamiętaj, aby zastąpić symbole zastępcze, w tym **&lt; nawiasy &gt; kątowe**, z własnymi wartościami, zgodnie z potrzebami.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Na poziomie klasy, należy również zdefiniować te dwie zmienne.  Użyjemy ich później, aby określić, czy profil i punkt końcowy już istnieją.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Zastąp `Main` metodę w następujący sposób:

   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();

       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };

       ListProfilesAndEndpoints(cdn);

       // Create CDN Profile
       CreateCdnProfile(cdn);

       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);

       Console.WriteLine();

       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);

       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);

       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);

       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Niektóre z naszych innych metod będą monitował użytkownika z pytaniami "tak/nie".  Dodaj następującą metodę, aby nieco łatwiej:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Teraz, gdy podstawowa struktura naszego programu jest zapisywana, należy utworzyć metody wywoływane przez `Main` metodę.

## <a name="authentication"></a>Authentication
Zanim będziemy mogli korzystać z biblioteki zarządzania Azure CDN, musimy uwierzytelnić naszą nazwę główną usługi i uzyskać token uwierzytelniania.  Ta metoda używa biblioteki ADAL do pobrania tokenu.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult =
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Jeśli używasz uwierzytelniania pojedynczego użytkownika, `GetAccessToken` Metoda będzie wyglądać nieco inaczej.

> [!IMPORTANT]
> Tego przykładowego kodu należy używać tylko w przypadku wybrania opcji uwierzytelniania poszczególnych użytkowników zamiast nazwy głównej usługi.
>
>

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Pamiętaj, aby zastąpić `<redirect URI>` Identyfikator URI przekierowania wprowadzony podczas rejestrowania aplikacji w usłudze Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Wyświetlanie listy profilów i punktów końcowych usługi CDN
Teraz jesteśmy gotowi do wykonania operacji usługi CDN.  Pierwsza rzecz naszej metody to lista wszystkich profilów i punktów końcowych w naszej grupie zasobów. w przypadku znalezienia dopasowania dla nazw profilu i punktu końcowego określonych w naszych stałych należy pamiętać, że w przyszłości nie próbujemy tworzyć duplikatów.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Utwórz profile i punkty końcowe usługi CDN
Następnie utworzymy profil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Po utworzeniu profilu zostanie utworzony punkt końcowy.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> Powyższy przykład przypisuje punkt końcowy o nazwie *contoso* z nazwą hosta `www.contoso.com` .  Należy zmienić tę pozycję, aby wskazywała nazwę hosta własnego źródła.
>
>

## <a name="purge-an-endpoint"></a>Przeczyszczanie punktu końcowego
Przy założeniu, że punkt końcowy został utworzony, jednym z typowych zadań, które możemy chcieć wykonać w naszym programie, jest przeczyszczanie zawartości w naszym punkcie końcowym.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> W powyższym przykładzie ciąg `/*` wskazuje, że chcę przeczyścić wszystko w elemencie głównym ścieżki punktu końcowego.  Jest to równoznaczne z sprawdzeniem, czy **wszystkie** w oknie dialogowym "przeczyszczanie" Azure Portal. W `CreateCdnProfile` metodzie został utworzony profil jako **Azure CDN z profilu Verizon** przy użyciu kodu `Sku = new Sku(SkuName.StandardVerizon)` , więc zostanie to wykonane pomyślnie.  Jednak **Azure CDN z profilów Akamai** nie obsługują **przeczyszczania**, więc jeśli w tym samouczku był używany profil Akamai, należy uwzględnić określone ścieżki do przeczyszczenia.
>
>

## <a name="delete-cdn-profiles-and-endpoints"></a>Usuń profile i punkty końcowe usługi CDN
Ostatnie metody spowodują usunięcie naszego punktu końcowego i profilu.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Uruchamianie programu
Teraz można skompilować i uruchomić program, klikając przycisk **Uruchom** w programie Visual Studio.

![Uruchomiony program](./media/cdn-app-dev-net/cdn-program-running-1.png)

Gdy program osiągnie powyższy monit, powinien być w stanie wrócić do grupy zasobów w Azure Portal i zobaczyć, że profil został utworzony.

![To wszystko!](./media/cdn-app-dev-net/cdn-success.png)

Następnie możemy potwierdzić poprawność uruchomienia pozostałej części programu.

![Trwa wykonywanie programu](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Następne kroki
Aby wyświetlić ukończony projekt z tego przewodnika, [Pobierz przykład](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Aby znaleźć dodatkową dokumentację dotyczącą Azure CDN biblioteki zarządzania dla platformy .NET, zapoznaj się z informacjami w [witrynie MSDN](/dotnet/api/overview/azure/cdn).

Zarządzanie zasobami usługi CDN przy użyciu [programu PowerShell](cdn-manage-powershell.md).
