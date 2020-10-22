---
title: Bezpieczne Zapisywanie ustawień tajnych aplikacji dla aplikacji sieci Web — Azure Key Vault | Microsoft Docs
description: Sposób bezpiecznego zapisywania ustawień tajnych aplikacji, takich jak poświadczenia platformy Azure lub klucze interfejsu API innych firm za pomocą ASP.NET Core Key Vault Provider, klucza tajnego użytkownika lub konstruktorów konfiguracji programu .NET 4.7.1
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 07/17/2019
ms.author: cawa
ms.custom: devx-track-csharp
ms.openlocfilehash: 96b6b262765a361befeadd9b5a42d37ca5e66497
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372059"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpieczne Zapisywanie ustawień tajnych aplikacji dla aplikacji sieci Web

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób bezpiecznego zapisywania ustawień konfiguracji tajnych aplikacji dla aplikacji platformy Azure.

Tradycyjnie wszystkie ustawienia konfiguracji aplikacji sieci Web są zapisywane w plikach konfiguracji, takich jak Web.config. To rozwiązanie prowadzi do sprawdzania ustawień tajnych, takich jak poświadczenia w chmurze, do publicznych systemów kontroli źródła, takich jak GitHub. W tym czasie może być trudne przestrzeganie najlepszych rozwiązań w zakresie zabezpieczeń z powodu obciążenia wymaganego do zmiany kodu źródłowego i ponownego skonfigurowania ustawień deweloperskich.

Aby upewnić się, że proces tworzenia jest bezpieczny, biblioteki narzędzi i struktur struktury są tworzone w celu bezpiecznego zapisywania ustawień tajnych aplikacji z minimalnym lub niezmienionym kodem źródłowym.

## <a name="aspnet-and-net-core-applications"></a>Aplikacje ASP.NET i .NET Core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Zapisz ustawienia tajne w magazynie kluczy tajnych użytkownika, który znajduje się poza folderem kontroli źródła
Jeśli wykonujesz szybki prototyp lub nie masz dostępu do Internetu, Rozpocznij od przeniesienia ustawień tajnych poza folder kontroli źródła do magazynu kluczy tajnych użytkownika. Magazyn kluczy tajnych użytkownika to plik zapisany w folderze profilera użytkownika, więc wpisy tajne nie są zaewidencjonowane do kontroli źródła. Na poniższym diagramie przedstawiono sposób działania [klucza tajnego użytkownika](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) .

