---
title: Uzyskiwanie dostępu do usługi App Configuration przy użyciu tożsamości zarządzanych
titleSuffix: Azure App Configuration
description: Uwierzytelnianie w usłudze Azure App Configuration przy użyciu tożsamości zarządzanych
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: 2f446df95c795eaac378340ed0d5de7b31dfcfee
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219045"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Uzyskiwanie dostępu do usługi App Configuration przy użyciu tożsamości zarządzanych

Azure Active Directory [tożsamości zarządzane](../active-directory/managed-identities-azure-resources/overview.md) upraszczają zarządzanie kluczami tajnymi aplikacji w chmurze. Przy użyciu tożsamości zarządzanej kod może używać jednostki usługi utworzonej dla usługi platformy Azure, w której jest uruchomiona. Użytkownik korzysta z tożsamości zarządzanej zamiast oddzielnego poświadczenia przechowywanego w Azure Key Vault lub lokalnych parametrów połączenia.

Konfiguracja aplikacji platformy Azure oraz jej biblioteki klienckie .NET Core, .NET Framework i Java sprężyny mają wbudowaną obsługę tożsamości. Chociaż nie jest to konieczne, zarządzana tożsamość eliminuje konieczność korzystania z tokenu dostępu zawierającego wpisy tajne. Twój kod może uzyskać dostęp do magazynu konfiguracji aplikacji przy użyciu tylko punktu końcowego usługi. Możesz osadzić ten adres URL w kodzie bezpośrednio bez ujawniania żadnych wpisów tajnych.

W tym artykule pokazano, jak można wykorzystać zarządzaną tożsamość w celu uzyskania dostępu do konfiguracji aplikacji. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem  [Utwórz najpierw aplikację ASP.NET Coreową z konfiguracją aplikacji](./quickstart-aspnet-core-app.md) .

> [!NOTE]
> W tym artykule jest używane Azure App Service na przykład, ale te same koncepcje dotyczą wszystkich innych usług platformy Azure, które obsługują tożsamość zarządzaną, na przykład [usługę Azure Kubernetes](../aks/use-azure-ad-pod-identity.md), [maszynę wirtualną platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)i [Azure Container Instances](../container-instances/container-instances-managed-identity.md). Jeśli obciążenie jest hostowane w jednej z tych usług, możesz również skorzystać z obsługi tożsamości zarządzanej przez usługę.

