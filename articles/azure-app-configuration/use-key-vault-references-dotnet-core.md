---
title: Samouczek dotyczący używania odwołań Azure App Configuration Key Vault w aplikacji ASP.NET Core | Microsoft Docs
description: Z tego samouczka dowiesz się, jak używać Azure App Configuration odwołań Key Vault z aplikacji ASP.NET Core
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
ms.openlocfilehash: 57abbeefe8e3f2abe527f2b282d643db766b9dc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775749"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Samouczek: używanie odwołań Key Vault w aplikacji ASP.NET Core

Z tego samouczka dowiesz się, jak używać usługi Azure App Configuration razem z Azure Key Vault. App Configuration i Key Vault to uzupełniające się usługi używane obok siebie w większości wdrożeń aplikacji.

App Configuration usług, tworząc klucze odwołujące się do wartości przechowywanych w Key Vault. Gdy App Configuration takie klucze, przechowuje ona Key Vault, a nie same wartości.

Aplikacja używa dostawcy App Configuration klienta do pobierania odwołań Key Vault, podobnie jak w przypadku innych kluczy przechowywanych w App Configuration. W tym przypadku wartości przechowywane w App Configuration są AMI odwołującymi się do wartości w Key Vault. Nie są to Key Vault ani poświadczenia. Ponieważ dostawca klienta rozpoznaje klucze jako odwołania Key Vault, używa Key Vault do pobierania ich wartości.

Aplikacja jest odpowiedzialna za prawidłowe uwierzytelnianie zarówno w App Configuration, jak i Key Vault. Te dwie usługi nie komunikują się bezpośrednio.

W tym samouczku pokazano, jak zaimplementować odwołania Key Vault w kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem zakończ [tworzenie aplikacji ASP.NET Core przy użyciu App Configuration](./quickstart-aspnet-core-app.md) najpierw.

