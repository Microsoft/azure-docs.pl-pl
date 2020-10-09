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
ms.openlocfilehash: e603aa8ba85fdd214c04de515f405bcf9028791e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88207104"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji Azure Functions

Dostawca konfiguracji .NET Standard konfiguracji aplikacji obsługuje buforowanie i odświeżanie konfiguracji dynamicznie sterowanej przez działanie aplikacji. W tym samouczku pokazano, jak zaimplementować dynamiczne aktualizacje konfiguracji w swoim kodzie. Jest ona oparta na aplikacji Azure Functions wprowadzonej w przewodnikach Szybki Start. Przed kontynuowaniem najpierw Zakończ [Tworzenie aplikacji usługi Azure Functions przy użyciu usługi Azure App Configuration](./quickstart-azure-functions-csharp.md) .

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

1. Otwórz *Function1.cs*. Oprócz `static` właściwości `Configuration` Dodaj nową `static` Właściwość, `ConfigurationRefresher` Aby zachować pojedyncze wystąpienie `IConfigurationRefresher` , które będzie używane do sygnalizowania aktualizacji konfiguracji podczas wywoływania funkcji w późniejszym czasie.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Zaktualizuj konstruktora i użyj metody, `ConfigureRefresh` Aby określić ustawienie do odświeżenia z magazynu konfiguracji aplikacji. Wystąpienie `IConfigurationRefresher` jest pobierane za pomocą `GetRefresher` metody. Opcjonalnie można także zmienić wartość ustawienia czas wygaśnięcia pamięci podręcznej konfiguracji na 1 minutę z domyślnego 30 sekund.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Zaktualizuj `Run` metodę i sygnał, aby odświeżyć konfigurację przy użyciu `TryRefreshAsync` metody na początku wywołania funkcji. Nie będzie to możliwe, jeśli przedział czasu wygaśnięcia pamięci podręcznej nie zostanie osiągnięty. Usuń `await` operator, jeśli wolisz odświeżać konfigurację bez blokowania.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

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

5. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

6. Wybierz pozycję **Eksplorator konfiguracji**i zaktualizuj wartości następującego klucza:

    | Klucz | Wartość |
    |---|---|
    | TestApp:Settings:Message | Dane z konfiguracji aplikacji platformy Azure — zaktualizowane |

7. Odśwież przeglądarkę kilka razy. Gdy ustawienia buforowane wygaśnie po minucie, na stronie zostanie wyświetlona odpowiedź wywołanie funkcji z zaktualizowaną wartością.

    ![Funkcja szybkiego startu — odświeżanie lokalne](./media/quickstarts/dotnet-core-function-refresh-local.png)

Przykładowy kod używany w tym samouczku można pobrać z [repozytorium GitHub konfiguracji aplikacji](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację Azure Functions do dynamicznego odświeżania ustawień konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej platformy Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
