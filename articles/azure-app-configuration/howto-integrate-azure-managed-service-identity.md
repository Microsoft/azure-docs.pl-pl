---
title: Uzyskiwanie dostępu do usługi App Configuration przy użyciu tożsamości zarządzanych
titleSuffix: Azure App Configuration
description: Uwierzytelnianie w Azure App Configuration przy użyciu tożsamości zarządzanych
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7a9eb992ff0cb98fdae2920da2beeda0bbd8941b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877538"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Uzyskiwanie dostępu do usługi App Configuration przy użyciu tożsamości zarządzanych

Azure Active Directory tożsamości [zarządzane upraszczają](../active-directory/managed-identities-azure-resources/overview.md) zarządzanie wpisami tajnymi dla aplikacji w chmurze. W przypadku tożsamości zarządzanej kod może używać jednostki usługi utworzonej dla usługi platformy Azure, w ramach która jest uruchamiana. Używasz tożsamości zarządzanej zamiast oddzielnych poświadczeń przechowywanych w Azure Key Vault lub parametrów połączenia lokalnego.

Azure App Configuration i jego biblioteki klienckie .NET Core, .NET Framework i Java Spring mają wbudowaną obsługę tożsamości zarządzanej. Mimo że korzystanie z niego nie jest wymagane, tożsamość zarządzana eliminuje potrzebę tokenu dostępu, który zawiera wpisy tajne. Kod może uzyskać dostęp do App Configuration tylko przy użyciu punktu końcowego usługi. Możesz osadzić ten adres URL bezpośrednio w kodzie bez ujawniania jakiegokolwiek tajnego kodu.

W tym artykule przedstawiono sposób korzystania z tożsamości zarządzanej w celu uzyskania dostępu do App Configuration. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem najpierw [utwórz aplikację ASP.NET Core przy użyciu App Configuration.](./quickstart-aspnet-core-app.md)

> [!NOTE]
> W tym artykule użyto Azure App Service przykładu, ale ta sama koncepcja ma zastosowanie do każdej innej usługi platformy Azure, która obsługuje tożsamość zarządzaną, na przykład [Azure Kubernetes Service,](../aks/use-azure-ad-pod-identity.md) [Azure Virtual Machine](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)i [Azure Container Instances.](../container-instances/container-instances-managed-identity.md) Jeśli obciążenie jest hostowane w jednej z tych usług, możesz również korzystać z obsługi tożsamości zarządzanej usługi.

W tym artykule pokazano również, jak używać tożsamości zarządzanej w połączeniu App Configuration odwołaniami Key Vault zarządzania. Za pomocą jednej tożsamości zarządzanej można bezproblemowo uzyskać dostęp do wpisów tajnych z Key Vault i wartości konfiguracji z App Configuration. Jeśli chcesz poznać tę funkcję, najpierw zakończ korzystanie [z odwołań Key Vault z ASP.NET Core.](./use-key-vault-references-dotnet-core.md)