W tym artykule pokazano również, jak można używać tożsamości zarządzanej w połączeniu z odwołaniami Key Vault konfiguracji aplikacji. Przy użyciu pojedynczej tożsamości zarządzanej można bezproblemowo uzyskać dostęp do tych samych wartości Key Vault i konfiguracji z konfiguracji aplikacji. Aby poznać tę możliwość, należy najpierw zakończyć [Korzystanie z Key Vault odwołań z ASP.NET Core](./use-key-vault-references-dotnet-core.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. [Visual Studio Code](https://code.visualstudio.com/) jest doskonałym rozwiązaniem dostępnym na platformach Windows, MacOS i Linux.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przyznaj zarządzanej tożsamości dostęp do konfiguracji aplikacji.
> * Skonfiguruj aplikację do korzystania z tożsamości zarządzanej podczas łączenia się z konfiguracją aplikacji.
> * Opcjonalnie można skonfigurować aplikację do korzystania z tożsamości zarządzanej podczas łączenia się z Key Vault za pomocą Key Vault informacji o konfiguracji aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell skonfigurowany](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Dodawanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzaną w portalu, należy najpierw utworzyć aplikację, a następnie włączyć tę funkcję.

1. Utwórz wystąpienie App Services w [Azure Portal](https://portal.azure.com) jak zwykle. Przejdź do niego w portalu.

1. Przewiń w dół do grupy **Ustawienia** w okienku po lewej stronie, a następnie wybierz pozycję **tożsamość**.

1. Na karcie **przypisane do systemu** Przełącz pozycję **stan** na **włączone** i wybierz pozycję **Zapisz**.

1. Odpowiedź **tak** po wyświetleniu monitu o włączenie tożsamości zarządzanej przypisanej przez system.

    ![Ustawianie tożsamości zarządzanej w usłudze App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udzielanie dostępu do usługi App Configuration

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby** i wybierz magazyn konfiguracji aplikacji utworzony w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Kontrola dostępu (IAM)** .

1. Na karcie **sprawdzanie dostępu** wybierz pozycję **Dodaj** w interfejsie użytkownika karty **Dodaj rolę** .

1. W obszarze **rola** wybierz pozycję **czytnik danych konfiguracji aplikacji**. W obszarze **Przypisz dostęp do** wybierz pozycję **App Service** w obszarze **system przypisanej tożsamości zarządzanej**.

1. W obszarze **subskrypcja** wybierz subskrypcję platformy Azure. Wybierz zasób App Service dla aplikacji.

1. Wybierz pozycję **Zapisz**.

    ![Dodawanie tożsamości zarządzanej](./media/add-managed-identity.png)

1. Opcjonalnie: Jeśli chcesz udzielić dostępu do Key Vault, postępuj zgodnie z instrukcjami w temacie [przypisywanie zasad dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md).

## <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

1. Dodaj odwołanie do pakietu *platformy Azure. Identity* :

    ```bash
    dotnet add package Azure.Identity
    ```

1. Znajdź punkt końcowy w magazynie konfiguracji aplikacji. Ten adres URL jest wyświetlany na karcie **klucze dostępu** dla sklepu w Azure Portal.

1. Otwórz *appsettings.jsna* i Dodaj następujący skrypt. Zamień *\<service_endpoint>* , łącznie z nawiasami, adres URL magazynu konfiguracji aplikacji.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Otwórz *program.cs* i Dodaj odwołanie do `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` przestrzeni nazw i:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Jeśli chcesz uzyskać dostęp tylko do wartości przechowywanych bezpośrednio w konfiguracji aplikacji, zaktualizuj `CreateWebHostBuilder` metodę, zastępując `config.AddAzureAppConfiguration()` metodę (jest to dostępne w `Microsoft.Azure.AppConfiguration.AspNetCore` pakiecie).

    > [!IMPORTANT]
    > `CreateHostBuilder` zastępuje `CreateWebHostBuilder` w programie .NET Core 3,0.  Wybierz poprawną składnię opartą na Twoim środowisku.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > W przypadku, gdy chcesz użyć **tożsamości zarządzanej przypisanej przez użytkownika**, pamiętaj o określeniu clientId podczas tworzenia [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential).
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Zgodnie z opisem w temacie [zarządzane tożsamości dla zasobów platformy Azure — często zadawane pytania](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)można ustalić, która zarządzana tożsamość jest używana. W takim przypadku Biblioteka tożsamości platformy Azure wymusza, aby określić żądaną tożsamość, aby uniknąć problemów z przekroczyła dozwoloną środowiska uruchomieniowego w przyszłości (na przykład jeśli zostanie dodana nowa tożsamość zarządzana przez użytkownika lub jest włączona tożsamość zarządzana przypisana przez system). W związku z tym należy określić clientId nawet wtedy, gdy zdefiniowana jest tylko jedna tożsamość zarządzana przypisana przez użytkownika i nie istnieje tożsamość zarządzana przypisana przez system.


1. Aby użyć zarówno wartości konfiguracji aplikacji, jak i Key Vault odwołań, należy zaktualizować *program.cs* , jak pokazano poniżej. Ten kod wywołuje `SetCredential` w `ConfigureKeyVault` celu poinformowania dostawcę konfiguracji o poświadczeniach, które mają być używane podczas uwierzytelniania do Key Vault.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    Teraz możesz uzyskiwać dostęp do Key Vault odwołań podobnie jak każdy inny klucz konfiguracji aplikacji. Dostawca konfiguracji użyje `ManagedIdentityCredential` do uwierzytelniania w celu Key Vault i pobrania wartości.

    > [!NOTE]
    > `ManagedIdentityCredential`Działa tylko w środowiskach platformy Azure usług, które obsługują uwierzytelnianie tożsamości zarządzanej. Nie działa w środowisku lokalnym. Służy [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) do pracy kodu w środowiskach lokalnych i na platformie Azure, ponieważ powróci do kilku opcji uwierzytelniania, łącznie z tożsamością zarządzaną.
    > 
    > Jeśli chcesz użyć **tożsamości zarządzanej przez użytkownika asigned** z `DefaultAzureCredential` wdrożeniem na platformie Azure, [Określ clientId](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Wdrażanie z lokalnego wdrożenia narzędzia Git

Najprostszym sposobem włączenia lokalnego wdrożenia narzędzia Git dla aplikacji przy użyciu serwera kompilacji kudu jest użycie [Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Włączanie lokalnej usługi Git za pomocą serwera Kudu
Jeśli nie masz lokalnego repozytorium git dla swojej aplikacji, musisz ją zainicjować. Aby zainicjować lokalne repozytorium git, uruchom następujące polecenia w katalogu projektu aplikacji:

```cmd
git init
git add .
git commit -m "Initial version"
```

Aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji przy użyciu serwera kompilacji kudu, uruchom polecenie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az-webapp-deployment-source-config-local-git) w Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

To polecenie daje coś podobnego do następującego:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Wdrażanie projektu

W _oknie terminalu lokalnego_ Dodaj zdalne Azure do lokalnego repozytorium git. Zastąp ciąg _\<url>_ adresem URL zdalnego systemu Git, który pochodzi z [włączenia lokalnego narzędzia Git przy użyciu usługi kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Po wyświetleniu monitu o podanie hasła wprowadź hasło utworzone w obszarze [Konfigurowanie użytkownika wdrożenia](#configure-a-deployment-user). Nie używaj hasła używanego do logowania się do Azure Portal.

```bash
git push azure main
```

W danych wyjściowych można zobaczyć automatyzację specyficzną dla środowiska uruchomieniowego, na przykład MSBuild for ASP.NET, `npm install` dla Node.js i `pip install` dla języka Python.

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Przejdź do aplikacji sieci Web przy użyciu przeglądarki, aby sprawdzić, czy zawartość została wdrożona.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Używanie tożsamości zarządzanej w innych językach

Dostawcy usługi App Configuration dla programu .NET Framework i Java Spring również mają wbudowaną obsługę tożsamości zarządzanej. Po skonfigurowaniu jednego z tych dostawców można użyć punktu końcowego adresu URL sklepu zamiast jego pełnych parametrów połączenia.

Można na przykład zaktualizować aplikację konsolową .NET Framework utworzoną w przewodniku Szybki Start, aby określić następujące ustawienia w pliku *App.config* :

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki
W tym samouczku dodano tożsamość zarządzaną platformy Azure, aby usprawnić dostęp do konfiguracji aplikacji i usprawnić zarządzanie poświadczeniami dla aplikacji. Aby dowiedzieć się więcej na temat korzystania z konfiguracji aplikacji, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)