![Klucz tajny użytkownika utrzymuje ustawienia tajne poza kontrolą źródła](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

W przypadku korzystania z aplikacji konsolowej programu .NET Core należy bezpiecznie zapisać tajne hasło przy użyciu Key Vault.

### <a name="save-secret-settings-in-azure-key-vault"></a>Zapisz ustawienia tajne w Azure Key Vault
Jeśli opracowujesz projekt i chcesz bezpiecznie udostępniać kod źródłowy, użyj [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Utwórz Key Vault w ramach subskrypcji platformy Azure. Wypełnij wszystkie wymagane pola w interfejsie użytkownika, a następnie kliknij pozycję *Utwórz* w dolnej części bloku.

    ![Utwórz Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Udziel użytkownikowi i członkom zespołu dostępu do Key Vault. Jeśli masz duży zespół, możesz utworzyć [grupę Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) i dodać tę grupę zabezpieczeń do Key Vault. Na liście rozwijanej uprawnienia do wpisów *tajnych* Sprawdź pozycję *Pobierz* i *wystaw* w obszarze *operacje zarządzania kluczami tajnymi*.
Jeśli masz już utworzoną aplikację sieci Web, przyznaj aplikacji sieci Web dostęp do Key Vault, aby mógł uzyskać dostęp do magazynu kluczy bez zapisywania wpisu tajnego w ustawieniach lub plikach aplikacji. Wyszukaj swoją aplikację sieci Web według jej nazwy i Dodaj ją w taki sam sposób, jak przyznano użytkownikom dostęp.

    ![Dodawanie zasad dostępu Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Dodaj klucz tajny do Key Vault na Azure Portal. W przypadku zagnieżdżonych ustawień konfiguracji Zastąp ciąg ":" "--", aby nazwa tajnego Key Vault była prawidłowa. znak ":" nie może być nazwą wpisu tajnego Key Vault.

    ![Dodaj Key Vault tajny](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Przed rozpoczęciem korzystania z programu Visual Studio 2017 15.6 w wersji zazalecamy zainstalowanie rozszerzenia uwierzytelniania usług platformy Azure dla programu Visual Studio. Jest to jednak przestarzałe, ponieważ funkcje są zintegrowane w ramach programu Visual Studio. W związku z tym, jeśli używasz starszej wersji programu Visual Studio 2017, Zalecamy zaktualizowanie do programu VS 2017 15,6 lub w taki sposób, aby można było używać tych funkcji natywnie i uzyskać dostęp do magazynu kluczy z użyciem tożsamości logowania programu Visual Studio.
    >

4. Zaloguj się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

    ```azurecli
    az login
    ```

5. Dodaj następujące pakiety NuGet do projektu:

    ```
    Azure.Identity
    Azure.Extensions.AspNetCore.Configuration.Secrets
    ```
6. Dodaj następujący kod do pliku Program.cs:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        builder.AddAzureKeyVault(new Uri(keyVaultEndpoint), new DefaultAzureCredential(), new KeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```

7. Dodaj adres URL Key Vault do launchsettings.jsna pliku. Nazwa zmiennej środowiskowej *KEYVAULT_ENDPOINT* jest zdefiniowana w kodzie, który został dodany w kroku 7.

    ![Dodaj adres URL Key Vault jako zmienną środowiskową projektu](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Rozpocznij debugowanie projektu. Powinien zostać uruchomiony pomyślnie.

## <a name="aspnet-and-net-applications"></a>Aplikacje ASP.NET i .NET

Platforma .NET 4.7.1 obsługuje konstruktory konfiguracji Key Vault i wpisów tajnych, co gwarantuje, że wpisy tajne mogą być przenoszone poza folder kontroli źródła bez wprowadzania zmian w kodzie.
Aby to zrobić, [Pobierz program .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) i Migruj swoją aplikację, jeśli używasz starszej wersji programu .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Zapisz ustawienia tajne w pliku tajnym, który znajduje się poza folderem kontroli źródła
Jeśli piszesz krótki prototyp i nie chcesz udostępniać zasobów platformy Azure, przejdź do tej opcji.

1. Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Zarządzaj kluczami tajnymi użytkownika**. Spowoduje to zainstalowanie pakietu NuGet **Microsoft.Configuration.ConfigurationBuilders. UserSecrets** , utworzenie pliku do zapisania ustawień tajnych poza plikiem web.config i dodanie sekcji **ConfigBuilders** w pliku web.config.

2. Umieść ustawienia wpisu tajnego w obszarze głównym elementu. Poniżej znajduje się przykład

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <root>
      <secrets ver="1.0">
        <secret name="secret" value="foo"/>
        <secret name="secret1" value="foo_one" />
        <secret name="secret2" value="foo_two" />
      </secrets>
    </root>
    ```

3. Określ sekcję appSettings przy użyciu programu Secret Configuration Builder. Upewnij się, że istnieje wpis dla ustawienia tajnego z wartością fikcyjną.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Debuguj aplikację. Powinien zostać uruchomiony pomyślnie.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Zapisywanie ustawień tajnych w Azure Key Vault
Postępuj zgodnie z instrukcjami w sekcji ASP.NET Core, aby skonfigurować Key Vault dla projektu.

1. Zainstaluj następujący pakiet NuGet w projekcie
   ```
   Microsoft.Configuration.ConfigurationBuilders.Azure
   ```

2. Zdefiniuj Key Vault konstruktora konfiguracji w Web.config. Umieść tę sekcję przed sekcją *AppSettings* . Zastąp wartość Nazwa *magazynu* nazwą Key Vault, jeśli key Vault znajduje się na globalnej platformie Azure, lub pełny identyfikator URI, jeśli korzystasz z chmury suwerennej.

    ```xml
     <configBuilders>
        <builders>
            <add name="Secrets" userSecretsId="695823c3-6921-4458-b60b-2b82bbd39b8d" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.UserSecrets, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
            <add name="AzureKeyVault" vaultName="[VaultName]" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
        </builders>
      </configBuilders>
    ```
3. Określ sekcję appSettings przy użyciu konstruktora konfiguracji Key Vault. Upewnij się, że dla ustawienia tajnego istnieje wpis z wartością fikcyjną.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Rozpocznij debugowanie projektu. Powinien zostać uruchomiony pomyślnie.
