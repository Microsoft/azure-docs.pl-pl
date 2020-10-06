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
ms.openlocfilehash: f57163b3d17877f7f8c66fa57fc8a0736c6affb7
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767715"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Szybki Start: Dodawanie flag funkcji do aplikacji Azure Functions

W tym przewodniku szybki start utworzysz implementację zarządzania funkcjami w aplikacji Azure Functions przy użyciu usługi Azure App Configuration. Za pomocą usługi konfiguracji aplikacji można centralnie przechowywać wszystkie flagi funkcji i kontrolować ich Stany. 

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

1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Wyszukaj i Dodaj następujące pakiety NuGet do swojego projektu. Sprawdź `Microsoft.Extensions.DependencyInjection` , czy jesteś w najnowszej stabilnej kompilacji. 

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration
    Microsoft.FeatureManagement
    ```


1. Otwórz *Function1.cs*i Dodaj przestrzenie nazw tych pakietów.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.DependencyInjection;
    ```

1. Dodaj `Function1` Poniższy Konstruktor statyczny, aby zainicjować dostawcę konfiguracji aplikacji platformy Azure. Następnie Dodaj dwa `static` elementy członkowskie, pole o nazwie `ServiceProvider` , aby utworzyć pojedyncze wystąpienie `ServiceProvider` , i Właściwość poniżej `Function1` o nazwie, `FeatureManager` Aby utworzyć pojedyncze wystąpienie `IFeatureManager` . Następnie połącz się z konfiguracją aplikacji w programie `Function1` , wywołując polecenie `AddAzureAppConfiguration()` . Ten proces spowoduje załadowanie konfiguracji podczas uruchamiania aplikacji. To samo wystąpienie konfiguracji będzie używane w celu późniejszego wywołania funkcji. 

    ```csharp
        // Implements IDisposable, cached for life time of function
        private static ServiceProvider ServiceProvider; 

        static Function1()
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            var services = new ServiceCollection();                                                                             
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            ServiceProvider = services.BuildServiceProvider(); 
        }

        private static IFeatureManager FeatureManager => ServiceProvider.GetRequiredService<IFeatureManager>();
    ```

1. Zaktualizuj `Run` metodę, aby zmienić wartość wyświetlanego komunikatu w zależności od stanu flagi funkcji.

    ```csharp
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
                [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
                ILogger log)
            {
                string message = await FeatureManager.IsEnabledAsync("Beta")
                     ? "The Feature Flag 'Beta' is turned ON"
                     : "The Feature Flag 'Beta' is turned OFF";
                
                return (ActionResult)new OkObjectResult(message); 
            }
    ```

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**, gdzie wartość jest kluczem dostępu pobranym wcześniej w magazynie konfiguracji aplikacji w obszarze **klucze dostępu**. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

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

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz utworzone wystąpienie magazynu konfiguracji aplikacji.

1. Wybierz pozycję **Menedżer funkcji**, a następnie Zmień stan klucza **beta** na **włączone**.

1. Wróć do wiersza polecenia i Anuluj uruchomiony proces przez naciśnięcie klawisza `Ctrl-C` .  Uruchom ponownie aplikację, naciskając klawisz F5. 

1. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego Azure Functions przy użyciu tego samego procesu jak w kroku 3. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Odpowiedź przeglądarki powinna ulec zmianie, aby wskazać, że flaga funkcji `Beta` jest włączona, jak pokazano na poniższej ilustracji.
 
    ![Włączono flagę funkcji szybkiego startu](./media/quickstarts/functions-launch-ff-enabled.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono flagę funkcji i użyto jej z aplikacją Azure Functionsową za pośrednictwem [dostawcy konfiguracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2074664).

- Dowiedz się więcej o [zarządzaniu funkcjami](./concept-feature-management.md).
- [Zarządzaj flagami funkcji](./manage-feature-flags.md).
- [Używanie konfiguracji dynamicznej w aplikacji Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)
