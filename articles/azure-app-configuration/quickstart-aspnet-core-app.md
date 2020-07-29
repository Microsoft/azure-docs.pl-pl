---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą ASP.NET Core | Microsoft Docs
description: Przewodnik Szybki Start dotyczący korzystania z usługi Azure App Configuration z aplikacjami ASP.NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 069bce6f2f8e46679cbc82dd92c9fd73bfecde85
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373525"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Szybki Start: Tworzenie aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure

W tym przewodniku szybki start będziesz używać konfiguracji aplikacji platformy Azure do scentralizowanego przechowywania i zarządzania ustawieniami aplikacji dla aplikacji ASP.NET Core. ASP.NET Core kompiluje pojedynczy obiekt konfiguracji oparty na kluczu wartości przy użyciu ustawień z co najmniej jednego źródła danych określonego przez aplikację. Te źródła danych są nazywane *dostawcami konfiguracji*. Ponieważ klient .NET Core konfiguracji aplikacji jest zaimplementowany jako dostawca konfiguracji, usługa będzie wyświetlana jak inne źródło danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/download)

>[!TIP]
> Azure Cloud Shell to bezpłatna interaktywna powłoka, której można użyć do uruchomienia instrukcji wiersza polecenia w tym artykule.  Ma ona popularne narzędzia platformy Azure preinstalowane, w tym zestaw .NET Core SDK. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz pozycję **Eksplorator konfiguracji**  >  **Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:BackgroundColor | Biali |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Czarnoskórzy |
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** . Wybierz przycisk **Zastosuj**.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Użyj [interfejsu wiersza polecenia platformy .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) , aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC. [Azure Cloud Shell](https://shell.azure.com) udostępnia te narzędzia.  Są one również dostępne na platformach Windows, macOS i Linux.

1. Utwórz nowy folder dla projektu. W tym przewodniku szybki start nadaj mu nazwę *TestAppConfig*.

1. W nowym folderze Uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji sieci Web ASP.NET Core MVC:

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>Dodawanie narzędzia Secret Manager

Aby użyć Menedżera wpisów tajnych, Dodaj `UserSecretsId` element do pliku *. csproj* .

1. Otwórz plik *. csproj* .

1.  Dodaj `UserSecretsId` element, jak pokazano tutaj. Możesz użyć tego samego identyfikatora GUID lub można zastąpić tę wartość własną.

    > [!IMPORTANT]
    > `CreateHostBuilder`zastępuje `CreateWebHostBuilder` w programie .NET Core 3,0.  Wybierz poprawną składnię opartą na Twoim środowisku.
    
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
        
        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
    </Project>
    ```
    ---

1. Zapisz plik *. csproj* .

Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

> [!TIP]
> Aby dowiedzieć się więcej na temat programu Secret Manager, zobacz [bezpieczne przechowywanie wpisów tajnych aplikacji w programie Development w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Dodaj odwołanie do `Microsoft.Azure.AppConfiguration.AspNetCore` pakietu NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```dotnetcli
    dotnet restore
    ```

1. Dodaj wpis tajny o nazwie *ConnectionStrings:AppConfig* do narzędzia Secret Manager.

    Ten wpis tajny zawiera parametry połączenia w celu uzyskania dostępu do magazynu konfiguracji aplikacji. Zastąp wartość w poniższym poleceniu parametrami połączenia dla magazynu konfiguracji aplikacji. Parametry połączenia w obszarze **klucze dostępu** można znaleźć w Azure Portal.

    To polecenie należy wykonać w tym samym katalogu, w którym znajduje się plik *csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Niektóre powłoki poobcinają parametry połączenia, chyba że są ujęte w cudzysłów. Upewnij się, że dane wyjściowe `dotnet user-secrets` polecenia pokazują wszystkie parametry połączenia. Jeśli tak nie jest, należy ponownie uruchomić polecenie, umieszczając parametry połączenia w cudzysłowie.

    Menedżer wpisów tajnych służy tylko do lokalnego testowania aplikacji sieci Web. Po wdrożeniu aplikacji do [Azure App Service](https://azure.microsoft.com/services/app-service/web)można na przykład użyć ustawienia aplikacja **parametry połączenia** w App Service zamiast tajnego Menedżera w celu przechowywania parametrów połączenia.

    Uzyskaj dostęp do tego wpisu tajnego za pomocą interfejsu API konfiguracji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja według środowiska](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Otwórz *program.cs*i Dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Zaktualizuj `CreateWebHostBuilder` metodę, aby użyć konfiguracji aplikacji przez wywołanie `config.AddAzureAppConfiguration()` metody.

    > [!IMPORTANT]
    > `CreateHostBuilder`zastępuje `CreateWebHostBuilder` w programie .NET Core 3,0.  Wybierz poprawną składnię opartą na Twoim środowisku.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
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
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

    ---

1. Przejdź do * <app root> /views/Home* i Otwórz *index. cshtml*. Zastąp jego zawartość następującym kodem:

    ```HTML
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

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

1. Przejdź do * <app root> /views/Shared* i Otwórz *_Layout. cshtml*. Zastąp jego zawartość następującym kodem:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, przejdź do katalogu głównego aplikacji, a następnie uruchom następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```dotnetcli
    dotnet run
    ```

1. Jeśli pracujesz na komputerze lokalnym, użyj przeglądarki, aby przejść do `http://localhost:5000` . Jest to domyślny adres URL aplikacji sieci Web hostowanej lokalnie.  

Jeśli pracujesz w Azure Cloud Shell, wybierz przycisk *Podgląd sieci Web* , a następnie pozycję *Konfiguruj*.  

![Znajdź przycisk Podgląd sieci Web](./media/quickstarts/cloud-shell-web-preview.png)

Po wyświetleniu monitu o skonfigurowanie portu dla wersji zapoznawczej wprowadź wartość "5000", a następnie wybierz pozycję *Otwórz i Przeglądaj*.  Na stronie sieci Web zostanie odczytana wartość "dane z konfiguracji aplikacji platformy Azure".

![Uruchamianie aplikacji szybkiego startu](./media/quickstarts/aspnet-core-app-launch-local-before.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i użył go za pomocą aplikacji internetowej ASP.NET Core za pośrednictwem [dostawcy konfiguracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2074664). Aby dowiedzieć się, jak skonfigurować aplikację ASP.NET Core do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-aspnet-core.md)
