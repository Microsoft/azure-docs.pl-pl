---
title: Monitorowanie działającej aplikacji internetowej platformy ASP.NET za pomocą usługi Application Insights | Microsoft Docs
description: Monitorowanie wydajności witryny sieci Web bez jej ponownego wdrażania. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie lub na maszynach wirtualnych.
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: 79e14c171adde89c43c5ea82a60db39133157293
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576439"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Instrumentacja aplikacji sieci Web w środowisku uruchomieniowym z dołączaniem bez kodu Application Insights

> [!IMPORTANT]
> Monitor stanu nie jest już zalecany do użycia i **od 1 czerwca 2021** ta wersja monitora stanu nie będzie obsługiwana. Został on zastąpiony przez agenta Application Insights Azure Monitor (dawniej nazwany monitor stanu v2). Zapoznaj się z naszą dokumentacją wdrożeń [serwera lokalnego](./status-monitor-v2-overview.md) lub [wdrożenia zestawu skalowania maszyn wirtualnych platformy Azure](./azure-vm-vmss-apps.md).

Możliwe jest instrumentowanie działającej aplikacji internetowej za pomocą usługi Azure Application Insights bez konieczności modyfikowania kodu ani jego ponownego wdrażania. Potrzebna jest subskrypcja platformy [Microsoft Azure](https://azure.com).

Monitor stanu służy do Instrumentacji aplikacji .NET hostowanej w usługach IIS lokalnie lub na maszynie wirtualnej.

- Jeśli aplikacja jest wdrażana na maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych platformy Azure, wykonaj [te instrukcje](azure-vm-vmss-apps.md).
- Jeśli Twoja aplikacja jest wdrożona w usłudze Azure App Services, wykonaj [te instrukcje](azure-web-apps.md).
- Jeśli aplikacja jest wdrożona na maszynie wirtualnej platformy Azure, możesz przełączyć się na Application Insights monitorowanie z poziomu panelu sterowania platformy Azure.
- (Istnieją również osobne artykuły na temat Instrumentacji [Cloud Services platformy Azure](./cloudservices.md)).


![Zrzut ekranu przedstawiający wykresy omówienia usługi App Insights zawierające informacje dotyczące żądań zakończonych niepowodzeniem, czas odpowiedzi serwera i żądania serwera](./media/monitor-performance-live-website-now/overview-graphs.png)

Dostępne są dwie trasy do zastosowania Application Insights do aplikacji sieci Web platformy .NET:

* **W czasie kompilacji:** [dodaj zestaw Application Insights SDK][greenbrown] do kodu aplikacji internetowej.
* **W czasie wykonywania:** przeprowadź instrumentację aplikacji internetowej na serwerze, jak opisano poniżej, bez konieczności ponownego kompilowania lub wdrażania kodu.

> [!NOTE]
> Jeśli używasz Instrumentacji czasu kompilacji, Instrumentacja czasu wykonywania nie będzie działać nawet wtedy, gdy jest włączona.

Poniżej przedstawiono podsumowanie tego, co można uzyskać, korzystając z danej trasy:

|  | W czasie kompilacji | W czasie wykonywania |
| --- | --- | --- |
| **Żądania & wyjątki** |Tak |Tak |
| **[Bardziej szczegółowe wyjątki](./asp-net-exceptions.md)** | |Tak |
| **[Diagnostyka zależności](./asp-net-dependencies.md)** |Na platformie .NET 4.6 +, ale mniej szczegółów |Tak, kompletne szczegóły: kody wyników, tekst polecenia SQL, czasownik HTTP|
| **[Liczniki wydajności sytemu](./performance-counters.md)** |Tak |Tak |
| **[Interfejs API dla telemetrii niestandardowej][api]** |Tak |Nie |
| **[Integracja dziennika śledzenia](./asp-net-trace-logs.md)** |Tak |Nie |
| **[Widok strony & dane użytkownika](./javascript.md)** |Tak |Nie |
| **Konieczność ponownej kompilacji kodu** |Tak | Nie |



## <a name="monitor-a-live-iis-web-app"></a>Monitorowanie działającej aplikacji internetowej usług IIS

Jeśli aplikacja jest hostowana na serwerze usług IIS, włącz usługę Application Insights przy użyciu monitora stanu.

1. Zaloguj się na serwerze sieci Web usług IIS, używając poświadczeń administratora.
2. Jeśli monitor stanu usługi Application Insights nie został jeszcze zainstalowany, [pobierz i uruchom instalatora](#download).
3. W monitorze stanu wybierz zainstalowaną aplikację internetową lub witrynę internetową, którą chcesz monitorować. Zaloguj się przy użyciu poświadczeń platformy Azure.

    Skonfiguruj zasób, w którym mają być wyświetlane wyniki w portalu usługi Application Insights. (Zazwyczaj najlepiej jest utworzyć nowy zasób. Wybierz istniejący zasób, jeśli masz już [testy sieci Web][availability] lub [monitorowanie klienta][client] dla tej aplikacji). 

    ![Wybór aplikacji i zasobu.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Uruchom ponownie usługi IIS.

    ![Wybór opcji Uruchom ponownie w górnej części okna dialogowego.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Działanie usługi sieci Web zostanie na krótko przerwane.

## <a name="customize-monitoring-options"></a>Dostosowywanie opcji monitorowania

Włączenie usługi Application Insights powoduje dodanie plików DLL i pliku ApplicationInsights.config do aplikacji internetowej. Możesz [edytować plik config](./configuration-with-applicationinsights-config.md), aby zmienić niektóre opcje.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Po ponownym opublikowaniu aplikacji ponownie włącz usługę Application Insights

Przed ponownym opublikowaniem aplikacji rozważ [dodanie usługi Application Insights do kodu w programie Visual Studio][greenbrown]. Uzyskasz bardziej szczegółowe dane telemetryczne oraz możliwość zapisywania niestandardowych danych telemetrycznych.

Jeśli chcesz ponownie przeprowadzić publikację bez dodawania usługi Application Insights do kodu, pamiętaj, że proces wdrażania może spowodować usunięcie plików DLL i pliku Application Insights z opublikowanej witryny sieci Web. Zatem:

1. Jeśli edytowano plik ApplicationInsights.config, utwórz jego kopię przed ponownym opublikowaniem aplikacji.
2. Ponownie opublikuj aplikację.
3. Ponownie włącz monitorowanie za pomocą usługi Application Insights. (Użyj odpowiedniej metody: panelu sterowania aplikacji internetowej platformy Azure lub monitora stanu na hoście usług IIS).
4. Przywróć wszystkie zmiany wprowadzone w pliku config.


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="confirm-a-valid-installation"></a>Potwierdź prawidłową instalację 

Oto kilka kroków, które można wykonać, aby upewnić się, że instalacja zakończyła się pomyślnie.

- Upewnij się, że plik applicationInsights.config znajduje się w docelowym katalogu aplikacji i zawiera iKey.

- Jeśli podejrzewasz, że brakuje danych, możesz uruchomić zapytanie w [analizie](../logs/log-analytics-tutorial.md) , aby wyświetlić listę wszystkich ról w chmurze aktualnie wysyłających dane telemetryczne.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Jeśli musisz potwierdzić, że Application Insights został pomyślnie dołączony, możesz uruchomić [uchwyt Sysinternals](/sysinternals/downloads/handle) w oknie polecenia, aby potwierdzić, że applicationinsights.dll został załadowany przez usługi IIS.

  ```console
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Nie można nawiązać połączenia? Brak telemetrii?

* Aby umożliwić działanie monitora stanu, na zaporze serwera otwórz [wymagane porty wychodzące](./ip-addresses.md#outgoing-ports).

### <a name="unable-to-login"></a>Nie można zalogować

Jeśli monitor stanu nie może się zalogować, zamiast tego należy zainstalować wiersz polecenia. Monitor stanu próbuje zalogować się, aby zebrać iKey, ale można je podać ręcznie przy użyciu polecenia:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Nie można załadować pliku lub zestawu "System. Diagnostics. DiagnosticSource"

Ten błąd może wystąpić po włączeniu Application Insights. Wynika to z faktu, że Instalator zastępuje tę bibliotekę DLL w katalogu bin.
Aby naprawić web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

[Tutaj](https://github.com/MohanGsk/ApplicationInsights-Home)śledzimy ten problem.


### <a name="application-diagnostic-messages"></a>Komunikaty diagnostyczne aplikacji

* Otwórz monitor stanu i wybierz swoją aplikację w lewym okienku. Sprawdź, czy w sekcji „Powiadomienia konfiguracyjne” występują komunikaty diagnostyczne dotyczące tej aplikacji:

  ![Otwórz blok Wydajność, aby zobaczyć żądanie, czas odpowiedzi, zależności i inne dane](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Szczegółowe dzienniki

* Domyślnie monitor stanu dane wyjściowe dzienników diagnostycznych znajdują się w: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Aby wyprowadzić pełne dzienniki, zmodyfikuj plik konfiguracji: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` i Dodaj `<add key="TraceLevel" value="All" />` do `appsettings` .
Następnie uruchom ponownie Monitor stanu.

* Jako monitor stanu jest aplikacją platformy .NET, można również włączyć [śledzenie .NET, dodając odpowiednią diagnostykę do pliku konfiguracji](/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). Na przykład w niektórych scenariuszach może być przydatne, aby zobaczyć, co dzieje się na poziomie sieci przez [skonfigurowanie śledzenia sieci](/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Niewystarczające uprawnienia
  
* Jeśli na serwerze zostanie wyświetlony komunikat o „niewystarczających uprawnieniach”, spróbuj wykonać następujące kroki:
  * W Menedżerze usług IIS wybierz pulę aplikacji, otwórz **Ustawienia zaawansowane** i zapamiętaj tożsamość w obszarze **Model procesu**.
  * W panelu sterowania Zarządzanie komputerem dodaj tę tożsamość do grupy Użytkownicy monitora wydajności.

### <a name="conflict-with-systems-center-operations-manager"></a>Konflikt z centrum systemów Operations Manager

* Jeśli na serwerze jest zainstalowany agent MMA/SCOM (Systems Center Operations Manager), niektóre wersje mogą powodować konflikt. Odinstaluj oprogramowanie SCOM i monitor stanu, a następnie ponownie zainstaluj najnowsze wersje.

### <a name="failed-or-incomplete-installation"></a>Instalacja zakończona niepowodzeniem lub nieukończona

Jeśli monitor stanu nie powiedzie się w trakcie instalacji, możesz pozostać z niekompletną instalacją, której monitor stanu nie można odzyskać z programu. Spowoduje to wymaganie resetowania ręcznego.

Usuń dowolne z tych plików znajdujących się w katalogu aplikacji:
- Wszystkie biblioteki DLL w katalogu bin zaczynają się od "Microsoft.AI". lub "Microsoft. ApplicationInsights.".
- Ta biblioteka DLL w katalogu bin "Microsoft.Web.Infrastructure.dll"
- Ta biblioteka DLL w katalogu bin "System.Diagnostics.DiagnosticSource.dll"
- W katalogu aplikacji Usuń "App_Data \packages"
- W katalogu aplikacji Usuń "applicationinsights.config"


### <a name="additional-troubleshooting"></a>Dodatkowe procedury rozwiązywania problemów

* Zobacz dodatkowe [Rozwiązywanie problemów][qna].

## <a name="system-requirements"></a>Wymagania systemowe
Serwerowe systemy operacyjne obsługiwane przez monitor stanu usługi Application Insights:

* Windows Server 2008
* Windows Server 2008 z dodatkiem R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

z najnowszymi SP i .NET Framework 4,5 (monitor stanu jest oparta na tej wersji platformy)

Po stronie klienta: systemy Windows 7, 8, 8.1 i 10, również z programem .NET Framework 4.5

Obsługiwane wersje usług IIS: 7, 7.5, 8, 8.5 (usługi IIS są wymagane)

## <a name="automation-with-powershell"></a>Automatyzacja przy użyciu programu PowerShell
Monitorowanie można uruchomić i zatrzymać przy użyciu programu PowerShell na serwerze usług IIS.

Najpierw zaimportuj moduł Application Insights:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
```

Dowiedz się, które aplikacje są monitorowane:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` Nazwa aplikacji internetowej (opcjonalnie).
* Wyświetla stan monitorowania usługi Application Insights dla każdej (lub wskazanej z nazwy) aplikacji internetowej na tym serwerze usług IIS.
* Zwraca obiekt `ApplicationInsightsApplication` dla każdej aplikacji:

  * `SdkState==EnabledAfterDeployment`: aplikacja jest monitorowana, a instrumentacja została przeprowadzona w czasie wykonywania za pomocą narzędzia Monitor stanu lub polecenia `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: nie ma instrumentacji aplikacji dla usługi Application Insights. Instrumentacja nie została nigdy przeprowadzona albo monitorowanie czasu wykonywania zostało wyłączone za pomocą monitora stanu lub polecenia `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: wykonano instrumentację aplikacji przez dodanie zestawu SDK do kodu źródłowego. Nie można zaktualizować ani zatrzymać tego zestawu SDK.
  * Zestaw `SdkVersion` wyświetla wersję używaną do monitorowania tej aplikacji.
  * Zestaw `LatestAvailableSdkVersion` wyświetla wersję aktualnie dostępną w galerii NuGet. Aby uaktualnić aplikację do tej wersji, użyj polecenia `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` Nazwa aplikacji w usługach IIS
* `-InstrumentationKey` Klucz instrumentacji zasobu usługi Application Insights, w którym mają być wyświetlane wyniki.
* To polecenie cmdlet dotyczy tylko aplikacji, dla których nie przeprowadzono jeszcze instrumentacji (czyli SdkState == NotInstrumented).

    To polecenie cmdlet nie ma wpływu na aplikację, dla której przeprowadzono już instrumentację. Nie ma znaczenia, czy instrumentacji aplikacji dokonano w czasie kompilacji przez dodanie zestawu SDK do kodu, czy w czasie wykonywania przez wcześniejsze użycie tego polecenia cmdlet.

    Wersja zestawu SDK użyta do instrumentacji aplikacji to wersja, która została ostatnio pobrana na ten serwer.

    Aby pobrać najnowszą wersję, użyj polecenia cmdlet Update-ApplicationInsightsVersion.
* Zwraca obiekt `ApplicationInsightsApplication` w przypadku powodzenia. W razie niepowodzenia rejestruje ślad do stderr.

   ```output
   Name                      : Default Web Site/WebApp1
   InstrumentationKey        : 00000000-0000-0000-0000-000000000000
   ProfilerState             : ApplicationInsights
   SdkState                  : EnabledAfterDeployment
   SdkVersion                : 1.2.1
   LatestAvailableSdkVersion : 1.2.3
   ```

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Nazwa aplikacji w usługach IIS
* `-All` Zatrzymuje monitorowanie wszystkich aplikacji na tym serwerze usług IIS, dla których `SdkState==EnabledAfterDeployment`
* Zatrzymuje monitorowanie określonych aplikacji i usuwa instrumentację. Działa to tylko w przypadku aplikacji, które zostały zinstrumentowane w czasie wykonywania za pomocą monitora stanu lub polecenia cmdlet Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Zwraca obiekt ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: nazwa aplikacji internetowej w usługach IIS.
* `-InstrumentationKey` (Opcjonalnie). Służy do zmiany zasobu, do którego jest wysyłana Telemetria aplikacji.
* To polecenie cmdlet:
  * Uaktualnia wskazaną aplikację do ostatniej wersji zestawu SDK pobranej na ten komputer. (Działa tylko wtedy, gdy `SdkState==EnabledAfterDeployment`)
  * Jeśli zostanie wprowadzony klucz instrumentacji, wskazana aplikacja jest konfigurowana ponownie do wysłania telemetrii do zasobu dotyczącego tego klucza. (Działa, jeśli `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Pobiera na serwer najnowszy zestaw SDK usługi Application Insights.

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Pytania dotyczące monitora stanu

### <a name="what-is-status-monitor"></a>Co to jest monitor stanu?

Aplikacja klasyczna, która jest instalowana na serwerze sieci Web usług IIS. Ułatwia ona instrumentację i konfigurowanie aplikacji internetowych. 

### <a name="when-do-i-use-status-monitor"></a>Kiedy należy używać monitora stanu?

* Do instrumentacji dowolnej aplikacji internetowej uruchamianej na serwerze usług IIS — nawet jeśli już działa.
* Aby włączyć dodatkową telemetrię dla aplikacji internetowych, które zostały [skompilowane przy użyciu zestawu SDK usługi Application Insights](./asp-net.md) w czasie kompilacji. 

### <a name="can-i-close-it-after-it-runs"></a>Czy można zamknąć go po uruchomieniu?

Tak. Możesz zamknąć go po zakończeniu instrumentacji wybranych witryn sieci Web.

Nie zbiera on telemetrii samodzielnie. Po prostu konfiguruje aplikacje internetowe i ustawia niektóre uprawnienia.

### <a name="what-does-status-monitor-do"></a>Co robi monitor stanu?

Po wybraniu aplikacji internetowej do instrumentacji za pomocą monitora stanu:

* Pobiera i umieszcza zestawy Application Insights i ApplicationInsights.config pliku w folderze plików binarnych aplikacji sieci Web.
* Umożliwia profilowanie aparatu CLR w celu gromadzenia wywołań zależności.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Jakiej wersji zestawu SDK Application Insights monitor stanu zainstalować?

Teraz monitor stanu można zainstalować tylko Application Insights SDK w wersji 2,3 lub 2,4. 

Zestaw SDK Application Insights w wersji 2,4 to [Ostatnia wersja do obsługi platformy .net 4,0](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) , która była [EOL stycznia 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). W związku z tym obecnie monitor stanu mogą służyć do instrumentowania aplikacji .NET 4,0. 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Czy monitor stanu należy uruchamiać podczas każdej aktualizacji aplikacji?

Nie, jeśli ponowne wdrażanie odbywa się przyrostowo. 

Jeśli w procesie publikowania wybierzesz opcję usuwania istniejących plików, trzeba będzie ponownie uruchomić monitor stanu w celu skonfigurowania usługi Application Insights.

### <a name="what-telemetry-is-collected"></a>Jakie dane telemetrii są zbierane?

W przypadku aplikacji z instrumentacją tylko w czasie wykonywania za pomocą monitora stanu:

* Żądania HTTP
* Wywołania do zależności
* Wyjątki
* Liczniki wydajności

W przypadku aplikacji już instrumentowanych w czasie kompilacji:

 * Liczniki procesu.
 * Wywołania zależności (.NET 4.5); wartości zwracane w wywołaniach zależności (.NET 4.6).
 * Wartości śladu stosu wyjątków.

[Dowiedz się więcej](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>Pobierz monitor stanu

- Użyj nowego [modułu programu PowerShell](./status-monitor-v2-overview.md)
- Pobieranie i uruchamianie [instalatora Monitor stanu](https://go.microsoft.com/fwlink/?LinkId=506648)
- Lub uruchom [Instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx) i wyszukaj go, aby uzyskać Monitor stanu usługi Application Insights.

## <a name="next-steps"></a><a name="next"></a>Następne kroki

Wyświetlanie telemetrii:

* [Eksplorowanie metryk](../essentials/metrics-charts.md) w celu monitorowania wydajności i użycia
* [Wyszukiwanie zdarzeń i dzienników][diagnostic] w celu diagnozowania problemów
* [Analiza](../logs/log-query-overview.md) dla bardziej zaawansowanych zapytań

Dodawanie kolejnych funkcji telemetrii:

* [Tworzenie testów sieci Web][availability], aby upewnić się, że witryna pozostaje aktywna.
* [Dodawanie telemetrii klienta sieci Web][usage], aby zobaczyć wyjątki pochodzące z kodu strony sieci Web i umożliwić wstawianie wywołań śladu.
* [Dodawanie zestawu SDK usługi Application Insights do kodu][greenbrown], aby móc wstawić ślad i rejestrować wywołania.

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[roles]: ./resources-roles-access-control.md
[usage]: ./javascript.md