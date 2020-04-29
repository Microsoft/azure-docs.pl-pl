---
title: Rozwiązywanie problemów z usługą Azure Application Insights Snapshot Debugger
description: W tym artykule przedstawiono kroki rozwiązywania problemów oraz informacje pomocne w przypadku deweloperów, którzy mają problemy z włączaniem lub używaniem Snapshot Debugger Application Insights.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671413"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a>Rozwiązywanie problemów z włączaniem Application Insights Snapshot Debugger lub wyświetlania migawek
Jeśli włączono Application Insights Snapshot Debugger dla aplikacji, ale nie widzisz migawek dla wyjątków, możesz użyć tych instrukcji do rozwiązywania problemów. Może istnieć wiele różnych powodów, dla których migawki nie są generowane. Możesz uruchomić kontrolę kondycji migawek, aby zidentyfikować niektóre z możliwych częstych przyczyn.

## <a name="use-the-snapshot-health-check"></a>Korzystanie z kontroli kondycji migawek
Kilka typowych problemów powoduje, że migawka Open Debug nie jest wyświetlana. Na przykład przy użyciu przestarzałego Snapshot Collector, osiągnięcie dziennego limitu przekazywania; lub prawdopodobnie migawka nie zajmuje dużo czasu. Użyj sprawdzania kondycji migawek, aby rozwiązać typowe problemy.

Istnieje łącze w okienku wyjątek kompleksowego widoku śledzenia, który umożliwia sprawdzenie kondycji migawek.

