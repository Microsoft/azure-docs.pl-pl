---
title: Rozwiązywanie problemów z profilera usługi Azure Application Insights
description: W tym artykule przedstawiono kroki rozwiązywania problemów i informacje, aby pomóc deweloperom, którzy mają problemy z włączaniem lub używaniem programu Profiler usługi Application Insights.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 55bc4ff05b650884ef17e0de10d7156cbf458a9c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640961"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Rozwiązywanie problemów z włączaniem lub wyświetlaniem programu Profiler usługi Application Insights

## <a name="active-issues"></a>Aktywne problemy

* Profilowanie aplikacji ASP.NET Core 3.x jest teraz obsługiwane w usługach Azure App Services.

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Ogólne rozwiązywanie problemów

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profile są przekazywane tylko wtedy, gdy istnieją żądania do aplikacji, gdy profiler jest uruchomiony

Usługa Azure Application Insights Profiler zbiera dane profilowania przez dwie minuty co godzinę. Zbiera również dane po wybraniu przycisku **Profil teraz** w okienku **Konfigurowanie profilowania aplikacji.** Ale dane profilowania są przekazywane tylko wtedy, gdy można dołączyć do żądania, które miało miejsce podczas profilowania był uruchomiony. 

Profiler zapisuje komunikaty śledzenia i zdarzenia niestandardowe do zasobu usługi Application Insights. Można użyć tych zdarzeń, aby zobaczyć, jak profiler jest uruchomiony. Jeśli uważasz, że profiler powinien być uruchomiony i przechwytywania śladów, ale nie są one wyświetlane w okienku **wydajność,** można sprawdzić, jak profiler jest uruchomiony:

1. Wyszukaj komunikaty śledzenia i zdarzenia niestandardowe wysyłane przez profiler do zasobu usługi Application Insights. Za pomocą tego ciągu wyszukiwania można znaleźć odpowiednie dane:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Na poniższej ilustracji przedstawiono dwa przykłady wyszukiwań z dwóch zasobów SI: 
    
   * Po lewej stronie aplikacja nie odbiera żądań, gdy profiler jest uruchomiony. Komunikat wyjaśnia, że przekazywanie zostało anulowane z powodu braku aktywności. 

   * Po prawej stronie profiler rozpoczął i wysłał zdarzenia niestandardowe, gdy wykrył żądania, które miały miejsce podczas uruchamiania programu Profiler. Jeśli ServiceProfilerSample zdarzenie niestandardowe jest wyświetlany, oznacza to, że profiler dołączone śledzenia do żądania i można wyświetlić śledzenia w okienku **wydajności usługi Application Insights.**

     Jeśli nie jest wyświetlana żadna telemetria, profiler nie jest uruchomiony. Aby rozwiązać problem, zobacz sekcje rozwiązywania problemów dla określonego typu aplikacji w dalszej części tego artykułu.  

     ![Wyszukiwanie danych telemetrycznych profilera][profiler-search-telemetry]

1. Jeśli były żądania podczas profilowania uruchomiony, upewnij się, że żądania są obsługiwane przez część aplikacji, która ma profiler włączone. Chociaż aplikacje czasami składają się z wielu składników, profiler jest włączony tylko dla niektórych składników. W okienku **Konfigurowanie profilera usługi Application Insights** są wyświetlane składniki, które przekazały ślady.

### <a name="other-things-to-check"></a>Inne rzeczy do sprawdzenia
* Upewnij się, że aplikacja jest uruchomiona w programie .NET Framework 4.6.
* Jeśli aplikacja sieci web jest aplikacją ASP.NET Core, musi działać co najmniej ASP.NET Core 2.0.
* Jeśli dane, które próbujesz wyświetlić, są starsze niż kilka tygodni, spróbuj ograniczyć filtr czasu i spróbuj ponownie. Ślady są usuwane po siedmiu dniach.
* Upewnij się, że serwery proxy lub zapora nie zablokowały dostępu do https://gateway.azureserviceprofiler.netpliku .
* Profiler nie jest obsługiwany w bezpłatnych lub udostępnionych planach usług aplikacji. Jeśli używasz jednego z tych planów, spróbuj skalować do jednego z podstawowych planów i profiler powinien rozpocząć pracę.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Podwójna liczenie w równoległych gwintach

