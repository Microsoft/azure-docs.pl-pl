---
title: Usuwanie Application Insights w programie Visual Studio — Azure Monitor
description: Jak usunąć zestaw Application Insights SDK dla ASP.NET i ASP.NET Core w programie Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 5bfa6ee21cc1a55f653c0e79807a14ac34082e73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981481"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Jak usunąć Application Insights w programie Visual Studio

W tym artykule przedstawiono sposób usuwania zestawu SDK ASP.NET i ASP.NET Core Application Insights w programie Visual Studio.

Aby usunąć Application Insights, należy usunąć pakiety NuGet i odwołania z interfejsu API w aplikacji. Pakiety NuGet można odinstalować za pomocą konsoli Zarządzanie pakietami lub rozwiązania NuGet w programie Visual Studio. Poniższe sekcje zawierają dwa sposoby usuwania pakietów NuGet i informacje, które zostały automatycznie dodane do projektu. Upewnij się, że dodano pliki i obszary z w własnym kodzie, w którym zostały wykonane wywołania interfejsu API.

## <a name="uninstall-using-the-package-management-console"></a>Odinstalowywanie przy użyciu konsoli Zarządzanie pakietami

# <a name="net"></a>[.NET](#tab/net)

1. Aby otworzyć konsolę Zarządzanie pakietami, w górnym menu wybierz kolejno pozycje Narzędzia > Menedżer pakietów NuGet > konsola Menedżera pakietów.
     
    ![W górnym menu kliknij kolejno pozycje Narzędzia > Menedżer pakietów NuGet > konsola Menedżera pakietów](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Jeśli zbieranie śladów jest włączone, należy najpierw odinstalować Microsoft. ApplicationInsights. TraceListener. Wprowadź `Uninstall-package Microsoft.ApplicationInsights.TraceListener` następujące polecenie, aby usunąć Microsoft. ApplicationInsights. Web.

1. Wprowadź następujące polecenie: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Po wprowadzeniu polecenia pakiet Application Insights i wszystkie jego zależności zostaną odinstalowane z projektu.
    
    ![Wprowadź polecenie w konsoli](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Aby otworzyć konsolę Zarządzanie pakietami, w górnym menu wybierz kolejno pozycje Narzędzia > Menedżer pakietów NuGet > konsola Menedżera pakietów.

    ![W górnym menu kliknij kolejno pozycje Narzędzia > Menedżer pakietów NuGet > konsola Menedżera pakietów](./media/remove-application-insights/package-manager.png)

1. Wprowadź następujące polecenie: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Po wprowadzeniu polecenia pakiet Application Insights i wszystkie jego zależności zostaną odinstalowane z projektu.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Odinstalowywanie przy użyciu interfejsu użytkownika NuGet programu Visual Studio

# <a name="net"></a>[.NET](#tab/net)

1. W *Eksplorator rozwiązań*po   prawej stronie kliknij prawym przyciskiem myszy **rozwiązanie**   i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

    Zobaczysz ekran, który umożliwia edytowanie wszystkich pakietów NuGet, które są częścią projektu.
    
     ![W Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję Zarządzaj pakietami NuGet dla rozwiązania](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Jeśli kolekcja śledzenia jest włączona, należy najpierw odinstalować program Microsoft. ApplicationInsights. TraceListener bez zaznaczenia opcję Usuń zależności, a następnie wykonać poniższe kroki, aby odinstalować program Microsoft. ApplicationInsights. Web z wybraną opcją Usuń zależności.
    
1. Kliknij pakiet "Microsoft. ApplicationInsights. Web".Po prawej stronie zaznacz pole wyboru obok pozycji *projekt*   , aby wybrać wszystkie projekty.
    
1. Aby usunąć wszystkie zależności podczas odinstalowywania, wybierz **Options**   przycisk listy rozwijanej opcje poniżej sekcji, w której został wybrany projekt.

    W obszarze *Opcje dezinstalacji*zaznacz pole wyboru obok pozycji *Usuń zależności*.

1. Wybierz pozycję **Odinstaluj**.
    
    ![Zrzut ekranu przedstawia okno Microsoft. ApplicationInsights. Web z opcją Usuń zależności zaznaczone i odinstalowane.](./media/remove-application-insights/uninstall-framework.png)

    Zostanie wyświetlone okno dialogowe, w którym zostaną wyświetlone wszystkie zależności, które mają zostać usunięte z aplikacji.Wybierz **przycisk OK**   , aby odinstalować.
    
    ![Zrzut ekranu przedstawia okno dialogowe z zależnościami, które mają zostać usunięte.](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Po odinstalowaniu wszystkiego w *Eksplorator rozwiązań*mogą być widoczne "ApplicationInsights.config" i "AiHandleErrorAttribute.cs".Te dwa pliki można usunąć ręcznie.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. W *Eksplorator rozwiązań*po   prawej stronie kliknij prawym przyciskiem myszy **rozwiązanie**   i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

    Zobaczysz ekran, który umożliwia edytowanie wszystkich pakietów NuGet, które są częścią projektu.

    ![W Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję Zarządzaj pakietami NuGet dla rozwiązania](./media/remove-application-insights/manage-nuget-core.png)

1. Kliknij pakiet "Microsoft. ApplicationInsights. AspNetCore". Po prawej stronie zaznacz pole wyboru obok pozycji *projekt* , aby wybrać wszystkie projekty, a następnie wybierz pozycję **Odinstaluj**.

    ![Zaznacz Usuń zależności, a następnie Odinstaluj](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Co jest tworzone podczas dodawania Application Insights

Po dodaniu Application Insights do projektu tworzy on pliki i dodaje kod do niektórych plików. Odinstalowanie pakietów NuGet nie zawsze powoduje odrzucanie plików i kodu. Aby całkowicie usunąć Application Insights, należy sprawdzić i ręcznie usunąć dodany kod lub pliki wraz z dowolnymi wywołaniami interfejsu API, które zostały dodane do projektu.

# <a name="net"></a>[.NET](#tab/net)

Dodanie telemetria usługi Application Insights do projektu programu Visual Studio ASP.NET powoduje dodanie następujących plików:

- Plik ApplicationInsights.config
- AiHandleErrorAttribute.cs

Dodano następujące fragmenty kodu:

- [Nazwa projektu]. csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout. cshtml

    Jeśli projekt zawiera plik Layout. cshtml, zostanie dodany Poniższy kod.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.jsna

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Po dodaniu telemetria usługi Application Insights do projektu szablonu ASP.NET Core programu Visual Studio dodaje następujący kod:

- [Nazwa projektu]. csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.js:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.jsna
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Następne kroki

- [Azure Monitor](../overview.md)
