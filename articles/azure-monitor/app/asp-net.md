---
title: Konfigurowanie monitorowania dla ASP.NET za pomocą platformy Azure Application Insights | Microsoft Docs
description: Konfigurowanie narzędzi analitycznych dotyczących wydajności, dostępności i zachowania użytkowników dla witryny sieci Web ASP.NET hostowanej lokalnie lub na platformie Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f9cb5370dcddf783e533664c4c141779402103f0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028992"
---
# <a name="configure-application-insights-for-your-aspnet-website"></a>Konfigurowanie Application Insights witryny sieci Web ASP.NET

Niniejsza procedura umożliwia skonfigurowanie aplikacji internetowej w technologii ASP.NET do wysyłania danych telemetrii do usługi [Azure Application Insights](./app-insights-overview.md). Działa on w przypadku aplikacji ASP.NET, które są hostowane na własnych serwerach IIS lokalnie lub w chmurze. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby dodać usługę Application Insights do witryny internetowej:

- Zainstaluj najnowszą wersję programu [Visual Studio 2019 dla systemu Windows](https://www.visualstudio.com/downloads/) z następującymi obciążeniami:
    - ASP.NET i programowanie w sieci Web.
    - Tworzenie aplikacji na platformie Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Utwórz [zasób oparty na obszarze roboczym Application Insights](create-workspace-resource.md).

> [!IMPORTANT]
> Nowe regiony platformy Azure **wymagają** użycia parametrów połączenia zamiast kluczy Instrumentacji. [Parametry połączenia](./sdk-connection-string.md?tabs=net) identyfikują zasób, z którym chcesz skojarzyć dane telemetryczne. Umożliwia również modyfikowanie punktów końcowych, które będą używane przez zasób jako miejsce docelowe dla danych telemetrycznych. Należy skopiować parametry połączenia i dodać je do kodu aplikacji lub do zmiennej środowiskowej.


## <a name="create-a-basic-aspnet-web-app"></a>Tworzenie aplikacji sieci Web w warstwie Podstawowa ASP.NET

1. Uruchom program Visual Studio 2019.
2. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.
3. Wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework) C#**.
4. Wprowadź nazwę projektu > **Wybierz pozycję Utwórz**.
5. Wybierz pozycję **MVC**  >  **Create**. 

## <a name="add-application-insights-automatically"></a>Dodaj Application Insights automatycznie

Ta sekcja przeprowadzi Cię przez automatyczne dodawanie Application Insights do aplikacji sieci Web opartej na szablonach ASP.NET. Z poziomu projektu aplikacji sieci Web ASP.NET w programie Visual Studio:

