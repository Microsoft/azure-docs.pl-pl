---
title: Rozwiązywanie problemów z usługą Azure Application Insights Snapshot Debugger
description: W tym artykule przedstawiono kroki rozwiązywania problemów i informacje ułatwiające deweloperom Włączanie i używanie Snapshot Debugger Application Insights.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: bd83367ae073e03f03188cdf62cb60faaad7ac97
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026457"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Rozwiązywanie problemów z włączaniem Application Insights Snapshot Debugger lub wyświetlania migawek
Jeśli włączono Application Insights Snapshot Debugger dla aplikacji, ale nie widzisz migawek dla wyjątków, możesz użyć tych instrukcji do rozwiązywania problemów.

Może istnieć wiele różnych powodów, dla których nie są generowane migawki. Możesz rozpocząć od uruchomienia kontroli kondycji migawek, aby zidentyfikować niektóre z możliwych częstych przyczyn.

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>Upewnij się, że używasz odpowiedniego punktu końcowego Snapshot Debugger

Obecnie jedyne regiony, które wymagają modyfikacji punktów końcowych, to [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) i [Chiny platformy Azure](/azure/china/resources-developer-guide).

W przypadku App Service i aplikacji korzystających z zestawu SDK Application Insights należy zaktualizować parametry połączenia przy użyciu obsługiwanych zastąpień dla Snapshot Debugger zgodnie z definicją poniżej:

