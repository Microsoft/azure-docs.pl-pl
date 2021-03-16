---
title: Rozwiązywanie problemów z brakiem danych — usługa Application Insights dla platformy .NET
description: Nie widzisz danych w usłudze Azure Application Insights? Spróbuj tutaj.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: 40fbe4d08676d7cc56478d3740424fccaa7addc0
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562199"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Rozwiązywanie problemów z brakiem danych — Application Insights dla platformy .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Brakuje niektórych danych telemetrycznych
*W Application Insights widoczny jest tylko ułamek zdarzeń generowanych przez moją aplikację.*

* Jeśli stale widzisz ten sam ułamek, prawdopodobnie jest on spowodowany [próbką](./sampling.md)adaptacyjną. Aby to potwierdzić, Otwórz wyszukiwanie (w bloku omówienie) i sprawdź wystąpienie żądania lub innego zdarzenia. W dolnej części sekcji Właściwości kliknij "..." Aby uzyskać pełne szczegóły właściwości. Jeśli liczba żądań > 1, wówczas próbkowanie jest operacją.
* W przeciwnym razie możliwe jest [ograniczenie liczby danych](./pricing.md#limits-summary) do planu cenowego. Te limity są stosowane na minutę.

*Przypadkowo występuje utrata danych.*

* Sprawdź, czy występują problemy z utratą danych w [kanale telemetrii](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Wyszukaj wszystkie znane problemy w [repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) kanału telemetrycznego

*Mam utratę danych w aplikacji konsolowej lub aplikacji sieci Web, gdy aplikacja zostanie zatrzymana.*

* Kanał zestawu SDK przechowuje dane telemetryczne w buforze i wysyła je w partiach. Jeśli aplikacja jest zamykana, może być konieczne jawne wywołanie metody [Flush ()](api-custom-events-metrics.md#flushing-data). Zachowanie `Flush()` zależy od rzeczywistego używanego [kanału](telemetry-channels.md#built-in-telemetry-channels) .

## <a name="no-data-from-my-server"></a>Brak danych z mojego serwera
*Moja aplikacja została zainstalowana na serwerze sieci Web, a teraz nie widzę żadnych danych telemetrycznych. Działa on prawidłowo na komputerze deweloperskim.*

* Prawdopodobnie występuje problem z zaporą. [Ustaw wyjątki zapory dla Application Insights, aby wysyłać dane](./ip-addresses.md).
* Serwer IIS może nie mieć niektórych wymagań wstępnych: rozszerzalność architektury .NET 4,5 i ASP.NET 4,5.

*[Zainstalowano Monitor stanu](./monitor-performance-live-website-now.md) na serwerze sieci Web w celu monitorowania istniejących aplikacji. Nie widzę żadnych wyników.*

* Zobacz [Rozwiązywanie problemów Monitor stanu](./monitor-performance-live-website-now.md#troubleshoot).

> [!IMPORTANT]
> Nowe regiony platformy Azure **wymagają** użycia parametrów połączenia zamiast kluczy Instrumentacji. [Parametry połączenia](./sdk-connection-string.md?tabs=net) identyfikują zasób, z którym chcesz skojarzyć dane telemetryczne. Umożliwia również modyfikowanie punktów końcowych, które będą używane przez zasób jako miejsce docelowe dla danych telemetrycznych. Należy skopiować parametry połączenia i dodać je do kodu aplikacji lub do zmiennej środowiskowej.


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException: nie można załadować pliku lub zestawu "Microsoft. AspNet TelemetryCorrelation

Aby uzyskać więcej informacji na temat tego błędu, zobacz [problem z usługą GitHub 1610] ( https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610) .

W przypadku uaktualniania z zestawów SDK starszych niż (2,4) należy upewnić się, że następujące zmiany zostały zastosowane do `web.config` i `ApplicationInsights.config` :

1. Dwa moduły HTTP, a nie jeden. W programie należy `web.config` mieć dwa moduły HTTP. Kolejność jest ważna w niektórych scenariuszach:

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. `ApplicationInsights.config`Oprócz `RequestTrackingTelemetryModule` tego należy mieć następujący moduł telemetrii:

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

***Niepowodzenie uaktualnienia może prowadzić do nieoczekiwanych wyjątków lub nie zbiera się danych telemetrycznych.***


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Brak opcji "Dodaj Application Insights" w programie Visual Studio
*Po kliknięciu prawym przyciskiem myszy istniejącego projektu w Eksplorator rozwiązań nie widzę żadnych Application Insights opcji.*

* Narzędzia nie obsługują wszystkich typów projektów programu .NET. Obsługiwane są projekty sieci Web i WCF. W przypadku innych typów projektów, takich jak aplikacje pulpitu lub usług, nadal można [ręcznie dodać zestaw Application Insights SDK do projektu](./windows-desktop.md).
* Upewnij się, że masz [Visual Studio 2013 aktualizację Update 3 lub nowszą](/visualstudio/releasenotes/vs2013-update3-rtm-vs). Jest ona wstępnie zainstalowana z narzędziami do analizy dla deweloperów, które udostępniają zestaw Application Insights SDK.
* Wybierz pozycję **Narzędzia**, **rozszerzenia i aktualizacje** , a następnie sprawdź, czy **Developer Analytics Tools** jest zainstalowana i włączona. Jeśli tak, kliknij pozycję **aktualizacje** , aby sprawdzić, czy jest dostępna aktualizacja.
* Otwórz okno dialogowe Nowy projekt i wybierz pozycję aplikacja sieci Web ASP.NET. Jeśli w tym miejscu zostanie wyświetlona opcja Application Insights, narzędzia zostaną zainstalowane. Jeśli nie, spróbuj odinstalować, a następnie ponownie zainstalować Developer Analytics Tools.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Dodawanie Application Insights nie powiodło się
*Gdy próbuję dodać Application Insights do istniejącego projektu, pojawia się komunikat o błędzie.*

Prawdopodobna przyczyna:

* Komunikacja z portalem Application Insights nie powiodła się; oraz
* Wystąpił problem z kontem platformy Azure;
* Masz dostęp tylko [do odczytu do subskrypcji lub grupy, w której podjęto próbę utworzenia nowego zasobu](./resources-roles-access-control.md).

Wiązane

* Sprawdź, czy podane poświadczenia logowania są odpowiednie dla odpowiedniego konta platformy Azure.
* Sprawdź, czy masz dostęp do [Azure Portal](https://portal.azure.com)w przeglądarce. Otwórz ustawienia i sprawdź, czy istnieją jakieś ograniczenia.
* [Dodaj Application Insights do istniejącego projektu](./asp-net.md): w Eksplorator rozwiązań, kliknij prawym przyciskiem myszy projekt i wybierz polecenie "Dodaj Application Insights".

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Otrzymuję błąd "klucz instrumentacji nie może być pusty"
Wygląda na to, że wystąpił problem podczas instalowania Application Insights lub karty rejestrowania.

W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Application Insights > skonfigurować Application Insights**. Zostanie wyświetlone okno dialogowe umożliwiające zalogowanie się do platformy Azure i utworzenie zasobu Application Insights lub ponowne użycie istniejącego.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> "Brak pakietów NuGet" na serwerze kompilacji
*Wszystko jest kompilowane po debugowaniu na moim komputerze deweloperskim, ale otrzymuję błąd NuGet na serwerze kompilacji.*

Zobacz [przywracanie pakietu NuGet](https://docs.nuget.org/Consume/Package-Restore) i [automatyczne przywracanie pakietów](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Brakujące polecenie menu do otwarcia Application Insights z programu Visual Studio
*Po kliknięciu prawym przyciskiem myszy mojego projektu Eksplorator rozwiązań nie widzę żadnych poleceń Application Insights lub nie widzę polecenia Otwórz Application Insights.*

Prawdopodobna przyczyna:

* Jeśli zasób Application Insights został utworzony ręcznie lub jeśli projekt jest typu, który nie jest obsługiwany przez narzędzia Application Insights.
* Narzędzia deweloperskie Analytics są wyłączone w programie Visual Studio.
* Program Visual Studio jest starszy niż 2013 Update 3.

Wiązane

* Upewnij się, że wersja programu Visual Studio to 2013 Update 3 lub nowszego.
* Wybierz pozycję **Narzędzia**, **rozszerzenia i aktualizacje** , a następnie sprawdź, czy **Narzędzia Developer Analytics** są zainstalowane i włączone. Jeśli tak, kliknij pozycję **aktualizacje** , aby sprawdzić, czy jest dostępna aktualizacja.
* Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań. Jeśli zobaczysz polecenie **Application Insights > skonfigurować Application Insights**, użyj go, aby połączyć projekt z zasobem w usłudze Application Insights.

W przeciwnym razie typ projektu nie jest obsługiwany bezpośrednio przez narzędzia deweloperskie. Aby wyświetlić dane telemetryczne, zaloguj się do [Azure Portal](https://portal.azure.com), wybierz pozycję Application Insights na lewym pasku nawigacyjnym, a następnie wybierz aplikację.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Odmowa dostępu" podczas otwierania Application Insights z programu Visual Studio
*Polecenie menu "Otwórz Application Insights" przenosi do Azure Portal, ale otrzymuję błąd "odmowa dostępu".*

Logowanie firmy Microsoft, które zostało ostatnio użyte w domyślnej przeglądarce, nie ma dostępu do [zasobu, który został utworzony po dodaniu Application Insights do tej aplikacji](./asp-net.md). Istnieją dwa możliwe przyczyny:

* Masz więcej niż jedną konto Microsoft — może to być służbowa i osobista konto Microsofta? Logowanie, które było ostatnio używane w domyślnej przeglądarce, dotyczyło innego konta niż konto, które ma dostęp do [dodawania Application Insights do projektu](./asp-net.md).
  * Poprawka: kliknij swoją nazwę w prawym górnym rogu okna przeglądarki i wyloguj się. Następnie zaloguj się przy użyciu konta, które ma dostęp. Następnie na lewym pasku nawigacyjnym kliknij pozycję Application Insights i wybierz aplikację.
* Ktoś inny dodał Application Insights do projektu i nie ma [dostępu do grupy zasobów](./resources-roles-access-control.md) , w której został utworzony.
  * Poprawka: Jeśli użył konta organizacyjnego, może dodać Cię do zespołu; lub mogą udzielić indywidualnego dostępu do grupy zasobów.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Nie znaleziono elementu zawartości" podczas otwierania Application Insights z programu Visual Studio
*Polecenie menu "Otwórz Application Insights" przenosi do Azure Portal, ale otrzymuję błąd "nie znaleziono zasobu".*

Prawdopodobna przyczyna:

* Zasób Application Insights aplikacji został usunięty. oraz
* Klucz Instrumentacji został ustawiony lub zmieniony w ApplicationInsights.config przez edytowanie go bezpośrednio, bez aktualizowania pliku projektu.

Klucz Instrumentacji w ApplicationInsights.config kontroluje, gdzie jest wysyłana Telemetria. Wiersz w pliku projektu określa, który zasób jest otwierany podczas korzystania z polecenia w programie Visual Studio.

Wiązane

* W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję Application Insights, skonfiguruj Application Insights. W oknie dialogowym możesz wybrać wysyłanie danych telemetrycznych do istniejącego zasobu lub utworzyć nowe. Oraz
* Otwórz zasób bezpośrednio. Zaloguj się do [Azure Portal](https://portal.azure.com), kliknij przycisk Application Insights na lewym pasku nawigacyjnym, a następnie wybierz aplikację.

## <a name="where-do-i-find-my-telemetry"></a>Gdzie mogę znaleźć moją telemetrię?
*Po zalogowaniu się do [Microsoft Azure Portal](https://portal.azure.com)i widzę pulpit nawigacyjny Home platformy Azure. Gdzie mogę znaleźć moje Application Insights dane?*

* Na pasku nawigacyjnym po lewej stronie kliknij pozycję Application Insights, a następnie nazwę aplikacji. Jeśli nie masz żadnych projektów, musisz [dodać lub skonfigurować Application Insights w projekcie sieci Web](./asp-net.md).  
  Zobaczysz kilka wykresów podsumowujących. Możesz kliknąć, aby wyświetlić więcej szczegółów.
* W programie Visual Studio podczas debugowania aplikacji kliknij przycisk Application Insights.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Brak danych serwera (lub nie ma żadnych danych)
*Uruchomiono moją aplikację, a następnie otwarto usługę Application Insights w Microsoft Azure, ale wszystkie wykresy pokazują "Dowiedz się, jak zbierać..." lub "nie skonfigurowano".* Lub *tylko widok strony i dane użytkownika, ale nie dane serwera.*

* Uruchom aplikację w trybie debugowania w programie Visual Studio (F5). Użyj aplikacji, aby wygenerować pewne dane telemetryczne. Sprawdź, czy można zobaczyć zdarzenia zarejestrowane w oknie danych wyjściowych programu Visual Studio.  
  ![Zrzut ekranu pokazujący uruchomioną aplikację w trybie debugowania w programie Visual Studio.](./media/asp-net-troubleshoot-no-data/output-window.png)
* W portalu Application Insights Otwórz pozycję [Wyszukiwanie diagnostyczne](./diagnostic-search.md). Dane zazwyczaj pojawiają się w tym miejscu jako pierwsze.
* Kliknij przycisk Odśwież. Blok jest odświeżany okresowo, ale można go również wykonać ręcznie. Interwał odświeżania jest dłuższy dla większych zakresów czasu.
* Sprawdź, czy klucze Instrumentacji są zgodne. W głównym bloku aplikacji w portalu Application Insights, na liście rozwijanej **podstawowe** , zapoznaj się z **kluczem Instrumentacji**. Następnie w projekcie w programie Visual Studio Otwórz ApplicationInsights.config i Znajdź `<instrumentationkey>` . Sprawdź, czy dwa klucze są równe. Jeśli nie:  
  * W portalu kliknij Application Insights i wyszukaj zasób aplikacji z właściwym kluczem; oraz
  * W programie Visual Studio Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję Application Insights, skonfiguruj. Zresetuj aplikację, aby wysłać dane telemetryczne do odpowiedniego zasobu.
  * Jeśli nie możesz znaleźć pasujących kluczy, sprawdź, czy używasz tych samych poświadczeń logowania w programie Visual Studio jak w portalu.
* Na [głównym pulpicie nawigacyjnym Microsoft Azure](https://portal.azure.com)obejrzyj mapę Service Health. Jeśli istnieją jakieś wskazania alertów, poczekaj, aż powróci do programu OK, a następnie zamknij i ponownie otwórz blok aplikacji Application Insights.
* Sprawdź również [nasz blog o stanie](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog).
* Czy piszesz kod dla [zestawu SDK po stronie serwera](./api-custom-events-metrics.md) , który może zmienić klucz Instrumentacji w `TelemetryClient` wystąpieniach lub w `TelemetryContext` ? Lub napisać [Filtr lub konfigurację próbkowania](./api-filtering-sampling.md) , która może być zbyt duża?
* W przypadku edytowania ApplicationInsights.config należy uważnie sprawdzić konfigurację [TelemetryInitializers i TelemetryProcessors](./api-filtering-sampling.md). Nieprawidłowo nazwany typ lub parametr może spowodować, że zestaw SDK nie wyśle danych.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Brak danych w widokach stron, przeglądarkach, użyciu
*Widzę dane na wykresach czas odpowiedzi serwera i żądania serwera, ale nie ma danych w czasie ładowania strony ani w przeglądarce ani w blokach użycia.*

Dane pochodzą ze skryptów na stronach sieci Web. 

* Jeśli dodano Application Insights do istniejącego projektu sieci Web, [należy ręcznie dodać skrypty](./javascript.md).
* Upewnij się, że w programie Internet Explorer nie jest wyświetlana witryna w trybie zgodności.
* Użyj funkcji debugowania przeglądarki (F12 w niektórych przeglądarkach, a następnie wybierz sieć), aby sprawdzić, czy dane są wysyłane do `dc.services.visualstudio.com` .

## <a name="no-dependency-or-exception-data"></a>Brak zależności lub danych wyjątków
Zobacz [Telemetria zależności](./asp-net-dependencies.md) i [telemetrię wyjątku](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Brak danych wydajności
Dane wydajności (procesor CPU, szybkość operacji we/wy itd.) są dostępne dla [usług sieci Web Java](./java-collectd.md), [aplikacji klasycznych systemu Windows](./windows-desktop.md), [aplikacji sieci Web i usług IIS, jeśli jest instalowany monitor stanu](./monitor-performance-live-website-now.md)i [usługa Azure Cloud Services](./app-insights-overview.md). znajdziesz je w obszarze Ustawienia, serwery.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Nie ma danych (serwer) od momentu opublikowania aplikacji na serwerze
* Sprawdź, czy wszystkie firmy Microsoft zostały faktycznie skopiowane. ApplicationInsights biblioteki dll na serwerze programu wraz z Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* W zaporze może być konieczne [otwarcie niektórych portów TCP](./ip-addresses.md).
* Jeśli musisz użyć serwera proxy do wysłania z sieci firmowej, ustaw [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71)) w Web.config
* Windows Server 2008: Upewnij się, że zainstalowano następujące aktualizacje: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://web.archive.org/web/20150129090641/http://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Użyto, aby wyświetlić dane, ale zostało ono zatrzymane
* Czy osiągnięto miesięczny limit liczby punktów danych? Aby dowiedzieć się, Otwórz ustawienia/przydział i Cennik. Jeśli tak, możesz uaktualnić plan lub uregulować dodatkową pojemność. Zobacz [schemat cen](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nie widzę wszystkich danych, których oczekuje
Jeśli aplikacja wysyła dużo danych i używasz zestawu Application Insights SDK dla ASP.NET w wersji 2.0.0-beta3 lub nowszej, funkcja [próbkowania adaptacyjnego](./sampling.md) może działać i wysyłać tylko procent danych telemetrycznych.

Można go wyłączyć, ale nie jest to zalecane. Próbkowanie jest zaprojektowana tak, aby powiązane dane telemetryczne były prawidłowo przesyłane do celów diagnostycznych.

## <a name="client-ip-address-is-0000"></a>Adres IP klienta to 0.0.0.0

W lutym 5 2018 ogłoszono, że usunięto rejestrowanie adresu IP klienta. Nie ma to wpływu na lokalizację geograficzną.

> [!NOTE]
> Jeśli potrzebujesz pierwszych 3 oktetów adresu IP, możesz użyć [inicjatora telemetrii](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) , aby dodać atrybut niestandardowy.
> Nie ma to wpływu na dane zebrane przed 5 lutego 2018 r.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Nieprawidłowe dane geograficzne w telemetrii użytkownika
Wymiary miasto, region i kraj są uzyskiwane z adresów IP i nie zawsze są dokładne. Te adresy IP są przetwarzane najpierw dla lokalizacji, a następnie zostały zmienione na 0.0.0.0, które mają być przechowywane.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Wyjątek „nie można odnaleźć metody” podczas uruchamiania w usługach Azure Cloud Services
Czy to kompilacja dla .NET 4.6? Wersja 4.6 nie jest automatycznie obsługiwana w rolach usług Azure Cloud Services. [Zainstaluj wersję 4.6 w każdej roli](../../cloud-services/cloud-services-dotnet-install-dotnet.md) przed uruchomieniem aplikacji.

## <a name="troubleshooting-logs"></a>Rozwiązywanie problemów z dziennikami

Postępuj zgodnie z tymi instrukcjami, aby przechwytywać dzienniki rozwiązywania problemów dla Twojej struktury.

### <a name="net-framework"></a>.NET Framework

1. Zainstaluj pakiet [Microsoft. ASPNET. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) z narzędzia NuGet. Instalowana wersja musi być zgodna z aktualnie zainstalowaną wersją programu `Microsoft.ApplicationInsighs`

2. Zmodyfikuj plik applicationinsights.config w taki sposób, aby obejmował następujące elementy:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.FileDiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Aplikacja musi mieć uprawnienia do zapisu w skonfigurowanej lokalizacji

3. Uruchom ponownie proces, aby nowe ustawienia były pobierane przez zestaw SDK

4. Przywróć te zmiany po zakończeniu.

### <a name="net-core"></a>.NET Core

1. Zainstaluj [pakiet NuGet zestawu Application Insights SDK dla pakietu ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) z narzędzia NuGet. Instalowana wersja musi być zgodna z aktualnie zainstalowaną wersją programu `Microsoft.ApplicationInsights` .

   Najnowsza wersja Microsoft. ApplicationInsights. AspNetCore to 2.14.0 i odnosi się do Microsoft. ApplicationInsights wersja 2.14.0. W związku z tym wersja Microsoft. ApplicationInsights. AspNetCore do zainstalowania powinna być 2.14.0.

2. Modyfikuj `ConfigureServices` metodę w `Startup.cs` klasie.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Aplikacja musi mieć uprawnienia do zapisu w skonfigurowanej lokalizacji

3. Uruchom ponownie proces, aby nowe ustawienia były pobierane przez zestaw SDK

4. Przywróć te zmiany po zakończeniu.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> Zbieranie dzienników za pomocą narzędzia PerfView
[Narzędzia PerfView](https://github.com/Microsoft/perfview) to bezpłatne narzędzie do analizy danych diagnostycznych i wydajności, które ułatwia odizolowanie procesora, pamięci i innych problemów przez gromadzenie i wizualizację informacji diagnostycznych z wielu źródeł.

Application Insights dzienników zestawu SDK, które mogą być przechwytywane przez narzędzia PerfView.

Aby zebrać dzienniki, Pobierz narzędzia PerfView i uruchom następujące polecenie:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Parametry te można modyfikować stosownie do wymagań:
- **MaxCollectSec**. Ustaw ten parametr, aby zapobiec uruchamianiu narzędzia PerfView w nieskończoność i wpływając na wydajność serwera.
- **OnlyProviders**. Ustaw ten parametr, aby zbierać tylko dzienniki z zestawu SDK. Możesz dostosować tę listę na podstawie określonych badań. 
- **NoGui**. Ustaw ten parametr, aby zbierać dzienniki bez graficznego interfejsu użytkownika.


Aby uzyskać więcej informacji,
- [Rejestrowanie śladów wydajności za pomocą narzędzia PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Application Insights źródła zdarzeń](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>Zbieranie dzienników przy użyciu funkcji monitorowania dotnet

Alternatywna metoda zbierania dzienników na potrzeby rozwiązywania problemów, które mogą być szczególnie przydatne w przypadku środowisk opartych na systemie Linux, to [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace)

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>Jak usunąć Application Insights

Dowiedz się, jak usunąć Application Insights w programie Visual Studio, wykonując kroki opisane w [artykule](./remove-application-insights.md)dotyczącego usuwania.

## <a name="still-not-working"></a>Nadal nie działa...
* [Strona pytania&pytań Application Insights](/answers/topics/azure-monitor.html)

