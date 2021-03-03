---
title: Rozwiązywanie problemów z usługą Azure Application Insights Profiler
description: W tym artykule przedstawiono kroki rozwiązywania problemów i informacje ułatwiające deweloperom Włączanie i używanie Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2ab719b47245f3adc2fba610f9c0473868889a7e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711454"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Rozwiązywanie problemów z włączaniem lub wyświetlaniem Application Insights Profiler

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Ogólne rozwiązywanie problemów

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profile są przekazywane tylko wtedy, gdy do aplikacji są wysyłane żądania, gdy profiler jest uruchomiony

Usługa Azure Application Insights Profiler zbiera dane przez dwie minuty co godzinę. Może również zbierać dane po wybraniu przycisku **profil teraz** w okienku **Konfigurowanie Application Insights Profiler** .

> [!NOTE]
> Dane profilowania są przekazywane tylko wtedy, gdy można je dołączyć do żądania, które wystąpiło, gdy profiler był uruchomiony. 

Profiler zapisuje komunikaty śledzenia i zdarzenia niestandardowe do zasobu Application Insights. Możesz użyć tych zdarzeń, aby zobaczyć, w jaki sposób Profiler jest uruchomiony:

1. Wyszukaj komunikaty śledzenia i zdarzenia niestandardowe wysyłane przez profiler do zasobu Application Insights. Możesz użyć tego ciągu wyszukiwania, aby znaleźć odpowiednie dane:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Na poniższej ilustracji przedstawiono dwa przykłady wyszukiwania z dwóch zasobów AI: 
    
   * Po lewej stronie aplikacja nie otrzymuje żądań, gdy profiler jest uruchomiony. Komunikat wyjaśnia, że przekazywanie zostało anulowane z powodu braku aktywności. 

   * Po prawej stronie Profiler uruchomił i wysłał zdarzenia niestandardowe, gdy wykryje żądania, które wystąpiły podczas działania profilera. W przypadku `ServiceProfilerSample` wyświetlenia zdarzenia niestandardowego oznacza to, że profil został przechwycony i jest dostępny w okienku **wydajności Application Insights** .

     Jeśli żadne rekordy nie są wyświetlane, profiler nie jest uruchomiony. Aby rozwiązać problem, zobacz sekcję Rozwiązywanie problemów dotyczących określonego typu aplikacji w dalszej części tego artykułu.  

     ![Wyszukaj dane telemetryczne profilera][profiler-search-telemetry]

### <a name="other-things-to-check"></a>Inne elementy do sprawdzenia
* Upewnij się, że aplikacja działa na .NET Framework 4,6.
* Jeśli aplikacja sieci Web jest aplikacją ASP.NET Core, musi być uruchomiona co najmniej ASP.NET Core 2,0.
* Jeśli dane, które próbujesz wyświetlić, są starsze niż kilka tygodni, spróbuj ograniczyć filtr czasu i spróbuj ponownie. Ślady są usuwane po upływie siedmiu dni.
* Upewnij się, że serwery proxy lub zapora nie zablokowano dostępu do programu https://gateway.azureserviceprofiler.net .
* Profiler nie jest obsługiwany w planach usługi App Service w warstwie Bezpłatna lub współdzielona. Jeśli używasz jednego z tych planów, spróbuj przeprowadzić skalowanie do jednego z podstawowych planów i profilera powinien zacząć pracę.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Podwójne zliczanie w wątkach równoległych

W niektórych przypadkach Metryka łącznego czasu w podglądzie stosu przekracza czas trwania żądania.

Taka sytuacja może wystąpić, gdy do żądania są skojarzone dwa lub więcej wątków równoległych. W takim przypadku łączny czas wątku jest większy niż czas, który upłynął.

Jeden wątek może oczekiwać na zakończenie. Przeglądarka próbuje wykryć tę sytuację i pomija nieinteresujące oczekiwania. W takim przypadku ERRS się na stronie wyświetlanie zbyt dużej ilości informacji zamiast pomijania, co może być istotnymi informacjami.

Gdy widzisz wątki równoległe w śladach, ustal, które wątki oczekują na identyfikację, aby można było zidentyfikować ścieżkę gorącą dla żądania.

Zwykle wątek, który szybko przechodzi do stanu oczekiwania, jest po prostu czekał na inne wątki. Skoncentrowanie się na innych wątkach i ignorowanie czasu w oczekujących wątkach.

