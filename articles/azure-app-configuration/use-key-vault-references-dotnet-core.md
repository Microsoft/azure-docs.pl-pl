---
title: Samouczek dotyczący używania usługi Azure App Configuration Key Vault References w aplikacji ASP.NET Core | Microsoft Docs
description: W tym samouczku dowiesz się, jak używać odwołań Key Vault konfiguracji aplikacji platformy Azure z poziomu aplikacji ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 37bc7fbcd366455668d5316e45ffbf79127a49f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981227"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Samouczek: Używanie odwołań Key Vault w aplikacji ASP.NET Core

W tym samouczku dowiesz się, jak korzystać z usługi Azure App Configuration razem z Azure Key Vault. Konfiguracja aplikacji i Key Vault to uzupełniające się usługi używane obok większości wdrożeń aplikacji.

Konfiguracja aplikacji ułatwia korzystanie z usług, tworząc klucze, które odwołują się do wartości przechowywanych w Key Vault. Gdy konfiguracja aplikacji tworzy takie klucze, przechowuje identyfikatory URI wartości Key Vault, a nie same wartości.

Aplikacja używa dostawcy klienta konfiguracji aplikacji do pobierania odwołań Key Vault, podobnie jak w przypadku innych kluczy przechowywanych w konfiguracji aplikacji. W takim przypadku wartości przechowywane w konfiguracji aplikacji to identyfikatory URI, które odwołują się do wartości w Key Vault. Nie Key Vault wartości ani poświadczeń. Ponieważ dostawca klienta rozpoznaje klucze jako odwołania Key Vault, używa Key Vault do pobierania ich wartości.

Aplikacja jest odpowiedzialna za prawidłowe uwierzytelnianie zarówno w konfiguracji aplikacji, jak i Key Vault. Te dwie usługi nie komunikują się bezpośrednio.

