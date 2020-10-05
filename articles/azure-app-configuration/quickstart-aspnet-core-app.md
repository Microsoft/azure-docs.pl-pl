---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą ASP.NET Core | Microsoft Docs
description: Tworzenie aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji dla aplikacji ASP.NET Core.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperfq1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5fd042b91ede91491590a53abf4dec552fbf6487
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91440419"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Szybki Start: Tworzenie aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure

W tym przewodniku szybki start będziesz używać konfiguracji aplikacji platformy Azure do scentralizowanego przechowywania i zarządzania ustawieniami aplikacji dla aplikacji ASP.NET Core. ASP.NET Core kompiluje pojedynczy obiekt konfiguracji oparty na wartości klucz-wartość przy użyciu ustawień z co najmniej jednego źródła danych określonego przez aplikację. Te źródła danych są nazywane *dostawcami konfiguracji*. Ponieważ klient .NET Core konfiguracji aplikacji jest zaimplementowany jako dostawca konfiguracji, usługa będzie wyświetlana jak inne źródło danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet)
* [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell to bezpłatna, interaktywna powłoka, której można użyć do uruchomienia instrukcji wiersza polecenia w tym artykule. Ma ona popularne narzędzia platformy Azure preinstalowane, w tym zestaw .NET Core SDK. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com. Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. Wybierz kolejno pozycje konfiguracja programu **Operations**  >  **Explorer**  >  **Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

    | Klucz                                | Wartość                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *codzienne*                                |
    | `TestApp:Settings:Message`         | *Dane z usługi Azure App Configuration* |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** . Wybierz pozycję **Zastosuj**.

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Użyj [interfejsu wiersza polecenia platformy .NET Core (CLI)](/dotnet/core/tools) , aby utworzyć nowy projekt ASP.NET Core MVC. [Azure Cloud Shell](https://shell.azure.com) udostępnia te narzędzia. Są one również dostępne na platformach Windows, macOS i Linux.

Uruchom następujące polecenie, aby utworzyć projekt ASP.NET Core MVC w nowym folderze *TestAppConfig* :

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Uruchom następujące polecenie, aby dodać odwołanie do pakietu NuGet [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) :

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Uruchom następujące polecenie w tym samym katalogu, w którym znajduje się plik *. csproj* . Polecenie używa Menedżera wpisów tajnych do przechowywania wpisu tajnego o nazwie `ConnectionStrings:AppConfig` , który przechowuje parametry połączenia dla magazynu konfiguracji aplikacji. Zastąp `<your_connection_string>` symbol zastępczy parametrami połączenia magazynu konfiguracji aplikacji. Parametry połączenia w obszarze **klucze dostępu** można znaleźć w Azure Portal.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Niektóre powłoki poobcinają parametry połączenia, chyba że są ujęte w cudzysłów. Upewnij się, że dane wyjściowe `dotnet user-secrets` polecenia pokazują wszystkie parametry połączenia. Jeśli tak nie jest, należy ponownie uruchomić polecenie, umieszczając parametry połączenia w cudzysłowie.

    Menedżer wpisów tajnych służy tylko do lokalnego testowania aplikacji sieci Web. Po wdrożeniu aplikacji do [Azure App Service](https://azure.microsoft.com/services/app-service/web)należy użyć ustawienia aplikacji **parametry połączenia** w App Service zamiast tajnego Menedżera w celu przechowywania parametrów połączenia.

    Uzyskaj dostęp do tego wpisu tajnego za pomocą interfejsu API konfiguracji platformy .NET Core. Dwukropek ( `:` ) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Aby uzyskać więcej informacji, zobacz [klucze konfiguracji i wartości](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. W *program.cs*Dodaj odwołanie do przestrzeni nazw interfejsu API konfiguracji platformy .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Zaktualizuj `CreateWebHostBuilder` metodę, aby użyć konfiguracji aplikacji przez wywołanie `AddAzureAppConfiguration` metody.

    > [!IMPORTANT]
    > `CreateHostBuilder` zastępuje program `CreateWebHostBuilder` .NET Core 3. x. Wybierz poprawną składnię opartą na Twoim środowisku.

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    Po powyższej zmianie [dostawca konfiguracji dla konfiguracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2074664) został zarejestrowany w interfejsie API konfiguracji platformy .NET Core.

## <a name="read-from-the-app-configuration-store"></a>Odczytaj z magazynu konfiguracji aplikacji

Wykonaj następujące kroki, aby odczytać i wyświetlić wartości przechowywane w magazynie konfiguracji aplikacji. Interfejs API konfiguracji platformy .NET Core zostanie użyty w celu uzyskania dostępu do magazynu. Składnia Razor będą używane do wyświetlania wartości kluczy.

Otwórz * \<app root> /views/Home/index.cshtml*i Zastąp jego zawartość następującym kodem:

```cshtml
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

W powyższym kodzie klucze magazynu konfiguracji aplikacji są używane w następujący sposób:

* `TestApp:Settings:BackgroundColor`Wartość klucza jest przypisywana do `background-color` właściwości CSS.
* `TestApp:Settings:FontColor`Wartość klucza jest przypisywana do `color` właściwości CSS.
* `TestApp:Settings:FontSize`Wartość klucza jest przypisywana do `font-size` właściwości CSS.
* `TestApp:Settings:Message`Wartość klucza jest wyświetlana jako nagłówek.

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, przejdź do katalogu głównego projektu. Uruchom następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```dotnetcli
    dotnet run
    ```

1. Jeśli pracujesz na komputerze lokalnym, użyj przeglądarki, aby przejść do `http://localhost:5000` . Ten adres jest domyślnym adresem URL hostowanej lokalnie aplikacji sieci Web. Jeśli pracujesz w Azure Cloud Shell, wybierz przycisk **Podgląd sieci Web** , a następnie pozycję **Konfiguruj**.

    ![Znajdź przycisk Podgląd sieci Web](./media/quickstarts/cloud-shell-web-preview.png)

    Po wyświetleniu monitu o skonfigurowanie portu na potrzeby wersji zapoznawczej wprowadź *5000* i wybierz pozycję **Otwórz i Przeglądaj**. Na stronie sieci Web zostanie odczytana wartość "dane z konfiguracji aplikacji platformy Azure".

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W ramach tego przewodnika Szybki start wykonasz następujące czynności:

* Zainicjowano nowy magazyn konfiguracji aplikacji.
* Zarejestrowano dostawcę konfiguracji programu .NET Core magazynu konfiguracji aplikacji.
* Odczytaj klucze magazynu konfiguracji aplikacji z dostawcą konfiguracji.
* Wartość klucza magazynu konfiguracji aplikacji jest wyświetlana przy użyciu składnia Razor.

Aby dowiedzieć się, jak skonfigurować aplikację ASP.NET Core do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie dynamicznej konfiguracji](./enable-dynamic-configuration-aspnet-core.md)
