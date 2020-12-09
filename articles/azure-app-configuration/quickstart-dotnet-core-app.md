---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą .NET Core | Microsoft Docs
description: W tym przewodniku szybki start utworzysz aplikację platformy .NET Core z konfiguracją aplikacji platformy Azure w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 1a5e756e05543d9f19775f4e2d1abcee82794f9e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932068"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Szybki Start: Tworzenie aplikacji platformy .NET Core przy użyciu konfiguracji aplikacji

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji konsolowej .NET Core w celu scentralizowanego przechowywania i zarządzania ustawieniami aplikacji oddzielonymi od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet)
- [Zestaw .NET Core SDK](https://dotnet.microsoft.com/download) -również dostępna w [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz pozycję **Eksplorator konfiguracji**  >  **Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

8. Wybierz przycisk **Zastosuj**.

## <a name="create-a-net-core-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET Core

Za pomocą [interfejsu wiersza polecenia platformy .NET Core](/dotnet/core/tools/) można utworzyć nowy projekt aplikacji konsoli .NET Core. Zaletą korzystania z interfejs wiersza polecenia platformy .NET Core przez program Visual Studio jest to, że jest on dostępny na platformach Windows, macOS i Linux.  Alternatywnie możesz użyć wstępnie zainstalowanych narzędzi dostępnych w [Azure Cloud Shell](https://shell.azure.com).

1. Utwórz nowy folder dla projektu.

2. W nowym folderze Uruchom następujące polecenie, aby utworzyć nowy projekt aplikacji konsoli .NET Core:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Dodaj odwołanie do `Microsoft.Extensions.Configuration.AzureAppConfiguration` pakietu NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```dotnetcli
    dotnet restore
    ```

3. Otwórz *program.cs* i Dodaj odwołanie do dostawcy konfiguracji aplikacji .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Zaktualizuj `Main` metodę, aby użyć konfiguracji aplikacji przez wywołanie `builder.AddAzureAppConfiguration()` metody.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. W wierszu polecenia Uruchom następujące polecenie:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    Ponownie uruchom wiersz polecenia, aby zmiany zaczęły obowiązywać. Wydrukuj wartość zmiennej środowiskowej, aby sprawdzić, czy została ona prawidłowo ustawiona.

2. Uruchom następujące polecenie, aby skompilować aplikację konsolową:

    ```dotnetcli
    dotnet build
    ```

3. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację lokalnie:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i używał go z aplikacją konsolową platformy .NET Core za pośrednictwem [dostawcy konfiguracji aplikacji](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration). Aby dowiedzieć się, jak skonfigurować aplikację .NET Core do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie dynamicznej konfiguracji](./enable-dynamic-configuration-dotnet-core.md)