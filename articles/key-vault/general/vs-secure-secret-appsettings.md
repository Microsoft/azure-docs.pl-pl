---
title: Bezpieczne zapisywanie tajnych ustawień aplikacji dla aplikacji sieci Web — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Jak bezpiecznie zapisywać tajne ustawienia aplikacji, takie jak poświadczenia platformy Azure lub klucze interfejsu API innych firm przy użyciu ASP.NET podstawowych dostawców usług Key Vault, funkcji Tajnego użytkownika lub konstruktorów konfiguracji .NET 4.7.1
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: bcacd5d2ed9e325383ec7ae75002ae0a6213111c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429761"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpieczne zapisywanie tajnych ustawień aplikacji dla aplikacji sieci Web

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób bezpiecznego zapisywania tajnych ustawień konfiguracji aplikacji dla aplikacji platformy Azure.

Tradycyjnie wszystkie ustawienia konfiguracji aplikacji sieci web są zapisywane w plikach konfiguracyjnych, takich jak Web.config. Ta praktyka prowadzi do sprawdzania w tajnych ustawień, takich jak poświadczenia w chmurze do systemów kontroli źródła publicznego, takich jak GitHub. Tymczasem może być trudne do naśladowania bezpieczeństwa najlepsze rozwiązania ze względu na obciążenie wymagane do zmiany kodu źródłowego i ponownie skonfigurować ustawienia programistyczne.

Aby upewnić się, że proces tworzenia jest bezpieczny, biblioteki narzędzi i struktury są tworzone w celu bezpiecznego zapisywania ustawień tajnych aplikacji przy minimalnej lub bez zmiany kodu źródłowego.

## <a name="aspnet-and-net-core-applications"></a>aplikacje ASP.NET i .NET Core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Zapisywanie ustawień tajnych w magazynie User Secret, który znajduje się poza folderem kontroli źródła
Jeśli robisz szybki prototyp lub nie masz dostępu do Internetu, zacznij od przeniesienia tajnych ustawień poza folder kontroli źródła do magazynu User Secret. Magazyn klucza tajnego użytkownika to plik zapisany w folderze profilera użytkownika, więc wpisy tajne nie są zaewidencjonowane do kontroli źródła. Na poniższym diagramie pokazano, jak działa [klucz tajny użytkownika.](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio)

![Klucz tajny użytkownika zachowuje tajne ustawienia poza kontrolą źródła](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Jeśli używasz aplikacji konsoli .NET core, użyj usługi Key Vault, aby bezpiecznie zapisać klucz tajny.

### <a name="save-secret-settings-in-azure-key-vault"></a>Zapisywanie ustawień tajnych w usłudze Azure Key Vault
Jeśli tworzysz projekt i musisz bezpiecznie udostępnić kod źródłowy, użyj [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Utwórz magazyn kluczy w ramach subskrypcji platformy Azure. Wypełnij wszystkie wymagane pola w interfejsie użytkownika i kliknij przycisk *Utwórz* na spodzie bloku

    ![Tworzenie usługi Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Zapewnij Tobie i Członkom Zespołu dostęp do Skarbca kluczy. Jeśli masz duży zespół, możesz utworzyć [grupę usługi Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) i dodać tę grupę zabezpieczeń do usługi Key Vault. W obszarze listy rozwijanej *Uprawnienia tajne* zaznacz pole *wyboru Pobierz* i *wylicz w* obszarze Tajne operacje *zarządzania*.
Jeśli aplikacja internetowa została już utworzona, przyznaj aplikacji sieci Web dostęp do magazynu kluczy, aby mogła uzyskać dostęp do magazynu kluczy bez przechowywania tajnej konfiguracji w ustawieniach aplikacji lub plikach. Wyszukaj aplikację internetową według jej nazwy i dodaj ją w taki sam sposób, w jaki udzielasz użytkownikom dostępu.

    ![Dodawanie zasad dostępu do usługi Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Dodaj swój klucz tajny do usługi Key Vault w witrynie Azure portal. W przypadku zagnieżdżonych ustawień konfiguracji zastąp ':' '--', aby nazwa tajnego magazynu kluczy była prawidłowa. ':' nie może być w nazwie klucza tajnego.

    ![Dodaj klucz tajny magazynu kluczy](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Przed programem Visual Studio 2017 v15.6 zalecamy zainstalowanie rozszerzenia uwierzytelniania usług azure dla programu Visual Studio. Ale jest przestarzałe teraz, jak funkcja jest zintegrowana w programie Visual Studio . W związku z tym, jeśli jesteś na starszej wersji programu Visual Studio 2017 , sugerujemy, aby zaktualizować do co najmniej VS 2017 15.6 lub w górę, dzięki czemu można korzystać z tej funkcji natywnie i uzyskać dostęp do magazynu kluczy przy użyciu samego logowania visual studio tożsamości.
    >

4. Dodaj następujące pakiety NuGet do projektu:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Dodaj następujący kod do pliku Program.cs:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                        keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Dodaj adres URL magazynu kluczy, aby uruchomić plik pliku.json. Nazwa zmiennej środowiskowej *KEYVAULT_ENDPOINT* jest zdefiniowana w kodzie dodanym w kroku 6.

    ![Dodawanie adresu URL magazynu kluczy jako zmiennej środowiska projektu](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Rozpocznij debugowanie projektu. Powinien działać pomyślnie.

## <a name="aspnet-and-net-applications"></a>aplikacje ASP.NET i .NET

.NET 4.7.1 obsługuje kreatorów konfiguracji usługi Key Vault i Secret, co zapewnia przenoszenie wpisów tajnych poza folder kontroli źródła bez zmian kodu.
Aby kontynuować, [pobierz .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) i migracji aplikacji, jeśli używa starszej wersji programu .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Zapisywanie ustawień tajnych w tajnym pliku, który znajduje się poza folderem kontroli źródła
Jeśli piszesz szybki prototyp i nie chcesz aprowizować zasobów platformy Azure, przejdź za pomocą tej opcji.

1. Zainstaluj następujący pakiet NuGet w projekcie
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Utwórz plik podobny do następującego. Zapisz go w lokalizacji poza folderem projektu.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Zdefiniuj tajny plik jako konstruktor konfiguracji w pliku Web.config. Umieść tę sekcję przed *sekcją appSettings.*

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Określ appSettings sekcja używa tajnego konstruktora konfiguracji. Upewnij się, że istnieje wpis dla ustawienia tajnego z wartością manekina.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Debuguj aplikację. Powinien działać pomyślnie.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Zapisywanie ustawień tajnych w magazynie kluczy platformy Azure
Postępuj zgodnie z instrukcjami z sekcji ASP.NET core, aby skonfigurować magazyn kluczy dla projektu.

1. Zainstaluj następujący pakiet NuGet w projekcie
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Zdefiniuj konstruktora konfiguracji magazynu kluczy w witrynie Web.config. Umieść tę sekcję przed *sekcją appSettings.* Zamień *vaultName* na nazwę magazynu kluczy, jeśli magazyn kluczy znajduje się na publicznej platformie Azure, lub pełny identyfikator URI, jeśli używasz chmury suwerennej.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. Określ appSettings sekcja używa konstruktora konfiguracji usługi Key Vault. Upewnij się, że istnieje dowolny wpis dla ustawienia tajnego z wartością manekina.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Rozpocznij debugowanie projektu. Powinien działać pomyślnie.