|Właściwość parametrów połączenia    | Chmura dla instytucji rządowych USA | Chmura Chińska |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Aby uzyskać więcej informacji na temat innych zastąpień połączeń, zobacz [dokumentację Application Insights](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

W przypadku aplikacja funkcji należy zaktualizować `host.json` przy użyciu obsługiwanych zastąpień poniżej:

|Właściwość    | Chmura dla instytucji rządowych USA | Chmura Chińska |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Poniżej znajduje się przykład `host.json` zaktualizowanego punktu końcowego agenta chmury dla instytucji rządowych Stanów Zjednoczonych:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>Korzystanie z kontroli kondycji migawek
Kilka typowych problemów powoduje, że migawka Open Debug nie jest wyświetlana. Na przykład przy użyciu przestarzałego Snapshot Collector, osiągnięcie dziennego limitu przekazywania; lub prawdopodobnie migawka nie zajmuje dużo czasu. Użyj sprawdzania kondycji migawek, aby rozwiązać typowe problemy.

Istnieje łącze w okienku wyjątek kompleksowego widoku śledzenia, który umożliwia sprawdzenie kondycji migawek.

![Wprowadź Sprawdzenie kondycji migawek](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktywny interfejs przypominający rozmowę wyszukuje typowe problemy i prowadzi Cię do ich rozwiązania.

![Sprawdzenie kondycji](./media/snapshot-debugger/healthcheck.png)

Jeśli to nie rozwiąże problemu, zapoznaj się z poniższymi krokami ręcznego rozwiązywania problemów.

## <a name="verify-the-instrumentation-key"></a>Weryfikowanie klucza Instrumentacji

Upewnij się, że używasz poprawnego klucza Instrumentacji w opublikowanej aplikacji. Zazwyczaj klucz Instrumentacji jest odczytywany z pliku ApplicationInsights.config. Sprawdź, czy wartość jest taka sama jak klucz Instrumentacji dla zasobu Application Insights widocznego w portalu.

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>Sprawdź ustawienia klienta protokołu TLS/SSL (ASP.NET)

Jeśli masz aplikację ASP.NET, która jest hostowana w Azure App Service lub w usługach IIS na maszynie wirtualnej, może się zdarzyć, że aplikacja nie będzie mogła nawiązać połączenia z usługą Snapshot Debugger ze względu na brak protokołu zabezpieczeń SSL.

[Punkt końcowy Snapshot Debugger wymaga protokołu TLS w wersji 1,2](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). Zestaw protokołów zabezpieczeń protokołu SSL jest jednym z osobliwości włączonych przez wartość httpRuntime targetFramework w sekcji System. Web web.config. Jeśli httpRuntime targetFramework ma wartość 4.5.2 lub Lower, wówczas protokół TLS 1,2 nie jest domyślnie włączony.

> [!NOTE]
> Wartość httpRuntime targetFramework jest niezależna od platformy docelowej używanej podczas kompilowania aplikacji.

Aby sprawdzić to ustawienie, Otwórz plik web.config i Znajdź sekcję system. Web. Upewnij się, że wartość `targetFramework` dla `httpRuntime` jest ustawiona na 4,6 lub wyższą.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> Modyfikacja wartości targetFramework httpRuntime powoduje zmianę osobliwości środowiska uruchomieniowego zastosowanego do aplikacji, co może spowodować inne, subtelne zmiany zachowań. Pamiętaj, aby dokładnie przetestować swoją aplikację po wprowadzeniu tej zmiany. Aby uzyskać pełną listę zmian zgodności, zobacz https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> Jeśli targetFramework ma wartość 4,7 lub wyższą, system Windows określi dostępne protokoły. W Azure App Service jest dostępny protokół TLS 1,2. Jeśli jednak korzystasz z własnej maszyny wirtualnej, może być konieczne włączenie protokołu TLS 1,2 w systemie operacyjnym.

## <a name="preview-versions-of-net-core"></a>Wersje zapoznawcze programu .NET Core
Jeśli używasz wersji zapoznawczej programu .NET Core lub aplikacja odwołuje się do Application Insights SDK bezpośrednio lub pośrednio za pośrednictwem zestawu zależnego, postępuj zgodnie z instrukcjami dotyczącymi [włączania Snapshot debugger dla innych środowisk](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json).

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>Sprawdź stronę stanu rozszerzenia lokacji usług diagnostycznych
Jeśli Snapshot Debugger został włączony w [okienku Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) w portalu, został on włączony przez rozszerzenie lokacji usług diagnostycznych.

> [!NOTE]
> Instalacja bezkodowa Snapshot Debugger Application Insights jest zgodna z zasadami obsługi .NET Core.
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

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uaktualnij do najnowszej wersji pakietu NuGet
W zależności od tego, jak Snapshot Debugger zostało włączone, zapoznaj się z następującymi opcjami:

* Jeśli Snapshot Debugger został włączony [w okienku Application Insights w portalu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), aplikacja powinna mieć już uruchomiony najnowszy pakiet NuGet.

* Jeśli Snapshot Debugger został włączony przez dołączenie pakietu NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , użyj Menedżera pakietów NuGet programu Visual Studio, aby upewnić się, że używasz najnowszej wersji Microsoft. ApplicationInsights. SnapshotCollector.

Aby zapoznać się z najnowszymi aktualizacjami i poprawkami błędów [, zapoznaj się z informacjami o wersji](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>Sprawdź dzienniki obiektu przekazującego

Po utworzeniu migawki plik minizrzutu (. dmp) jest tworzony na dysku. Oddzielny proces obiektu przekazującego tworzy plik minizrzutu i przekazuje go wraz ze wszystkimi skojarzonymi plików PDB, do Application Insights Snapshot Debugger Storage. Po pomyślnym przekazaniu minizrzutu zostanie on usunięty z dysku. Pliki dziennika dla procesu obiektu przekazującego są przechowywane na dysku. W środowisku App Service można znaleźć te dzienniki `D:\Home\LogFiles` . Aby znaleźć te pliki dzienników, użyj App Service witryny zarządzania kudu.

1. Otwórz aplikację App Service w Azure Portal.
2. Wybierz pozycję **Narzędzia zaawansowane** lub wyszukaj ciąg **kudu**.
3. Wybierz pozycję **Przejdź**.
4. W polu listy rozwijanej **konsola debugowania** wybierz pozycję **cmd**.
5. Wybierz pozycję **LogFiles**.

Powinien zostać wyświetlony co najmniej jeden plik o nazwie rozpoczynającej się od `Uploader_` lub `SnapshotUploader_` i `.log` rozszerzeniem. Wybierz odpowiednią ikonę, aby pobrać pliki dziennika lub otworzyć je w przeglądarce.
Nazwa pliku zawiera unikatowy sufiks, który identyfikuje wystąpienie App Service. Jeśli wystąpienie App Service jest hostowane na więcej niż jednym komputerze, istnieją osobne pliki dziennika dla każdej maszyny. Gdy obiektu przekazującego wykrywa nowy plik minizrzutu, jest on zapisywany w pliku dziennika. Oto przykład pomyślnej migawki i przekazania:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Powyższy przykład pochodzi z wersji 1.2.0 pakietu NuGet Microsoft. ApplicationInsights. SnapshotCollector. We wcześniejszych wersjach proces obiektu przekazującego jest wywoływany, `MinidumpUploader.exe` a dziennik jest mniej szczegółowy.

W poprzednim przykładzie klucz Instrumentacji to `c12a605e73c44346a984e00000000000` . Ta wartość powinna być zgodna z kluczem Instrumentacji aplikacji.
Minizrzutu jest skojarzony z migawką o IDENTYFIKATORze `139e411a23934dc0b9ea08a626db16c5` . Tego identyfikatora można użyć później do zlokalizowania skojarzonego rekordu wyjątku w Application Insights analizie.

Obiektu przekazującego skanuje nowe plików PDB co 15 minut. Oto przykład:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

W przypadku aplikacji, które _nie są_ hostowane w App Service, dzienniki obiektu przekazującego są w tym samym folderze co minizrzutów: `%TEMP%\Dumps\<ikey>` (gdzie `<ikey>` jest kluczem Instrumentacji).

## <a name="troubleshooting-cloud-services"></a>Cloud Services rozwiązywania problemów
W Cloud Services domyślny folder tymczasowy może być za mały, aby pomieścić pliki minizrzutu, co prowadzi do utraconych migawek.

Wymagana ilość miejsca zależy od łącznego zestawu roboczego aplikacji oraz liczby współbieżnych migawek.

Zestaw roboczy roli sieci Web 32-bitowej ASP.NET ma zwykle od 200 MB do 500 MB. Zezwalaj na co najmniej dwie współbieżne migawki.

Jeśli na przykład aplikacja korzysta z 1 GB całkowitego zestawu roboczego, należy upewnić się, że istnieje co najmniej 2 GB miejsca na dysku do przechowywania migawek.

Wykonaj następujące kroki, aby skonfigurować rolę usługi w chmurze za pomocą dedykowanego zasobu lokalnego dla migawek.

1. Dodaj nowy zasób lokalny do usługi w chmurze, edytując plik definicji usługi w chmurze (csdef). W poniższym przykładzie zdefiniowano zasób o nazwie `SnapshotStore` o rozmiarze 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Zmodyfikuj kod uruchomienia roli, aby dodać zmienną środowiskową, która wskazuje na `SnapshotStore` zasób lokalny. W przypadku ról procesów roboczych kod powinien zostać dodany do `OnStart` metody roli:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   W przypadku ról sieci Web (ASP.NET) należy dodać kod do metody aplikacji sieci Web `Application_Start` :
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Zaktualizuj plik ApplicationInsights.config roli, aby zastąpić tymczasową lokalizację folderu używaną przez `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Zastępowanie folderu kopii w tle

Po rozpoczęciu Snapshot Collector próbuje znaleźć folder na dysku, który jest odpowiedni do uruchamiania procesu obiektu przekazującego migawek. Wybrany folder jest znany jako folder kopii w tle.

Snapshot Collector sprawdza kilka dobrze znanych lokalizacji, upewniając się, że ma uprawnienia do kopiowania plików binarnych obiektu przekazującego migawek. Używane są następujące zmienne środowiskowe:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- FOLDER
- TEMP

Jeśli nie można znaleźć odpowiedniego folderu, Snapshot Collector zgłasza błąd mówiący, że _nie można znaleźć odpowiedniego folderu kopii w tle "._

Jeśli kopia nie powiedzie się, Snapshot Collector zgłasza `ShadowCopyFailed` błąd.

Jeśli nie można uruchomić obiektu przekazującego, Snapshot Collector zgłasza `UploaderCannotStartFromShadowCopy` błąd. Treść komunikatu często zawiera `System.UnauthorizedAccessException` . Ten błąd jest zwykle spowodowany tym, że aplikacja działa w ramach konta z ograniczonymi uprawnieniami. Konto ma uprawnienia do zapisu w folderze kopii w tle, ale nie ma uprawnień do wykonywania kodu.

Ponieważ te błędy są zwykle wykonywane podczas uruchamiania, zazwyczaj następuje `ExceptionDuringConnect` błąd mówiący, że _nie można uruchomić obiektu przekazującego._

Aby obejść te błędy, można ręcznie określić folder kopii w tle za pomocą `ShadowCopyFolder` opcji konfiguracji. Na przykład przy użyciu ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Lub, jeśli używasz appsettings.jsna platformie .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Użyj wyszukiwania Application Insights, aby znaleźć wyjątki z migawkami

Gdy tworzona jest migawka, zgłaszany wyjątek jest otagowany przy użyciu identyfikatora migawki. Ten identyfikator migawki jest uwzględniany jako właściwość niestandardowa po zgłoszeniu wyjątku do Application Insights. Korzystając z **wyszukiwania** w Application Insights, można znaleźć wszystkie rekordy z `ai.snapshot.id` właściwością niestandardową.

1. Przejdź do zasobu Application Insights w Azure Portal.
2. Wybierz pozycję **Wyszukaj**.
3. Wpisz `ai.snapshot.id` w polu tekstowym Wyszukaj, a następnie naciśnij klawisz ENTER.

![Wyszukaj dane telemetryczne z IDENTYFIKATORem migawki w portalu](./media/snapshot-debugger/search-snapshot-portal.png)

Jeśli to wyszukiwanie nie zwróci żadnych wyników, nie zgłoszono żadnych migawek do Application Insights w wybranym zakresie czasu.

Aby wyszukać konkretny identyfikator migawki z dzienników obiektu przekazującego, wpisz ten identyfikator w polu wyszukiwania. Jeśli nie możesz znaleźć rekordów dla utworzonej migawki, wykonaj następujące kroki:

1. Sprawdź dokładnie, czy oglądasz właściwy Application Insights zasób, sprawdzając klucz Instrumentacji.

2. Używając sygnatury czasowej z dziennika obiektu przekazującego, Dostosuj filtr zakresu czasu wyszukiwania, aby uwzględnić ten zakres czasu.

Jeśli nadal nie widzisz wyjątku z tym IDENTYFIKATORem migawki, rekord wyjątku nie został zgłoszony do Application Insights. Taka sytuacja może wystąpić, jeśli aplikacja uległa awarii po przeprowadzeniu migawki, ale przed zgłoszeniem rekordu wyjątku. W takim przypadku Sprawdź dzienniki App Service w obszarze, `Diagnose and solve problems` Aby sprawdzić, czy wystąpiły nieoczekiwane ponowne uruchomienia lub Nieobsłużone wyjątki.

## <a name="edit-network-proxy-or-firewall-rules"></a>Edytowanie sieci proxy lub reguł zapory

Jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy lub zapory, może być konieczne zaktualizowanie reguł w celu komunikowania się z usługą Snapshot Debugger.

Adresy IP używane przez Application Insights Snapshot Debugger są zawarte w tagu usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą tagów usług](../../virtual-network/service-tags-overview.md).