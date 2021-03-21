---
title: Przewodnik Szybki Start dotyczący dodawania flag funkcji do Azure Functions | Microsoft Docs
description: W tym przewodniku szybki start Użyj Azure Functions z flagami funkcji z usługi Azure App Configuration i Przetestuj funkcję lokalnie.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 96efc0ea6300e482ddeeda8fa177847f02b7e126
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724258"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Szybki Start: Dodawanie flag funkcji do aplikacji Azure Functions

W tym przewodniku szybki start utworzysz aplikację Azure Functions i używasz w niej flag funkcji. Za pomocą funkcji zarządzania funkcją w konfiguracji aplikacji platformy Azure można centralnie przechowywać wszystkie flagi funkcji i kontrolować ich Stany.

Biblioteki zarządzania funkcjami platformy .NET zwiększają platformę z obsługą flagi funkcji. Te biblioteki są zbudowane na podstawie systemu konfiguracji platformy .NET. Integrują się z konfiguracją aplikacji za pomocą jej dostawcy konfiguracji platformy .NET.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs) z obciążeniem **programowania na platformie Azure** .
- [Narzędzia Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz pozycję **Menedżer funkcji**  >  **+ Dodaj** , aby dodać flagę funkcji o nazwie `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Włącz flagę funkcji o nazwie beta](media/add-beta-feature-flag.png)

    `label` `Description` Na razie pozostaw i niezdefiniowane.

8. Wybierz pozycję **Zastosuj** , aby zapisać nową flagę funkcji.

## <a name="create-a-functions-app"></a>Tworzenie aplikacji funkcji

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Nawiązywanie połączenia z magazynem konfiguracji aplikacji

Ten projekt będzie używać [iniekcji zależności w programie .net Azure Functions](../azure-functions/functions-dotnet-dependency-injection.md). Dodaje konfigurację aplikacji platformy Azure jako dodatkowe źródło konfiguracji, w którym są przechowywane flagi funkcji.

1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Wyszukaj i Dodaj następujące pakiety NuGet do projektu.
   - [Microsoft.Extensions.Configwersja. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) w wersji 4.1.0 lub nowszej
   - [Microsoft. FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/) w wersji 2.2.0 lub nowszej
   - [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) w wersji 1.1.0 lub nowszej 

2. Dodaj nowy plik, *Start. cs*, z poniższym kodem. Definiuje klasę o nazwie `Startup` implementującej `FunctionsStartup` klasę abstrakcyjną. Atrybut Assembly służy do określania nazwy typu używanej podczas uruchamiania Azure Functions.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. Zaktualizuj `ConfigureAppConfiguration` metodę i Dodaj dostawcę konfiguracji aplikacji platformy Azure jako dodatkowe źródło konfiguracji, wywołując polecenie `AddAzureAppConfiguration()` . 

   `UseFeatureFlags()`Metoda nakazuje dostawcy załadować flagi funkcji. Wszystkie flagi funkcji mają domyślne wygaśnięcie pamięci podręcznej wynoszącą 30 sekund przed ponownym sprawdzeniem pod kątem zmian. Interwał wygaśnięcia można zaktualizować, ustawiając `FeatureFlagsOptions.CacheExpirationInterval` Właściwość przekazaną do `UseFeatureFlags` metody. 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```
   > [!TIP]
   > Jeśli nie chcesz, aby żadna konfiguracja inna niż flagi funkcji została załadowana do aplikacji, możesz wywołać `Select("_")` tylko załadowanie nieistniejącego klucza fikcyjnego "_". Domyślnie wszystkie wartości klucza konfiguracji w magazynie konfiguracji aplikacji zostaną załadowane, jeśli żadna `Select` Metoda nie zostanie wywołana.

4. Zaktualizuj `Configure` metodę, aby udostępnić usługi Azure App Configuration Services i Menedżera funkcji za pomocą iniekcji zależności.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. Otwórz *Function1. cs* i Dodaj następujące przestrzenie nazw.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Dodaj Konstruktor używany do uzyskiwania wystąpień `_featureManagerSnapshot` i `IConfigurationRefresherProvider` poprzez iniekcję zależności. W programie `IConfigurationRefresherProvider` można uzyskać wystąpienie `IConfigurationRefresher` .

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. Zaktualizuj `Run` metodę, aby zmienić wartość wyświetlanego komunikatu w zależności od stanu flagi funkcji.

   `TryRefreshAsync`Metoda jest wywoływana na początku wywołania funkcji w celu odświeżenia flag funkcji. Jeśli przedział czasu wygaśnięcia pamięci podręcznej nie zostanie osiągnięty, będzie to wartość No-op. Usuń `await` operator, jeśli wolisz odświeżać flagi funkcji bez blokowania bieżącego wywołania funkcji. W takim przypadku późniejsze wywołania funkcji będą otrzymywać zaktualizowane wartości.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**, gdzie wartość to parametry połączenia pobrane wcześniej w magazynie konfiguracji aplikacji w obszarze **klucze dostępu**. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

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

1. Naciśnij klawisz F5, aby przetestować funkcję. Jeśli zostanie wyświetlony monit, zaakceptuj żądanie z programu Visual Studio, aby pobrać i zainstalować narzędzia **Azure Functions Core (CLI)** . Może być również konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP.

1. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Debugowanie funkcji w programie VS z przewodnika Szybki start](./media/quickstarts/function-visual-studio-debugging.png)

1. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Na poniższej ilustracji przedstawiono odpowiedź wskazującą, że flaga funkcji `Beta` jest wyłączona. 

    ![Flaga funkcji szybkiego startu wyłączona](./media/quickstarts/functions-launch-ff-disabled.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby** i wybierz utworzony przez siebie magazyn konfiguracji aplikacji.

1. Wybierz pozycję **Menedżer funkcji**, a następnie Zmień stan klucza **beta** na **włączone**.

1. Odśwież przeglądarkę kilka razy. Gdy flaga funkcji w pamięci podręcznej wygaśnie po 30 sekundach, Strona powinna zostać zmieniona, aby wskazać, że flaga funkcji `Beta` jest włączona, jak pokazano na poniższej ilustracji.
 
    ![Włączono flagę funkcji szybkiego startu](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> Przykładowy kod używany w tym samouczku można pobrać z [repozytorium GitHub konfiguracji aplikacji platformy Azure](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono flagę funkcji i użyto jej z aplikacją Azure Functions za pośrednictwem biblioteki [Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement) .

- Dowiedz się więcej o [zarządzaniu funkcjami](./concept-feature-management.md)
- [Zarządzanie flagami funkcji](./manage-feature-flags.md)
- [Używanie flag funkcji warunkowej](./howto-feature-filters-aspnet-core.md)
- [Włącz etapowe wdrażanie funkcji dla docelowych odbiorców](./howto-targetingfilter-aspnet-core.md)
- [Używanie konfiguracji dynamicznej w aplikacji Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)