W tym samouczku pokazano, jak zaimplementować Key Vault odwołania w kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji ASP.NET Coreej z konfiguracją aplikacji](./quickstart-aspnet-core-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. Na przykład [Visual Studio Code](https://code.visualstudio.com/) to Międzyplatformowy Edytor kodu, który jest dostępny dla systemów operacyjnych Windows, MacOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w Key Vault.
> * Uzyskaj dostęp do wartości tego klucza z aplikacji sieci Web ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Wybierz opcję **Utwórz zasób** w lewym górnym rogu Azure Portal:

    ![Zrzut ekranu przedstawia opcję Utwórz zasób w Azure Portal.](./media/quickstarts/search-services.png)
1. W polu wyszukiwania wprowadź **magazyn kluczy**.
1. Z listy wyników wybierz pozycję **magazyny kluczy** po lewej stronie.
1. W obszarze **magazyny kluczy** wybierz pozycję **Dodaj**.
1. Po prawej stronie w temacie **Tworzenie magazynu kluczy** podaj następujące informacje:
    - Wybierz pozycję **subskrypcja** , aby wybrać subskrypcję.
    - W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
    - W **nazwie magazynu kluczy** wymagana jest unikatowa nazwa. Na potrzeby tego samouczka wprowadź **contoso-vault2**.
    - Z listy rozwijanej **region** wybierz lokalizację.
1. Pozostaw inne opcje **tworzenia magazynu kluczy** z wartościami domyślnymi.
1. Wybierz przycisk **Utwórz**.

W tym momencie Twoje konto platformy Azure jest jedynym autoryzowanym dostępem do tego nowego magazynu.

![Zrzut ekranu przedstawia Twój Magazyn kluczy.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, należy wykonać zaledwie kilka dodatkowych kroków. W takim przypadku Dodaj komunikat, którego można użyć do przetestowania pobierania Key Vault. Wiadomość jest nazywana **komunikatem** i przechowuje w niej wartość "Hello from Key Vault".

1. Na stronie właściwości Key Vault wybierz pozycję wpisy **tajne**.
1. Wybierz pozycję **Generuj/Importuj**.
1. W okienku **Utwórz wpis tajny** wprowadź następujące wartości:
    - **Opcje przekazywania**: wprowadź **Ręczne**.
    - **Nazwa**: wprowadź **komunikat**.
    - **Wartość**: wprowadź **Hello z Key Vault**.
1. Pozostaw inne właściwości **klucza tajnego** z wartościami domyślnymi.
1. Wybierz przycisk **Utwórz**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodaj odwołanie Key Vault do konfiguracji aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji**.

1. Wybierz pozycję **+ Utwórz**  >  **odwołanie do magazynu kluczy**, a następnie określ następujące wartości:
    - **Klucz**: Wybierz **TestApp: Settings: KeyVaultMessage**.
    - **Etykieta**: pozostaw tę wartość pustą.
    - **Subskrypcja**, **Grupa zasobów** i **Magazyn kluczy**: wprowadź wartości odpowiadające tym w magazynie kluczy utworzonym w poprzedniej sekcji.
    - **Wpis tajny**: Wybierz **komunikat** o nazwie Secret utworzony w poprzedniej sekcji.

## <a name="connect-to-key-vault"></a>Połącz z Key Vault

1. W tym samouczku użyjesz nazwy głównej usługi do uwierzytelniania do Key Vault. Aby utworzyć tę nazwę główną usługi, użyj interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ta operacja zwraca serię par klucz/wartość:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Uruchom następujące polecenie, aby umożliwić jednostce usługi dostęp do magazynu kluczy:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Dodaj zmienne środowiskowe do przechowywania wartości *clientId*, *clientSecret* i *tenantId*.

    #### <a name="windows-command-prompt"></a>[Wiersz polecenia systemu Windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Te poświadczenia Key Vault są używane tylko w aplikacji. Aplikacja uwierzytelnia się bezpośrednio do Key Vault przy użyciu tych poświadczeń. Nie są one nigdy przesyłane do usługi konfiguracji aplikacji.

1. Uruchom ponownie Terminal, aby załadować te nowe zmienne środowiskowe.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Zaktualizuj kod, aby użyć odwołania Key Vault

1. Dodaj odwołanie do wymaganych pakietów NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Otwórz *Program Package. cs* i Dodaj odwołania do następujących wymaganych pakietów:

    ```csharp
    using Azure.Identity;
    ```

1. Zaktualizuj `CreateWebHostBuilder` metodę, aby użyć konfiguracji aplikacji przez wywołanie `config.AddAzureAppConfiguration` metody. Uwzględnij `ConfigureKeyVault` opcję i przekaż poprawne poświadczenia do Key Vault.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Po zainicjowaniu połączenia z konfiguracją aplikacji należy skonfigurować połączenie do Key Vault przez wywołanie `ConfigureKeyVault` metody. Po zainicjowaniu można uzyskać dostęp do wartości odwołań Key Vault w ten sam sposób, w jaki uzyskuje się dostęp do wartości zwykłych kluczy konfiguracji aplikacji.

    Aby wyświetlić ten proces w działaniu, Otwórz *index. cshtml* w   >  folderze **głównym** widoki. Zastąp zawartość poniższym kodem:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Dostęp do wartości Key Vault Reference **TestApp: Settings: KeyVaultMessage** w taki sam sposób jak w przypadku wartości konfiguracji **TestApp: Settings: Message**.

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

1. Po zakończeniu kompilacji Użyj następującego polecenia, aby uruchomić aplikację sieci Web lokalnie:

    ```dotnetcli
    dotnet run
    ```

1. Otwórz okno przeglądarki i przejdź do `http://localhost:5000` , który jest domyślnym adresem URL aplikacji sieci Web hostowanej lokalnie.

    ![Szybkiego startu uruchamiania aplikacji](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w Key Vault. Aby dowiedzieć się, jak dodać tożsamość usługi zarządzanej przez platformę Azure, która usprawnia dostęp do konfiguracji aplikacji i Key Vault, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
