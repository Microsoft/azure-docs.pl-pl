---
title: Usługa Application Insights dla usług w chmurze platformy Azure | Dokumenty firmy Microsoft
description: Skutecznie monitoruj role sieci Web i procesu roboczego za pomocą usługi Application Insights
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: 17813d17a1c40caac5587e37e279be6376992b90
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537597"
---
# <a name="application-insights-for-azure-cloud-services"></a>Usługa Application Insights dla usług w chmurze platformy Azure
[Usługa Application Insights][start] może monitorować [aplikacje usługi w chmurze platformy Azure](https://azure.microsoft.com/services/cloud-services/) pod kątem dostępności, wydajności, błędów i użycia, łącząc dane z zestawów SDK usługi Application Insights z danymi usługi Azure [Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) z usług w chmurze. Dzięki uzyskiwanym opiniom dotyczącym wydajności i skuteczności aplikacji możesz dokonać opartych na informacjach wyborów dotyczących kierunku projektu w każdym cyklu życia.

![Pulpit nawigacyjny przeglądu](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Wymagania wstępne
Zanim zaczniesz, potrzebujesz:

* Subskrypcja [platformy Azure.](https://azure.com) Zaloguj się za pomocą swojego konta Microsoft dla systemu Windows, Xbox Live lub innych usług w chmurze firmy Microsoft. 
* Narzędzia platformy Microsoft Azure 2.9 lub nowsze.
* Narzędzia do analizy dla deweloperów 7.10 lub nowsze.

## <a name="get-started-quickly"></a>Szybkie rozpoczęcie pracy
Najszybszym i najłatwiejszym sposobem monitorowania usługi w chmurze za pomocą usługi Application Insights jest wybranie tej opcji podczas publikowania usługi na platformie Azure.

![Przykładowa strona Ustawienia diagnostyki](./media/cloudservices/azure-cloud-application-insights.png)

Ta opcja instrumentów aplikacji w czasie wykonywania, podając wszystkie dane telemetryczne, które są potrzebne do monitorowania żądań, wyjątków i zależności w roli sieci web. Monitoruje również liczniki wydajności z ról procesu roboczego. Wszelkie ślady diagnostyki generowane przez aplikację są również wysyłane do usługi Application Insights.

Jeśli ta opcja jest wszystko, czego potrzebujesz, gotowe. 

Kolejne kroki to [wyświetlanie danych z aplikacji](../../azure-monitor/platform/metrics-charts.md), wykonywanie [zapytań o dane za pomocą Analytics](../../azure-monitor/app/analytics.md). 

Aby monitorować wydajność przeglądarki, można również skonfigurować [testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) i dodać kod do [stron internetowych](../../azure-monitor/app/javascript.md).

W następnych sekcjach omówiono następujące dodatkowe opcje:

* Wysyłaj dane z różnych składników i twórz konfiguracje w celu oddzielenia zasobów.
* Dodawanie niestandardowej telemetrii z aplikacji.

## <a name="sample-app-instrumented-with-application-insights"></a>Przykładowa aplikacja oprzyrządowana za pomocą usługi Application Insights
W tej [przykładowej aplikacji](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)usługa Application Insights jest dodawana do usługi w chmurze z dwiema rolami procesu roboczego hostowanymi na platformie Azure. 

W następnej sekcji dowiesz się, jak dostosować własny projekt usługi w chmurze w taki sam sposób.

## <a name="plan-resources-and-resource-groups"></a>Planowanie zasobów i grup zasobów
Dane telemetryczne z aplikacji są przechowywane, analizowane i wyświetlane w zasobie platformy Azure typu Application Insights. 

Każdy zasób należy do grupy zasobów. Grupy zasobów są używane do zarządzania kosztami, udzielania dostępu członkom zespołu i wdrażania aktualizacji w jednej skoordynowanej transakcji. Na przykład można [napisać skrypt do wdrożenia](../../azure-resource-manager/templates/deploy-powershell.md) usługi w chmurze platformy Azure i jej zasobów monitorowania usługi Application Insights w jednej operacji.

### <a name="resources-for-components"></a>Zasoby dla składników
Zaleca się utworzenie oddzielnego zasobu dla każdego składnika aplikacji. Oznacza to, że można utworzyć zasób dla każdej roli sieci web i roli procesu roboczego. Każdy składnik można analizować oddzielnie, ale tworzy się [pulpit nawigacyjny,](../../azure-monitor/app/overview-dashboard.md) który łączy wykresy kluczowe ze wszystkich składników, dzięki czemu można je porównywać i monitorować razem w jednym widoku. 

Alternatywne podejście jest wysłanie danych telemetrycznych z więcej niż jednej roli do tego samego zasobu, ale [dodać właściwość wymiaru do każdego elementu telemetrii,](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) który identyfikuje jego rolę źródłową. W tym podejściu wykresy metryki, takie jak wyjątki, zwykle pokazują agregację zliczeń z różnych ról, ale w razie potrzeby można segmentować wykres według identyfikatora roli. Można również filtrować wyszukiwania według tego samego wymiaru. Ta alternatywa sprawia, że nieco łatwiej jest wyświetlić wszystko w tym samym czasie, ale może również prowadzić do pewnego zamieszania między rolami.

Telemetria przeglądarki zwykle jest zawarta w tym samym zasobie, co jej rola sieci Web po stronie serwera.

Umieść zasoby usługi Application Insights dla różnych składników w jednej grupie zasobów. Takie podejście ułatwia zarządzanie nimi razem. 

### <a name="separate-development-test-and-production"></a>Oddzielanie programowania, testowania i produkcji
Jeśli opracowujesz zdarzenia niestandardowe dla następnej funkcji, gdy poprzednia wersja działa, wyślij telemetrię tworzenia do oddzielnych zasobów usługi Application Insights. W przeciwnym razie może być trudno znaleźć dane telemetryczne testu wśród całego ruchu z witryny na żywo.

Aby uniknąć tej sytuacji, należy utworzyć oddzielne zasoby dla każdej konfiguracji kompilacji lub "stempel" (rozwój, test, produkcja i tak dalej) systemu. Umieść zasoby dla każdej konfiguracji kompilacji w oddzielnej grupie zasobów. 

Aby wysłać dane telemetryczne do odpowiednich zasobów, można skonfigurować zestaw SDK usługi Application Insights, tak aby odebrał inny klucz instrumentacji, w zależności od konfiguracji kompilacji. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Tworzenie zasobu usługi Application Insights dla każdej roli

Jeśli zdecydujesz się utworzyć oddzielny zasób dla każdej roli i być może oddzielny zestaw dla każdej konfiguracji kompilacji, najłatwiej jest utworzyć je wszystkie w portalu usługi Application Insights. Jeśli dużo utworzysz zasoby, możesz [zautomatyzować proces](../../azure-monitor/app/powershell.md).

1. W [witrynie Azure portal][portal]wybierz pozycję **New** > **Developer Services** > **Application Insights**.  

    ![Okienko Usługi aplikacji](./media/cloudservices/01-new.png)

1. Z listy rozwijanej **Typ aplikacji** wybierz pozycję ASP.NET aplikacji **sieci Web**.

Każdy zasób jest identyfikowany za pomocą klucza instrumentacji. Ten klucz może być potrzebny później, jeśli chcesz ręcznie skonfigurować lub zweryfikować konfigurację sdk.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Konfigurowanie diagnostyki platformy Azure dla każdej roli
Ustaw tę opcję, aby monitorować aplikację za pomocą usługi Application Insights. W przypadku ról sieci web ta opcja zapewnia monitorowanie wydajności, alerty, diagnostykę i analizę użycia. W przypadku innych ról można wyszukiwać i monitorować diagnostykę platformy Azure, takie jak ponowne uruchamianie, liczniki wydajności i wywołania system.Diagnostics.Trace. 

1. W Eksploratorze rozwiązań programu Visual Studio w obszarze **Roles**  >  ** \<Role>Usługi YourCloud **,otwórz właściwości każdej roli.

1. W **obszarze Konfiguracja**zaznacz pole wyboru **Wyślij dane diagnostyczne do usługi Application Insights,** a następnie wybierz utworzony wcześniej zasób usługi Application Insights.

Jeśli zamierzasz używać osobnego zasobu usługi Application Insights dla każdej konfiguracji kompilacji, najpierw wybierz konfigurację.

![Konfigurowanie statystyk aplikacji](./media/cloudservices/configure-azure-diagnostics.png)

Ma to wpływ na wstawianie kluczy instrumentacji usługi Application Insights do plików o nazwie *\*ServiceConfiguration. . cscfg*. Oto [przykładowy kod](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Jeśli chcesz zmienić poziom informacji diagnostycznych wysyłanych do usługi Application Insights, możesz to zrobić, [edytując pliki *.cscfg* bezpośrednio](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Zainstaluj zestaw SDK w każdym projekcie
Za pomocą tej opcji można dodać niestandardowe dane telemetryczne biznesowe do dowolnej roli. Opcja ta zapewnia bliższą analizę sposobu, w jaki aplikacja jest używana i działa.

W programie Visual Studio skonfiguruj zestaw SDK usługi Application Insights dla każdego projektu aplikacji w chmurze.

1. Aby skonfigurować **role sieci Web,** kliknij projekt prawym przyciskiem myszy, a następnie wybierz polecenie **Konfiguruj wgląd w aplikacje** lub **Dodaj > dane telemetryczne usługi Application Insights**.

1. Aby skonfigurować **role procesu roboczego:** 

    a. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

    b. Dodaj pozycję [Usługa Application Insights dla serwerów systemu Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Wyszukaj „Application Insights”](./media/cloudservices/04-ai-nuget.png)

1. Aby skonfigurować zestaw SDK do wysyłania danych do zasobu usługi Application Insights:

    a. W odpowiedniej funkcji uruchamiania ustaw klucz instrumentacji z ustawienia konfiguracji w pliku *cscfg:*
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Powtórz "krok a" dla każdej roli w aplikacji. Zobacz przykłady:
   
    * [Rola sieci Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Rola procesu roboczego](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Dla stron internetowych](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Ustaw plik *ApplicationInsights.config,* który ma być kopiowany zawsze do katalogu wyjściowego.

   Komunikat w pliku *.config* z prośbą o umieszczenie tam klucza instrumentacji. Jednak w przypadku aplikacji w chmurze lepiej jest ustawić go z pliku *cscfg.* Takie podejście zapewnia, że rola jest poprawnie identyfikowana w portalu.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Konfigurowanie Monitora stanu w celu zbierania pełnych zapytań SQL (opcjonalnie)

Ten krok jest potrzebny tylko wtedy, gdy chcesz przechwycić pełne zapytania SQL w programie .NET Framework. 

1. W `\*.csdef` pliku Dodaj [zadanie uruchamiania](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks) dla każdej roli podobnej do 

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
    
2. Pobierz za darmo [InstallAgent.bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) i [InstallAgent.ps1](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), umieść je w folderze `AppInsightsAgent` dla każdego projektu roli. Upewnij się, aby skopiować je do katalogu wyjściowego za pośrednictwem właściwości pliku programu Visual Studio lub skryptów kompilacji.

3. We wszystkich rolach procesu roboczego dodaj zmienne środowiskowe: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Uruchom i opublikuj aplikację

1. Uruchom aplikację i zaloguj się na platformie Azure. 

1. Otwórz utworzone zasoby usługi Application Insights.

   Poszczególne punkty danych są wyświetlane w [wyszukiwarce,][diagnostic]a zagregowane dane są wyświetlane w [Eksploratorze metryk.](../../azure-monitor/platform/metrics-charts.md)

1. Dodaj więcej danych telemetrycznych (zobacz następne sekcje), a następnie opublikuj aplikację, aby uzyskać informacje na temat diagnostyki i użycia na żywo. 

Jeśli nie ma żadnych danych, wykonaj następujące czynności:

1. Aby wyświetlić poszczególne zdarzenia, otwórz kafelek [Wyszukiwanie.][diagnostic]
1. W aplikacji otwórz różne strony, aby wygenerować niektóre dane telemetryczne.
1. Odczekaj kilka sekund, a następnie kliknij przycisk **Odśwież**.  

Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów][qna].

## <a name="view-azure-diagnostics-events"></a>Wyświetlanie zdarzeń diagnostyki platformy Azure
Informacje dotyczące [diagnostyki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) można znaleźć w usłudze Application Insights w następujących lokalizacjach:

* Liczniki wydajności są wyświetlane jako metryki niestandardowe. 
* Dzienniki zdarzeń systemu Windows są wyświetlane jako ślady i zdarzenia niestandardowe.
* Dzienniki aplikacji, dzienniki śledzenia zdarzeń systemu Windows i wszelkie dzienniki infrastruktury diagnostycznej są wyświetlane jako ślady.

Aby wyświetlić liczniki wydajności i liczbę zdarzeń, otwórz [Eksploratora metryk](../../azure-monitor/platform/metrics-charts.md) i dodaj następujący wykres:

![Dane diagnostyki platformy Azure](./media/cloudservices/23-wad.png)

Aby przeszukiwać różne dzienniki śledzenia wysyłane przez usługę Azure Diagnostics, użyj [funkcji Wyszukiwania](../../azure-monitor/app/diagnostic-search.md) lub [zapytania Analytics](../../azure-monitor/log-query/get-started-portal.md). Na przykład załóżmy, że masz nieobsługiowany wyjątek, który spowodował rolę awarii i recyklingu. Te informacje będą wyświetlane w kanale aplikacji dziennika zdarzeń systemu Windows. Za pomocą wyszukiwania można wyświetlić błąd dziennika zdarzeń systemu Windows i uzyskać pełny ślad stosu dla wyjątku. W ten sposób pomaga znaleźć główną przyczynę problemu.

![Wyszukiwanie diagnostyki platformy Azure](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Dalsze funkcje telemetrii
W następnych sekcjach omówiono sposób uzyskania dodatkowych danych telemetrycznych z różnych aspektów aplikacji.

## <a name="track-requests-from-worker-roles"></a>Śledzenie żądań z ról pracowników
W rolach sieci Web moduł żądań automatycznie zbiera dane o żądaniach HTTP. Przykłady zastępowania domyślnego zachowania kolekcji można znaleźć w [przykładowym polu MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Możesz przechwycić wydajności wywołań do ról procesów roboczych, śledząc je w taki sam sposób, jak żądania HTTP. W usłudze Application Insights żądanie typu telemetrii mierzy jednostkę po stronie nazwanego serwera, które może być synchronizowane i może niezależnie zakończyć się sukcesem lub niepowodzeniem. Mimo że żądania HTTP są przechwytywane automatycznie przez SDK, można wstawić własny kod do śledzenia żądań do ról procesu roboczego.

Zobacz dwie przykładowe role procesu roboczego instrumentowane do zgłaszania żądań: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [RobotniczaRolaB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Wyjątki
Aby uzyskać informacje dotyczące sposobu zbierania nieobsługiwałem wyjątków z różnych typów aplikacji sieci web, zobacz [Monitorowanie wyjątków w usłudze Application Insights](../../azure-monitor/app/asp-net-exceptions.md).

Przykładowa rola sieci Web ma kontrolery MVC5 i Web API 2. Nieobsługiwane wyjątki od tych dwóch są przechwytywane za pomocą następujących programów obsługi:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) skonfigurowany dla kontrolerów MVC5, [jak pokazano w tym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) skonfigurowany dla kontrolerów interfejsu API sieci Web [2, jak pokazano w tym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

W przypadku ról procesu roboczego wyjątki można śledzić na dwa sposoby:

* Użyj TrackException(ex).
* Jeśli dodano pakiet NuGet odbiornika śledzenia usługi Application Insights, można użyć system.Diagnostics.Trace do rejestrowania [wyjątków, jak pokazano w tym przykładzie.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## <a name="performance-counters"></a>Liczniki wydajności
Domyślnie są zbierane następujące liczniki:

* \Proces(?? APP_WIN32_PROC?) \% Czas procesora
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Następujące liczniki są również zbierane dla ról sieci Web:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Można określić dodatkowe niestandardowe lub inne liczniki wydajności systemu Windows, edytując *plik ApplicationInsights.config,* [jak pokazano w tym przykładzie.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)

  ![Liczniki wydajności](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Skorelowane dane telemetryczne dla ról procesu roboczego
W przypadku zaawansowanego środowiska diagnostycznego można wyświetlić, co doprowadziło do żądania nieudanego lub wysokiego opóźnienia. W rolach sieci Web zestaw SDK automatycznie konfiguruje korelację między powiązanymi danemi telemetrycznymi. 

Aby osiągnąć ten widok dla ról procesu roboczego, można użyć inicjatora niestandardowych danych telemetrycznych, aby ustawić wspólny atrybut kontekstu Operation.Id dla wszystkich danych telemetrycznych. W ten sposób można wyświetlić na pierwszy rzut oka, czy problem opóźnienia lub awarii został spowodowany przez zależność lub kodu. 

Oto kroki tej procedury:

* Ustaw correlationId w CallContext, [jak pokazano w tym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). W takim przypadku używamy identyfikatora żądania jako identyfikatora korelacji.
* Dodaj niestandardową implementację telemetryinitializer, aby ustawić Operation.Id na correlationId, który został ustawiony wcześniej. Na przykład zobacz [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Dodaj inicjator niestandardowej telemetrii. Można to zrobić w *pliku ApplicationInsights.config* lub w kodzie, [jak pokazano w tym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetria klienta
Aby uzyskać dane telemetryczne oparte na przeglądarce, takie jak liczba wyświetleń strony, czasy ładowania strony lub wyjątki skryptów, oraz aby zapisać niestandardowe dane telemetryczne w skryptach stron, zobacz [Dodawanie zestawie SDK JavaScript do stron sieci Web][client].

## <a name="availability-tests"></a>Testy dostępności
Aby upewnić się, że aplikacja pozostaje na żywo i responsywna, [skonfiguruj testy internetowe][availability].

## <a name="display-everything-together"></a>Wyświetlanie wszystkich elementów razem
Aby uzyskać ogólny obraz systemu, można wyświetlić wykresy monitorowania kluczy razem na jednym [pulpicie nawigacyjnym](../../azure-monitor/app/overview-dashboard.md). Na przykład możesz przypiąć liczbę żądań i awarii poszczególnych ról. 

Jeśli system korzysta z innych usług platformy Azure, takich jak Usługa Stream Analytics, należy również uwzględnić ich wykresy monitorowania. 

Jeśli masz klienta aplikacji mobilnej, użyj pakietu [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Utwórz zapytania w pozycji [Analiza](../../azure-monitor/app/analytics.md), aby wyświetlić liczby zdarzeń i przypiąć je do pulpitu nawigacyjnego.

## <a name="example"></a>Przykład
[Przykład](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitoruje usługę mającą rolę sieci Web i dwie role procesów roboczych.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Wyjątek "nie znaleziono metody" podczas uruchamiania w usługach w chmurze platformy Azure
Czy to kompilacja dla .NET 4.6? .NET 4.6 nie jest automatycznie obsługiwana w rolach usług w chmurze platformy Azure. [Zainstaluj .NET 4.6 na każdej roli](../../cloud-services/cloud-services-dotnet-install-dotnet.md) przed uruchomieniem aplikacji.

## <a name="video"></a>Film wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie wysyłania usługi Diagnostyka Azure do usługi Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automatyczne tworzenie zasobów usługi Application Insights](../../azure-monitor/app/powershell.md)
* [Automatyzacja diagnostyki platformy Azure](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