W niektórych przypadkach metryka całkowitego czasu w przeglądarce stosu jest większa niż czas trwania żądania.

Taka sytuacja może wystąpić, gdy dwa lub więcej wątków są skojarzone z żądaniem i działają równolegle. W takim przypadku całkowity czas wątku jest więcej niż czas, który upłynął. Jeden wątek może czekać na innych do ukończenia. Widz próbuje wykryć tę sytuację i pomija nieciekawe oczekiwanie. W ten sposób błądzi po stronie wyświetlania zbyt dużej ilości informacji, zamiast pomijać to, co może być informacjami krytycznymi.

Po wyświetleniu równoległych wątków w ślady, należy określić, które wątki czekają, dzięki czemu można ustalić ścieżkę krytyczną dla żądania. Zwykle wątek, który szybko przechodzi w stan oczekiwania, po prostu czeka na inne wątki. Skoncentruj się na innych wątkach i ignoruj czas w wątkach oczekujących.

### <a name="error-report-in-the-profile-viewer"></a>Raport o błędach w przeglądarce profili
Prześlij zgłoszenie pomocy technicznej w portalu. Pamiętaj, aby dołączyć identyfikator korelacji z komunikatu o błędzie.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Rozwiązywanie problemów z profilera w usłudze Azure App Service
Aby profiler działał poprawnie:
* Plan usługi aplikacji sieci web musi być warstwą podstawową lub wyższą.
* Aplikacja sieci Web musi mieć włączoną statystykę aplikacji.
* Aplikacja internetowa musi mieć następujące ustawienia aplikacji:

    |Ustawienia aplikacji    | Wartość    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Aplikacja iKey dla zasobu usługi Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* **AplikacjaInsightsProfiler3** webjob musi być uruchomiona. Aby sprawdzić webjob:
   1. Przejdź do [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. W menu **Narzędzia** wybierz polecenie **Pulpit nawigacyjny aplikacji WebJobs**.  
      Zostanie otwarte okienko **WebJobs.** 
   
      ![profiler-webjob]   
   
   1. Aby wyświetlić szczegóły webjob, w tym dziennika, wybierz **ApplicationInsightsProfiler3** link.  
     Zostanie otwarte **okienko Ciągłe szczegóły usługi WebJob.**

      ![profiler-webjob-log]

Jeśli nie możesz dowiedzieć się, dlaczego Profiler nie działa dla Ciebie, możesz pobrać dziennik serviceprofilerhelp@microsoft.comi wysłać go do naszego zespołu, aby uzyskać pomoc, . 
    
### <a name="manual-installation"></a>Instalacja ręczna

Podczas konfigurowania profilera są aktualizowane w ustawieniach aplikacji sieci web. Jeśli środowisko tego wymaga, można zastosować aktualizacje ręcznie. Przykładem może być to, że aplikacja jest uruchomiona w środowisku aplikacji sieci Web dla usługi PowerApps. Aby ręcznie zastosować aktualizacje:

1. W okienku **Formantu aplikacji sieci Web** otwórz okno **Ustawienia**.

1. Ustaw **wersję programu .NET Framework** na wersję **4.6**.

1. Ustaw **zawsze włączone** **.**
1. Utwórz następujące ustawienia aplikacji:

    |Ustawienia aplikacji    | Wartość    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Aplikacja iKey dla zasobu usługi Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Zbyt wiele aktywnych sesji profilowania

Obecnie można włączyć profiler na maksymalnie cztery aplikacje sieci web platformy Azure i gniazda wdrażania, które są uruchomione w tym samym planie usługi. Jeśli masz więcej niż cztery aplikacje sieci web uruchomione w jednym planie usługi aplikacji, profiler może zgłosić *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler działa oddzielnie dla każdej aplikacji sieci web i próbuje uruchomić sesję śledzenia zdarzeń dla systemu Windows (ETW) dla każdej aplikacji. Ale ograniczona liczba sesji ETW może być aktywna w tym czasie. Jeśli schemat webjob zgłasza zbyt wiele aktywnych sesji profilowania, przenieś niektóre aplikacje sieci web do innego planu usługi.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Błąd wdrożenia: Katalog nie jest\\\\pusty\\"D: strona główna wwwroot\\App_Data\\zadania"

Jeśli ponownie rozmieszczasz aplikację sieci web do zasobu aplikacji sieci Web z włączonym profilem, może zostać wyświetlony następujący komunikat:

*Katalog\\nie pusty "D:\\strona\\główna\\\\wwwroot App_Data pracach"*

Ten błąd występuje, jeśli uruchomisz wdrażanie w sieci Web ze skryptów lub z potoku wdrażania usługi Azure DevOps. Rozwiązaniem jest dodanie następujących dodatkowych parametrów wdrażania do zadania wdrażania w sieci Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Te parametry usuwają folder używany przez program Application Insights Profiler i odblokowuj proces ponownego rozmieszczania. Nie mają one wpływu na wystąpienie profilera, które jest aktualnie uruchomione.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak ustalić, czy program Profiler usługi Application Insights jest uruchomiony?

Profiler działa jako ciągły webjob w aplikacji sieci web. Zasób aplikacji sieci Web można otworzyć w [witrynie Azure portal](https://portal.azure.com). W okienku **WebJobs** sprawdź stan **aplikacji ApplicationInsightsProfiler**. Jeśli nie jest uruchomiony, otwórz **dzienniki,** aby uzyskać więcej informacji.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Rozwiązywanie problemów z profilera i diagnostyki platformy Azure

>**Naprawiono błąd w profilu, który jest dostarczany w wad dla usług w chmurze.** Najnowsza wersja wad (1.12.2.0) dla usług w chmurze współpracuje ze wszystkimi najnowszymi wersjami SDK usługi App Insights. Hosty usługi w chmurze uaktualnią wad automatycznie, ale nie jest natychmiastowe. Aby wymusić uaktualnienie, można ponownie wdrożyć usługę lub ponownie uruchomić węzeł.

Aby sprawdzić, czy profiler jest poprawnie skonfigurowany przez usługę Azure Diagnostics, wykonaj następujące trzy czynności: 
1. Najpierw sprawdź, czy zawartość konfiguracji diagnostyki platformy Azure, które są wdrażane są oczekiwane. 

1. Po drugie upewnij się, że diagnostyka platformy Azure przekazuje prawidłowy klucz iKey w wierszu polecenia profilera. 

1. Po trzecie, sprawdź plik dziennika profilera, aby zobaczyć, czy profiler został uruchomiony, ale zwrócił błąd. 

Aby sprawdzić ustawienia używane do konfigurowania diagnostyki platformy Azure:

1. Zaloguj się do maszyny wirtualnej (VM), a następnie otwórz plik dziennika w tej lokalizacji. (Dysk może być c: lub d: a wersja wtyczki może być inna.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    lub
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. W pliku można wyszukać ciąg **WadCfg,** aby znaleźć ustawienia, które zostały przekazane do maszyny Wirtualnej, aby skonfigurować diagnostykę platformy Azure. Można sprawdzić, czy iKey używany przez ujście profilera jest poprawna.

1. Sprawdź wiersz polecenia używany do uruchamiania programu Profiler. Argumenty, które są używane do uruchamiania Profiler znajdują się w następującym pliku. (Napęd może być c: lub d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Upewnij się, że klawisz iKey w wierszu polecenia profilera jest poprawny. 

1. Korzystając ze ścieżki znalezionej w poprzednim pliku *config.json,* sprawdź plik dziennika profilera. Wyświetla informacje debugowania, który wskazuje ustawienia, które profiler używa. Wyświetla również komunikaty o stanie i błędach z profilera.  

    Jeśli profiler jest uruchomiony podczas odbierania żądań przez aplikację, wyświetlany jest następujący komunikat: *Działanie wykryte z programu iKey*. 

    Podczas przekazywania śledzenia wyświetlany jest następujący komunikat: *Start do przekazywania śledzenia*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Edytowanie reguł serwera proxy lub zapory sieciowej

Jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy lub zapory, może być konieczne edytowanie reguł, aby umożliwić aplikacji komunikowanie się z usługą Profiler usługi Application Insights. Pliki IP używane przez profiler usługi Application Insights są uwzględniane w tagu usługi Usługi Azure Monitor.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