1. Wybierz pozycję **Dodaj Telemetria usługi Application Insights**  >  **Application Insights SDK (lokalny)**, a  >  **następnie**  >  **Zakończ**  >  **zamykanie**.
2. Otwórz plik `ApplicationInsights.config`. 
3. Przed tagiem zamykającym `</ApplicationInsights>` Dodaj wiersz zawierający klucz Instrumentacji dla zasobu Application Insights.  Klucz Instrumentacji można znaleźć w okienku Przegląd nowo utworzonego zasobu Application Insights utworzonego w ramach wymagań wstępnych dotyczących tego artykułu.

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```
4. Wybierz kolejno pozycje **projekt**  >  **Zarządzaj pakietami NuGet**  >  **aktualizacje** > Aktualizuj każdy `Microsoft.ApplicationInsights` pakiet NuGet do najnowszej stabilnej wersji.   
5. Uruchom aplikację, wybierając pozycję **IIS Express**. Zostanie uruchomiona podstawowa aplikacja ASP.NET. Po przejściu do stron w ramach telemetrii lokacji zostanie wysłana do Application Insights.

## <a name="add-application-insights-manually"></a>Dodaj Application Insights ręcznie

Ta sekcja przeprowadzi Cię przez ręczne Dodawanie Application Insights do aplikacji sieci Web opartej na szablonach ASP.NET. W tej sekcji założono, że używasz aplikacji sieci Web opartej na szablonie aplikacji sieci Web Standard ASP.NET Framework MVC.

1. Dodaj następujące pakiety NuGet i ich zależności do projektu:

    - [`Microsoft.ApplicationInsights.WindowsServer`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer)
    - [`Microsoft.ApplicationInsights.Web`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
    - [`Microsoft.AspNet.TelemetryCorrelation`](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation)

2. W niektórych przypadkach `ApplicationInsights.config` plik zostanie automatycznie utworzony. Jeśli plik już istnieje, przejdź do kroku #4. Jeśli nie zostanie ona utworzona automatycznie, należy utworzyć ją samodzielnie. Na tym samym poziomie projektu, w którym znajduje się `Global.asax` plik, Utwórz nowy plik o nazwie `ApplicationInsights.config`

3. Skopiuj następującą konfigurację XML do nowo utworzonego pliku:

     ```xml
     <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
      <TelemetryInitializers>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureRoleEnvironmentTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.Web.WebTestTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SyntheticUserAgentTelemetryInitializer, Microsoft.AI.Web">
          <!-- Extended list of bots:
                search|spider|crawl|Bot|Monitor|BrowserMob|BingPreview|PagePeeker|WebThumb|URL2PNG|ZooShot|GomezA|Google SketchUp|Read Later|KTXN|KHTE|Keynote|Pingdom|AlwaysOn|zao|borg|oegp|silk|Xenu|zeal|NING|htdig|lycos|slurp|teoma|voila|yahoo|Sogou|CiBra|Nutch|Java|JNLP|Daumoa|Genieo|ichiro|larbin|pompos|Scrapy|snappy|speedy|vortex|favicon|indexer|Riddler|scooter|scraper|scrubby|WhatWeb|WinHTTP|voyager|archiver|Icarus6j|mogimogi|Netvibes|altavista|charlotte|findlinks|Retreiver|TLSProber|WordPress|wsr-agent|http client|Python-urllib|AppEngine-Google|semanticdiscovery|facebookexternalhit|web/snippet|Google-HTTP-Java-Client-->
          <Filters>search|spider|crawl|Bot|Monitor|AlwaysOn</Filters>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ClientIpHeaderTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AzureAppServiceRoleNameFromHostNameHeaderInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationNameTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationCorrelationTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.UserTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SessionTelemetryInitializer, Microsoft.AI.Web" />
      </TelemetryInitializers>
      <TelemetryModules>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
          <ExcludeComponentCorrelationHttpHeadersOnDomains>
            <!-- 
            Requests to the following hostnames will not be modified by adding correlation headers.         
            Add entries here to exclude additional hostnames.
            NOTE: this configuration will be lost upon NuGet upgrade.
            -->
            <Add>core.windows.net</Add>
            <Add>core.chinacloudapi.cn</Add>
            <Add>core.cloudapi.de</Add>
            <Add>core.usgovcloudapi.net</Add>
          </ExcludeComponentCorrelationHttpHeadersOnDomains>
          <IncludeDiagnosticSourceActivities>
            <Add>Microsoft.Azure.EventHubs</Add>
            <Add>Microsoft.Azure.ServiceBus</Add>
          </IncludeDiagnosticSourceActivities>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <!--
          Use the following syntax here to collect additional performance counters:
          
          <Counters>
            <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
            ...
          </Counters>
          
          PerformanceCounter must be either \CategoryName(InstanceName)\CounterName or \CategoryName\CounterName
          
          NOTE: performance counters configuration will be lost upon NuGet upgrade.
          
          The following placeholders are supported as InstanceName:
            ??APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
            ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
            ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AppServicesHeartbeatTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureInstanceMetadataTelemetryModule, Microsoft.AI.WindowsServer">
          <!--
          Remove individual fields collected here by adding them to the ApplicationInsighs.HeartbeatProvider 
          with the following syntax:
          
          <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
            <ExcludedHeartbeatProperties>
              <Add>osType</Add>
              <Add>location</Add>
              <Add>name</Add>
              <Add>offer</Add>
              <Add>platformFaultDomain</Add>
              <Add>platformUpdateDomain</Add>
              <Add>publisher</Add>
              <Add>sku</Add>
              <Add>version</Add>
              <Add>vmId</Add>
              <Add>vmSize</Add>
              <Add>subscriptionId</Add>
              <Add>resourceGroupName</Add>
              <Add>placementGroupId</Add>
              <Add>tags</Add>
              <Add>vmScaleSetName</Add>
            </ExcludedHeartbeatProperties>
          </Add>
                
          NOTE: exclusions will be lost upon upgrade.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule, Microsoft.AI.WindowsServer">
          <!--</Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.FirstChanceExceptionStatisticsTelemetryModule, Microsoft.AI.WindowsServer">-->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule, Microsoft.AI.Web">
          <Handlers>
            <!-- 
            Add entries here to filter out additional handlers: 
            
            NOTE: handler configuration will be lost upon NuGet upgrade.
            -->
            <Add>Microsoft.VisualStudio.Web.PageInspector.Runtime.Tracing.RequestDataHttpHandler</Add>
            <Add>System.Web.StaticFileHandler</Add>
            <Add>System.Web.Handlers.AssemblyResourceLoader</Add>
            <Add>System.Web.Optimization.BundleHandler</Add>
            <Add>System.Web.Script.Services.ScriptHandlerFactory</Add>
            <Add>System.Web.Handlers.TraceHandler</Add>
            <Add>System.Web.Services.Discovery.DiscoveryRequestHandler</Add>
            <Add>System.Web.HttpDebugHandler</Add>
          </Handlers>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web" />
      </TelemetryModules>
      <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
      <TelemetrySinks>
        <Add Name="default">
          <TelemetryProcessors>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryProcessor, Microsoft.AI.PerfCounterCollector" />
            <Add Type="Microsoft.ApplicationInsights.Extensibility.AutocollectedMetricsExtractor, Microsoft.ApplicationInsights" />
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <ExcludedTypes>Event</ExcludedTypes>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <IncludedTypes>Event</IncludedTypes>
            </Add>
          </TelemetryProcessors>
          <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel" />
        </Add>
      </TelemetrySinks>
      <!-- 
        Learn more about Application Insights configuration with ApplicationInsights.config here: 
        http://go.microsoft.com/fwlink/?LinkID=513840
      -->
      <InstrumentationKey>your-instrumentation-key-here</InstrumentationKey>
    </ApplicationInsights>
     ```

4. Przed tagiem zamykającym `</ApplicationInsights>` Dodaj swój klucz Instrumentacji dla zasobu Application Insights.  Klucz Instrumentacji można znaleźć w okienku Przegląd nowo utworzonego zasobu Application Insights utworzonego w ramach wymagań wstępnych dotyczących tego artykułu.

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```

