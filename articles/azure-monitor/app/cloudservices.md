---
title: Application Insights dla usług Azure Cloud Services | Microsoft Docs
description: Skutecznie monitoruj role sieci Web i procesu roboczego za pomocą usługi Application Insights
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 09/05/2018
ms.openlocfilehash: 264d6d4b0b397a29b5dc1db4bb299297c1e30584
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419239"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights dla usług Azure Cloud Services
[Application Insights][start] mogą monitorować [aplikacje usługi w chmurze platformy Azure](https://azure.microsoft.com/services/cloud-services/) pod kątem dostępności, wydajności, błędów i użycia przez połączenie danych z zestawów sdk Application Insights z danymi [Diagnostyka Azure](../agents/diagnostics-extension-overview.md) z usług w chmurze. Dzięki uzyskiwanym opiniom dotyczącym wydajności i skuteczności aplikacji możesz dokonać opartych na informacjach wyborów dotyczących kierunku projektu w każdym cyklu życia.

![Pulpit nawigacyjny przeglądu](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem należy:

* Subskrypcja [platformy Azure](https://azure.com) . Zaloguj się przy użyciu konto Microsoft dla systemu Windows, usługi Xbox Live lub innych usług firmy Microsoft w chmurze. 
* Microsoft Azure Tools 2,9 lub nowszy.
* Developer Analytics Tools 7,10 lub nowszy.

## <a name="get-started-quickly"></a>Szybkie rozpoczynanie pracy
Najszybszym i najłatwiejszym sposobem monitorowania usługi w chmurze za pomocą usługi Application Insights jest wybranie tej opcji podczas publikowania usługi na platformie Azure.

![Przykładowa strona ustawień diagnostyki](./media/cloudservices/azure-cloud-application-insights.png)

Ta opcja służy do przyrządowania aplikacji w czasie wykonywania, która zapewnia wszystkie dane telemetryczne potrzebne do monitorowania żądań, wyjątków i zależności w roli sieci Web. Monitoruje również liczniki wydajności z ról procesów roboczych. Wszystkie ślady diagnostyczne wygenerowane przez aplikację są również wysyłane do Application Insights.

Jeśli ta opcja jest wymagana, wszystko jest gotowe. 

W następnych krokach są [wyświetlane metryki z aplikacji](../essentials/metrics-charts.md), [wykonywanie zapytań dotyczących danych za pomocą analizy](../logs/log-query-overview.md). 

Aby monitorować wydajność w przeglądarce, możesz również skonfigurować [testy dostępności](./monitor-web-app-availability.md) i [dodać kod do stron sieci Web](./javascript.md).

W następnych sekcjach omówiono następujące opcje dodatkowe:

* Wysyłanie danych z różnych składników i kompilowanie konfiguracji w celu oddzielenia zasobów.
* Dodawanie niestandardowej telemetrii z aplikacji.

## <a name="sample-app-instrumented-with-application-insights"></a>Przykładowa aplikacja z Application Insights
W tej [przykładowej aplikacji](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)Application Insights zostanie dodany do usługi w chmurze z dwoma rolami procesu roboczego hostowanymi na platformie Azure. 

W następnej sekcji dowiesz się, jak dostosować własny projekt usługi w chmurze w taki sam sposób.

## <a name="plan-resources-and-resource-groups"></a>Planowanie zasobów i grup zasobów
Dane telemetryczne z aplikacji są przechowywane, analizowane i wyświetlane w zasobie platformy Azure typu Application Insights. 

Każdy zasób należy do grupy zasobów. Grupy zasobów służą do zarządzania kosztami, przyznawania dostępu członkom zespołu i wdrażania aktualizacji w ramach jednej skoordynowanej transakcji. Można na przykład [napisać skrypt służący do wdrażania usługi w](../../azure-resource-manager/templates/deploy-powershell.md) chmurze platformy Azure i jej Application Insights wszystkich zasobów monitorowania w ramach jednej operacji.

### <a name="resources-for-components"></a>Zasoby dla składników

Zalecamy [dodanie właściwości wymiaru do każdego elementu telemetrii](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) , który identyfikuje jego rolę źródłową. W tym podejściu wykresy metryk, takie jak wyjątki, zwykle pokazują agregację liczników z różnych ról, ale w razie potrzeby można podzielić wykres na segmenty. Możesz również filtrować wyszukiwania według tego samego wymiaru. Alternatywą jest łatwiejsze wyświetlanie wszystkiego w tym samym czasie, ale może to również prowadzić do pomyłek między rolami.

Telemetria przeglądarki zwykle jest zawarta w tym samym zasobie, co jej rola sieci Web po stronie serwera.

Umieść Application Insights zasoby dla różnych składników w jednej grupie zasobów. Takie podejście ułatwia zarządzanie nimi jednocześnie. 

### <a name="separate-development-test-and-production"></a>Oddzielanie programowania, testowania i produkcji
Jeśli opracowujesz zdarzenia niestandardowe dla następnej funkcji, gdy poprzednia wersja działa, wyślij telemetrię tworzenia do oddzielnych zasobów usługi Application Insights. W przeciwnym razie może być trudno znaleźć dane telemetryczne testu między całym ruchem z działającej witryny.

Aby uniknąć tej sytuacji, należy utworzyć osobne zasoby dla każdej konfiguracji kompilacji lub "stempel" (Programowanie, testowanie, produkcja itd.) w systemie. Umieść zasoby dla każdej konfiguracji kompilacji w oddzielnej grupie zasobów. 

Aby wysłać dane telemetryczne do odpowiednich zasobów, można skonfigurować zestaw Application Insights SDK w taki sposób, aby pobierał inny klucz instrumentacji, w zależności od konfiguracji kompilacji. 

Dowiedz się, jak [dynamicznie ustawiać klucz Instrumentacji](./separate-resources.md#dynamic-ikey) dla różnych etapów. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Tworzenie zasobu usługi Application Insights dla każdej roli

Jeśli postanowisz utworzyć oddzielny zasób dla każdej roli i być może jest to osobny zestaw dla każdej konfiguracji kompilacji, najłatwiej ją utworzyć w portalu Application Insights. W przypadku tworzenia zasobów o dużej ilości można [zautomatyzować proces](./powershell.md).

1. W [Azure Portal][portal]wybierz pozycję **nowe**  >  **usługi dla deweloperów**  >  **Application Insights**.  

    ![Okienko Application Insights](./media/cloudservices/01-new.png)

1. Z listy rozwijanej **Typ aplikacji** wybierz pozycję **aplikacja sieci Web ASP.NET**.

Każdy zasób jest identyfikowany przez klucz Instrumentacji. Ten klucz może być potrzebny później, jeśli chcesz ręcznie skonfigurować lub zweryfikować konfigurację zestawu SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Konfigurowanie diagnostyki platformy Azure dla każdej roli
Ustaw tę opcję, aby monitorować aplikację za pomocą usługi Application Insights. W przypadku ról sieci Web Ta opcja zapewnia monitorowanie wydajności, alerty, diagnostykę i analizę użycia. W przypadku innych ról można wyszukiwać i monitorować Diagnostyka Azure, takie jak ponowne uruchamianie, liczniki wydajności i wywołania do System. Diagnostics. Trace. 

1. W programie Visual Studio Eksplorator rozwiązań w obszarze **\<YourCloudService>**  >  **role** Otwórz właściwości każdej roli.

1. W obszarze **Konfiguracja** zaznacz pole wyboru **Wyślij dane diagnostyczne do Application Insights** , a następnie wybierz utworzony wcześniej zasób Application Insights.

Jeśli zamierzasz używać osobnego zasobu usługi Application Insights dla każdej konfiguracji kompilacji, najpierw wybierz konfigurację.

![Konfigurowanie usługi Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

Ma to wpływ na wstawianie kluczy Instrumentacji Application Insights do plików o nazwie *ServiceConfiguration. \* cscfg*. Oto [przykładowy kod](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Jeśli chcesz zmienić poziom informacji diagnostycznych wysyłanych do Application Insights, możesz to zrobić, [edytując pliki *. cscfg* bezpośrednio](../agents/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Zainstaluj zestaw SDK w każdym projekcie
Za pomocą tej opcji można dodać do dowolnej roli niestandardowe telemetrię biznesową. Opcja zapewnia bliższą analizę sposobu używania i wykonywania aplikacji.

W programie Visual Studio skonfiguruj zestaw SDK usługi Application Insights dla każdego projektu aplikacji w chmurze.

1. Aby skonfigurować **role sieci Web**, kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Konfiguruj Application Insights** lub **Dodaj > Application Insights dane telemetryczne**.

1. Aby skonfigurować **role procesów roboczych**: 

    a. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

    b. Dodaj pozycję [Usługa Application Insights dla serwerów systemu Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

1. Aby skonfigurować zestaw SDK do wysyłania danych do zasobu Application Insights:

    a. W odpowiedniej funkcji uruchamiania Ustaw klucz Instrumentacji na podstawie ustawienia konfiguracji w pliku *cscfg* :
 
    ```csharp
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
        var telemetryClient = new TelemetryClient(configuration);
    ```
   
    b. Powtórz "krok a" dla każdej roli w aplikacji. Zobacz przykłady:
   
    * [Rola sieci Web](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Rola procesu roboczego](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Dla stron sieci Web](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Ustaw plik *ApplicationInsights.config* , który ma być zawsze kopiowany do katalogu wyjściowego.

   W pliku *. config* zostanie wyświetlony komunikat z prośbą o umieszczenie w niej klucza Instrumentacji. Jednak w przypadku aplikacji w chmurze lepiej jest ustawić ją z pliku *. cscfg* . Takie podejście zapewnia, że rola jest prawidłowo identyfikowana w portalu.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Skonfiguruj monitor stanu, aby zbierać pełne zapytania SQL (opcjonalnie)

Ten krok jest wymagany tylko wtedy, gdy chcesz przechwytywać pełne zapytania SQL na .NET Framework. 

1. W oknie `\*.csdef` Dodawanie [zadania uruchamiania](../../cloud-services/cloud-services-startup-tasks.md) dla każdej roli podobnej do 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Pobierz [InstallAgent.bat](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) i [InstallAgent.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), umieść je w `AppInsightsAgent` folderze w każdym projekcie roli. Upewnij się, że skopiujesz je do katalogu wyjściowego za pomocą właściwości pliku programu Visual Studio lub skryptów kompilacji.

3. Na wszystkich rolach procesów roboczych Dodaj zmienne środowiskowe: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Uruchom i opublikuj aplikację

1. Uruchom aplikację i zaloguj się do platformy Azure. 

1. Otwórz utworzone zasoby Application Insights.

   Poszczególne punkty danych są wyświetlane w obszarze [wyszukiwania][diagnostic], a zagregowane dane są wyświetlane w [Eksploratorze metryk](../essentials/metrics-charts.md).

1. Dodaj więcej danych telemetrycznych (Zobacz następne sekcje), a następnie Opublikuj swoją aplikację, aby uzyskać informacje na temat diagnostyki i użycia na żywo. 

Jeśli nie ma żadnych danych, wykonaj następujące czynności:

1. Aby wyświetlić poszczególne zdarzenia, Otwórz kafelek [wyszukiwania][diagnostic] .
1. W aplikacji otwórz różne strony, aby wygenerowały pewne dane telemetryczne.
1. Zaczekaj kilka sekund, a następnie kliknij przycisk **Odśwież**.  

Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów][qna].

## <a name="view-azure-diagnostics-events"></a>Wyświetlanie zdarzeń Diagnostyka Azure
[Diagnostyka Azure](../agents/diagnostics-extension-overview.md) informacje można znaleźć w Application Insights w następujących lokalizacjach:

* Liczniki wydajności są wyświetlane jako metryki niestandardowe. 
* Dzienniki zdarzeń systemu Windows są wyświetlane jako ślady i zdarzenia niestandardowe.
* Dzienniki aplikacji, dzienniki śledzenia zdarzeń systemu Windows i wszelkie dzienniki infrastruktury diagnostycznej są wyświetlane jako ślady.

Aby wyświetlić liczniki wydajności i liczbę zdarzeń, Otwórz [Eksplorator metryk](../essentials/metrics-charts.md) i Dodaj następujący wykres:

![Diagnostyka Azure dane](./media/cloudservices/23-wad.png)

Aby wyszukiwać w różnych dziennikach śledzenia, które są wysyłane przez Diagnostyka Azure, użyj [wyszukiwania](./diagnostic-search.md) lub [zapytania analizy](../logs/log-analytics-tutorial.md). Załóżmy na przykład, że masz nieobsłużony wyjątek, który spowodował awarię i odtwarzanie roli. Te informacje będą wyświetlane w kanale aplikacji dziennika zdarzeń systemu Windows. Możesz użyć wyszukiwania, aby wyświetlić błąd dziennika zdarzeń systemu Windows i uzyskać pełny ślad stosu dla wyjątku. Dzięki temu można znaleźć główną przyczynę problemu.

![Diagnostyka Azure wyszukiwanie](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Dalsze funkcje telemetrii
W następnych sekcjach omówiono, jak uzyskać dodatkową telemetrię z różnych aspektów aplikacji.

## <a name="track-requests-from-worker-roles"></a>Śledzenie żądań z ról procesów roboczych
W rolach sieci Web moduł żądań automatycznie zbiera dane o żądaniach HTTP. Aby zapoznać się z przykładami, jak można zastąpić domyślne zachowanie kolekcji, zobacz [przykład MVCWebRole](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Możesz przechwycić wydajności wywołań do ról procesów roboczych, śledząc je w taki sam sposób, jak żądania HTTP. W usłudze Application Insights żądanie typu telemetrii mierzy jednostkę po stronie nazwanego serwera, które może być synchronizowane i może niezależnie zakończyć się sukcesem lub niepowodzeniem. Mimo że żądania HTTP są przechwytywane automatycznie przez zestaw SDK, można wstawić własny kod do śledzenia żądań do ról procesów roboczych.

Zobacz dwie przykładowe role procesów roboczych przystosowane do żądań raportów: 
* [WorkerRoleA](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Wyjątki
Aby uzyskać informacje dotyczące sposobu zbierania nieobsłużonych wyjątków z różnych typów aplikacji sieci Web, zobacz [Monitorowanie wyjątków w Application Insights](./asp-net-exceptions.md).

Przykładowa rola sieci Web ma kontrolery MVC5 i Web API 2. Nieobsługiwane wyjątki od tych dwóch są przechwytywane za pomocą następujących programów obsługi:

* [AiHandleErrorAttribute](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) konfigurację dla kontrolerów MVC5 [, jak pokazano w tym przykładzie](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) konfigurację dla kontrolerów Web API 2 [, jak pokazano w tym przykładzie](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

W przypadku ról procesów roboczych można śledzić wyjątki na dwa sposoby:

* Użyj metody Trackexception (np.).
* Jeśli dodano pakiet NuGet odbiornika śledzenia Application Insights, można użyć programu System. Diagnostics. Trace do rejestrowania wyjątków, [jak pokazano w tym przykładzie](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Liczniki wydajności
Domyślnie są zbierane następujące liczniki:

* \Process (?? APP_WIN32_PROC??) \% Czas procesora
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Następujące liczniki są również zbierane dla ról sieci Web:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Możesz określić dodatkowe niestandardowe lub inne liczniki wydajności systemu Windows, edytując *ApplicationInsights.config* [jak pokazano w tym przykładzie](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Liczniki wydajności](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Skorelowane dane telemetryczne dla ról procesów roboczych
Aby zapoznać się z rozbudowanym interfejsem diagnostycznym, możesz zobaczyć, co doprowadziło do niepowodzenia lub dużego opóźnienia żądania. W przypadku ról sieci Web zestaw SDK automatycznie konfiguruje korelację między powiązanymi danymi telemetrycznymi. 

Aby uzyskać ten widok dla ról procesów roboczych, można użyć niestandardowego inicjatora telemetrii do ustawienia wspólnego atrybutu kontekstu Operation.Id dla całej telemetrii. Dzięki temu można szybko zobaczyć, czy problem opóźnienia lub błędu został spowodowany przez zależność, czy kod. 

Oto kroki tej procedury:

* Ustaw identyfikator korelacji na CallContext [, jak pokazano w tym przykładzie](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). W takim przypadku używany jest identyfikator żądania jako identyfikator korelacji.
* Dodaj niestandardową implementację TelemetryInitializer, aby ustawić Operation.Id na identyfikator korelacji, który został wcześniej ustawiony. Aby zapoznać się z przykładem, zobacz [ItemCorrelationTelemetryInitializer](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Dodaj inicjator niestandardowej telemetrii. Można to zrobić w pliku *ApplicationInsights.config* lub w kodzie [, jak pokazano w tym przykładzie](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetria klienta
Aby uzyskać dane telemetryczne oparte na przeglądarce, takie jak liczba wyświetleń stron, czasy ładowania stron lub wyjątki skryptów, a także aby napisać niestandardową telemetrię w skryptach stron, zobacz [Dodawanie zestawu SDK języka JavaScript do stron sieci Web][client].

## <a name="availability-tests"></a>Testy dostępności
Aby upewnić się, że aplikacja pozostaje aktywna i będzie odpowiadać, [Skonfiguruj testy sieci Web][availability].

## <a name="display-everything-together"></a>Wyświetlanie wszystkich elementów razem
Aby zapoznać się z ogólnym obrazem systemu, można wyświetlić wykresy monitorowania kluczy razem na jednym [pulpicie nawigacyjnym](./overview-dashboard.md). Na przykład możesz przypiąć liczbę żądań i awarii poszczególnych ról. 

Jeśli system używa innych usług platformy Azure, takich jak Stream Analytics, należy również uwzględnić ich wykresy monitorowania. 

Jeśli masz klienta aplikacji mobilnej, użyj pakietu [App Center](../app/mobile-center-quickstart.md). Utwórz zapytania w pozycji [Analiza](../logs/log-query-overview.md), aby wyświetlić liczby zdarzeń i przypiąć je do pulpitu nawigacyjnego.

## <a name="example"></a>Przykład
[Przykład](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitoruje usługę mającą rolę sieci Web i dwie role procesów roboczych.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Wyjątek "nie znaleziono metody" w działaniu w usługach Azure Cloud Services
Czy to kompilacja dla .NET 4.6? Platforma .NET 4,6 nie jest automatycznie obsługiwana w rolach usług Azure Cloud Services. [Zainstaluj program .net 4,6 na każdej roli](../../cloud-services/cloud-services-dotnet-install-dotnet.md) przed uruchomieniem aplikacji.

## <a name="video"></a>Wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie wysyłania usługi Diagnostyka Azure do usługi Application Insights](../agents/diagnostics-extension-to-application-insights.md)
* [Automatycznie twórz zasoby Application Insights](./powershell.md)
* [Automatyzacja Diagnostyka Azure](./powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ./api-custom-events-metrics.md
[availability]: ./monitor-web-app-availability.md
[azure]: ./app-insights-overview.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[netlogs]: ./asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md