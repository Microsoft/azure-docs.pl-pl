---
title: Wprowadzenie do zestawu SDK zadań WebJob
description: Wprowadzenie do zestawu SDK zadań WebJob na potrzeby przetwarzania w tle opartego na zdarzeniach. Dowiedz się, jak uzyskać dostęp do danych w usługach platformy Azure i usługach innych firm.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a22602dd9638b981cfe3d8bae9b5cdaacbf90dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652044"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Get started with the Azure WebJobs SDK for event-driven background processing (Rozpoczynanie pracy z zestawem SDK usługi Azure WebJobs umożliwiającym oparte na zdarzeniach przetwarzanie w tle)

W tym artykule pokazano, jak za pomocą programu Visual Studio 2019 utworzyć projekt zestawu SDK Azure WebJobs, uruchomić go lokalnie, a następnie wdrożyć do [Azure App Service](overview.md). Wersja 3. x zestawu SDK usługi WebJobs obsługuje aplikacje konsolowe platformy .NET Core i .NET Framework. Aby dowiedzieć się więcej o pracy z zestawem SDK usługi WebJobs, zobacz [jak używać zestawu sdk Azure WebJobs na potrzeby przetwarzania w tle opartego na zdarzeniach](webjobs-sdk-how-to.md).

W tym artykule opisano sposób wdrażania zadań WebJob jako aplikacji konsolowej programu .NET Core. Aby wdrożyć Zadania WebJob jako aplikację konsolową .NET Framework, zobacz [Zadania WebJob jako .NET Framework aplikacje konsolowe](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Jeśli interesuje Cię zestaw SDK usługi WebJobs w wersji 2. x, który obsługuje tylko .NET Framework, zobacz [Tworzenie i wdrażanie zadań WebJob za pomocą programu Visual Studio — Azure App Service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj program Visual Studio 2019](/visualstudio/install/) przy użyciu obciążeń **programistycznych platformy Azure** . Jeśli masz już program Visual Studio, ale nie masz tego obciążenia, Dodaj obciążenie, wybierając pozycję **narzędzia > Pobierz narzędzia i funkcje**.

* Aby opublikować projekt zestawu SDK zadań WebJob na platformie Azure, musisz mieć [konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="create-a-project"></a>Tworzenie projektu

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.

2. Wybierz pozycję **aplikacja konsoli (.NET Core)**.

3. Nazwij projekt *WebJobsSDKSample*, a następnie wybierz pozycję **Utwórz**.

   ![Okno dialogowe Nowy projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Pakiety NuGet zadań WebJob

1. Zainstaluj najnowszą wersję [ `Microsoft.Azure.WebJobs.Extensions` pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)o stabilnej wersji 3. x, która obejmuje `Microsoft.Azure.WebJobs` .

     Oto polecenie **konsoli Menedżera pakietów** :

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    W tym poleceniu Zastąp `<3_X_VERSION>` wartość obsługiwaną wersją pakietu. 

## <a name="create-the-host"></a>Tworzenie hosta

Host jest kontenerem środowiska uruchomieniowego dla funkcji, które nasłuchują dla wyzwalaczy i wywołań funkcji. Poniższe kroki tworzą hosta, który implementuje [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) , który jest hostem ogólnym w ASP.NET Core.

1. W *program.cs*, Dodaj następujące `using` instrukcje:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Zastąp metodę `Main` poniższym kodem:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

W ASP.NET Core konfiguracje hosta są ustawiane przez wywołanie metod w [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) wystąpieniu. Aby uzyskać więcej informacji, zobacz [host ogólny programu .NET](/aspnet/core/fundamentals/host/generic-host). `ConfigureWebJobs`Metoda rozszerzająca Inicjuje hosta zadań WebJob. W programie `ConfigureWebJobs` inicjowane są określone rozszerzenia zadań WebJob i są ustawiane właściwości tych rozszerzeń.  

## <a name="enable-console-logging"></a>Włącz rejestrowanie konsoli

W tej sekcji skonfigurujesz Rejestrowanie konsoli, która używa [struktury rejestrowania ASP.NET Core](/aspnet/core/fundamentals/logging).

1. Zainstaluj najnowszą stabilną wersję [ `Microsoft.Extensions.Logging.Console` pakietu NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/), która obejmuje `Microsoft.Extensions.Logging` .

   Oto polecenie **konsoli Menedżera pakietów** :

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   W tym poleceniu Zastąp `<3_X_VERSION>` wartość obsługiwaną wersją 3. x pakietu.

1. W *program.cs*Dodaj `using` instrukcję:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Wywołaj [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metodę w [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole)Metoda dodaje do konfiguracji Rejestrowanie konsoli.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main`Teraz Metoda wygląda następująco:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Ta aktualizacja wykonuje następujące czynności:

    * Wyłącza [Rejestrowanie pulpitu nawigacyjnego](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Pulpit nawigacyjny jest starszym narzędziem do monitorowania, a rejestrowanie pulpitu nawigacyjnego nie jest zalecane w scenariuszach produkcyjnych o wysokiej przepływności.
    * Dodaje dostawcę konsoli z domyślnym [filtrowaniem](webjobs-sdk-how-to.md#log-filtering).

Teraz można dodać funkcję, która jest wyzwalana przez komunikaty docierające do kolejki usługi Azure Storage.

## <a name="install-the-storage-binding-extension"></a>Instalowanie rozszerzenia powiązania usługi Storage

Począwszy od wersji 3. x, należy jawnie zainstalować rozszerzenie powiązania magazynu wymagane przez zestaw SDK zadań WebJob. W poprzednich wersjach powiązania magazynu zostały uwzględnione w zestawie SDK.

1. Zainstaluj najnowszą stabilną wersję pakietu NuGet  [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) w wersji 3. x. 

    Oto polecenie **konsoli Menedżera pakietów** :

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    W tym poleceniu Zastąp `<3_X_VERSION>` wartość obsługiwaną wersją pakietu. 

2. W `ConfigureWebJobs` metodzie rozszerzenia Wywołaj `AddAzureStorage` metodę w wystąpieniu, [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) Aby zainicjować rozszerzenie magazynu. W tym momencie `ConfigureWebJobs` Metoda wygląda podobnie do poniższego przykładu:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Tworzenie funkcji

1. Kliknij prawym przyciskiem myszy projekt, wybierz polecenie **Dodaj**  >  **nowy element...**, wybierz **klasę**, nazwij nowy plik klasy C# *Functions.cs*i wybierz pozycję **Dodaj**.

1. W Functions.cs Zastąp wygenerowany szablon następującym kodem:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   Ten `QueueTrigger` atrybut informuje środowisko uruchomieniowe o wywołaniu tej funkcji, gdy zostanie zapisany nowy komunikat w kolejce usługi Azure Storage o nazwie `queue` . Zawartość komunikatu w kolejce jest dostarczana do kodu metody w `message` parametrze. Treść metody polega na przetwarzaniu danych wyzwalacza. W tym przykładzie kod właśnie rejestruje komunikat.

   `message`Parametr nie musi być ciągiem. Można również powiązać z obiektem JSON, tablicą bajtową lub obiektem [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) . [Zobacz Użycie wyzwalacza kolejki](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Każdy typ powiązania (taki jak kolejki, obiekty blob lub tabele) ma inny zestaw typów parametrów, do których można utworzyć powiązanie.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Emulator usługi Azure Storage, który działa lokalnie, nie ma wszystkich funkcji potrzebnych przez zestaw SDK zadań WebJob. W tej sekcji utworzysz konto magazynu na platformie Azure i skonfigurujesz projekt do korzystania z niego. Jeśli masz już konto magazynu, przejdź do kroku 6.

1. Otwórz **Eksplorator serwera** w programie Visual Studio i zaloguj się do platformy Azure. Kliknij prawym przyciskiem myszy węzeł **platformy Azure** , a następnie wybierz pozycję **Połącz z subskrypcją Microsoft Azure**.

   ![Logowanie do platformy Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. W węźle **platformy Azure** w **Eksplorator serwera**kliknij prawym przyciskiem myszy pozycję **Magazyn**, a następnie wybierz pozycję **Utwórz konto magazynu**.

   ![Menu Tworzenie konta magazynu](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. W oknie dialogowym **Tworzenie konta magazynu** wprowadź unikatową nazwę konta magazynu.

1. Wybierz ten sam **region** , w którym została utworzona aplikacja App Service, lub region blisko siebie.

1. Wybierz przycisk **Utwórz**.

   ![Utwórz konto magazynu](./media/webjobs-sdk-get-started/create-storage-account.png)

1. W węźle **Magazyn** w obszarze **Eksplorator serwera**wybierz nowe konto magazynu. W oknie **Właściwości** wybierz wielokropek (**...**) z prawej strony pola wartość **parametrów połączenia** .

   ![Wielokropek parametrów połączenia](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Skopiuj parametry połączenia i Zapisz tę wartość w miejscu, w którym można łatwo skopiować ją.

   ![Kopiuj parametry połączenia](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Konfigurowanie magazynu do lokalnego uruchamiania

Zestaw SDK zadań WebJob szuka parametrów połączenia magazynu w ustawieniach aplikacji na platformie Azure. W przypadku uruchamiania lokalnego program szuka tej wartości w lokalnym pliku konfiguracyjnym lub w zmiennych środowiskowych.

1. Kliknij prawym przyciskiem myszy projekt, wybierz polecenie **Dodaj**  >  **nowy element...**, wybierz **plik konfiguracji JSON języka JavaScript**, nazwij nowy plik *appsettings.js* pliku i wybierz polecenie **Dodaj**. 

1. W nowym pliku Dodaj `AzureWebJobsStorage` pole, jak w poniższym przykładzie:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Zastąp *{Parametry połączenia magazynu}* parametrami połączenia, które zostały wcześniej skopiowane.

1. Wybierz *appsettings.jsw* pliku Eksplorator rozwiązań i w oknie **Właściwości** ustaw wartość **Kopiuj do katalogu wyjściowego** na **Kopiuj, jeśli nowszy**.

Później należy dodać to samo ustawienie aplikacji parametrów połączenia w aplikacji w Azure App Service.

## <a name="test-locally"></a>Testuj lokalnie

W tej sekcji utworzysz i uruchamiasz projekt lokalnie i Wyzwalasz funkcję, tworząc komunikat w kolejce.

1. Naciśnij **klawisze CTRL + F5** , aby uruchomić projekt.

   Konsola programu pokazuje, że środowisko uruchomieniowe odnalazło funkcję i czeka na wywołanie komunikatów w kolejce. Następujące dane wyjściowe są generowane przez hosta v3. x:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Zamknij okno konsoli.

1. W **Eksplorator serwera** w programie Visual Studio rozwiń węzeł nowego konta magazynu, a następnie kliknij prawym przyciskiem myszy pozycję **kolejki**.

1. Wybierz pozycję **Utwórz kolejkę**.

1. Wprowadź *kolejkę* jako nazwę kolejki, a następnie wybierz przycisk **OK**.

   ![Zrzut ekranu pokazujący, gdzie tworzysz kolejkę i nadaj jej nazwę "queue". ](./media/webjobs-sdk-get-started/create-queue.png)

1. Kliknij prawym przyciskiem myszy węzeł nowej kolejki, a następnie wybierz pozycję **Wyświetl kolejkę**.

1. Wybierz ikonę **Dodaj wiadomość** .

   ![Zrzut ekranu, który podświetla ikonę Dodaj wiadomość.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. W oknie dialogowym **Dodawanie wiadomości** wprowadź *Hello World!* jako **tekst komunikatu**, a następnie wybierz przycisk **OK**. W kolejce znajduje się teraz komunikat.

   ![Utwórz kolejkę](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Uruchom ponownie projekt.

   Ponieważ `QueueTrigger` w funkcji użyto atrybutu `ProcessQueueMessage` , środowisko uruchomieniowe zestawu WeJobs SDK nasłuchuje komunikatów w kolejce podczas uruchamiania. Odnajdzie nowy komunikat kolejki w kolejce o nazwie *Queue* i wywołuje funkcję.

   Ze względu na to, że w [kolejce jest wycofywania wykładniczy](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm), wykonanie komunikatu przez środowisko uruchomieniowe może trwać tak długo, jak i wywoływać funkcję. Ten czas oczekiwania można skrócić, uruchamiając w [trybie tworzenia](webjobs-sdk-how-to.md#host-development-settings).

   Dane wyjściowe konsoli wyglądają następująco:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Zamknij okno konsoli. 

1. Wróć do okna kolejki i Odśwież go. Komunikat został usunięty, ponieważ został przetworzony przez funkcję uruchomioną lokalnie. 

## <a name="add-application-insights-logging"></a>Dodawanie rejestrowania Application Insights

Gdy projekt jest uruchamiany na platformie Azure, nie można monitorować wykonywania funkcji przez wyświetlenie danych wyjściowych konsoli. Zalecane rozwiązanie do monitorowania jest [Application Insights](../azure-monitor/app/app-insights-overview.md). Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](../azure-functions/functions-monitoring.md).

W tej sekcji wykonaj następujące zadania, aby skonfigurować rejestrowanie Application Insights przed wdrożeniem na platformie Azure:

* Upewnij się, że masz aplikację App Service i wystąpienie Application Insights, z którym chcesz współpracować.
* Skonfiguruj aplikację App Service tak, aby korzystała z wystąpienia Application Insights i utworzonego wcześniej konta magazynu.
* Skonfiguruj projekt do rejestrowania w Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Tworzenie App Service aplikacji i wystąpienia Application Insights

1. Jeśli nie masz jeszcze aplikacji App Service, której możesz użyć, [Utwórz ją](quickstart-dotnet-framework.md). Podczas tworzenia aplikacji można również utworzyć połączony zasób Application Insights. Gdy to zrobisz, `APPINSIGHTS_INSTRUMENTATIONKEY` zostanie ustawione dla Ciebie w aplikacji.

1. Jeśli nie masz jeszcze zasobu Application Insights, którego możesz użyć, [Utwórz go](../azure-monitor/app/create-new-resource.md ). W polu **Typ aplikacji** ustaw wartość **Ogólne**i Pomiń następujące sekcje, które obserwują **kopię klucza Instrumentacji**.

1. Jeśli masz już zasób Application Insights, którego chcesz użyć, [skopiuj klucz Instrumentacji](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji 

1. W **Eksplorator serwera** w programie Visual Studio rozwiń węzeł **App Service** na **platformie Azure**.

1. Rozwiń grupę zasobów, w której znajduje się aplikacja App Service, a następnie kliknij prawym przyciskiem myszy aplikację App Service.

1. Wybierz pozycję **Wyświetl ustawienia**.

1. W polu **Parametry połączenia** Dodaj następujący wpis.

   |Nazwa  |Parametry połączenia  |Typ bazy danych|
   |---------|---------|------|
   |AzureWebJobsStorage | {wcześniej skopiowane parametry połączenia magazynu}|Niestandardowy|

1. Jeśli pole **Ustawienia aplikacji** nie ma klucza instrumentacji Application Insights, należy dodać skopiowane wcześniej. (W zależności od sposobu tworzenia aplikacji App Service może już istnieć klucz Instrumentacji).

   |Nazwa  |Wartość  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {Instrumentation Key} |

1. Zastąp klucz " *{Instrumentation Key}* " kluczem Instrumentacji z zasobu Application Insights, którego używasz.

1. Wybierz pozycję **Zapisz**.

1. Dodaj Application Insights połączenie z projektem, aby można było uruchomić je lokalnie. W *appsettings.jsna* pliku Dodaj `APPINSIGHTS_INSTRUMENTATIONKEY` pole, jak w poniższym przykładzie:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Zastąp klucz " *{Instrumentation Key}* " kluczem Instrumentacji z zasobu Application Insights, którego używasz.

1. Zapisz zmiany.

### <a name="add-application-insights-logging-provider"></a>Dodaj dostawcę rejestrowania Application Insights

Aby skorzystać z funkcji rejestrowania [Application Insights](../azure-monitor/app/app-insights-overview.md) , Zaktualizuj swój kod rejestrowania, aby wykonać następujące czynności:

* Dodaj dostawcę rejestrowania Application Insights przy użyciu domyślnego [filtrowania](webjobs-sdk-how-to.md#log-filtering). W przypadku uruchamiania lokalnego wszystkie informacje i dzienniki wyższego poziomu są zapisywane w konsoli programu i Application Insights.
* Umieść obiekt [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) w bloku, `using` Aby upewnić się, że dane wyjściowe dziennika są opróżniane po zakończeniu działania hosta.

1. Zainstaluj najnowszą wersję [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)o stabilnej wersji 3. x.

   Oto polecenie **konsoli Menedżera pakietów** :

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    W tym poleceniu Zastąp `<3_X_VERSION>` wartość obsługiwaną wersją pakietu.

1. Otwórz *program.cs* i Zastąp kod w `Main` metodzie następującym kodem:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Spowoduje to dodanie do rejestrowania dostawcy Application Insights przy użyciu klucza, który został wcześniej dodany do ustawień aplikacji.

## <a name="test-application-insights-logging"></a>Rejestrowanie Application Insights testowych

W tej sekcji należy ponownie uruchomić lokalnie, aby sprawdzić, czy dane rejestrowania są teraz Application Insights jak również w konsoli programu.

1. Użyj **Eksplorator serwera** w programie Visual Studio, aby utworzyć wiadomość w kolejce tak jak [wcześniej](#test-locally), z wyjątkiem wprowadzania danych *Hello App Insights!* jako tekst komunikatu.

1. Uruchom projekt.

   Zestaw SDK zadań WebJob przetwarza komunikat kolejki, a dzienniki są wyświetlane w oknie konsoli.

1. Zamknij okno konsoli.

1. Przejdź do [Azure Portal](https://portal.azure.com/) , aby wyświetlić zasób Application Insights. Wyszukaj i wybierz **Application Insights**.

1. Wybierz wystąpienie Application Insights.

1. Wybierz pozycję **Wyszukaj**.

   ![Wybierz pozycję Wyszukaj](./media/webjobs-sdk-get-started/select-search.png)

1. Jeśli nie widzisz usługi *Hello App Insights!* Kliknij pozycję **Odśwież** okresowo przez kilka minut. (Dzienniki nie pojawiają się natychmiast, ponieważ trwają przez klienta Application Insights opróżnianie dzienników przetwarzanych przez proces IT).

   ![Dzienniki w Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zamknij okno konsoli.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Wdrażanie na platformie Azure

Podczas wdrażania należy utworzyć wystąpienie usługi App Service, w której będą uruchamiane funkcje. Po opublikowaniu aplikacji konsolowej .NET Core w celu App Service na platformie Azure jest ona automatycznie uruchamiana jako zadanie WebJob. Aby dowiedzieć się więcej o publikowaniu, zobacz [Tworzenie i wdrażanie zadań WebJob za pomocą programu Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Wyzwalanie funkcji na platformie Azure

1. Upewnij się, że nie są uruchamiane lokalnie (Zamknij okno konsoli, jeśli jest nadal otwarte). W przeciwnym razie lokalne wystąpienie może być pierwszym procesem przetwarzania wszystkich tworzonych przez siebie komunikatów w kolejce.

1. Na stronie **Kolejka** w programie Visual Studio Dodaj komunikat do kolejki tak jak wcześniej.

1. Odśwież stronę **kolejki** , a nowa wiadomość znika, ponieważ została przetworzona przez funkcję uruchomioną na platformie Azure.

   > [!TIP]
   > Podczas testowania na platformie Azure Użyj [trybu projektowania](webjobs-sdk-how-to.md#host-development-settings) , aby upewnić się, że funkcja wyzwalacza kolejki jest wywoływana od razu i unikaj opóźnień spowodowanych [sondowaniem w kolejce wykładniczej wycofywania](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Wyświetlanie dzienników w Application Insights

1. Otwórz [Azure Portal](https://portal.azure.com/)i przejdź do zasobu Application Insights.

1. Wybierz pozycję **Wyszukaj**.

1. Jeśli nie widzisz *Witaj na platformie Azure!* Kliknij pozycję **Odśwież** okresowo przez kilka minut.

   Dzienniki są wyświetlane z funkcji uruchomionej w zadaniach WebJob, w tym *Witaj na platformie Azure!* tekst wprowadzony w poprzedniej sekcji.

## <a name="add-an-input-binding"></a>Dodawanie powiązania danych wejściowych

Powiązania wejściowe upraszczają kod, który odczytuje dane. W tym przykładzie komunikat kolejki będzie nazwą obiektu BLOB i zostanie użyta nazwa obiektu BLOB do znajdowania i odczytywania obiektu BLOB w usłudze Azure Storage.

1. W *Functions.cs*Zastąp `ProcessQueueMessage` metodę następującym kodem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   W tym kodzie `queueTrigger` jest [wyrażeniem powiązania](../azure-functions/functions-bindings-expressions-patterns.md), co oznacza, że jest on rozpoznawany jako inna wartość w czasie wykonywania.  W czasie wykonywania ma zawartość komunikatu w kolejce.

1. Dodaj `using` :

   ```cs
   using System.IO;
   ```

1. Utwórz kontener obiektów BLOB na koncie magazynu.

   a. W **Eksplorator serwera** w programie Visual Studio rozwiń węzeł konta magazynu, kliknij prawym przyciskiem myszy pozycję **obiekty blob**, a następnie wybierz pozycję **Utwórz kontener obiektów BLOB**.

   b. W oknie dialogowym **Tworzenie kontenera obiektów BLOB** wprowadź *kontener* jako nazwę kontenera, a następnie kliknij przycisk **OK**.

1. Przekaż plik *program.cs* do kontenera obiektów BLOB. (Ten plik jest używany tutaj jako przykład; można przekazać dowolny plik tekstowy i utworzyć komunikat kolejki z nazwą pliku).

   a. W **Eksplorator serwera**kliknij dwukrotnie węzeł utworzonego kontenera.

   b. W oknie **kontenera** wybierz przycisk **Przekaż** .

   ![Przycisk przekazywania obiektów BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Znajdź i wybierz pozycję *program.cs*, a następnie wybierz przycisk **OK**.

1. Utwórz wiadomość w kolejce w kolejce utworzonej wcześniej przy użyciu *program.cs* jako tekst komunikatu.

   ![Program.cs komunikatu kolejki](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Uruchom projekt lokalnie.

   Komunikat kolejki wyzwala funkcję, która następnie odczytuje obiekt BLOB i rejestruje jego długość. Dane wyjściowe konsoli wyglądają następująco:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

Powiązania wyjściowe upraszczają kod, który zapisuje dane. Ten przykład modyfikuje poprzednią przez zapisanie kopii obiektu BLOB zamiast rejestrowania jego rozmiaru. Powiązania magazynu obiektów BLOB są zawarte w pakiecie rozszerzenia usługi Azure Storage, który został zainstalowany wcześniej.

1. Zastąp metodę `ProcessQueueMessage` poniższym kodem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Utwórz kolejny komunikat kolejki z *program.cs* jako tekst komunikatu.

1. Uruchom projekt lokalnie.

   Komunikat kolejki wyzwala funkcję, która następnie odczytuje obiekt BLOB, rejestruje jego długość i tworzy nowy obiekt BLOB. Dane wyjściowe konsoli są takie same, ale po przejściu do okna kontenera obiektów blob i wybraniu opcji **Odśwież**zobaczysz nowy obiekt BLOB o nazwie *copy-program.cs.*

## <a name="republish-the-updates-to-azure"></a>Opublikuj ponownie aktualizacje na platformie Azure

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W oknie dialogowym **Publikowanie** upewnij się, że bieżący profil jest zaznaczony, a następnie wybierz pozycję **Publikuj**. Wyniki publikowania są szczegółowo opisane w oknie **danych wyjściowych** .
 
1. Sprawdź funkcję na platformie Azure, przekazując plik do kontenera obiektów blob i dodając komunikat do kolejki, która jest nazwą przekazanego pliku. Zobaczysz, że komunikat został usunięty z kolejki i kopię pliku utworzonego w kontenerze obiektów BLOB. 

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak utworzyć, uruchomić i wdrożyć projekt WebJobs SDK 3. x.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zestawie SDK zadań WebJob](webjobs-sdk-how-to.md)