Możesz użyć dowolnego edytora kodu, aby wykonać kroki opisane w tym samouczku. [Visual Studio Code](https://code.visualstudio.com/) to doskonała opcja dostępna na platformach Windows, macOS i Linux.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Udzielanie tożsamości zarządzanej dostępu do App Configuration.
> * Skonfiguruj aplikację do używania tożsamości zarządzanej podczas nawiązywania połączenia z App Configuration.
> * Opcjonalnie możesz skonfigurować aplikację do używania tożsamości zarządzanej podczas nawiązywania połączenia z Key Vault za pośrednictwem App Configuration Key Vault referencyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [zestaw .NET Core SDK .](https://dotnet.microsoft.com/download)
* [Azure Cloud Shell skonfigurowano .](../cloud-shell/quickstart.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Dodawanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzaną w portalu, należy najpierw utworzyć aplikację, a następnie włączyć tę funkcję.

1. Utwórz wystąpienie App Services w Azure Portal [jak](https://portal.azure.com) zwykle. Przejdź do niego w portalu.

1. Przewiń w dół do **grupy Ustawienia** w okienku po lewej stronie i wybierz pozycję **Tożsamość.**

1. Na karcie **Przypisana przez** system przełącz pozycję **Stan** na **Wł.** i wybierz pozycję **Zapisz.**

1. Po **wyświetleniu** monitu o włączenie tożsamości zarządzanej przypisanej przez system odpowiedz Tak.

    ![Ustawianie tożsamości zarządzanej w usłudze App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udzielanie dostępu do usługi App Configuration

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Wszystkie** zasoby i wybierz App Configuration magazynu, który został utworzony w przewodniku Szybki start.

1. Wybierz pozycję **Kontrola dostępu (IAM)** .

1. Na karcie **Sprawdzanie dostępu** wybierz pozycję **Dodaj w** **interfejsie użytkownika karty Dodawanie przypisania** roli.

1. W **obszarze Rola** wybierz pozycję App Configuration Czytnik **danych.** W **obszarze Przypisz dostęp do** wybierz pozycję **App Service** obszarze **Tożsamość zarządzana przypisana przez system.**

1. W **obszarze Subskrypcja** wybierz swoją subskrypcję platformy Azure. Wybierz App Service zasobu aplikacji.

1. Wybierz pozycję **Zapisz**.

    ![Dodawanie tożsamości zarządzanej](./media/add-managed-identity.png)

1. Opcjonalnie: Jeśli chcesz również udzielić dostępu do Key Vault, postępuj zgodnie z instrukcjami w tece Przypisywanie Key Vault [dostępu.](../key-vault/general/assign-access-policy-portal.md)

## <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

1. Dodaj odwołanie do pakietu *Azure.Identity:*

    ```bash
    dotnet add package Azure.Identity
    ```

1. Znajdź punkt końcowy w App Configuration magazynu. Ten adres URL jest wymieniony na **karcie Klucze** dostępu dla sklepu w Azure Portal.

1. Otwórz *appsettings.jsw skrypcie* i dodaj następujący skrypt. Zastąp *\<service_endpoint>* , łącznie z nawiasami kwadratowymi, adresem URL App Configuration magazynu.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Otwórz *program Program.cs* i dodaj odwołanie do `Azure.Identity` przestrzeni nazw i `Microsoft.Azure.Services.AppAuthentication` :

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Jeśli chcesz uzyskać dostęp tylko do wartości przechowywanych bezpośrednio w App Configuration, zaktualizuj metodę , zastępując metodę (znajduje się ona `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` w `Microsoft.Azure.AppConfiguration.AspNetCore` pakiecie ).

    > [!IMPORTANT]
    > `CreateHostBuilder` zastępuje element `CreateWebHostBuilder` w programie .NET Core 3.0.  Wybierz poprawną składnię w zależności od środowiska.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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
    > Jeśli chcesz użyć tożsamości zarządzanej przypisanej przez **użytkownika,** pamiętaj o określeniu wartości clientId podczas tworzenia wartości [ManagedIdentityCredential.](/dotnet/api/azure.identity.managedidentitycredential)
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Jak wyjaśniono w często [zadawanych pytaniach na](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)temat tożsamości zarządzanych dla zasobów platformy Azure, istnieje domyślny sposób rozpoznawania używanej tożsamości zarządzanej. W takim przypadku biblioteka tożsamości platformy Azure wymusza określenie żądanej tożsamości w celu uniknięcia problemów ze środowiskiem uruchomieniowym w przyszłości (na przykład w przypadku dodania nowej tożsamości zarządzanej przypisanej przez użytkownika lub jeśli tożsamość zarządzana przypisana przez system jest włączona). Dlatego należy określić identyfikator clientId, nawet jeśli zdefiniowano tylko jedną tożsamość zarządzaną przypisaną przez użytkownika i nie ma przypisanej przez system tożsamości zarządzanej.


1. Aby użyć wartości App Configuration i odwołań Key Vault, zaktualizuj *program.cs,* jak pokazano poniżej. Ten kod wywołuje element w ramach funkcji , aby poinformować dostawcę konfiguracji, jakich poświadczeń użyć podczas uwierzytelniania w `SetCredential` `ConfigureKeyVault` Key Vault.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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

    Teraz możesz uzyskać dostęp do Key Vault, tak jak każdy inny klucz App Configuration klucza. Dostawca konfiguracji będzie używać do uwierzytelniania w celu `ManagedIdentityCredential` Key Vault i pobierania wartości.

    > [!NOTE]
    > Działa `ManagedIdentityCredential` tylko w środowiskach platformy Azure usług, które obsługują uwierzytelnianie tożsamości zarządzanej. Nie działa w środowisku lokalnym. Użyj metody , aby kod działał zarówno w środowisku lokalnym, jak i na platformie Azure, ponieważ powróci do [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) kilku opcji uwierzytelniania, w tym tożsamości zarządzanej.
    > 
    > Jeśli chcesz użyć tożsamości  zarządzanej z podpisem użytkownika z wartością po wdrożeniu na platformie `DefaultAzureCredential` Azure, [określ wartość clientId](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Wdrażanie z lokalnego wdrożenia narzędzia Git

Najprostszym sposobem włączenia lokalnego wdrożenia usługi Git dla aplikacji przy użyciu serwera kompilacji Kudu jest użycie Azure Cloud Shell [.](https://shell.azure.com)

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Włączanie lokalnej usługi Git za pomocą serwera Kudu
Jeśli nie masz lokalnego repozytorium Git dla aplikacji, musisz je zainicjować. Aby zainicjować lokalne repozytorium Git, uruchom następujące polecenia z katalogu projektu aplikacji:

```cmd
git init
git add .
git commit -m "Initial version"
```

Aby włączyć lokalne wdrożenie usługi Git dla aplikacji przy użyciu serwera kompilacji Kudu, uruchom w [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az_webapp_deployment_source_config_local_git) Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

To polecenie daje dane wyjściowe podobne do następujących:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Wdrażanie projektu

W lokalnym _oknie terminalu_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp _\<url>_ adresem URL zdalnego repozytorium Git, który pochodzi z [tematu Enable local Git with Kudu (Włączanie lokalnego narzędzia Git za pomocą usługi Kudu).](#enable-local-git-with-kudu)

```bash
git remote add azure <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Po wyświetleniu monitu o hasło wprowadź hasło utworzone w te sposób, aby [skonfigurować użytkownika wdrożenia.](#configure-a-deployment-user) Nie używaj hasła, za pomocą których loguje się do Azure Portal.

```bash
git push azure main
```

W danych wyjściowych może być widać automatyzację specyficzną dla środowiska uruchomieniowego, taką jak MSBuild dla środowiska ASP.NET, Node.js `npm install` i `pip install` dla języka Python.

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Przejdź do aplikacji internetowej przy użyciu przeglądarki, aby sprawdzić, czy zawartość została wdrożona.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Używanie tożsamości zarządzanej w innych językach

Dostawcy usługi App Configuration dla programu .NET Framework i Java Spring również mają wbudowaną obsługę tożsamości zarządzanej. Podczas konfigurowania jednego z tych dostawców możesz użyć punktu końcowego adresu URL sklepu zamiast jego pełnych parametrów połączenia.

Na przykład możesz zaktualizować aplikację konsoli .NET Framework utworzoną w przewodniku Szybki start, aby określić następujące ustawienia w *App.config* pliku:

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
W tym samouczku dodano tożsamość zarządzaną platformy Azure, aby usprawnić dostęp do usługi App Configuration i usprawnić zarządzanie poświadczeniami dla aplikacji. Aby dowiedzieć się więcej na temat używania interfejsu App Configuration, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](./cli-samples.md)