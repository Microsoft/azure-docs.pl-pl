---
title: Jak używać że program perfinsights w Microsoft Azure | Microsoft Docs
description: Informacje na temat rozwiązywania problemów z wydajnością maszyn wirtualnych z systemem Windows przy użyciu programu że program perfinsights.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f49ae5139dc92ec1448e5dea05be8c8c216ef91e
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361351"
---
# <a name="how-to-use-perfinsights-in-azure"></a>Jak używać że program perfinsights na platformie Azure

[Że program perfinsights](https://aka.ms/perfinsightsdownload) to narzędzie do samodzielnego rozwiązywania problemów, które zbiera i analizuje dane diagnostyczne i zawiera raport, który pomaga rozwiązywać problemy z wydajnością maszyn wirtualnych systemu Windows na platformie Azure. Że program perfinsights można uruchamiać na maszynach wirtualnych jako autonomiczne narzędzie, bezpośrednio z portalu przy użyciu [diagnostyki wydajności dla maszyn wirtualnych platformy Azure](performance-diagnostics.md)lub przez zainstalowanie [rozszerzenia maszyny wirtualnej usługi Diagnostyka wydajności Azure](performance-diagnostics-vm-extension.md).

Jeśli występują problemy z wydajnością maszyn wirtualnych przed skontaktowaniem się z pomocą techniczną, Uruchom to narzędzie.

## <a name="supported-troubleshooting-scenarios"></a>Obsługiwane scenariusze rozwiązywania problemów

Że program perfinsights może zbierać i analizować kilka rodzajów informacji. W poniższych sekcjach omówiono typowe scenariusze.

### <a name="quick-performance-analysis"></a>Analiza szybkiej wydajności

W tym scenariuszu zbierane są informacje o konfiguracji dysku i inne istotne dane, takie jak:

-   Dzienniki zdarzeń

-   Stan sieci dla wszystkich połączeń przychodzących i wychodzących

-   Ustawienia konfiguracji sieci i zapory

-   Lista zadań dla wszystkich aplikacji, które są aktualnie uruchomione w systemie

-   Microsoft SQL Server ustawienia konfiguracji bazy danych (jeśli maszyna wirtualna jest zidentyfikowana jako serwer z systemem SQL Server)

-   Liczniki niezawodności magazynu

-   Ważne poprawki systemu Windows

-   Zainstalowane sterowniki filtrów

Jest to pasywna kolekcja informacji, która nie powinna mieć wpływu na system. 

>[!Note]
>Ten scenariusz jest automatycznie uwzględniany w każdym z następujących scenariuszy:

### <a name="benchmarking"></a>Testów porównawczych

W tym scenariuszu jest uruchamiany test testu porównawczego [Narzędzia diskspd](https://github.com/Microsoft/diskspd) (IOPS i MB/s) dla wszystkich dysków dołączonych do maszyny wirtualnej. 

> [!Note]
> Ten scenariusz może mieć wpływ na system i nie powinien być uruchamiany w działającym systemie produkcyjnym. W razie potrzeby Uruchom ten scenariusz w dedykowanym oknie obsługi, aby uniknąć jakichkolwiek problemów. Zwiększone obciążenie spowodowane przez test śledzenia lub testy porównawcze może niekorzystnie wpłynąć na wydajność maszyny wirtualnej.
>

### <a name="performance-analysis"></a>Analiza wydajności

W tym scenariuszu jest uruchamiany ślad [licznika wydajności](/windows/win32/perfctrs/performance-counters-portal) przy użyciu liczników określonych w RuleEngineConfig.jspliku. Jeśli maszyna wirtualna zostanie zidentyfikowana jako serwer z uruchomionym SQL Server, zostanie uruchomione śledzenie licznika wydajności. Robi to przy użyciu liczników znalezionych w RuleEngineConfig.jsna pliku. Ten scenariusz obejmuje również dane diagnostyki wydajności.

### <a name="azure-files-analysis"></a>Analiza Azure Files

W tym scenariuszu jest uruchamiany specjalny przechwycenie licznika wydajności wraz z funkcją śledzenia sieci. Przechwytywanie obejmuje wszystkie liczniki udziałów klienta bloku komunikatów serwera (SMB). Poniżej przedstawiono niektóre liczniki wydajności udziałów klienta SMB, które są częścią przechwytywania:

| **Typ**     | **Licznik udziałów klienta SMB** |
|--------------|-------------------------------|
| Liczba operacji we/wy na sekundę         | Żądania danych/s             |
|              | Żądania odczytu/s             |
|              | Żądania zapisu/s            |
| Opóźnienie      | Średnia liczba sekund/żądanie danych         |
|              | Średni czas odczytu/odczyt                 |
|              | Średni czas zapisu/s                |
| Rozmiar we/wy      | Średnia liczba bajtów/żądanie danych       |
|              | Średnia b/odczyt               |
|              | Średnia liczba bajtów/zapis              |
| Przepływność   | Bajty danych/s                |
|              | Bajty odczytu/s                |
|              | Bajty zapisu/s               |
| Długość kolejki | Średnia długość kolejki odczytu        |
|              | Średnia długość kolejki zapisu       |
|              | Średnia długość kolejki danych        |

### <a name="advanced-performance-analysis"></a>Zaawansowana analiza wydajności

Po uruchomieniu zaawansowanej analizy wydajności Wybierz pozycję ślady do uruchomienia równolegle. Jeśli chcesz, możesz uruchomić je wszystkie (licznik wydajności, Xperf, Sieć i StorPort).  

> [!Note]
> Ten scenariusz może mieć wpływ na system i nie powinien być uruchamiany w działającym systemie produkcyjnym. W razie potrzeby Uruchom ten scenariusz w dedykowanym oknie obsługi, aby uniknąć jakichkolwiek problemów. Zwiększone obciążenie spowodowane przez test śledzenia lub testy porównawcze może niekorzystnie wpłynąć na wydajność maszyny wirtualnej.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jakiego rodzaju informacje są zbierane przez że program perfinsights?

Zbierane są informacje o konfiguracji maszyn wirtualnych systemu Windows, dysków lub pul magazynu, licznikach wydajności, dziennikach i różnych śledzenia. Jest to zależne od scenariusza wydajności, z którego korzystasz. Szczegóły zawiera poniższa tabela:

| Zbierane dane | Analiza szybkiej wydajności | Testów porównawczych | Analiza wydajności | Analiza Azure Files | Zaawansowana analiza wydajności |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|
| Informacje z dzienników zdarzeń       | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Informacje o systemie                | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Mapa woluminów                        | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Mapa dysku                          | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Uruchomione zadania                     | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Liczniki niezawodności magazynu      | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Informacje o magazynie               | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Fsutil Output                     | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Informacje o sterowniku filtru                | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Dane wyjściowe polecenia netstat                    | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Konfiguracja sieci             | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Konfiguracja zapory            | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Konfiguracja programu SQL Server          | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Ślady diagnostyki wydajności *  | Tak                        | Tak                                | Tak                      | Tak                  | Tak                  |
| Śledzenie licznika wydajności * *      |                            |                                    | Tak                      |                      | Tak                  |
| Śledzenie licznika SMB * *              |                            |                                    |                          | Tak                  |                      |
| Śledzenie licznika SQL Server * *       |                            |                                    | Tak                      |                      | Tak                  |
| Śledzenie Xperf                       |                            |                                    |                          |                      | Tak                  |
| Ślad StorPort                    |                            |                                    |                          |                      | Tak                  |
| Śledzenie sieci                     |                            |                                    |                          | Tak                  | Tak                  |
| Śledzenie testu porównawczego narzędzia diskspd * * *       |                            | Tak                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Śledzenie diagnostyki wydajności (*)

Uruchamia aparat oparty na regułach w tle w celu zbierania danych i diagnozowania bieżących problemów z wydajnością. Obecnie obsługiwane są następujące reguły:

- Reguła HighCpuUsage: wykrywa duże okresy użycia procesora CPU i pokazuje klientów użycia procesora CPU w tych okresach.
- Reguła HighDiskUsage: wykrywa duże okresy użycia dysku na dyskach fizycznych i pokazuje odbiorców w tych okresach.
- Reguła HighResolutionDiskMetric: przedstawia metryki czasowe IOPS, przepływności i operacje we/wy na 50 milisekund dla każdego dysku fizycznego. Ułatwia ona szybkie identyfikowanie okresów ograniczania dysku.
- Reguła HighMemoryUsage: wykrywa duże okresy użycia pamięci i pokazuje odbiorców w tych okresach.

> [!NOTE] 
> Obecnie obsługiwane są wersje systemu Windows, które zawierają .NET Framework 4,5 lub nowsze wersje.

### <a name="performance-counter-trace-"></a>Ślad licznika wydajności (* *)

Zbiera następujące liczniki wydajności:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk i \Dysk logiczny
- \Cache\Dirty stron, \Cache\Lazy zapisu opróżnień/s, \Server\Pool niestronicowane, niepowodzenia i \Server\Pool błędów stronicowania
- Wybrane liczniki w obszarze \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network, QoS Policy\Packets, \Per procesor Network Interface Activity

#### <a name="for-sql-server-instances"></a>Dla wystąpień SQL Server
- Serwer \SQL: Menedżer buforów, \SQLServer: statystyki puli zasobów i \SQLServer: Statystyka SQL \
- \SQLServer: Locks, \SQLServer: General, Statistics
- \SQLServer: metody dostępu

#### <a name="for-azure-files"></a>Dla Azure Files
\SMB udziały klientów

### <a name="diskspd-benchmark-trace-"></a>Ślad testu porównawczego narzędzia diskspd (* * *)
Narzędzia diskspd testów obciążenia wejścia/wyjścia (dysk systemu operacyjnego [zapis] i stacje puli [odczyt/zapis])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Uruchamianie narzędzia że program perfinsights na maszynie wirtualnej

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co muszę wiedzieć przed uruchomieniem narzędzia? 

#### <a name="tool-requirements"></a>Wymagania dotyczące narzędzi

-  To narzędzie musi być uruchomione na maszynie wirtualnej, na której występuje problem z wydajnością. 

-  Obsługiwane są następujące systemy operacyjne:
   * Windows Server 2019
   * Windows Server 2016
   * Windows Server 2012 z dodatkiem R2
   * Windows Server 2012
   * Windows Server 2008 z dodatkiem R2
   * Windows 10
   * Windows 8.1
   * Windows 8

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Możliwe problemy podczas uruchamiania narzędzia na maszynach wirtualnych produkcji

-  W przypadku scenariusza porównawczego lub scenariusza "Zaawansowana analiza wydajności", który jest skonfigurowany do korzystania z Xperf lub narzędzia diskspd, narzędzie może niekorzystnie wpłynąć na wydajność maszyny wirtualnej. Te scenariusze nie powinny być uruchamiane w środowisku produkcyjnym na żywo.

-  W scenariuszu porównawczym lub w scenariuszu "Zaawansowana analiza wydajności" skonfigurowanym do korzystania z narzędzia diskspd upewnij się, że żadne inne działania w tle nie zakłócają obciążenia we/wy.

-  Domyślnie narzędzie używa tymczasowego dysku magazynującego do zbierania danych. Jeśli śledzenie pozostaje włączone przez dłuższy czas, ilość zbieranych danych może być istotna. Może to zmniejszyć dostępność miejsca na dysku tymczasowym. w związku z tym może mieć wpływ na wszystkie aplikacje, które opierają się na tym dysku.

### <a name="how-do-i-run-perfinsights"></a>Jak mogę uruchomić że program perfinsights? 

Program że program perfinsights można uruchomić na maszynie wirtualnej, instalując [rozszerzenie maszyny wirtualnej diagnostyki wydajności Azure](performance-diagnostics-vm-extension.md). Można go również uruchomić jako autonomiczne narzędzie. 

**Instalowanie i uruchamianie że program perfinsights z poziomu Azure Portal**

Aby uzyskać więcej informacji na temat tej opcji, zobacz [Instalowanie rozszerzenia maszyny wirtualnej diagnostyki wydajności platformy Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Uruchom że program perfinsights w trybie autonomicznym**

Aby uruchomić narzędzie że program perfinsights, wykonaj następujące kroki:


1. Pobierz [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Odblokuj plik PerfInsights.zip. Aby to zrobić, kliknij prawym przyciskiem myszy plik PerfInsights.zip i wybierz polecenie **Właściwości**. Na karcie **Ogólne** wybierz opcję **odblokowywanie**, a następnie wybierz przycisk **OK**. Dzięki temu narzędzie zostanie uruchomione bez dodatkowych wskazówek dotyczących zabezpieczeń.  

    ![Zrzut ekranu przedstawiający właściwości że program perfinsights z wyróżnionym odblokowywaniem](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Rozwiń skompresowany plik PerfInsights.zip na dysk tymczasowy (domyślnie jest to dysk D). 

4.  Otwórz wiersz polecenia systemu Windows jako administrator, a następnie uruchom PerfInsights.exe, aby wyświetlić dostępne parametry commandline.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Zrzut ekranu przedstawiający dane wyjściowe wiersza polecenia że program perfinsights](media/how-to-use-perfInsights/pi-commandline.png)
    
    Podstawowa składnia do uruchamiania scenariuszy że program perfinsights jest następująca:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Możesz użyć poniższego przykładu, aby uruchomić scenariusz analizy wydajności dla 5 minut:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Poniższego przykładu można użyć do uruchomienia scenariusza zaawansowanego z wynikami śledzenia Xperf i licznika wydajności dla 5 minut:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Możesz użyć poniższego przykładu, aby uruchomić scenariusz analizy wydajności dla 5 minut i przekazać plik zip wynik do konta magazynu:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Wszystkie dostępne scenariusze i opcje można wyszukać za pomocą polecenia **/list** :
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Przed uruchomieniem scenariusza że program perfinsights prosi użytkownika o zgodę na udostępnienie informacji diagnostycznych i zaakceptowanie umowy licencyjnej. Użyj opcji **/AcceptDisclaimerAndShareDiagnostics** , aby pominąć te komunikaty. 
    >
    >Jeśli masz aktywny bilet pomocy technicznej firmy Microsoft i uruchomiono że program perfinsights na żądanie inżyniera pomocy technicznej, z którym pracujesz, upewnij się, że podano numer biletu pomocy technicznej przy użyciu opcji **/SR** .
    >
    >Domyślnie program że program perfinsights próbuje zaktualizować się do najnowszej wersji, jeśli jest dostępna. Użyj parametru **/SkipAutoUpdate** lub **/sau** , aby pominąć autoaktualizację.  
    >
    >Jeśli przełącznik czasu trwania **/d** nie zostanie określony, że program perfinsights wyświetli monit o Odtwórz problemu podczas uruchamiania vmslow, migracji pamięci i zaawansowanych scenariuszy. 

Po zakończeniu śledzenia lub operacji, nowy plik zostanie wyświetlony w tym samym folderze co że program perfinsights. Nazwa pliku to **PerformanceDiagnostics \_ rrrr-mm-dd \_hh-mm-ss-fff.zip.** Możesz wysłać ten plik do agenta pomocy technicznej na potrzeby analizy lub otworzyć raport w pliku zip, aby przejrzeć wyniki i zalecenia.

## <a name="review-the-diagnostics-report"></a>Przejrzyj raport diagnostyczny

W pliku **PerformanceDiagnostics \_ rrrr-mm-dd \_hh-mm-ss-fff.zip** można znaleźć raport HTML, który zawiera szczegółowe informacje o że program perfinsights. Aby przejrzeć raport, rozwiń plik **PerformanceDiagnostics \_ rrrr-mm-dd \_hh-mm-ss-fff.zip** , a następnie otwórz plik **że program perfinsights Report.html** .

Wybierz kartę **wnioski** .

![Zrzut ekranu przedstawiający kartę Przegląd raportu że program perfinsights. ](media/how-to-use-perfInsights/pi-finding-tab.png)
 ![ Zrzut ekranu karty magazyn raportu że program perfinsights.](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Ustalenia sklasyfikowane jako duże są znanymi problemami, które mogą powodować problemy z wydajnością. Ustalenia sklasyfikowane jako średnie reprezentują nieoptymalne konfiguracje, które nie muszą powodować problemów z wydajnością. Ustalenia sklasyfikowane jako niskie są tylko w instrukcjach informacyjnych.

Przejrzyj zalecenia i linki dla wszystkich dużych i średnich wyników. Dowiedz się, jak mogą one wpływać na wydajność, a także najlepsze rozwiązania dotyczące konfiguracji zoptymalizowanych pod kątem wydajności.

### <a name="storage-tab"></a>Karta magazyn

Sekcja **spostrzeżenia** zawiera różne wyniki i zalecenia dotyczące magazynu.

W sekcjach **Mapa dysku** i **Mapa woluminu** opisano sposób, w jaki woluminy logiczne i dyski fizyczne są ze sobą powiązane.

W perspektywie dysku fizycznego (mapa dysku) w tabeli są wyświetlane wszystkie woluminy logiczne, które są uruchomione na dysku. W poniższym przykładzie **PhysicalDrive2** uruchamia dwa woluminy logiczne utworzone na wielu partycjach (J i H):

![Zrzut ekranu przedstawiający kartę dysk](media/how-to-use-perfInsights/pi-disk-tab.png)

W perspektywie woluminu (mapa woluminu) w tabelach są wyświetlane wszystkie dyski fizyczne w poszczególnych woluminach logicznych. Należy zauważyć, że w przypadku dysków macierzy RAID/dynamicznych można uruchomić wolumin logiczny na wielu dyskach fizycznych. W poniższym przykładzie *C: \\ Mount* jest punktem instalacji skonfigurowanym jako *SpannedDisk* na dyskach fizycznych 2 i 3:

![Zrzut ekranu przedstawiający kartę woluminu](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Karta SQL

Jeśli docelowa maszyna wirtualna hostuje dowolne SQL Server wystąpienia, w raporcie zostanie wyświetlona dodatkowa karta o nazwie **SQL**:

![Zrzut ekranu przedstawiający kartę SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Ta sekcja zawiera kartę **wyniki** i dodatkowe karty dla każdego wystąpienia SQL Server hostowanego na maszynie wirtualnej.

Karta **wyniki** zawiera listę wszystkich znalezionych problemów z wydajnością związanych z usługą SQL wraz z zaleceniami.

W poniższym przykładzie zostanie wyświetlony komunikat **PhysicalDrive0** (uruchamianie dysku C). Wynika to z faktu, że pliki **modeldev** i **Modellog** znajdują się na dysku C i są różnymi typami (takimi jak plik danych i dziennik transakcji).

![Zrzut ekranu przedstawiający informacje o dzienniku](media/how-to-use-perfInsights/pi-log-info.png)

Karty dla określonych wystąpień SQL Server zawierają sekcję ogólne, która wyświetla podstawowe informacje o wybranym wystąpieniu. Karty zawierają również dodatkowe sekcje dotyczące zaawansowanych informacji, takich jak ustawienia, konfiguracje i opcje użytkownika.

### <a name="diagnostic-tab"></a>Karta Diagnostyka
Karta **Diagnostyka** zawiera informacje na temat najważniejszych odbiorców procesora, dysku i pamięci na komputerze na czas trwania uruchamiania programu że program perfinsights. Możesz również znaleźć informacje o krytycznych poprawkach, których może brakować system, listę zadań i ważne zdarzenia systemowe. 

## <a name="references-to-the-external-tools-used"></a>Odwołania do zewnętrznych narzędzi

### <a name="diskspd"></a>Narzędzia diskspd

Narzędzia diskspd to narzędzie do generowania generatora obciążenia magazynu i testowania wydajności firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Narzędzia diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Xperf to narzędzie wiersza polecenia do przechwytywania śladów z zestawu narzędzi wydajności systemu Windows. Aby uzyskać więcej informacji, zobacz [Windows Performance Toolkit – Xperf](/archive/blogs/ntdebugging/windows-performance-toolkit-xperf).

## <a name="next-steps"></a>Następne kroki

Dzienniki diagnostyczne i raporty można przekazać do pomoc techniczna firmy Microsoft do dalszej analizy. Obsługa może wymagać wysłania danych wyjściowych generowanych przez że program perfinsights w celu uzyskania pomocy w procesie rozwiązywania problemów.

Poniższy zrzut ekranu przedstawia komunikat podobny do następującego:

![Zrzut ekranu przedstawiający przykładowy komunikat z pomoc techniczna firmy Microsoft](media/how-to-use-perfInsights/pi-support-email.png)

Postępuj zgodnie z instrukcjami wyświetlanymi w komunikacie, aby uzyskać dostęp do obszaru roboczego transferu plików. Aby zwiększyć bezpieczeństwo, należy zmienić hasło przy pierwszym użyciu.

Po zalogowaniu się zostanie wyświetlone okno dialogowe umożliwiające przekazanie **PerformanceDiagnostics \_ rrrr-mm-dd \_hh-mm-ss-fff.zip** pliku, który został zebrany przez że program perfinsights.
