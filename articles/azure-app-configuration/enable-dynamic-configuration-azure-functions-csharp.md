---
title: Samouczek dotyczący używania dynamicznej konfiguracji usługi Azure App Configuration w aplikacji Azure Functions | Microsoft Docs
description: W tym samouczku dowiesz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji Azure Functions
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963568"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji Azure Functions

Dostawca konfiguracji platformy .NET konfiguracji aplikacji obsługuje buforowanie i odświeżanie konfiguracji dynamicznie sterowanej przez działanie aplikacji. W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Jest ona oparta na aplikacji Azure Functions wprowadzonej w przewodnikach Szybki Start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji usługi Azure Functions przy użyciu usługi Azure App Configuration](./quickstart-azure-functions-csharp.md) .

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj aplikację Azure Functions, aby zaktualizować jej konfigurację w odpowiedzi na zmiany w magazynie konfiguracji aplikacji.
> * Wsuń najnowszą konfigurację do Azure Functions wywołań.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) z obciążeniem **Programowanie na platformie Azure**
- [Narzędzia Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Kończenie szybkiego startu [Tworzenie aplikacji usługi Azure Functions przy użyciu konfiguracji aplikacji platformy Azure](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Ponowne ładowanie danych z usługi App Configuration

1. Otwórz *Start. cs* i zaktualizuj `ConfigureAppConfiguration` metodę. 

   `ConfigureRefresh`Metoda rejestruje ustawienie do sprawdzenia pod kątem zmian przy każdym wyzwoleniu odświeżania w aplikacji, którą wykonasz w późniejszym kroku podczas dodawania `_configurationRefresher.TryRefreshAsync()` . `refreshAll`Parametr instruuje dostawcę konfiguracji aplikacji, aby załadujeł całą konfigurację za każdym razem, gdy zostanie wykryta zmiana w zarejestrowanym ustawieniu.

    Wszystkie ustawienia zarejestrowane do odświeżenia mają domyślne wygaśnięcie pamięci podręcznej wynoszącą 30 sekund. Można ją zaktualizować, wywołując `AzureAppConfigurationRefreshOptions.SetCacheExpiration` metodę.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Jeśli aktualizujesz wiele wartości klucza w konfiguracji aplikacji, zwykle nie chcesz, aby aplikacja była ponownie ładowana do konfiguracji przed wprowadzeniem wszystkich zmian. Możesz zarejestrować klucz **wskaźnikowy** i zaktualizować go tylko wtedy, gdy wszystkie inne zmiany konfiguracji zostaną ukończone. Pomaga to zapewnić spójność konfiguracji w aplikacji.

2. Zaktualizuj `Configure` metodę, aby udostępnić usługi konfiguracji aplikacji platformy Azure za pomocą iniekcji zależności.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Otwórz *Function1. cs* i Dodaj następujące przestrzenie nazw.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Zaktualizuj konstruktora, aby uzyskać wystąpienie `IConfigurationRefresherProvider` przez iniekcję zależności, z którego można uzyskać wystąpienie `IConfigurationRefresher` .

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Zaktualizuj `Run` metodę i sygnał, aby odświeżyć konfigurację przy użyciu `TryRefreshAsync` metody na początku wywołania funkcji. Jeśli przedział czasu wygaśnięcia pamięci podręcznej nie zostanie osiągnięty, będzie to wartość No-op. Usuń `await` operator, jeśli wolisz odświeżać konfigurację bez blokowania bieżącego wywołania funkcji. W takim przypadku późniejsze wywołania funkcji będą otrzymywać zaktualizowane wartości.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Jeśli używasz macOS lub Linux, uruchom następujące polecenie:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Aby przetestować funkcję, naciśnij klawisz F5. Jeśli zostanie wyświetlony monit, zaakceptuj żądanie z programu Visual Studio, aby pobrać i zainstalować narzędzia **Azure Functions Core (CLI)** . Może być również konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP.

3. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Debugowanie funkcji w programie VS z przewodnika Szybki start](./media/quickstarts/function-visual-studio-debugging.png)

4. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Na poniższej ilustracji przedstawiono odpowiedź w przeglądarce do lokalnego żądania GET zwróconego przez funkcję.

    ![Lokalne uruchamianie funkcji z przewodnika Szybki start](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz magazyn konfiguracji aplikacji utworzony w ramach przewodnika Szybki Start.

6. Wybierz pozycję **Eksplorator konfiguracji** i zaktualizuj wartość następującego klucza:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

   Następnie Utwórz klucz wskaźnikowy lub zmodyfikuj jego wartość, jeśli już istnieje, na przykład

    | Klucz | Wartość |
    |---|---|
    | TestApp: Settings: wskaźnik kontrolny | wersjach |


7. Odśwież przeglądarkę kilka razy. Gdy buforowane ustawienie wygaśnie po 30 sekundach, na stronie zostanie wyświetlona odpowiedź wywołanie funkcji z zaktualizowaną wartością.

    ![Funkcja szybkiego startu — odświeżanie lokalne](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> Przykładowy kod używany w tym samouczku można pobrać z [repozytorium GitHub konfiguracji aplikacji](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację Azure Functions do dynamicznego odświeżania ustawień konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej platformy Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