![Wprowadź Sprawdzenie kondycji migawek](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktywny interfejs przypominający rozmowę wyszukuje typowe problemy i prowadzi Cię do ich rozwiązania.

![Sprawdzenie kondycji](./media/snapshot-debugger/healthcheck.png)

Jeśli to nie rozwiąże problemu, zapoznaj się z poniższymi krokami ręcznego rozwiązywania problemów.

## <a name="verify-the-instrumentation-key"></a>Weryfikowanie klucza Instrumentacji

Upewnij się, że używasz poprawnego klucza Instrumentacji w opublikowanej aplikacji. Zazwyczaj klucz Instrumentacji jest odczytywany z pliku ApplicationInsights. config. Sprawdź, czy wartość jest taka sama jak klucz Instrumentacji dla zasobu Application Insights widocznego w portalu.

## <a name="preview-versions-of-net-core"></a>Wersje zapoznawcze programu .NET Core
Jeśli aplikacja korzysta z wersji zapoznawczej programu .NET Core, a Snapshot Debugger została włączona za pośrednictwem [okienka Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) w portalu, Snapshot Debugger mogą nie zostać uruchomione. Postępuj zgodnie z instrukcjami w temacie [Enable Snapshot Debugger w innych środowiskach](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , aby w pierwszej kolejności uwzględnić pakiet NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) z aplikacją, która jest ***również włączona w*** [okienku Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uaktualnij do najnowszej wersji pakietu NuGet

Jeśli Snapshot Debugger został włączony [w okienku Application Insights w portalu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), aplikacja powinna mieć już uruchomiony najnowszy pakiet NuGet. Jeśli Snapshot Debugger został włączony przez dołączenie pakietu NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , użyj Menedżera pakietów NuGet programu Visual Studio, aby upewnić się, że używasz najnowszej wersji Microsoft. ApplicationInsights. SnapshotCollector. Informacje o wersji można znaleźć pod adresemhttps://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Sprawdź dzienniki obiektu przekazującego

Po utworzeniu migawki plik minizrzutu (. dmp) jest tworzony na dysku. Oddzielny proces obiektu przekazującego tworzy plik minizrzutu i przekazuje go wraz ze wszystkimi skojarzonymi plików PDB, do Application Insights Snapshot Debugger Storage. Po pomyślnym przekazaniu minizrzutu zostanie on usunięty z dysku. Pliki dziennika dla procesu obiektu przekazującego są przechowywane na dysku. W środowisku App Service można znaleźć te dzienniki `D:\Home\LogFiles`. Aby znaleźć te pliki dzienników, użyj App Service witryny zarządzania kudu.

1. Otwórz aplikację App Service w Azure Portal.
2. Kliknij przycisk **Narzędzia zaawansowane**lub Wyszukaj pozycję **kudu**.
3. Kliknij pozycję **Przejdź**.
4. W polu listy rozwijanej **konsola debugowania** wybierz pozycję **cmd**.
5. Kliknij pozycję **LogFiles**.

Powinien zostać wyświetlony co najmniej jeden plik o nazwie rozpoczynającej się od `Uploader_` lub `SnapshotUploader_` i `.log` rozszerzeniem. Kliknij odpowiednią ikonę, aby pobrać pliki dziennika lub otworzyć je w przeglądarce.
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
> Powyższy przykład pochodzi z wersji 1.2.0 pakietu NuGet Microsoft. ApplicationInsights. SnapshotCollector. We wcześniejszych wersjach proces obiektu przekazującego jest wywoływany `MinidumpUploader.exe` , a dziennik jest mniej szczegółowy.

W poprzednim przykładzie klucz Instrumentacji to `c12a605e73c44346a984e00000000000`. Ta wartość powinna być zgodna z kluczem Instrumentacji aplikacji.
Minizrzutu jest skojarzony z migawką o IDENTYFIKATORze `139e411a23934dc0b9ea08a626db16c5`. Tego identyfikatora można użyć później, aby zlokalizować skojarzoną telemetrię wyjątku w Application Insights analizie.

Obiektu przekazującego skanuje nowe plików PDB co 15 minut. Przykład:

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

W przypadku aplikacji, które _nie są_ hostowane w App Service, dzienniki obiektu przekazującego są w tym samym folderze co `%TEMP%\Dumps\<ikey>` minizrzutów: `<ikey>` (gdzie jest kluczem Instrumentacji).

## <a name="troubleshooting-cloud-services"></a>Cloud Services rozwiązywania problemów
W przypadku ról w Cloud Services domyślny folder tymczasowy może być za mały, aby pomieścić pliki minizrzutu, co prowadzi do utraconych migawek.
Wymagana ilość miejsca zależy od łącznego zestawu roboczego aplikacji oraz liczby współbieżnych migawek.
Zestaw roboczy roli sieci Web 32-bitowej ASP.NET ma zwykle od 200 MB do 500 MB.
Zezwalaj na co najmniej dwie współbieżne migawki.
Jeśli na przykład aplikacja korzysta z 1 GB całkowitego zestawu roboczego, należy upewnić się, że istnieje co najmniej 2 GB miejsca na dysku do przechowywania migawek.
Wykonaj następujące kroki, aby skonfigurować rolę usługi w chmurze za pomocą dedykowanego zasobu lokalnego dla migawek.

1. Dodaj nowy zasób lokalny do usługi w chmurze, edytując plik definicji usługi w chmurze (csdef). W poniższym przykładzie zdefiniowano zasób o `SnapshotStore` nazwie o rozmiarze 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Zmodyfikuj kod uruchomienia roli, aby dodać zmienną środowiskową, która wskazuje na zasób `SnapshotStore` lokalny. W przypadku ról procesów roboczych kod powinien zostać dodany do `OnStart` metody roli:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   W przypadku ról sieci Web (ASP.NET) należy dodać kod do `Application_Start` metody aplikacji sieci Web:
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

3. Zaktualizuj plik ApplicationInsights. config roli w celu przesłaniania tymczasowej lokalizacji folderu używanej przez program`SnapshotCollector`
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

Jeśli nie można znaleźć odpowiedniego folderu, Snapshot Collector zgłasza błąd mówiący _"nie można znaleźć odpowiedniego folderu kopii w tle"._

Jeśli kopia nie powiedzie się, Snapshot Collector `ShadowCopyFailed` zgłasza błąd.

Jeśli nie można uruchomić obiektu przekazującego, Snapshot Collector zgłasza `UploaderCannotStartFromShadowCopy` błąd. Treść komunikatu często zawiera `System.UnauthorizedAccessException`. Ten błąd jest zwykle spowodowany tym, że aplikacja działa w ramach konta z ograniczonymi uprawnieniami. Konto ma uprawnienia do zapisu w folderze kopii w tle, ale nie ma uprawnień do wykonywania kodu.

Ponieważ te błędy są zwykle wykonywane podczas uruchamiania, zazwyczaj następuje `ExceptionDuringConnect` błąd mówiący, że _nie można uruchomić obiektu przekazującego._

Aby obejść te błędy, można ręcznie określić folder kopii w tle za pomocą opcji `ShadowCopyFolder` konfiguracji. Na przykład przy użyciu pliku ApplicationInsights. config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Lub, jeśli używasz pliku appSettings. JSON z aplikacją .NET Core:

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

Gdy tworzona jest migawka, zgłaszany wyjątek jest otagowany przy użyciu identyfikatora migawki. Ten identyfikator migawki jest uwzględniany jako właściwość niestandardowa, gdy dane telemetryczne wyjątku są zgłaszane do Application Insights. Korzystając z **wyszukiwania** w Application Insights, można znaleźć wszystkie dane telemetryczne z właściwością `ai.snapshot.id` niestandardową.

1. Przejdź do zasobu Application Insights w Azure Portal.
2. Kliknij przycisk **Wyszukaj**.
3. Wpisz `ai.snapshot.id` w polu tekstowym Wyszukaj, a następnie naciśnij klawisz ENTER.

![Wyszukaj dane telemetryczne z IDENTYFIKATORem migawki w portalu](./media/snapshot-debugger/search-snapshot-portal.png)

Jeśli to wyszukiwanie nie zwróci żadnych wyników, nie zgłoszono żadnych migawek do Application Insights dla aplikacji w wybranym zakresie czasu.

Aby wyszukać konkretny identyfikator migawki z dzienników obiektu przekazującego, wpisz ten identyfikator w polu wyszukiwania. Jeśli nie możesz znaleźć danych telemetrycznych dla utworzonej migawki, wykonaj następujące kroki:

1. Sprawdź dokładnie, czy oglądasz właściwy Application Insights zasób, sprawdzając klucz Instrumentacji.

2. Używając sygnatury czasowej z dziennika obiektu przekazującego, Dostosuj filtr zakresu czasu wyszukiwania, aby uwzględnić ten zakres czasu.

Jeśli nadal nie widzisz wyjątku z tym IDENTYFIKATORem migawki, dane telemetryczne wyjątku nie zostały zgłoszone do Application Insights. Taka sytuacja może wystąpić, jeśli aplikacja uległa awarii po przeprowadzeniu migawki, ale przed zgłoszeniem telemetrii wyjątku. W takim przypadku Sprawdź dzienniki App Service w obszarze `Diagnose and solve problems` , aby sprawdzić, czy wystąpiły nieoczekiwane ponowne uruchomienia lub Nieobsłużone wyjątki.

## <a name="edit-network-proxy-or-firewall-rules"></a>Edytowanie sieci proxy lub reguł zapory

Jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy lub zapory, może być konieczne edytowanie reguł, aby umożliwić aplikacji komunikację z usługą Snapshot Debugger. Adresy IP używane przez Snapshot Debugger są zawarte w tagu usługi Azure Monitor.
