---
title: Przewodnik Szybki Start dotyczący dodawania flag funkcji do aplikacji .NET Framework | Microsoft Docs | Microsoft Docs
description: Przewodnik Szybki Start dotyczący dodawania flag funkcji do .NET Framework aplikacji i zarządzania nimi w konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/19/2020
ms.author: alkemper
ms.openlocfilehash: 513c826e11ff9dfe6ea94349c67620da9d1bba48
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932051"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Szybki Start: Dodawanie flag funkcji do aplikacji .NET Framework

W tym przewodniku szybki start dołączysz konfigurację aplikacji platformy Azure do aplikacji .NET Framework, aby utworzyć kompleksową implementację zarządzania funkcjami. Za pomocą usługi konfiguracji aplikacji można centralnie przechowywać wszystkie flagi funkcji i kontrolować ich Stany. 

Biblioteki zarządzania funkcjami platformy .NET zwiększają platformę z obsługą flagi funkcji. Te biblioteki są zbudowane na podstawie systemu konfiguracji platformy .NET. Integrują się z konfiguracją aplikacji za pomocą jej dostawcy konfiguracji platformy .NET.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [ .NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz pozycję **Menedżer funkcji**  >  **+ Dodaj** , aby dodać flagę funkcji o nazwie `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Włącz flagę funkcji o nazwie beta](media/add-beta-feature-flag.png)

    `label`Dla tej pory pozostaw niezdefiniowane.

## <a name="create-a-net-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET

1. Uruchom program Visual Studio i wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

1. W obszarze **Utwórz nowy projekt** odfiltruj typ projektu **konsoli** i kliknij pozycję **Aplikacja konsolowa (.NET Framework)**. Kliknij przycisk **Dalej**.

1. W obszarze **Konfigurowanie nowego projektu** wprowadź nazwę projektu. W obszarze **Struktura** wybierz opcję **.NET Framework 4,8** lub wyższą. Kliknij pozycję **Utwórz**.

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Wyszukaj i Dodaj następujące pakiety NuGet do swojego projektu. Jeśli nie możesz ich znaleźć, zaznacz pole wyboru **Uwzględnij wersję wstępną** .

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Otwórz *program.cs* i Dodaj następujące instrukcje:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    using System.Threading.Tasks;
    ```

1. Zaktualizuj `Main` metodę, aby połączyć się z konfiguracją aplikacji, określając `UseFeatureFlags` opcję, aby można było pobrać flagi funkcji. Następnie Wyświetl komunikat, jeśli `Beta` Flaga funkcji jest włączona.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
            Console.WriteLine("Press any key to continue ...");
            Console.Read();
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** na parametry połączenia magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Uruchom ponownie program Visual Studio, aby zmiany zaczęły obowiązywać. 

1. Naciśnij kombinację klawiszy CTRL + F5, aby skompilować i uruchomić aplikację konsolową.

    ![Aplikacja z włączoną flagą funkcji](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono flagę funkcji w konfiguracji aplikacji i użyto jej z aplikacją konsolową .NET Framework. Aby dowiedzieć się, jak dynamicznie aktualizować flagi funkcji i inne wartości konfiguracji bez ponownego uruchamiania aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie dynamicznej konfiguracji](./enable-dynamic-configuration-dotnet.md)
