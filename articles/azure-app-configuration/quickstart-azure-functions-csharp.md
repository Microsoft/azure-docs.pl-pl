---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą Azure Functions | Microsoft Docs
description: W tym przewodniku szybki start Utwórz aplikację Azure Functions przy użyciu konfiguracji aplikacji platformy Azure i języka C#. Utwórz i Połącz się z magazynem konfiguracji aplikacji. Przetestuj funkcję lokalnie.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 9d378b21132e6646329c459401255ef9a3ed9426
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724246"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Szybki Start: Tworzenie aplikacji Azure Functions przy użyciu konfiguracji aplikacji platformy Azure

W tym przewodniku szybki start dołączysz usługę Azure App Configuration do aplikacji Azure Functions, aby scentralizować magazyn i zarządzać wszystkimi ustawieniami aplikacji oddzielonymi od kodu.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet)
- [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs) z obciążeniem **programowania na platformie Azure** .
- [Narzędzia Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz kolejno pozycje **Eksplorator konfiguracji**  >  **+ Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z usługi Azure App Configuration |

    Dla tej pory pozostaw pustą **etykietę** i **Typ zawartości** .

8. Wybierz przycisk **Zastosuj**.

## <a name="create-a-functions-app"></a>Tworzenie aplikacji funkcji

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji
Ten projekt będzie używać [iniekcji zależności w programie .net Azure Functions](../azure-functions/functions-dotnet-dependency-injection.md) i dodać konfigurację aplikacji platformy Azure jako dodatkowe źródło konfiguracji.

1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Wyszukaj i Dodaj następujące pakiety NuGet do projektu.
   - [Microsoft.Extensions.Configwersja. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) w wersji 4.1.0 lub nowszej
   - [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) w wersji 1.1.0 lub nowszej 

2. Dodaj nowy plik, *Start. cs*, z poniższym kodem. Definiuje klasę o nazwie `Startup` implementującej `FunctionsStartup` klasę abstrakcyjną. Atrybut Assembly służy do określania nazwy typu używanej podczas uruchamiania Azure Functions.

    `ConfigureAppConfiguration`Metoda zostanie zastąpiona, a dostawca konfiguracji aplikacji platformy Azure jest dodawany jako dodatkowe źródło konfiguracji przez wywołanie `AddAzureAppConfiguration()` . `Configure`Ta metoda jest pusta, ponieważ nie trzeba rejestrować żadnych usług w tym momencie.
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. Otwórz *Function1. cs* i Dodaj następującą przestrzeń nazw.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   Dodaj Konstruktor używany do uzyskania wystąpienia `IConfiguration` przez iniekcję zależności.

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. Zaktualizuj `Run` metodę, aby odczytywać wartości z konfiguracji.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   `Function1`Klasa i `Run` Metoda nie powinny być statyczne. Usuń `static` modyfikator, jeśli został wygenerowany automatycznie.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Naciśnij klawisz F5, aby przetestować funkcję. Jeśli zostanie wyświetlony monit, zaakceptuj żądanie z programu Visual Studio, aby pobrać i zainstalować narzędzia **Azure Functions Core (CLI)** . Może być również konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP.

3. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Debugowanie funkcji w programie VS z przewodnika Szybki start](./media/quickstarts/function-visual-studio-debugging.png)

4. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Na poniższej ilustracji przedstawiono odpowiedź w przeglądarce do lokalnego żądania GET zwróconego przez funkcję.

    ![Lokalne uruchamianie funkcji z przewodnika Szybki start](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i używał go z aplikacją Azure Functions za pośrednictwem [dostawcy konfiguracji aplikacji](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration). Aby dowiedzieć się, jak zaktualizować aplikację Azure Functions, aby dynamicznie odświeżać konfigurację, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie dynamicznej konfiguracji w usłudze Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)