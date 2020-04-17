---
title: Wprowadzenie do sdk WebJobs
description: Wprowadzenie do sdk WebJobs dla przetwarzania w tle oparte na zdarzeniach. Dowiedz się, jak uzyskać dostęp do danych w usługach platformy Azure i usługach innych firm.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4976be485a9b7609c6e8d23f6b897092217663fc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535676"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Get started with the Azure WebJobs SDK for event-driven background processing (Rozpoczynanie pracy z zestawem SDK usługi Azure WebJobs umożliwiającym oparte na zdarzeniach przetwarzanie w tle)

W tym artykule pokazano, jak za pomocą programu Visual Studio 2019 utworzyć projekt SDK usługi Azure WebJobs, uruchomić go lokalnie, a następnie wdrożyć w [usłudze Azure App Service.](overview.md) Wersja 3.x zestawu WebJobs SDK obsługuje zarówno aplikacje konsoli .NET Core, jak i .NET Framework. Aby dowiedzieć się więcej na temat pracy z zestawem SDK webjobs, zobacz [Jak używać sdk azure WebJobs do przetwarzania w tle opartego na zdarzeniach.](webjobs-sdk-how-to.md)

W tym artykule pokazano, jak wdrożyć aplikacje WebJobs jako aplikację konsoli .NET Core. Aby wdrożyć webjobs jako aplikację konsoli .NET Framework, zobacz [WebJobs jako aplikacje konsoli .NET Framework](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Jeśli jesteś zainteresowany webjobs SDK w wersji 2.x, który obsługuje tylko .NET Framework, zobacz [Tworzenie i wdrażanie WebJobs przy użyciu programu Visual Studio — Azure App Service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj program Visual Studio 2019](/visualstudio/install/) z obciążeniem **dewelopera platformy Azure.** Jeśli masz już program Visual Studio, ale nie masz tego obciążenia, dodaj obciążenie, wybierając **pozycję Narzędzia > Pobierz narzędzia i funkcje**.

* Musisz mieć [konto platformy Azure,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aby opublikować projekt SDK WebJobs na platformie Azure.

## <a name="create-a-project"></a>Tworzenie projektu

1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.

2. Wybierz **aplikację konsoli (.NET Core)**.

3. Nazwij projekt *WebJobsSDKSample*, a następnie wybierz pozycję **Utwórz**.

   ![Okno dialogowe Nowy projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Pakiety WebJobs NuGet

1. Zainstaluj najnowszą stabilną wersję [ `Microsoft.Azure.WebJobs.Extensions` pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)3.x, która zawiera `Microsoft.Azure.WebJobs`.

     Oto polecenie **Konsola Menedżera pakietów:**

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    W tym poleceniu zastąp `<3_X_VERSION>` obsługiwaną wersją pakietu. 

## <a name="create-the-host"></a>Tworzenie hosta

Host jest kontenerem środowiska wykonawczego dla funkcji, które nasłuchuje wyzwalaczy i wywołuje funkcje. Poniższe kroki tworzą hosta, który implementuje [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost), który jest hostem rodzajowym w ASP.NET Core.

1. W *Program.cs*, dodaj `using` następujące instrukcje:

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

W ASP.NET Core konfiguracje hosta są ustawiane [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) przez metody wywoływania w wystąpieniu. Aby uzyskać więcej informacji, zobacz [Host ogólny platformy .NET](/aspnet/core/fundamentals/host/generic-host). Metoda `ConfigureWebJobs` rozszerzenia inicjuje hosta WebJobs. W `ConfigureWebJobs`, należy zainicjować określone rozszerzenia WebJobs i ustawić właściwości tych rozszerzeń.  

## <a name="enable-console-logging"></a>Włączanie rejestrowania konsoli

W tej sekcji skonfigurowano rejestrowanie konsoli, która korzysta z [ASP.NET core rejestrowania struktury](/aspnet/core/fundamentals/logging).

1. Zainstaluj najnowszą `Microsoft.Extensions.Logging`stabilną [ `Microsoft.Extensions.Logging.Console` wersję pakietu NuGet,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)która zawiera .

   Oto polecenie **Konsola Menedżera pakietów:**

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```

1. W *Program.cs*, dodaj `using` instrukcję:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

    W tym poleceniu zastąp `<3_X_VERSION>` obsługiwaną wersją pakietu 3.x.

1. Wywołanie [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metody [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)na . Metoda [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) dodaje rejestrowanie konsoli do konfiguracji.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    Metoda `Main` wygląda teraz następująco:

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

    * Wyłącza [rejestrowanie pulpitu nawigacyjnego](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Pulpit nawigacyjny jest starszym narzędziem monitorowania, a rejestrowanie pulpitu nawigacyjnego nie jest zalecane w scenariuszach produkcji o wysokiej przepływności.
    * Dodaje dostawcę konsoli z [domyślnym filtrem](webjobs-sdk-how-to.md#log-filtering).

Teraz możesz dodać funkcję, która jest wyzwalana przez wiadomości przychodzące w [kolejce usługi Azure Storage](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Instalowanie rozszerzenia powiązania usługi Storage

Począwszy od wersji 3.x, należy jawnie zainstalować rozszerzenie powiązania magazynu wymagane przez webjobs SDK. W poprzednich wersjach powiązania magazynu zostały uwzględnione w zestawie SDK.

1. Zainstaluj najnowszą stabilną wersję pakietu [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet w wersji 3.x. 

    Oto polecenie **Konsola Menedżera pakietów:**

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    W tym poleceniu zastąp `<3_X_VERSION>` obsługiwaną wersją pakietu. 

2. W `ConfigureWebJobs` metodzie rozszerzenia `AddAzureStorage` wywołaj [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) metodę w wystąpieniu, aby zainicjować rozszerzenie magazynu. W tym momencie `ConfigureWebJobs` metoda wygląda następująco:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Tworzenie funkcji

1. Kliknij prawym przyciskiem myszy projekt, wybierz polecenie **Dodaj** > **nowy element...**, wybierz polecenie **Klasa**, nazwij nowy plik klasy C# *Functions.cs*, a następnie wybierz pozycję **Dodaj**.

1. W Functions.cs zastąp wygenerowany szablon następującym kodem:
    
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

   Atrybut `QueueTrigger` informuje środowisko wykonawcze, aby wywołać tę funkcję, gdy nowa `queue`wiadomość jest napisana w kolejce usługi Azure Storage o nazwie . Zawartość komunikatu kolejki są dostarczane do kodu `message` metody w parametrze. Treść metody jest, gdzie można przetwarzać dane wyzwalacza. W tym przykładzie kod po prostu rejestruje komunikat.

   Parametr `message` nie musi być ciągiem. Można również powiązać z obiektem JSON, tablicą bajtową lub obiektem [CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) [Zobacz Użycie wyzwalacza kolejki](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Każdy typ powiązania (na przykład kolejki, obiekty BLOB lub tabele) ma inny zestaw typów parametrów, które można powiązać.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Emulator usługi Azure Storage, który działa lokalnie nie ma wszystkich funkcji, które wymaga zestaw SDK WebJobs. Dlatego w tej sekcji można utworzyć konto magazynu na platformie Azure i skonfigurować projekt, aby go używać. Jeśli masz już konto magazynu, przejdź w dół do kroku 6.

1. Otwórz **Eksploratora serwera** w programie Visual Studio i zaloguj się na platformie Azure. Kliknij prawym przyciskiem myszy węzeł **platformy Azure,** a następnie wybierz pozycję **Połącz z subskrypcją platformy Microsoft Azure**.

   ![Logowanie do platformy Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. W obszarze węzła **Azure** w **Eksploratorze serwera**kliknij prawym przyciskiem myszy **pozycję Magazyn,** a następnie wybierz polecenie **Utwórz konto magazynu**.

   ![Menu Utwórz konto magazynu](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. W oknie dialogowym **Tworzenie konta magazynu** wprowadź unikatową nazwę konta magazynu.

1. Wybierz ten sam **region,** w którego utworzono aplikację usługi App Service lub region blisko Ciebie.

1. Wybierz pozycję **Utwórz**.

   ![Utwórz konto magazynu](./media/webjobs-sdk-get-started/create-storage-account.png)

1. W węźle **Magazyn** w **Eksploratorze serwera**wybierz nowe konto Magazynu. W oknie **Właściwości** wybierz wielokropek (**...**) po prawej stronie pola Wartość **Parametry połączenia.**

   ![Wielokropek ciągu połączenia](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Skopiuj ciąg połączenia i zapisz tę wartość w miejscu, w których można go łatwo skopiować.

   ![Kopiowanie ciągu połączenia](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Konfigurowanie magazynu do lokalnego uruchamiania

Zestaw WebJobs SDK wyszukuje parametry połączenia magazynu w ustawieniach aplikacji na platformie Azure. Po uruchomieniu lokalnie, szuka tej wartości w pliku konfiguracji lokalnej lub w zmiennych środowiskowych.

1. Kliknij prawym przyciskiem myszy projekt, wybierz pozycję **Dodaj** > **nowy element...**, wybierz **polecenie Plik konfiguracyjny JavaScript JSON**, nazwij nowy plik *appsettings.json* i wybierz pozycję **Dodaj**. 

1. W nowym pliku dodaj `AzureWebJobsStorage` pole, jak w poniższym przykładzie:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Zastąp *{ciąg połączenia magazynu}* na parametry połączenia skopiowane wcześniej.

1. Wybierz plik *appsettings.json* w Eksploratorze **rozwiązań** i w oknie Właściwości ustaw **polecenie Kopiuj do katalogu wyjściowego,** aby **skopiować, jeśli jest nowszy.**

Później dodasz to samo ustawienie aplikacji ciągu połączenia w aplikacji w usłudze Azure App Service.

## <a name="test-locally"></a>Testowanie lokalnie

W tej sekcji można skompilować i uruchomić projekt lokalnie i wyzwolić funkcję, tworząc komunikat kolejki.

1. Naciśnij **klawisze Ctrl+F5,** aby uruchomić projekt.

   Konsola pokazuje, że środowisko wykonawcze odnalazł funkcję i oczekuje na komunikaty kolejki, aby ją wyzwolić. Następujący wynik jest generowany przez hosta v3.x:

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

1. W **Eksploratorze serwera** w programie Visual Studio rozwiń węzeł dla nowego konta magazynu, a następnie kliknij prawym przyciskiem myszy pozycję **Kolejki**.

1. Wybierz **pozycję Utwórz kolejkę**.

1. Wprowadź *kolejkę* jako nazwę kolejki, a następnie wybierz **przycisk OK**.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/create-queue.png)

1. Kliknij prawym przyciskiem myszy węzeł nowej kolejki, a następnie wybierz polecenie **Wyświetl kolejkę**.

1. Wybierz ikonę **Dodaj wiadomość.**

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/create-queue-message.png)

1. W oknie dialogowym **Dodawanie wiadomości** wprowadź *Hello World!* jako **tekst wiadomości**, a następnie wybierz przycisk **OK**. W kolejce znajduje się teraz komunikat.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Uruchom ponownie projekt.

   Ponieważ użyto `QueueTrigger` atrybutu `ProcessQueueMessage` w funkcji, środowisko uruchomieniowe WeJobs SDK nasłuchuje komunikatów kolejki po uruchomieniu. Znajduje nową wiadomość kolejki w kolejce o nazwie *kolejki* i wywołuje funkcję.

   Ze względu na [kolejkowania sondowania wykładniczego wycofywania,](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm)może upłynąć tak długo, jak 2 minuty dla środowiska wykonawczego, aby znaleźć komunikat i wywołać funkcję. Ten czas oczekiwania można skrócić, uruchamiając w [trybie rozwoju](webjobs-sdk-how-to.md#host-development-settings).

   Wyjście konsoli wygląda następująco:

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

1. Wróć do okna Kolejki i odśwież go. Komunikat zniknął, ponieważ został przetworzony przez funkcję działającą lokalnie. 

## <a name="add-application-insights-logging"></a>Dodawanie rejestrowania usługi Application Insights

Gdy projekt jest uruchamiany na platformie Azure, nie można monitorować wykonywania funkcji przez wyświetlanie danych wyjściowych konsoli. Zaleca się, że rozwiązaniem do monitorowania jest [usługa Application Insights.](../azure-monitor/app/app-insights-overview.md) Aby uzyskać więcej informacji, zobacz [Monitorowanie funkcji platformy Azure](../azure-functions/functions-monitoring.md).

W tej sekcji wykonaj następujące zadania, aby skonfigurować rejestrowanie usługi Application Insights przed wdrożeniem na platformie Azure:

* Upewnij się, że masz aplikację usługi App Service i wystąpienie usługi Application Insights do pracy z.
* Skonfiguruj aplikację usługi App Service, aby używała wystąpienia usługi Application Insights i konta magazynu utworzonego wcześniej.
* Skonfiguruj projekt do rejestrowania w usłudze Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Tworzenie aplikacji usługi App Service i wystąpienia usługi Application Insights

1. Jeśli nie masz jeszcze aplikacji usługi App Service, której możesz użyć, [utwórz ją](app-service-web-get-started-dotnet-framework.md). Podczas tworzenia aplikacji można również utworzyć połączony zasób usługi Application Insights. Po wykonaniu tej `APPINSIGHTS_INSTRUMENTATIONKEY` tej pracy jest ustawiona dla Ciebie w aplikacji.

1. Jeśli nie masz jeszcze zasobu usługi Application Insights, którego można użyć, [utwórz jeden z nich](../azure-monitor/app/create-new-resource.md ). Ustaw **typ aplikacji** na **Ogólne**i pomiń sekcje, które następują **po kopiowaniu klawisza instrumentacji**.

1. Jeśli masz już zasób usługi Application Insights, którego chcesz użyć, [skopiuj klucz instrumentacji](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji 

1. W **Eksploratorze serwera** w programie Visual Studio rozwiń węzeł **usługi app service** w obszarze **Azure**.

1. Rozwiń grupę zasobów, w których znajduje się aplikacja usługi App Service, a następnie kliknij prawym przyciskiem myszy aplikację usługi App Service.

1. Wybierz **pozycję Ustawienia widoku**.

1. W polu **Parametry połączenia** dodaj następujący wpis.

   |Nazwa  |Ciąg połączenia  |Typ bazy danych|
   |---------|---------|------|
   |AzureWebJobsStorage | {parametry połączenia Magazynu skopiowane wcześniej}|Niestandardowy|

1. Jeśli pole **Ustawienia aplikacji** nie ma klucza instrumentacji usługi Application Insights, dodaj ten, który został skopiowany wcześniej. (Klucz instrumentacji może już tam być, w zależności od sposobu utworzenia aplikacji Usługi app service).

   |Nazwa  |Wartość  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {klucz instrumentacji} |

1. Zastąp *{klucz instrumentacji}* kluczem instrumentacji z używanego zasobu usługi Application Insights.

1. Wybierz pozycję **Zapisz**.

1. Dodaj połączenie usługi Application Insights do projektu, aby można było uruchomić je lokalnie. W pliku *appsettings.json* dodaj `APPINSIGHTS_INSTRUMENTATIONKEY` pole, jak w poniższym przykładzie:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Zastąp *{klucz instrumentacji}* kluczem instrumentacji z używanego zasobu usługi Application Insights.

1. Zapisz zmiany.

### <a name="add-application-insights-logging-provider"></a>Dodawanie dostawcy rejestrowania usługi Application Insights

Aby skorzystać z rejestrowania [usługi Application Insights,](../azure-monitor/app/app-insights-overview.md) zaktualizuj kod rejestrowania, aby wykonać następujące czynności:

* Dodaj dostawcę rejestrowania usługi Application Insights z [domyślnym filtrowaniem](webjobs-sdk-how-to.md#log-filtering). Podczas uruchamiania lokalnie wszystkie informacje i dzienniki wyższego poziomu są zapisywane w konsoli i usługi Application Insights.
* Umieść [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) obiektu `using` w bloku, aby upewnić się, że dane wyjściowe dziennika jest opróżniany po zamknięciu hosta.

1. Zainstaluj najnowszą stabilną wersję [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)3.x .

   Oto polecenie **Konsola Menedżera pakietów:**

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    W tym poleceniu zastąp `<3_X_VERSION>` obsługiwaną wersją pakietu.

1. Otwórz *Program.cs* i zastąp `Main` kod w metodzie następującym kodem:

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

    Spowoduje to dodanie dostawcy usługi Application Insights do rejestrowania przy użyciu klucza dodanego wcześniej do ustawień aplikacji.

## <a name="test-application-insights-logging"></a>Rejestrowanie usługi Test Application Insights

W tej sekcji ponownie uruchom lokalnie, aby sprawdzić, czy dane rejestrowania są teraz przesyłane do usługi Application Insights, a także do konsoli.

1. Użyj **Eksploratora serwera** w programie Visual Studio, aby utworzyć komunikat kolejki, tak jak [wcześniej,](#test-locally)z wyjątkiem *wprowadzania hello app insights!* jako tekst wiadomości.

1. Uruchom projekt.

   WebJobs SDK przetwarza komunikat kolejki i zobaczysz dzienniki w oknie konsoli.

1. Zamknij okno konsoli.

1. Przejdź do [witryny Azure portal,](https://portal.azure.com/) aby wyświetlić zasób usługi Application Insights. Wyszukaj i wybierz **pozycję Application Insights**.

1. Wybierz wystąpienie usługi Application Insights.

1. Wybierz **pozycję Szukaj**.

   ![Wybierz pozycję Szukaj](./media/webjobs-sdk-get-started/select-search.png)

1. Jeśli nie widzisz *Hello App Insights!* komunikat, wybierz **odświeżanie** okresowo przez kilka minut. (Dzienniki nie pojawiają się natychmiast, ponieważ zajmuje trochę czasu dla klienta usługi Application Insights opróżnić dzienniki, które przetwarza.)

   ![Dzienniki w usłudze Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zamknij okno konsoli.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Wdrażanie na platformie Azure

Podczas wdrażania utworzysz wystąpienie usługi aplikacji, w którym mają być uruchamiane funkcje. Po opublikowaniu aplikacji konsoli .NET Core do usługi App Service na platformie Azure, automatycznie pobiera uruchamiane jako WebJob. Aby dowiedzieć się więcej na temat publikowania, zobacz [Tworzenie i wdrażanie aplikacji WebJobs przy użyciu programu Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Wyzwalanie funkcji na platformie Azure

1. Upewnij się, że nie korzystasz lokalnie (zamknij okno konsoli, jeśli jest nadal otwarte). W przeciwnym razie wystąpienie lokalne może być pierwszym, który przetworzy wszystkie utworzone wiadomości kolejki.

1. Na stronie **Kolejka** w programie Visual Studio dodaj wiadomość do kolejki, jak poprzednio.

1. Odśwież stronę **kolejki,** a nowa wiadomość zniknie, ponieważ została przetworzona przez funkcję uruchomioną na platformie Azure.

   > [!TIP]
   > Podczas testowania na platformie Azure należy użyć [trybu rozwoju,](webjobs-sdk-how-to.md#host-development-settings) aby upewnić się, że funkcja wyzwalacza kolejki jest wywoływana od razu i uniknąć opóźnień spowodowanych [sondowaniem kolejki wykładniczego wycofywania.](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm)

### <a name="view-logs-in-application-insights"></a>Wyświetlanie dzienników w usłudze Application Insights

1. Otwórz [witrynę Azure portal](https://portal.azure.com/)i przejdź do zasobu usługi Application Insights.

1. Wybierz **pozycję Szukaj**.

1. Jeśli nie widzisz *Hello Azure!* komunikat, wybierz **odświeżanie** okresowo przez kilka minut.

   Zobaczysz dzienniki z funkcji uruchomionej w usłudze WebJob, w tym *Hello Azure!* w poprzednim punkcie.

## <a name="add-an-input-binding"></a>Dodawanie powiązania wejściowego

Powiązania wejściowe upraszczają kod, który odczytuje dane. W tym przykładzie komunikat kolejki będzie nazwą obiektu blob i użyjesz nazwy obiektu blob, aby znaleźć i odczytać obiekt blob w usłudze Azure Storage.

1. W *Functions.cs*, zastąp `ProcessQueueMessage` metodę następującym kodem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   W tym `queueTrigger` kodzie jest [wyrażeniem wiązania](../azure-functions/functions-bindings-expressions-patterns.md), co oznacza, że jest rozpoznawany na inną wartość w czasie wykonywania.  W czasie wykonywania ma zawartość komunikatu kolejki.

1. Dodaj `using`:

   ```cs
   using System.IO;
   ```

1. Utwórz kontener obiektów blob na koncie magazynu.

   a. W **Eksploratorze serwera** w programie Visual Studio rozwiń węzeł dla konta magazynu, kliknij prawym przyciskiem myszy pozycję **Obiekty Blobs**, a następnie wybierz polecenie **Utwórz kontener obiektów blob**.

   b. W oknie dialogowym **Tworzenie kontenera obiektów blob** wprowadź jako nazwę *kontenera,* a następnie kliknij przycisk **OK**.

1. Przekaż plik *Program.cs* do kontenera obiektów blob. (Ten plik jest używany tutaj jako przykład; można przekazać dowolny plik tekstowy i utworzyć wiadomość kolejki o nazwie pliku).

   a. W **Eksploratorze serwera**kliknij dwukrotnie węzeł utworzonego kontenera.

   b. W oknie **Kontener** wybierz przycisk **Przekaż.**

   ![Przycisk przekazywania obiektów blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   d. Znajdź i wybierz *Program.cs*, a następnie wybierz **przycisk OK**.

1. Utwórz wiadomość kolejki w wcześniej utworzonej kolejce, z *Program.cs* jako tekst wiadomości.

   ![Program.cs komunikat kolejki](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Uruchom projekt lokalnie.

   Komunikat kolejki wyzwala funkcję, która następnie odczytuje obiekt blob i rejestruje jego długość. Wyjście konsoli wygląda następująco:

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

Powiązania wyjściowe upraszczają kod, który zapisuje dane. W tym przykładzie modyfikuje poprzedni, zapisując kopię obiektu blob zamiast rejestrowania jego rozmiar. Powiązania magazynu obiektów Blob są zawarte w pakiecie rozszerzenia usługi Azure Storage, który wcześniej instalowaliśmy.

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

1. Utwórz kolejną wiadomość kolejki z *Program.cs* jako tekst wiadomości.

1. Uruchom projekt lokalnie.

   Komunikat kolejki wyzwala funkcję, która następnie odczytuje obiekt blob, rejestruje jego długość i tworzy nowy obiekt blob. Dane wyjściowe konsoli są takie same, ale po wybraniu okna kontenera obiektów blob i **wybraniu**opcji Odśwież zostanie wyświetlony nowy obiekt blob o nazwie *copy-Program.cs.*

## <a name="republish-the-updates-to-azure"></a>Ponowne publikowanie aktualizacji na platformie Azure

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W oknie dialogowym **Publikowanie** upewnij się, że jest zaznaczony bieżący profil, a następnie wybierz pozycję **Publikuj**. Wyniki publikowania są wyszczególnione w oknie **Dane wyjściowe.**
 
1. Sprawdź funkcję na platformie Azure, ponownie przekazując plik do kontenera obiektów blob i dodając wiadomość do kolejki, która jest nazwą przekazanego pliku. Zostanie wyświetlony komunikat usunięty z kolejki i kopię pliku utworzonego w kontenerze obiektów blob. 

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak utworzyć, uruchomić i wdrożyć projekt SDK 3.x WebJobs.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o sdku WebJobs](webjobs-sdk-how-to.md)
