---
title: Profilowanie na żywo aplikacji Service Fabric platformy Azure z Application Insights
description: Włącz Profiler dla aplikacji Service Fabric
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589560"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilowanie aplikacji Service Fabric platformy Azure przy użyciu Application Insights

Application Insights Profiler można również wdrożyć w następujących usługach:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurowanie definicji wdrożenia środowiska

Application Insights Profiler jest dołączona do Diagnostyka Azure. Rozszerzenie Diagnostyka Azure można zainstalować przy użyciu szablonu Azure Resource Manager dla klastra Service Fabric. Pobierz [szablon, który instaluje Diagnostyka Azure w klastrze Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Aby skonfigurować środowisko, należy wykonać następujące czynności:

1. Profiler obsługuje .NET Framework i .Net Core. Jeśli używasz .NET Framework, upewnij się, że używasz [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszego. Wystarczy upewnić się, że wdrożony system operacyjny jest w `Windows Server 2012 R2` wersji lub nowszej. Profiler obsługuje program .NET Core 2,1 i nowsze aplikacje.

1. Wyszukaj rozszerzenie [Diagnostyka Azure](../agents/diagnostics-extension-overview.md) w pliku szablonu wdrożenia.

1. Dodaj następującą `SinksConfig` sekcję jako element podrzędny `WadCfg` . Zastąp `ApplicationInsightsProfiler` wartość właściwości własnym kluczem instrumentacji Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Aby uzyskać informacje na temat dodawania rozszerzenia diagnostyki do szablonu wdrożenia, zobacz [Używanie monitorowania i diagnostyki z maszyną wirtualną z systemem Windows i szablonami Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json).

1. Wdróż klaster Service Fabric przy użyciu szablonu Azure Resource Manager.  
  Jeśli ustawienia są poprawne, Application Insights Profiler zostaną zainstalowane i włączone, gdy rozszerzenie Diagnostyka Azure zostanie zainstalowane. 

1. Dodaj Application Insights do aplikacji Service Fabric.  
  Aby Profiler zbierał profile dla żądań, aplikacja musi śledzić operacje przy użyciu Application Insights. W przypadku bezstanowych interfejsów API można zapoznać się z instrukcjami dotyczącymi [śledzenia żądań profilowania](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Aby uzyskać więcej informacji na temat śledzenia operacji niestandardowych w innych rodzajach aplikacji, zobacz [śledzenie niestandardowych operacji przy użyciu zestawu SDK programu Application Insights .NET](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Wdróż ponownie aplikację.


## <a name="next-steps"></a>Następne kroki

* Generuj ruch do aplikacji (na przykład uruchom [Test dostępności](monitor-web-app-availability.md)). Następnie poczekaj od 10 do 15 minut, aż ślady rozpoczną wysyłanie do wystąpienia Application Insights.
* Zobacz [ślady profilera](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w Azure Portal.
* Aby uzyskać pomoc dotyczącą rozwiązywania problemów z profilerem, zobacz [Rozwiązywanie problemów z narzędziem Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)
