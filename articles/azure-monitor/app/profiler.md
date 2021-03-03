---
title: Profilowanie aplikacji Azure App Service na żywo za pomocą Application Insights | Microsoft Docs
description: Profilowanie aplikacji na żywo na Azure App Service z Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b743b5be195f44c03adbee75c3108f4908d8d4e8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717761"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilowanie aplikacji Azure App Service na żywo za pomocą Application Insights

Program Profiler można uruchomić na ASP.NET i ASP.NET Core aplikacji, które są uruchomione w Azure App Service za pomocą warstwy usługi Basic lub nowszej. Włączenie profilera w systemie Linux jest obecnie możliwe tylko za pośrednictwem [tej metody](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Włącz Profiler dla swojej aplikacji
Aby włączyć program Profiler dla aplikacji, postępuj zgodnie z poniższymi instrukcjami. Jeśli używasz innego typu usługi platformy Azure, poniżej znajdują się instrukcje dotyczące włączania profilera na innych obsługiwanych platformach:
* [Usługi w chmurze](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric aplikacji](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Virtual Machines](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler jest wstępnie zainstalowany w ramach środowiska uruchomieniowego App Services. W poniższych krokach pokazano, jak włączyć ją dla App Service. Wykonaj następujące kroki, nawet jeśli zestaw SDK usługi App Insights został uwzględniony w aplikacji w czasie kompilacji.

> [!NOTE]
> Instalacja bezkodowa Application Insights Profiler jest zgodna z zasadami obsługi .NET Core.
> Aby uzyskać więcej informacji na temat obsługiwanych środowisk uruchomieniowych, zobacz temat [zasady obsługi platformy .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

1. Przejdź do panelu sterowania platformy Azure, aby uzyskać App Service.
1. Włącz ustawienie "Always On" dla usługi App Service. To ustawienie można znaleźć w obszarze **Ustawienia**, na stronie **Konfiguracja** (Zobacz zrzut ekranu w następnym kroku), a następnie wybrać kartę **Ustawienia ogólne** .
1. Przejdź do strony **ustawienia > Application Insights** .

   ![Włączanie usługi App Insights w portalu App Services](./media/profiler/AppInsights-AppServices.png)

1. Postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób, lub wybierz istniejący zasób usługi App Insights, aby monitorować aplikację. Upewnij się również, że profiler jest **włączony**. Jeśli zasób Application Insights znajduje się w innej subskrypcji z App Service, nie można użyć tej strony do skonfigurowania Application Insights. Można to zrobić ręcznie, tworząc ręcznie niezbędne ustawienia aplikacji. [Następna sekcja zawiera instrukcje dotyczące ręcznego włączania profilera.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

1. Profiler jest teraz włączony przy użyciu ustawienia aplikacji App Services.

    ![Ustawienie aplikacji profilera][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Ręczne włączanie profilera lub Azure Resource Manager
Application Insights Profiler można włączyć, tworząc ustawienia aplikacji dla Azure App Service. Na stronie z opisanymi powyżej opcjami tworzone są te ustawienia aplikacji. Można jednak zautomatyzować tworzenie tych ustawień przy użyciu szablonu lub innych metod. Te ustawienia będą również działały, jeśli zasób Application Insights jest w innej subskrypcji z Azure App Service.
Oto ustawienia, które są konieczne do włączenia profilera:

|Ustawienia aplikacji    | Wartość    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey zasobów Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


Można ustawić te wartości przy użyciu [szablonów Azure Resource Manager](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell](/powershell/module/az.websites/set-azwebapp),  [interfejsu wiersza polecenia platformy Azure](/cli/azure/webapp/config/appsettings).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Ręczne włączanie profilera dla innych chmur

Jeśli chcesz włączyć Profiler dla innych chmur, możesz użyć poniższych ustawień aplikacji.

|Ustawienia aplikacji    | Wartości dla instytucji rządowych USA| Chmura Chińska |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Wyłącz Profiler

Aby zatrzymać lub uruchomić ponownie Profiler dla wystąpienia pojedynczej aplikacji, na lewym pasku bocznym wybierz pozycję **WebJobs** i Zatrzymaj zadanie WebJob o nazwie `ApplicationInsightsProfiler3` .

  ![Wyłącz Profiler dla zadania sieci Web][disable-profiler-webjob]

Zaleca się, aby na wszystkich Twoich aplikacjach był włączony Profiler, aby odnajdywał jakiekolwiek problemy z wydajnością tak szybko, jak to możliwe.

Pliki profilera można usuwać, gdy jest używany program webdeploy do wdrażania zmian w aplikacji sieci Web. Można zapobiec usunięciu przez wykluczenie folderu App_Data podczas wdrażania. 


## <a name="next-steps"></a>Następne kroki

* [Praca z usługą Application Insights w programie Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