5. Na tym samym poziomie projektu, w którym znajduje się `ApplicationInsights.config` plik, Utwórz folder o nazwie `ErrorHandler` z nowym plikiem języka C# o nazwie `AiHandleErrorAttribute.cs` . Zawartość pliku będzie wyglądać następująco:

    ```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;
    
    namespace WebApplication10.ErrorHandler //namespace will vary based on your project name
    {
        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
        public class AiHandleErrorAttribute : HandleErrorAttribute
        {
            public override void OnException(ExceptionContext filterContext)
            {
                if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
                {
                    //If customError is Off, then AI HTTPModule will report the exception
                    if (filterContext.HttpContext.IsCustomErrorEnabled)
                    {   
                        var ai = new TelemetryClient();
                        ai.TrackException(filterContext.Exception);
                    } 
                }
                base.OnException(filterContext);
            }
        }
    }
    
    ```

6. W `App_Start` folderze Otwórz `FilterConfig.cs` plik i zmień go tak, aby był zgodny z przykładem:

    ```csharp
    using System.Web;
    using System.Web.Mvc;
    
    namespace WebApplication10 //Namespace will vary based on project name
    {
        public class FilterConfig
        {
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());
            }
        }
    }
    ```

7. Zaktualizuj plik Web.config w następujący sposób:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <!--
      For more information on how to configure your ASP.NET application, please visit
      https://go.microsoft.com/fwlink/?LinkId=301880
      -->
    <configuration>
      <appSettings>
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.7.2" />
        <httpRuntime targetFramework="4.7.2" />
        <httpModules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" />
        </httpModules>
      </system.web>
      <runtime>
        <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
          <dependentAssembly>
            <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" />
            <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" />
            <bindingRedirect oldVersion="0.0.0.0-12.0.0.0" newVersion="12.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Optimization" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-1.1.0.0" newVersion="1.1.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-5.2.7.0" newVersion="5.2.7.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Memory" publicKeyToken="cc7b13ffcd2ddd51" culture="neutral" />
            <bindingRedirect oldVersion="0.0.0.0-4.0.1.1" newVersion="4.0.1.1" />
          </dependentAssembly>
        </assemblyBinding>
      </runtime>
      <system.codedom>
        <compilers>
          <compiler language="c#;cs;csharp" extension=".cs" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:1659;1699;1701" />
          <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:41008 /define:_MYTYPE=\&quot;Web\&quot; /optionInfer+" />
        </compilers>
      </system.codedom>
      <system.webServer>
        <validation validateIntegratedModeConfiguration="false" />
        <modules>
          <remove name="TelemetryCorrelationHttpModule" />
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="managedHandler" />
          <remove name="ApplicationInsightsWebTracking" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
        </modules>
      </system.webServer>
    </configuration>
    
    ```

Pomyślnie skonfigurowano monitorowanie aplikacji po stronie serwera. Jeśli uruchomisz aplikację sieci Web, zobaczysz, że dane telemetryczne zaczynają pojawiać się w Application Insights.

## <a name="add-client-side-monitoring"></a>Dodawanie monitorowania po stronie klienta

W poprzednich sekcjach przedstawiono wskazówki dotyczące metod automatycznego i ręcznego konfigurowania monitorowania po stronie serwera. Aby dodać monitorowanie po stronie klienta, należy użyć naszego [zestawu SDK JavaScript po stronie klienta](javascript.md). Możesz monitorować wszystkie transakcje strony sieci Web po stronie klienta, dodając [fragment kodu JavaScript](javascript.md#snippet-based-setup) przed tagiem zamykającym `</head>` HTML strony. 

Chociaż możliwe jest ręczne dodanie fragmentu kodu do nagłówka każdej strony HTML, zaleca się dodanie tego fragmentu do strony głównej, co spowoduje wstrzyknięcie fragmentu do wszystkich stron w witrynie. W przypadku aplikacji ASP.NET MVC opartej na szablonach z tego artykułu plik, który należy edytować jest wywoływany `_Layout.cshtml` i znajduje się w obszarze **widoki**  >  **udostępnione**.

Aby dodać monitorowanie po stronie klienta, Otwórz `_Layout.cshtml` plik i postępuj zgodnie z [instrukcjami konfiguracji opartymi na wstawek](javascript.md#snippet-based-setup) z artykułu konfiguracji zestawu SDK języka JavaScript po stronie klienta.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Istnieje znany problem w bieżącej wersji programu Visual Studio 2019, który podczas przechowywania klucza Instrumentacji w kluczu tajnym użytkownika jest uszkodzony dla aplikacji opartych na .NET Framework, a klucz ostatecznie musi być stałe w pliku applicationinsights.config, aby obejść ten błąd. Ten artykuł został zaprojektowany, aby całkowicie uniknąć tego problemu, nie używając kluczy tajnych użytkownika.  

## <a name="open-source-sdk"></a>Zestaw SDK open source

* [Odczytuj i współtworzyć kod](https://github.com/microsoft/ApplicationInsights-dotnet).

Aby zapoznać się z najnowszymi aktualizacjami i poprawkami błędów [, zapoznaj się z informacjami o wersji](./release-notes.md).

## <a name="next-steps"></a>Następne kroki

* Dodaj transakcje syntetyczne, aby sprawdzić, czy witryna sieci Web jest dostępna ze wszystkich na świecie z [monitorowaniem dostępności](monitor-web-app-availability.md).
* [Konfigurowanie próbkowania](sampling.md) w celu zmniejszenia ruchu telemetrii oraz kosztów magazynowania danych.