### <a name="error-report-in-the-profile-viewer"></a>Raport o błędach w podglądzie profilów
Prześlij bilet pomocy technicznej w portalu. Pamiętaj, aby dołączyć identyfikator korelacji z komunikatu o błędzie.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Rozwiązywanie problemów z profilerem na Azure App Service

Aby Profiler działał prawidłowo:
* Plan usługi aplikacji sieci Web musi być w warstwie Podstawowa lub wyższej.
* Aplikacja sieci Web musi mieć włączony Application Insights.
* Aplikacja sieci Web musi mieć następujące ustawienia aplikacji:

    |Ustawienia aplikacji    | Wartość    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey zasobów Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* Zadanie WebJob **ApplicationInsightsProfiler3** musi być uruchomione. Aby sprawdzić zadanie WebJob:
   1. Przejdź do [kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. W menu **Narzędzia** wybierz pozycję **pulpit nawigacyjny WebJobs**.  
      Zostanie otwarte okienko **Zadania WebJob** . 
   
      ![Zrzut ekranu przedstawia okienko Zadania WebJob, w którym wyświetlana jest nazwa, stan i czas ostatniego uruchomienia zadań.][profiler-webjob]   
   
   1. Aby wyświetlić szczegóły zadania WebJob, w tym dziennik, wybierz łącze **ApplicationInsightsProfiler3** .  
     Zostanie otwarte okienko **szczegóły ciągłego Zadania WebJob** .

      ![Zrzut ekranu przedstawia okienko Szczegóły ciągłego Zadania WebJob.][profiler-webjob-log]

Jeśli profiler nie działa, możesz pobrać dziennik i wysłać go do naszego zespołu w celu uzyskania pomocy serviceprofilerhelp@microsoft.com .

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>Sprawdź stronę stanu rozszerzenia lokacji usług diagnostycznych
Jeśli profiler został włączony w [okienku Application Insights](profiler.md) w portalu, został on włączony przez rozszerzenie lokacji usług diagnostycznych.

> [!NOTE]
> Instalacja bezkodowa Application Insights Profiler jest zgodna z zasadami obsługi .NET Core.
> Aby uzyskać więcej informacji na temat obsługiwanych środowisk uruchomieniowych, zobacz temat [zasady obsługi platformy .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Możesz sprawdzić stronę stanu tego rozszerzenia, przechodząc do następującego adresu URL: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Domena linku do strony stanu będzie się różnić w zależności od chmury.
Ta domena będzie taka sama jak kudu Management site for App Service.

Ta strona stanu zawiera stan instalacji agentów profilera i Snapshot Collector. Jeśli wystąpił nieoczekiwany błąd, będzie on wyświetlany i pokazuje, jak go naprawić.

Aby uzyskać podstawowy adres URL tej strony stanu, można użyć App Service witryny zarządzania kudu:
1. Otwórz aplikację App Service w Azure Portal.
2. Wybierz pozycję **Narzędzia zaawansowane** lub wyszukaj ciąg **kudu**.
3. Wybierz pozycję **Przejdź**.
4. Po umieszczeniu w witrynie kudu Management w adresie URL **Dodaj następujące polecenie `/DiagnosticServices` i naciśnij klawisz ENTER**.
 Zostanie ona zakończona: `https://<kudu-url>/DiagnosticServices`

Zostanie wyświetlona strona stanu podobna do poniższej: ![ strona stanu usług diagnostycznych](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>Instalacja ręczna

Podczas konfigurowania programu Profiler są wprowadzane aktualizacje ustawień aplikacji sieci Web. Jeśli środowisko tego wymaga, można zastosować aktualizacje ręcznie. Przykładem może być to, że aplikacja działa w środowisku Web Apps dla usługi PowerApps. Aby ręcznie zastosować aktualizacje:

1. W okienku **kontroli aplikacji sieci Web** Otwórz pozycję **Ustawienia**.

1. Ustaw **wersję .NET Framework** na **v 4.6**.

1. Ustawienie **zawsze włączone** . 
1. Utwórz następujące ustawienia aplikacji:

    |Ustawienia aplikacji    | Wartość    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey zasobów Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>Zbyt wiele aktywnych sesji profilowania

Program Profiler można włączyć na maksymalnie cztery Web Apps, które są uruchomione w ramach tego samego planu usługi. Jeśli masz więcej niż cztery, profiler może zgłosić element *Microsoft. Serviceprofiler. Exceptions. TooManyETWSessionException*. Aby rozwiązać ten problem, przenieś niektóre aplikacje sieci Web do innego planu usługi.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Błąd wdrożenia: katalog nie jest pusty. \\ : \\ witryna główna \\ wwwroot \\ App_Data \\ zadania "

W przypadku ponownego wdrażania aplikacji sieci Web w ramach zasobu Web Apps z włączonym profilerem może zostać wyświetlony następujący komunikat:

*Katalog nie jest pusty: \\ witryna główna katalogu \\ \\ wwwroot \\ App_Data \\ zadania "*

Ten błąd występuje w przypadku uruchamiania Web Deploy ze skryptów lub z Azure Pipelines. Rozwiązanie ma dodać następujące parametry wdrożenia do zadania Web Deploy:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Te parametry usuwają folder, który jest używany przez Application Insights Profiler i odblokowywają proces ponownego wdrażania. Nie wpływają one na już uruchomione wystąpienie profilera.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak mogę określić, czy Application Insights Profiler jest uruchomiony?

Profiler działa jako ciągły element WebJob w aplikacji sieci Web. Możesz otworzyć zasób aplikacji sieci Web w [Azure Portal](https://portal.azure.com). W okienku **Zadania WebJob** Sprawdź stan **ApplicationInsightsProfiler**. Jeśli nie jest uruchomiona, Otwórz **dzienniki** , aby uzyskać więcej informacji.

## <a name="troubleshoot-vms-and-cloud-services"></a>Rozwiązywanie problemów z maszynami wirtualnymi i Cloud Services

>**Usterka w profilerze, która jest dostarczana w funkcji wad dla Cloud Services, została naprawiona.** Najnowsza wersja programu funkcji wad (1.12.2.0) dla Cloud Services współpracuje ze wszystkimi najnowszymi wersjami zestawu SDK usługi App Insights. Hosty usługi w chmurze uaktualniają funkcji wad automatycznie, ale nie będą natychmiast. Aby wymusić uaktualnienie, można ponownie wdrożyć usługę lub przeprowadzić ponowny rozruch węzła.

Aby sprawdzić, czy Profiler jest skonfigurowany prawidłowo Diagnostyka Azure, wykonaj poniższe czynności: 
1. Sprawdź, czy zawartość wdrożonej konfiguracji Diagnostyka Azure jest oczekiwana. 

1. Następnie upewnij się, że Diagnostyka Azure przekazuje odpowiednie iKey w wierszu polecenia profilera. 

1. Po trzecie Sprawdź plik dziennika profilera, aby sprawdzić, czy profiler został uruchomiony, ale zwrócił błąd. 

Aby sprawdzić ustawienia, które zostały użyte do skonfigurowania Diagnostyka Azure:

1. Zaloguj się do maszyny wirtualnej, a następnie otwórz plik dziennika w tej lokalizacji. Wersja wtyczki może być nowsza na maszynie.
    
    Dla maszyn wirtualnych:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Dla Cloud Services:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. W pliku można wyszukać ciąg **WadCfg** , aby znaleźć ustawienia, które zostały przesłane do maszyny wirtualnej w celu skonfigurowania Diagnostyka Azure. Możesz sprawdzić, czy iKey używany przez ujścia profilera jest prawidłowy.

1. Sprawdź wiersz polecenia, który służy do uruchamiania profilera. Argumenty, które są używane do uruchamiania profilera, znajdują się w następującym pliku. (Może to być dysk c: lub d:, a katalog może być ukryty).

    Dla maszyn wirtualnych:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    dla Cloud Services:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Upewnij się, że iKey w wierszu polecenia profilera jest prawidłowy. 

1. Używając ścieżki znalezionej w poprzednim *config.jsw* pliku, sprawdź plik dziennika profilera o nazwie **BootstrapN. log**. Wyświetla informacje o debugowaniu, które wskazują ustawienia, które są używane przez profiler. Wyświetla również komunikaty o stanie i błędach z profilera.  

    W przypadku maszyn wirtualnych plik jest tutaj:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Dla Cloud Services:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Jeśli profiler jest uruchomiony, gdy aplikacja otrzymuje żądania, zostanie wyświetlony następujący komunikat: *działanie wykryte z iKey*. 

    Podczas przekazywania śladu zostanie wyświetlony następujący komunikat: *Rozpocznij przekazywanie śledzenia*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Edytowanie sieci proxy lub reguł zapory

Jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy lub zapory, może być konieczne zaktualizowanie reguł w celu komunikowania się z usługą profilera.

Adresy IP używane przez Application Insights Profiler są zawarte w tagu usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą tagów usług](../../virtual-network/service-tags-overview.md).


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png