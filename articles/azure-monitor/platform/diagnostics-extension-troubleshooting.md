---
title: Rozwiązywanie problemów Diagnostyka Azure rozszerzenia
description: Rozwiązywanie problemów podczas korzystania z diagnostyki platformy Azure na platformie Azure Virtual Machines, Service Fabric lub Cloud Services.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: de42a70cf2950aca3dbe151407671306c793ed10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86515499"
---
# <a name="azure-diagnostics-troubleshooting"></a>Rozwiązywanie problemów za pomocą Diagnostyki Azure
W tym artykule opisano Rozwiązywanie problemów związanych z używaniem Diagnostyka Azure. Aby uzyskać więcej informacji na temat diagnostyki platformy Azure, zobacz [omówienie Diagnostyka Azure](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Składniki logiczne
**Uruchamianie wtyczki diagnostyki (DiagnosticsPluginLauncher.exe)**: uruchamia rozszerzenie Diagnostyka Azure. Służy jako proces punktu wejścia.

**Wtyczka diagnostyki (DiagnosticsPlugin.exe)**: konfiguruje, uruchamia i zarządza okresem istnienia agenta monitorowania. Jest to główny proces uruchamiany przez program uruchamiający.

**Agent monitorowania ( \* procesy MonAgent. exe)**: monitoruje, zbiera i przesyła dane diagnostyczne.  

## <a name="logartifact-paths"></a>Ścieżki dzienników/artefaktów
Poniżej przedstawiono ścieżki do ważnych dzienników i artefaktów. Te informacje są używane w pozostałej części dokumentu.

### <a name="azure-cloud-services"></a>usług Azure Cloud Services
| Artefakt | Ścieżka |
| --- | --- |
| **Plik konfiguracji Diagnostyka Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics \<version>\Config.txt |
| **Pliki dziennika** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Magazyn lokalny dla danych diagnostycznych** | Folderze c:\resources\directory \<CloudServiceDeploymentID> . \<RoleName> . DiagnosticStore\WAD0107\Tables |
| **Plik konfiguracyjny monitorowania agenta** | Folderze c:\resources\directory \<CloudServiceDeploymentID> . \<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Diagnostyka Azure pakiet rozszerzenia** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Ścieżka narzędzia do zbierania dzienników** | %SystemDrive%\Packages\GuestAgent\ |
| **Plik dziennika MonAgentHost** | Folderze c:\resources\directory \<CloudServiceDeploymentID> . \<RoleName> . DiagnosticStore\WAD0107\Configuration\MonAgentHost. <seq_num>. log |

### <a name="virtual-machines"></a>Maszyny wirtualne
| Artefakt | Ścieżka |
| --- | --- |
| **Plik konfiguracji Diagnostyka Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \RuntimeSettings |
| **Pliki dziennika** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Magazyn lokalny dla danych diagnostycznych** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Tables |
| **Plik konfiguracyjny monitorowania agenta** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Plik stanu** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \Status |
| **Diagnostyka Azure pakiet rozszerzenia** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Ścieżka narzędzia do zbierania dzienników** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Plik dziennika MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Configuration\MonAgentHost. <seq_num>. log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Dane metryk nie są wyświetlane w witrynie Azure Portal
Diagnostyka Azure udostępnia dane metryk, które mogą być wyświetlane w Azure Portal. Jeśli masz problemy z wyświetlaniem danych w portalu, sprawdź tabelę WADMetrics na \* koncie magazynu Diagnostyka Azure, aby sprawdzić, czy odpowiednie rekordy metryk są tam dostępne, i upewnij się, że [dostawca zasobów](../../azure-resource-manager/management/resource-providers-and-types.md) Microsoft. Insights został zarejestrowany.

W tym miejscu **PartitionKey** tabeli jest identyfikatorem zasobu, maszyną wirtualną lub zestawem skalowania maszyn wirtualnych. **RowKey** to nazwa metryki (znana także jako Nazwa licznika wydajności).

Jeśli identyfikator zasobu jest niepoprawny, sprawdź identyfikatory zasobów metryk **konfiguracji** **diagnostyki** ,  >  **Metrics**  >  **ResourceId** aby sprawdzić, czy identyfikator zasobu jest poprawnie ustawiony.

Jeśli nie ma żadnych danych dla określonej metryki, sprawdź **konfigurację diagnostyki**  >  **PerformanceCounter** , aby sprawdzić, czy jest uwzględniona Metryka (licznik wydajności). Domyślnie włączone są następujące liczniki:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ ASP.NET aplikacje (__łącznie__) \ żądania/s
- \ ASP.NET aplikacje (__łącznie__) \Errors łącznie/s
- \ASP.NET\Requests w kolejce
- \ASP.NET\Requests odrzucone
- \Processor (w3wp) — \% czas procesora
- \Process (w3wp) \private bajtów
- \Process (WaIISHost) — \% czas procesora
- \Process (WaIISHost) \private bajtów
- \Process (WaWorkerHost) — \% czas procesora
- \Process (WaWorkerHost) \private bajtów
- \Memory\Page błędy/s
- \.Czas (_globalna_) środowiska CLR \% w GC
- \Dysk logiczny (C:) \Bajty zapisu/s
- \Dysk logiczny (C:) \Bajty bajty odczytu/s
- \Dysk logiczny (D:) \Bajty zapisu/s
- \Dysk logiczny (D:) \Bajty bajty odczytu/s

Jeśli konfiguracja jest poprawnie ustawiona, ale nadal nie będzie można zobaczyć danych metryk, Skorzystaj z poniższych wskazówek, aby pomóc w rozwiązywaniu problemów.


## <a name="azure-diagnostics-is-not-starting"></a>Nie można uruchomić Diagnostyki Azure
Aby uzyskać informacje o tym, dlaczego nie można uruchomić Diagnostyka Azure, zobacz pliki **DiagnosticsPluginLauncher. log** i **DiagnosticsPlugin. log** w lokalizacji plików dziennika, która została dostarczona wcześniej.

Jeśli te dzienniki wskazują `Monitoring Agent not reporting success after launch` , oznacza to, że wystąpił błąd podczas uruchamiania MonAgentHost.exe. Sprawdź dzienniki w lokalizacji wskazanej `MonAgentHost log file` w poprzedniej sekcji.

Ostatni wiersz plików dziennika zawiera kod zakończenia.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Jeśli znajdziesz **negatywny** kod zakończenia, zapoznaj się z [tabelą kod zakończenia](#azure-diagnostics-plugin-exit-codes) w [sekcji odwołania](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dane diagnostyczne nie są rejestrowane w usłudze Azure Storage
Ustal, czy nie są wyświetlane żadne dane, czy pojawiają się pewne dane.

### <a name="diagnostics-infrastructure-logs"></a>Dzienniki infrastruktury diagnostyki
Diagnostyka rejestruje wszystkie błędy w dziennikach infrastruktury diagnostyki. Upewnij się, że [w konfiguracji włączono funkcję przechwytywania dzienników infrastruktury diagnostyki](#how-to-check-diagnostics-extension-configuration). Następnie można szybko wyszukać wszelkie istotne błędy, które pojawiają się w `DiagnosticInfrastructureLogsTable` tabeli na skonfigurowanym koncie magazynu.

### <a name="no-data-is-appearing"></a>Nie są wyświetlane żadne dane
Najczęstszym powodem, że dane zdarzenia nie pojawiają się w ogóle, są zdefiniowane nieprawidłowe informacje o koncie magazynu.

Rozwiązanie: Popraw konfigurację diagnostyki i ponownie zainstaluj diagnostykę.

Jeśli konto magazynu jest poprawnie skonfigurowane, dostęp zdalny do maszyny i sprawdź, czy *DiagnosticsPlugin.exe* i *MonAgentCore.exe* są uruchomione. Jeśli nie są uruchomione, wykonaj kroki opisane w [Diagnostyka Azure nie uruchamia się](#azure-diagnostics-is-not-starting).

Jeśli procesy są uruchomione, przejdź do pozycji [czy dane są przechwytywane lokalnie?](#is-data-getting-captured-locally) i postępuj zgodnie z instrukcjami.

Jeśli to nie rozwiąże problemu, spróbuj wykonać następujące polecenie:

1. Odinstalowywanie agenta
2. Usuń katalog C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Zainstaluj agenta ponownie


### <a name="part-of-the-data-is-missing"></a>Brak części danych
Jeśli otrzymujesz pewne dane, ale nie wszystkie, oznacza to, że potok zbierania/transferu danych jest poprawnie ustawiony. Postępuj zgodnie z podsekcjami tutaj, aby zawęzić problem.

#### <a name="is-the-collection-configured"></a>Czy kolekcja jest skonfigurowana?
Konfiguracja diagnostyki zawiera instrukcje dotyczące określonego typu danych, które mają być zbierane. [Przejrzyj konfigurację](#how-to-check-diagnostics-extension-configuration) , aby upewnić się, że szukasz tylko danych skonfigurowanych dla kolekcji.

#### <a name="is-the-host-generating-data"></a>Czy host generuje dane?
- **Liczniki wydajności**: Otwórz perfmon i sprawdź licznik.

- **Dzienniki śledzenia**: dostęp zdalny do maszyny wirtualnej i Dodawanie TextWriterTraceListener do pliku konfiguracji aplikacji.  Zobacz https://msdn.microsoft.com/library/sk36c28t.aspx , aby skonfigurować odbiornik tekstu.  Upewnij się, że `<trace>` element ma `<trace autoflush="true">` .<br />
Jeśli dzienniki śledzenia nie są wyświetlane, Zobacz więcej informacji na temat brakujących dzienników śledzenia.

- **Ślady ETW**: dostęp zdalny do maszyny wirtualnej i Instalowanie narzędzia PerfView.  W narzędzia PerfView, uruchom **File**  >  **polecenie File User Command**  >  **Listen etwprovder1**  >  **etwprovider2**i tak dalej. Polecenie **Listen** ma wielkość liter i nie może zawierać spacji między listą oddzielonych przecinkami dostawców ETW. Jeśli wykonanie polecenia nie powiedzie się, możesz wybrać przycisk **dziennika** w prawym dolnym rogu narzędzia Narzędzia PerfView, aby zobaczyć, co próbowano uruchomić, oraz jaki był wynik.  Przy założeniu, że dane wejściowe są poprawne, pojawi się nowe okno. W ciągu kilku sekund zaczniesz oglądać ślady ETW.

- **Dzienniki zdarzeń**: dostęp zdalny do maszyny wirtualnej. Otwórz `Event Viewer` program, a następnie upewnij się, że zdarzenia istnieją.

#### <a name="is-data-getting-captured-locally"></a>Czy dane są przechwytywane lokalnie?
Następnie upewnij się, że dane są przechwytywane lokalnie.
Dane są przechowywane lokalnie w `*.tsf` plikach w lokalnym magazynie dla danych diagnostycznych. Różne rodzaje dzienników są zbierane w różnych `.tsf` plikach. Nazwy są podobne do nazw tabel w usłudze Azure Storage.

Na przykład `Performance Counters` Pobierz dane w `PerformanceCountersTable.tsf` . Dzienniki zdarzeń są zbierane w programie `WindowsEventLogsTable.tsf` . Skorzystaj z instrukcji w sekcji [wyodrębnianie dzienników lokalnych](#local-log-extraction) , aby otworzyć lokalne pliki kolekcji i sprawdzić, czy są one widoczne na dysku.

Jeśli nie widzisz dzienników, które są zbierane lokalnie, i już sprawdzono, że host generuje dane, prawdopodobnie występuje problem z konfiguracją. Uważnie Przejrzyj konfigurację.

Zapoznaj się również z konfiguracją wygenerowaną dla MonitoringAgent MaConfig.xml. Sprawdź, czy istnieje sekcja opisująca odpowiednie źródło dziennika. Następnie sprawdź, czy nie jest on tracony podczas tłumaczenia między konfiguracją diagnostyki a konfiguracją agenta monitorowania.

#### <a name="is-data-getting-transferred"></a>Czy dane są transferowane?
Jeśli sprawdzono, że dane są przechwytywane lokalnie, ale nadal nie są wyświetlane na koncie magazynu, wykonaj następujące czynności:

- Sprawdź, czy podano poprawne konto magazynu i czy nie zostały wycofane klucze dla danego konta magazynu. W przypadku usługi Azure Cloud Services czasami widzimy, że osoby nie są aktualizowane `useDevelopmentStorage=true` .

- Sprawdź, czy podane konto magazynu jest poprawne. Upewnij się, że nie masz ograniczeń sieci, które uniemożliwiają składnikom osiągnięcie publicznych punktów końcowych magazynu. Jednym ze sposobów jest uzyskanie dostępu zdalnego do maszyny, a następnie próba napisania na tym samym koncie magazynu.

- Na koniec możesz sprawdzić, jakie błędy są zgłaszane przez agenta monitorowania. Agent monitorowania zapisuje swoje dzienniki w `maeventtable.tsf` lokalizacji, która znajduje się w lokalnym magazynie dla danych diagnostycznych. Aby otworzyć ten plik, postępuj zgodnie z instrukcjami w sekcji [wyodrębnianie dzienników lokalnych](#local-log-extraction) . Następnie spróbuj określić, czy istnieją `errors` błędy odczytu do plików lokalnych zapisu do magazynu.

### <a name="capturing-and-archiving-logs"></a>Przechwytywanie i archiwizowanie dzienników
Jeśli myślisz o kontaktowanie się z pomocą techniczną, w pierwszej kolejności mogą wystąpić monity o zebranie dzienników z komputera. Możesz zaoszczędzić czas, wykonując te czynności. Uruchom `CollectGuestLogs.exe` Narzędzie w ścieżce narzędzia do zbierania dzienników. Generuje plik. zip ze wszystkimi odpowiednimi dziennikami platformy Azure w tym samym folderze.

## <a name="diagnostics-data-tables-not-found"></a>Nie znaleziono tabel danych diagnostycznych
Tabele w usłudze Azure Storage, które przechowują zdarzenia ETW, są nazwane przy użyciu następującego kodu:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Oto przykład:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Ten kod generuje cztery tabele:

| Wydarzenie | Nazwa tabeli |
| --- | --- |
| Provider = "prov1" &lt; Identyfikator zdarzenia = "1"/&gt; |WADEvent + MD5 ("prov1") + "1" |
| Provider = "prov1" &lt; Identyfikator zdarzenia = "2" eventDestination = "dest1"/&gt; |WADdest1 |
| Provider = "prov1" &lt; DefaultEvents/&gt; |WADDefault + MD5 ("prov1") |
| Provider = "prov2" &lt; DefaultEvents eventDestination = "dest2"/&gt; |WADdest2 |

## <a name="references"></a>Odwołania

### <a name="how-to-check-diagnostics-extension-configuration"></a>Jak sprawdzić konfigurację rozszerzenia diagnostyki
Najprostszym sposobem sprawdzenia konfiguracji rozszerzenia jest przejście do [Azure Resource Explorer](https://resources.azure.com), a następnie przejście do maszyny wirtualnej lub usługi w chmurze, w której znajduje się rozszerzenie Diagnostyka Azure (IaaSDiagnostics/PaaDiagnostics).

Możesz również przyjrzeć się pulpitowi zdalnemu do maszyny i sprawdzić Diagnostyka Azure pliku konfiguracji, który opisano w sekcji ścieżka artefaktów dziennika.

W obu przypadkach Wyszukaj pozycję **Microsoft. Azure. Diagnostics**, a następnie pole **xmlCfg** lub **WadCfg** .

Jeśli szukasz maszyny wirtualnej, a pole **WadCfg** jest obecne, oznacza to, że konfiguracja jest w formacie JSON. Jeśli pole **xmlCfg** jest obecne, oznacza to, że konfiguracja jest w formacie XML i jest zakodowana w formacie base64. Należy [zdekodować ją](https://www.bing.com/search?q=base64+decoder) , aby zobaczyć kod XML, który został załadowany przez diagnostykę.

W przypadku roli usługi w chmurze w przypadku wybrania konfiguracji z dysku dane są kodowane algorytmem Base64, więc należy [je zdekodować](https://www.bing.com/search?q=base64+decoder) w celu wyświetlenia kodu XML, który został załadowany przez diagnostykę.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Kody zakończenia wtyczki Diagnostyka Azure
Wtyczka zwraca następujące kody zakończenia:

| Kod zakończenia | Opis |
| --- | --- |
| 0 |Powodzenie. |
| -1 |Błąd rodzajowy. |
| -2 |Nie można załadować pliku RCF.<p>Ten błąd wewnętrzny powinien wystąpić tylko w przypadku ręcznego wywołania programu uruchamiającego wtyczki agenta gościa na maszynie wirtualnej. |
| –3 |Nie można załadować pliku konfiguracji diagnostyki.<p><p>Rozwiązanie: spowodowane przez plik konfiguracji, który nie przekazuje walidacji schematu. Rozwiązanie ma dostarczyć plik konfiguracji, który jest zgodny ze schematem. |
| -4 |Inne wystąpienie diagnostyki agenta monitorowania korzysta już z lokalnego katalogu zasobów.<p><p>Rozwiązanie: Określ inną wartość dla **LocalResourceDirectory**. |
| -6 |Program uruchamiający wtyczki agenta gościa podjął próbę uruchomienia diagnostyki z nieprawidłowym wierszem polecenia.<p><p>Ten błąd wewnętrzny powinien wystąpić tylko w przypadku ręcznego wywołania programu uruchamiającego wtyczki agenta gościa na maszynie wirtualnej. |
| -10 |Wtyczka diagnostyki zakończyła działanie, gdy wystąpił nieobsługiwany wyjątek. |
| -11 |Agent gościa nie mógł utworzyć procesu odpowiedzialnego za uruchamianie i monitorowanie agenta monitorowania.<p><p>Rozwiązanie: Sprawdź, czy w celu uruchomienia nowych procesów są dostępne wystarczające zasoby systemowe.<p> |
| -101 |Nieprawidłowe argumenty podczas wywoływania wtyczki diagnostyki.<p><p>Ten błąd wewnętrzny powinien wystąpić tylko w przypadku ręcznego wywołania programu uruchamiającego wtyczki agenta gościa na maszynie wirtualnej. |
| -102 |Proces wtyczki nie może zainicjować samego siebie.<p><p>Rozwiązanie: Sprawdź, czy w celu uruchomienia nowych procesów są dostępne wystarczające zasoby systemowe. |
| -103 |Proces wtyczki nie może zainicjować samego siebie. W odróżnieniu od tego nie można utworzyć obiektu rejestratora.<p><p>Rozwiązanie: Sprawdź, czy w celu uruchomienia nowych procesów są dostępne wystarczające zasoby systemowe. |
| -104 |Nie można załadować pliku RCF dostarczonego przez agenta gościa.<p><p>Ten błąd wewnętrzny powinien wystąpić tylko w przypadku ręcznego wywołania programu uruchamiającego wtyczki agenta gościa na maszynie wirtualnej. |
| -105 |Wtyczka diagnostyki nie może otworzyć pliku konfiguracji diagnostyki.<p><p>Ten błąd wewnętrzny powinien wystąpić tylko wtedy, gdy wtyczka diagnostyki zostanie niepoprawnie wywołana na maszynie wirtualnej. |
| -106 |Nie można odczytać pliku konfiguracji diagnostyki.<p><p>Spowodowane przez plik konfiguracji, który nie przekazuje walidacji schematu. <br><br>Rozwiązanie: podaj plik konfiguracji, który jest zgodny ze schematem. Aby uzyskać więcej informacji, zobacz [Jak sprawdzić konfigurację rozszerzenia diagnostyki](#how-to-check-diagnostics-extension-configuration). |
| -107 |Katalog zasobów przekazywany do agenta monitorowania jest nieprawidłowy.<p><p>Ten błąd wewnętrzny powinien wystąpić tylko wtedy, gdy Agent monitorowania jest niepoprawnie wywoływany ręcznie na maszynie wirtualnej.</p> |
| -108 |Nie można przekonwertować pliku konfiguracji diagnostyki na plik konfiguracyjny agenta monitorowania.<p><p>Ten błąd wewnętrzny powinien wystąpić tylko wtedy, gdy wtyczka diagnostyki zostanie wywołana ręcznie przy użyciu nieprawidłowego pliku konfiguracji. |
| -110 |Ogólny błąd konfiguracji diagnostyki.<p><p>Ten błąd wewnętrzny powinien wystąpić tylko wtedy, gdy wtyczka diagnostyki zostanie wywołana ręcznie przy użyciu nieprawidłowego pliku konfiguracji. |
| -111 |Nie można uruchomić agenta monitorowania.<p><p>Rozwiązanie: Sprawdź, czy są dostępne wystarczające zasoby systemowe. |
| -112 |Błąd ogólny |

### <a name="local-log-extraction"></a>Wyodrębnianie dzienników lokalnych
Agent monitorowania zbiera dzienniki i artefakty jako `.tsf` pliki. `.tsf`Plik nie jest czytelny, ale można go przekonwertować na `.csv` następujący sposób:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Nowy plik o nazwie `<relevantLogFile>.csv` jest tworzony w tej samej ścieżce co odpowiedni `.tsf` plik.

>[!NOTE]
> Wystarczy uruchomić to narzędzie tylko w odniesieniu do głównego pliku. TSF (na przykład PerformanceCountersTable. TSF). Pliki towarzyszące (na przykład PerformanceCountersTables_ \* \* 001. tsf, PerformanceCountersTables_ \* \* 002. TSF itd.) są automatycznie przetwarzane.

### <a name="more-about-missing-trace-logs"></a>Więcej informacji o braku dzienników śledzenia

>[!NOTE]
> Poniższe informacje odnoszą się głównie do usługi Azure Cloud Services, chyba że skonfigurowano DiagnosticsMonitorTraceListener na aplikacji działającej na maszynie wirtualnej IaaS.

- Upewnij się, że **DiagnosticMonitorTraceListener** jest skonfigurowany w web.config lub app.config.  Ta konfiguracja jest domyślnie konfigurowana w projektach usługi w chmurze. Niektórzy klienci mają jednak komentarz, co powoduje, że instrukcje śledzenia nie są zbierane przez diagnostykę.

- Jeśli dzienniki nie są zapisywane z metody **OnStart** lub **Run** , upewnij się, że **DiagnosticMonitorTraceListener** znajduje się w app.config.  Domyślnie znajduje się on w web.config, ale ma zastosowanie tylko do kodu uruchomionego w w3wp.exe. Jest to potrzebne w app.config do przechwytywania śladów uruchomionych w programie WaIISHost.exe.

- Upewnij się, że używasz funkcji **Diagnostics. Trace. TraceXXX** zamiast elementu **Diagnostics. Debug. WriteXXX.** Instrukcje debugowania są usuwane z kompilacji wydania.

- Upewnij się, że skompilowany kod ma rzeczywistą wartość **Diagnostics. Trace** (Użyj reflektora, Ildasm lub ILSpy do zweryfikowania). Polecenia **Diagnostics. Trace** są usuwane z skompilowanego pliku binarnego, chyba że jest używany symbol kompilacji warunkowej śledzenia. Jest to typowy problem występujący podczas kompilowania projektu przy użyciu programu MSBuild.   

## <a name="known-issues-and-mitigations"></a>Znane problemy i środki zaradcze
Poniżej znajduje się lista znanych problemów z znanymi ograniczeniami:

**1. zależność .NET 4,5**

Rozszerzenie Diagnostyka Azure systemu Windows ma zależność środowiska uruchomieniowego w programie .NET 4,5 Framework lub nowszym. W chwili pisania wszystkie komputery, które są obsługiwane dla platformy Azure Cloud Services, a także wszystkie obrazy oficjalne, które są oparte na usłudze Azure Virtual Machines, mają zainstalowany program .NET 4,5 lub nowszy.

Nadal można napotkać sytuację, w której próbujesz uruchomić rozszerzenie Diagnostyka Azure systemu Windows na komputerze, na którym nie jest zainstalowany program .NET 4,5 lub nowszy. Dzieje się tak, gdy tworzysz maszynę ze starego obrazu lub migawki lub przeniesiesz własny dysk niestandardowy.

Jest to zazwyczaj manifest jako kod wyjścia **255** podczas uruchamiania **DiagnosticsPluginLauncher.exe.** Błąd występuje z powodu następującego nieobsługiwanego wyjątku:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

Środki **zaradcze:** Zainstaluj program .NET 4,5 lub nowszy na komputerze.

**2. dane liczników wydajności są dostępne w magazynie, ale nie są wyświetlane w portalu**

Środowisko portalu w maszynach wirtualnych domyślnie pokazuje pewne liczniki wydajności. Jeśli nie widzisz liczników wydajności i wiesz, że dane są generowane, ponieważ są one dostępne w magazynie, sprawdź następujące kwestie:

- Czy dane w magazynie mają nazwy liczników w języku angielskim. Jeśli nazwy liczników nie są w języku angielskim, wykres metryki portalu nie będzie mógł go rozpoznać. Środki **zaradcze**: Zmień język maszyny na angielski w przypadku kont systemowych. W tym celu wybierz kolejno pozycje **Panel sterowania**  >  **region**  >  **Administrative**  >  **Ustawienia kopiowanie**administracyjne. Następnie usuń zaznaczenie opcji **ekran powitalny i konta systemowe** , aby język niestandardowy nie został zastosowany do konta System.

- Jeśli używasz symboli wieloznacznych ( \* ) w nazwach liczników wydajności, portal nie będzie w stanie skorelować skonfigurowanych i zebranych liczników, gdy liczniki wydajności są wysyłane do ujścia usługi Azure Storage. Środki **zaradcze**: aby upewnić się, że można użyć symboli wieloznacznych i że portal rozwinie ( \* ), Roześlij liczniki wydajności do ujścia Azure monitor.
