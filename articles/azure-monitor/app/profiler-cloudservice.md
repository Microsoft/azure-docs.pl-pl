---
title: Profilowanie na żywo w usłudze Azure Cloud Services przy użyciu Application Insights | Microsoft Docs
description: Włącz Application Insights Profiler dla Cloud Services platformy Azure.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 75927534bf62fa4733bb552906ffa7e3a6e76286
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935615"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilowanie na żywo Cloud Services platformy Azure z Application Insights

Application Insights Profiler można również wdrożyć w następujących usługach:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje Service Fabric platformy Azure](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler jest instalowany z rozszerzeniem Diagnostyka Azure. Wystarczy skonfigurować Diagnostyka Azure, aby zainstalować Profiler i wysyłać profile do zasobu Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Włącz Profiler dla Cloud Services platformy Azure
1. Upewnij się, że używasz [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszego. Jeśli używasz rodziny systemów operacyjnych 4, musisz zainstalować .NET Framework 4.6.1 lub nowsze z [zadania uruchamiania](../../cloud-services/cloud-services-dotnet-install-dotnet.md). Rodzina systemów operacyjnych 5 zawiera domyślnie zgodną wersję programu .NET Framework. 

1. Dodaj [zestaw Application Insights SDK do usługi Azure Cloud Services](./cloudservices.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

    **Usterka w profilerze, która jest dostarczana w funkcji wad dla Cloud Services, została naprawiona.** Najnowsza wersja programu funkcji wad (1.12.2.0) dla Cloud Services współpracuje ze wszystkimi najnowszymi wersjami zestawu SDK usługi App Insights. Hosty usługi w chmurze uaktualniają funkcji wad automatycznie, ale nie będą natychmiast. Aby wymusić uaktualnienie, można ponownie wdrożyć usługę lub przeprowadzić ponowny rozruch węzła.

1. Śledź żądania przy użyciu Application Insights:

    * W przypadku ról sieci Web ASP.NET Application Insights może śledzić żądania automatycznie.

    * W przypadku ról procesów roboczych [Dodaj kod do śledzenia żądań](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Skonfiguruj rozszerzenie Diagnostyka Azure, aby umożliwić programowi profiler:

    a. Znajdź plik [Diagnostyka Azure](../platform/diagnostics-extension-overview.md) *Diagnostics. wadcfgx* dla roli aplikacji, jak pokazano poniżej:  

      ![Lokalizacja pliku konfiguracji diagnostyki](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Jeśli nie możesz znaleźć pliku, zobacz [Konfigurowanie diagnostyki dla platformy Azure Cloud Services i Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Dodaj następującą `SinksConfig` sekcję jako element podrzędny `WadCfg` :  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Jeśli plik *Diagnostics. wadcfgx* zawiera również inny ujścia typu ApplicationInsights, wszystkie trzy następujące klucze Instrumentacji muszą być zgodne:  
    > * Klucz, który jest używany przez aplikację. 
    > * Klucz, który jest używany przez ujścia ApplicationInsights. 
    > * Klucz, który jest używany przez ujścia ApplicationInsightsProfiler. 
    >
    > Rzeczywistą wartość klucza Instrumentacji używaną przez ujścia można znaleźć `ApplicationInsights` w elemencie     *ServiceConfiguration. \* cscfg* — pliki. 
    > Po wydaniu zestawu SDK dla programu Visual Studio 15,5 Azure, tylko klucze instrumentacji, które są używane przez aplikację i ujścia ApplicationInsightsProfiler, muszą być zgodne ze sobą.

1. Wdróż usługę przy użyciu nowej konfiguracji diagnostyki, a Application Insights Profiler jest skonfigurowana do uruchamiania w usłudze.
 
## <a name="next-steps"></a>Następne kroki

* Generuj ruch do aplikacji (na przykład uruchom [Test dostępności](monitor-web-app-availability.md)). Następnie poczekaj od 10 do 15 minut, aż ślady rozpoczną wysyłanie do wystąpienia Application Insights.
* Zobacz [ślady profilera](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w Azure Portal.
* Aby rozwiązać problemy z profilerem, zobacz [Rozwiązywanie problemów z narzędziem Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