Możesz użyć dowolnego edytora kodu, aby wykonać kroki opisane w tym samouczku. Na przykład [Visual Studio Code](https://code.visualstudio.com/) to międzyplatformowy edytor kodu dostępny dla systemów operacyjnych Windows, macOS i Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz klucz App Configuration, który odwołuje się do wartości przechowywanej w Key Vault.
> * Uzyskaj dostęp do wartości tego klucza z aplikacji internetowej ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Wybierz **opcję Utwórz zasób** w lewym górnym rogu okna Azure Portal:

    ![Zrzut ekranu przedstawia opcję Utwórz zasób w Azure Portal.](./media/quickstarts/search-services.png)
1. W polu wyszukiwania wprowadź **magazyn kluczy**.
1. Z listy wyników wybierz pozycję **Magazyny kluczy** po lewej stronie.
1. W **chmurze Magazyny kluczy** wybierz pozycję **Dodaj**.
1. Po prawej stronie w **te tematu Create key vault (Tworzenie** magazynu kluczy) podaj następujące informacje:
    - Wybierz **pozycję Subskrypcja,** aby wybrać subskrypcję.
    - W **grupie zasobów** wybierz pozycję Utwórz **nową** i wprowadź nazwę grupy zasobów.
    - W **nazwie magazynu kluczy** wymagana jest unikatowa nazwa. W tym samouczku wprowadź **wartość Contoso-vault2**.
    - Z **listy rozwijanej** Region wybierz lokalizację.
1. Pozostaw wartości domyślne pozostałych **opcji Utwórz** magazyn kluczy.
1. Wybierz przycisk **Utwórz**.

W tym momencie Twoje konto platformy Azure jest jedynym kontem autoryzowanym do uzyskiwania dostępu do tego nowego magazynu.

![Zrzut ekranu przedstawia magazyn kluczy.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać klucz tajny do magazynu, należy wykonać kilka dodatkowych kroków. W takim przypadku dodaj komunikat, który umożliwia przetestowanie Key Vault pobierania. Komunikat ma nazwę **Message** i przechowujesz w nim wartość "Hello from Key Vault".

1. Na stronie Key Vault właściwości wybierz pozycję **Wpisy tajne.**
1. Wybierz **pozycję Generuj/Zaimportuj.**
1. W **okienku Tworzenie wpisów** tajnych wprowadź następujące wartości:
    - **Opcje przekazywania:** wprowadź **ręczne .**
    - **Nazwa:** Wprowadź **komunikat**.
    - **Wartość:** wprowadź **Hello z Key Vault**.
1. Pozostaw pozostałe **właściwości Utwórz klucz tajny** z ich wartościami domyślnymi.
1. Wybierz przycisk **Utwórz**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodawanie odwołania Key Vault do App Configuration

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **pozycję Wszystkie** zasoby, a następnie wybierz App Configuration magazynu utworzone w przewodniku Szybki start.

1. Wybierz **pozycję Configuration Explorer**.

1. Wybierz **pozycję + Utwórz** odwołanie do magazynu  >  **kluczy,** a następnie określ następujące wartości:
    - **Klucz:** wybierz **pozycję TestApp:Settings:KeyVaultMessage.**
    - **Etykieta:** pozostaw tę wartość pustą.
    - **Subskrypcja,** **grupa zasobów** i **magazyn kluczy:** wprowadź wartości odpowiadające wartościom w magazynie kluczy utworzonym w poprzedniej sekcji.
    - **Klucz** tajny: wybierz klucz tajny o nazwie **Message** utworzony w poprzedniej sekcji.

## <a name="connect-to-key-vault"></a>Nawiązywanie połączenia z Key Vault

1. W tym samouczku użyjemy jednostki usługi do uwierzytelniania w Key Vault. Aby utworzyć tę jednostkę usługi, użyj polecenia [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) interfejsu wiersza polecenia platformy Azure:

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

1. Uruchom następujące polecenie, aby pozwolić jednostki usługi na dostęp do magazynu kluczy:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Dodaj zmienne środowiskowe do przechowywania wartości *clientId,* *clientSecret* i *tenantId*.

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
    > Te Key Vault są używane tylko w aplikacji. Aplikacja uwierzytelnia się bezpośrednio w Key Vault przy użyciu tych poświadczeń. Nigdy nie są przekazywane do App Configuration usługi.

1. Uruchom ponownie terminal, aby załadować te nowe zmienne środowiskowe.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizowanie kodu w celu używania odwołania Key Vault danych

1. Dodaj odwołanie do wymaganych pakietów NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Otwórz *program Program.cs* i dodaj odwołania do następujących wymaganych pakietów:

    ```csharp
    using Azure.Identity;
    ```

1. Zaktualizuj metodę `CreateWebHostBuilder` , aby używać App Configuration przez wywołanie metody `config.AddAzureAppConfiguration` . Dołącz opcję i przekaż poprawne poświadczenia do swojego `ConfigureKeyVault` Key Vault.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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

1. Po zainicjowaniu połączenia z usługą App Configuration należy skonfigurować połączenie do Key Vault przez wywołanie `ConfigureKeyVault` metody . Po zainicjowaniu można uzyskać dostęp do wartości odwołań Key Vault w taki sam sposób, w jaki uzyskuje się dostęp do wartości zwykłych kluczy App Configuration kluczy.

    Aby zobaczyć, jak ten proces jest w akcji, otwórz *plik Index.cshtml* w **folderze Views**  >  **Home.** Zastąp zawartość poniższym kodem:

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

    Dostęp do wartości właściwości **TestApp:Settings:KeyVaultMessage** Key Vault uzyskać w taki sam sposób jak dla wartości konfiguracji **TestApp:Settings:Message.**

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoki poleceń:

    ```dotnetcli
    dotnet build
    ```

1. Po zakończeniu kompilacji użyj następującego polecenia, aby uruchomić aplikację internetową lokalnie:

    ```dotnetcli
    dotnet run
    ```

1. Otwórz okno przeglądarki i przejdź do adresu , który jest domyślnym adresem URL aplikacji internetowej `http://localhost:5000` hostowanej lokalnie.

    ![Szybki start: uruchamianie aplikacji lokalnej](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono klucz App Configuration, który odwołuje się do wartości przechowywanej w Key Vault. Aby dowiedzieć się, jak dodać tożsamość usługi zarządzanej przez platformę Azure, która usprawnia dostęp do App Configuration i Key Vault, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
