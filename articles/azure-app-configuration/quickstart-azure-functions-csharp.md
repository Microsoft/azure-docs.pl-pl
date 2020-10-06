---
title: Przewodnik Szybki start dotyczący używania usługi Azure App Configuration z platformą Azure Functions | Microsoft Docs
description: W tym przewodniku szybki start Utwórz aplikację Azure Functions przy użyciu konfiguracji aplikacji platformy Azure i języka C#. Utwórz i Połącz się z magazynem konfiguracji aplikacji. Przetestuj funkcję lokalnie.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: b62bf900e3e7859437b10b7b45801ee0dc575282
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767768"
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

1. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** Wyszukaj i Dodaj `Microsoft.Extensions.Configuration.AzureAppConfiguration` pakiet NuGet do projektu. Jeśli nie możesz go znaleźć, zaznacz pole wyboru **Uwzględnij wersję wstępną** .

2. Otwórz *Function1.cs*i Dodaj przestrzenie nazw konfiguracji .NET Core i dostawcy konfiguracji konfiguracji aplikacji.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Dodaj `static` Właściwość o nazwie `Configuration` , aby utworzyć pojedyncze wystąpienie `IConfiguration` . Następnie Dodaj `static` Konstruktor, aby połączyć się z konfiguracją aplikacji przez wywołanie `AddAzureAppConfiguration()` . Spowoduje to załadowanie konfiguracji raz podczas uruchamiania aplikacji. To samo wystąpienie konfiguracji będzie używane w celu późniejszego wywołania funkcji.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Zaktualizuj `Run` metodę, aby odczytywać wartości z konfiguracji.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString**i ustaw ją na klucz dostępu do magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie i ponownie uruchom wiersz polecenia, aby zezwolić na wprowadzenie zmiany:

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

W tym przewodniku szybki start utworzono nowy magazyn konfiguracji aplikacji i używał go z aplikacją Azure Functions za pośrednictwem [dostawcy konfiguracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2074664). Aby dowiedzieć się, jak skonfigurować aplikację Azure Functions do dynamicznego odświeżania ustawień konfiguracji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włączanie dynamicznej konfiguracji](./enable-dynamic-configuration-azure-functions-csharp.md)
