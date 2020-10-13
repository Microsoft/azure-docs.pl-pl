---
title: Rozwiązywanie problemów z wysokim procesorem CPU w przypadku maszyn wirtualnych z systemem Windows Azure
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: ffac5ac4d1a8143590e1d72aaafc8a02d6ab04ca
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977259"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Rozwiązywanie problemów z wysokim procesorem CPU w przypadku maszyn wirtualnych z systemem Windows Azure

## <a name="summary"></a>Podsumowanie

Problemy z wydajnością występujące w różnych systemach operacyjnych lub aplikacjach, a każdy problem wymaga unikatowego podejścia do rozwiązywania problemów. Większość z tych problemów obraca się między procesorami CPU, pamięci, sieci i wejścia/wyjścia (we/wy) jako kluczowymi lokalizacjami, w których występuje problem. Każdy z tych obszarów generuje różne objawy (czasami jednocześnie) i wymaga innej diagnostyki i rozwiązania.

W tym artykule omówiono problemy dotyczące użycia procesora na platformie Azure Virtual Machines (maszyny wirtualne) z systemem operacyjnym Windows.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Problemy z wysokim procesorem CPU na maszynach wirtualnych z systemem Windows Azure

Oprócz problemów z opóźnieniami we/wy i sieci, rozwiązywanie problemów z PROCESORAmi i pamięcią wymaga tych samych narzędzi i kroków, co w przypadku serwerów lokalnych. Jednym z narzędzi obsługiwanych przez firmę Microsoft jest że program perfinsights (dostępny dla systemów Windows i Linux). Że program perfinsights mogą zapewnić diagnostykę najlepszych rozwiązań maszyn wirtualnych platformy Azure w raporcie przyjaznym dla użytkownika. Że program perfinsights jest również narzędziem otoki, które może pomóc zbierać dane monitora wydajności, Xperf i netmon, w zależności od flag wybranych w narzędziu.

Większość istniejących narzędzi do rozwiązywania problemów z wydajnością, takich jak perfmon lub ProcMon, które są używane dla serwerów lokalnych, będzie działała na maszynach wirtualnych z systemem Windows Azure. Jednak że program perfinsights jest zaprojektowana specjalnie dla maszyn wirtualnych platformy Azure, aby uzyskać więcej szczegółowych informacji, w tym najlepszych rozwiązań dotyczących platformy Azure, najlepszych rozwiązań dotyczących języka SQL, wykresów opóźnień we/wy o wysokiej rozdzielczości, kart CPU i pamięci itd.

Niezależnie od tego, czy działa jako User-Mode, czy tryb jądra, każdy wątek aktywnego procesu wymaga od cykli procesora CPU uruchomienia kodu, z którego został skompilowany. Wiele problemów jest bezpośrednio związanych z obciążeniem. Rodzaj obciążenia, który istnieje na dysku serwera użycie zasobów, w tym procesor CPU.

#### <a name="common-factors"></a>Typowe czynniki

Następujące czynniki są wspólne w sytuacji o wysokim poziomie procesora:

- Ostatnia zmiana lub wdrożenie kodu, które jest przeznaczone głównie dla aplikacji, takich jak Internet Information Services (IIS), Microsoft SharePoint, Microsoft SQL Server lub aplikacje innych firm.

- Ostatnia aktualizacja, która może być związana z aktualizacją na poziomie systemu operacyjnego lub do aktualizacji zbiorczych oraz poprawek na poziomie aplikacji.

- Zmiany zapytania lub indeksy nieaktualne. Aplikacje warstwy danych SQL Server i Oracle mają także optymalizację planu zapytania jako inny czynnik. Zmiany danych lub brak odpowiednich indeksów mogą spowodować, że niektóre zapytania staną się bardziej czasochłonne.

- Specyficzne dla maszyny wirtualnej platformy Azure. Istnieją pewne procesy, takie jak RDAgent, i procesy specyficzne dla rozszerzenia, takie jak Agent monitorowania, Agent MMA lub klient zabezpieczeń, które mogą spowodować użycie wysokiego procesora. Te procesy muszą być wyświetlane w perspektywie konfiguracji lub znane problemy.

## <a name="troubleshoot-the-issue"></a>Rozwiązywanie problemu

Ten artykuł koncentruje się na odizolowaniu problematycznego procesu. Dalsze analizy będą specyficzne dla procesu, który zapewnia wysoką zużycie procesora CPU.

Na przykład, jeśli proces jest SQL Server (sqlservr.exe), następne kroki będą analizować, które zapytanie wykorzystuje najwięcej cykli procesora CPU w określonym przedziale czasu.

### <a name="scope-the-issue"></a>Określanie zakresu problemu

Poniżej przedstawiono kilka pytań, na które należy zadać problemy podczas rozwiązywania problemu:

- Czy istnieje wzorzec problemu? Czy na przykład występuje problem z dużym procesorem CPU w danym momencie każdego dnia, tygodnia lub miesiąca? Jeśli tak, możesz skorelować ten problem z zadaniem, raportem lub logowaniem użytkownika?

- Czy problem z dużym procesorem CPU został uruchomiony po ostatniej zmianie kodu? Czy zastosowano aktualizację w systemie Windows lub w aplikacji?

- Czy problem z dużym procesorem CPU został uruchomiony po zmianie obciążenia, na przykład zwiększenie liczby użytkowników, wyższego strumienia danych lub większej liczby raportów?

- W przypadku platformy Azure problem z dużym procesorem CPU został uruchomiony w jednym z następujących warunków?

  - Po ostatnim ponownym wdrożeniu lub uruchomieniu
  - Gdy Zmieniono typ jednostki SKU lub maszyny wirtualnej
  - Po dodaniu nowego rozszerzenia
  - Po wprowadzeniu zmian modułu równoważenia obciążenia

### <a name="azure-caveats"></a>Ograniczenia dotyczące platformy Azure

Zapoznaj się z obciążeniem. Po wybraniu maszyny wirtualnej można oszacować liczbę wirtualnych procesorów CPU (vCPU), gdy zobaczysz ogólny miesięczny koszt hostingu. Jeśli obciążenie jest czasochłonne, wybranie mniejszej jednostki SKU maszyny wirtualnej, która ma jeden lub dwa procesorów wirtualnych vCPU może powodować problemy z obciążeniem. Przetestuj różne konfiguracje dla obciążenia, aby określić najlepszą wymaganą funkcję obliczeniową.

Istnieją pewne serie maszyn wirtualnych, takie jak Seria B (Tryb serii), które są zalecane w celu zapewnienia jakości i testowania. Użycie tych serii w środowisku produkcyjnym ogranicza możliwości obliczeniowe po wyczerpaniu kredytów procesora CPU.

W przypadku znanych aplikacji, takich jak SQL Server, Oracle, RDS (Usługi pulpitu zdalnego), Windows Virtual Desktop, IIS lub SharePoint, istnieją artykuły z najlepszymi rozwiązaniami dotyczącymi platformy Azure, które obejmują zalecenia dotyczące minimalnej konfiguracji tych obciążeń.

### <a name="ongoing-high-cpu-issues"></a>Bieżące problemy z dużym procesorem CPU

Jeśli problem występuje już teraz, jest to Najlepsza szansa, aby przechwycić ślad procesu, aby określić przyczynę problemu. Aby zlokalizować ten proces, można użyć istniejących narzędzi, które były używane przez lokalne serwery z systemem Windows. Do obsługi maszyn wirtualnych platformy Azure zaleca się używanie następujących narzędzi.

### <a name="perfinsights"></a>PerfInsights

Że program perfinsights jest zalecanym narzędziem z pomocy technicznej platformy Azure w przypadku problemów z wydajnością maszyn wirtualnych. Zaprojektowano w celu pokrycia najlepszych rozwiązań i dedykowanych kart analitycznych na potrzeby obsługi procesora CPU, pamięci i wysokiej rozdzielczości wykresów we/wy. Można uruchomić go OnDemand za pośrednictwem Azure Portal lub z poziomu maszyny wirtualnej. Dane można udostępniać członkom zespołu pomocy technicznej systemu Azure.

#### <a name="run-perfinsights"></a>Uruchom że program perfinsights

Że program perfinsights jest dostępny zarówno dla [systemu operacyjnego Windows](./how-to-use-perfinsights.md) , jak i [Linux](./how-to-use-perfinsights-linux.md) . W przypadku systemu Windows poniżej przedstawiono opcje.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Uruchamianie i analizowanie raportów za poorednictwem Azure Portal

Po [zainstalowaniu za pomocą Azure Portal](./performance-diagnostics.md)w rzeczywistości zainstaluje rozszerzenie na maszynie wirtualnej. Użytkownicy mogą również instalować że program perfinsights jako rozszerzenie, przechodząc bezpośrednio do [rozszerzeń w bloku maszyny wirtualnej](./performance-diagnostics-vm-extension.md), a następnie wybierając opcję diagnostyki wydajności.

#### <a name="azure-portal-option-1"></a>Azure Portal opcja 1

Przejrzyj blok maszyny wirtualnej i wybierz opcję **Diagnostyka wydajności** . Zostanie wyświetlony monit o zainstalowanie opcji (przy użyciu rozszerzeń) na maszynie wirtualnej, dla której została wybrana.

  ![Instalowanie diagnostyki wydajności](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Azure Portal opcja 2

Przeglądaj w celu **zdiagnozowania i rozwiązania problemów** w bloku maszyny wirtualnej, a następnie wyszukaj **problemy z wydajnością maszyny wirtualnej**.

  ![Rozwiązywanie problemów z wydajnością maszyn wirtualnych](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

W przypadku wybrania opcji **Rozwiązywanie problemów**zostanie załadowany ekran instalacji że program perfinsights.

W przypadku wybrania opcji **Zainstaluj**instalacja oferuje różne opcje kolekcji.

  ![Analiza wydajności](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

Opcje numerowane na zrzucie ekranu odnoszą się do następujących komentarzy:

1. W przypadku opcji **High-CPU**wybierz pozycję **Analiza wydajności** lub **Zaawansowane**.

2. Gdy dodasz tutaj objawy, zostaną one dodane do raportu, co pomoże Ci udostępnić informacje z pomocą techniczną platformy Azure.

3. Wybierz czas trwania zbierania danych. Dla opcji High-CPU wybierz co najmniej 15 minut lub więcej. W trybie Azure Portal można zbierać dane do 15 minut. W przypadku dłuższych okresów zbierania należy uruchomić program jako plik wykonywalny w ramach maszyny wirtualnej.

4. Jeśli otrzymasz prośbę o pomoc techniczną platformy Azure w celu zebrania tych danych, możesz dodać tutaj numer biletu. To pole jest opcjonalne.

5. Zaznacz to pole, aby zaakceptować umowę licencyjną użytkownika oprogramowania (EULA).

6. Zaznacz to pole, jeśli zamierzasz udostępnić ten raport dla zespołu pomocy technicznej systemu Azure pomagającego w tym przypadku.

Raport jest przechowywany na jednym z kont magazynu w ramach Twojej subskrypcji. Jest on dostępny do późniejszego wyświetlenia i pobrania.

#### <a name="run-perfinsights-from-within-the-vm"></a>Uruchamianie że program perfinsights z poziomu maszyny wirtualnej

Ta metoda może być używana, jeśli zamierzasz uruchamiać że program perfinsights przez dłuższy czas. [Artykuł że program perfinsights](./how-to-use-perfinsights.md#how-do-i-run-perfinsights) zawiera szczegółowy przewodnik dotyczący różnych poleceń i flag, które są wymagane do uruchomienia że program perfinsights jako pliku wykonywalnego. Na potrzeby użycia wysokiego procesora trzeba mieć jeden z następujących trybów:

- Scenariusz zaawansowany

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- Scenariusz powolnej maszyny wirtualnej (wydajność)

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

Dane wyjściowe polecenia będą znajdować się w tym samym folderze, w którym zapisano plik wykonywalny że program perfinsights.

#### <a name="what-to-look-for-in-the-report"></a>Czego szukać w raporcie

Po uruchomieniu raportu lokalizacja zawartości zależy od tego, czy został on uruchomiony za pomocą Azure Portal, czy jako pliku wykonywalnego. Dla każdej opcji można uzyskać dostęp do wygenerowanego folderu dziennika lub pobrać (jeśli Azure Portal) lokalnie na potrzeby analizy.

### <a name="run-through-the-azure-portal"></a>Uruchamiaj za pomocą Azure Portal

  ![Diagnostyka wydajności o dużym wpływie](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![Pobierz raport](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>Uruchamianie z poziomu maszyny wirtualnej

Struktura folderów powinna wyglądać podobnie do następujących:

  ![Wybierz dane wyjściowe](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![Struktura folderów](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. Wszystkie dodatkowe kolekcje, takie jak perfmon, Xperf, netmon, dzienniki protokołu SMB, dzienniki zdarzeń itd., można znaleźć w folderze danych wyjściowych.

1. Rzeczywisty raport wraz z analizą i zaleceniami.

1. W przypadku wydajności (VMslow) i zaawansowanego raport zbiera informacje **monitora wydajności** na czas trwania przebiegu że program perfinsights.

1. Dzienniki zdarzeń przedstawiają szybki wgląd w szczegóły dotyczące przydatnego poziomu systemu lub awarii procesu.

#### <a name="where-to-start"></a>Od czego zacząć

Otwórz raport że program perfinsights. Karta **wyniki** rejestruje wszystkie wartości odstające w odniesieniu do zużycia zasobów. Jeśli istnieją wystąpienia o wysokim poziomie użycia procesora, na karcie **ustalenia** zostanie skategoryzowana wartość "duży wpływ" lub "średni wpływ".

  ![Zasoby poziomu wpływu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

Podobnie jak w poprzednim przykładzie, że program perfinsights został uruchomiony przez 30 minut. W połowie tego czasu podświetlony proces wyczerpuje procesor CPU na wyższym poziomie. Jeśli ten sam proces był uruchomiony w ciągu czasu zbierania, poziom wpływu zostałby zmieniony na **wysoki**.

Po rozwinięciu zdarzenia **ustaleń** zobaczysz kilka najważniejszych informacji. Karta zawiera listę procesów w kolejności malejącej, na **średnie użycie procesora CPU** i pokazuje, czy proces został powiązany z systemem, aplikacją firmy Microsoft (SQL, IIS) lub procesem innej firmy.

#### <a name="more-details"></a>Więcej szczegółów

W ramach **procesora CPU** istnieje dedykowany subtab, który może służyć do szczegółowej analizy wzorców, na rdzeń lub na proces.

Karta klienci z największą ilością **zasobów CPU** ma dwie oddzielne sekcje i w tym miejscu można wyświetlić statystyki dotyczące poszczególnych procesorów. Projekt aplikacji jest często Single-Threaded lub przypinany do jednego procesora. W tym scenariuszu jeden lub kilka rdzeni jest uruchomionych o 100%, podczas gdy inne rdzenie są uruchamiane na oczekiwanych poziomach. Te scenariusze są bardziej skomplikowane, ponieważ średni procesor CPU na serwerze wydaje się działać zgodnie z oczekiwaniami, ale procesy, które są przypięte na rdzeniach o wysokim poziomie użycia, będą wolniejsze niż oczekiwano.

  ![Użycie wysokiego procesora](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

Druga sekcja (równie ważna) to **bardzo długotrwałi klienci korzystający z procesora CPU**. Ta sekcja zawiera szczegóły procesu i ich wzorzec użycia procesora CPU. Lista jest sortowana według wysokiego średniego odbiorcy procesora w górnej części.

  ![Niedługotrwałi klienci korzystający z procesora CPU](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

Te dwie karty będą wystarczające do ustawienia ścieżki do następnych kroków rozwiązywania problemów. W zależności od procesu, który zapewnia wysoką kondycję, należy zająć się wcześniej zapytaniem. Procesy, takie jak SQL Server (sqlservr.exe) lub IIS (w3wp.exe) wymagają konkretnego przechodzenia do szczegółów zapytania lub zmiany kodu, które powodują ten warunek. W przypadku procesów systemowych, takich jak WMI lub Lsass.exe, należy postępować zgodnie z inną ścieżką.

W przypadku procesów związanych z maszyną wirtualną platformy Azure, takich jak RDAgent, OMS i pliki wykonywalne rozszerzenia monitorowania, może być konieczne naprawienie nowej kompilacji lub wersji przez uzyskanie pomocy od zespołu pomocy technicznej systemu Azure.

### <a name="perfmon"></a>Monitora wydajności

**Monitor wydajności** jest jednym z najwcześniejszych narzędzi do rozwiązywania problemów z zasobami w systemie Windows Server. Nie daje jasnego raportu, który ma zalecenia lub wnioski. Zamiast tego wymaga użytkownika do eksplorowania zebranych danych i używania określonego filtru w różnych kategoriach liczników.

Że program perfinsights zbiera informacje o monitorze wydajności jako dodatkowy dziennik dla VMSlow i zaawansowanych scenariuszy. Jednak Monitor wydajności może być zbierane niezależnie i ma następujące dodatkowe korzyści:

- Można je zbierać zdalnie.

- **Zadanie**można zaplanować za jego podstawie.

- Może być zbierane przez dłuższy czas lub w trybie ciągłym przy użyciu funkcji przejęcia.

Rozważmy ten sam przykład, który jest przedstawiony w że program perfinsights, aby zobaczyć, jak Monitor wydajności wyświetla te dane. Wymagane kategorie liczników są następujące:

- Informacje o procesorze, > czas procesora (%) > _Total

- Proces >% ProcessorTime > wszystkich wystąpień

#### <a name="where-to-start"></a>Od czego zacząć

Nazwa pliku wyjściowego monitora wydajności ma `.blg` rozszerzenie. Te pliki można zbierać niezależnie lub przy użyciu że program perfinsights. W tej dyskusji będziesz używać monitora wydajności `.blg` dołączonego do danych że program perfinsights, który został zebrany w poprzednim przykładzie.

W Monitorze wydajności nie ma żadnych domyślnych raportów gotowych dla użytkownika. Istnieją różne widoki, które zmieniają typ wykresu, ale filtrowanie procesów (lub pracy wymaganej do identyfikacji procesów przyczyna) jest ręczne.

> [!NOTE]
> [Narzędzie PAL](https://github.com/clinthuffman/PAL) może zużywać `.blg` pliki i generować szczegółowe raporty.

Aby rozpocząć, wybierz kategorię **Dodawanie liczników** .

1. W obszarze **dostępne liczniki**wybierz licznik **% ProcessorTime** w kategorii **Informacje o procesorze** .

1. Wybierz pozycję **_Total**, która zapewnia statystykę wszystkich połączonych rdzeni.

1. Wybierz pozycję **Dodaj**. W oknie zostanie wyświetlona wartość **% ProcessorTime**  **dodana do liczników**.

  ![Dodawanie czasu procesora](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

Po załadowaniu liczników zobaczysz wykresy trendu liniowego w przedziale czasu kolekcji. Możesz zaznaczyć lub wyczyścić te liczniki. Do tej pory dodano tylko jeden licznik.

  ![Ustawienia monitora wydajności](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

Każdy licznik będzie miał wartości **średnie**, **minimum**i **maksimum** . Skup się na wartościach **średnich** i **maksymalnych** , ponieważ średnia wartość może się różnić w zależności od czasu trwania zbierania danych. Jeśli działanie wysokiego procesora CPU było widoczne przez 10 minut, podczas gdy Ogólna kolekcja była 40 minut, średnia wartość będzie znacznie niższa.

Poprzedni wykres trendu pokazuje, że **całkowity procesor** był w przybliżeniu zbliżony do 80% przez około 15 minut.

#### <a name="identify-the-process"></a>Identyfikowanie procesu

Wykryliśmy, że serwer miał duże użycie procesora CPU przez określony czas, ale nie zidentyfikowano jeszcze sterownika. W przeciwieństwie do korzystania z programu że program perfinsights, w tym przypadku należy ręcznie wyszukać proces przyczyna.

Dla tego zadania musisz wyczyścić lub usunąć liczniki **% ProcessorTime** , które zostały wcześniej dodane, a następnie dodać nową kategorię:

- Proces >% ProcessorTime > wszystkich wystąpień

Ta kategoria będzie ładować liczniki dla wszystkich procesów uruchomionych w tym czasie.

  ![Dodawanie liczników](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

Na typowym komputerze produkcyjnym można uruchamiać setki lub procesy. W związku z tym może upłynąć trochę czasu, aby wyczyścić każdy licznik, który wydaje się mieć niską lub płaską tendencję trendu.

Aby przyspieszyć ten proces, użyj widoku **histogramu** i Zmień typ widoku z **liniowy** na **histogram**, co spowoduje wyświetlenie wykresu słupkowego. Łatwiej jest wybrać procesy, które napotykają na użycie wysokiego procesora podczas zbierania.

Ze względu na to, że zawsze będzie wyświetlany pasek **łączny**, należy skupić na paskach, które pokazują wysoką szybkość wyczerpania. Aby wyczyścić widok, można usunąć inne paski. Teraz przejdź wstecz do widoku **wiersza** .

  ![Wskaźniki monitora wydajności](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

Teraz można łatwiej przechwycić proces przyczyna. Domyślnie **maksymalne** i **minimalne** wartości są wielokrotnościami liczby rdzeni na serwerze, lub wątków procesu.

  ![Wyniki monitora wydajności](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

Lista dostępnych narzędzi nie kończy się na że program perfinsights dla monitora wydajności. Masz dostęp do innych narzędzi, takich jak **ProcessMonitor** (ProcMon) lub **Xperf**. Dostępnych jest wiele narzędzi innych firm, które mogą być używane w razie potrzeby.

### <a name="azure-monitoring-tools"></a>Narzędzia do monitorowania platformy Azure

Maszyny wirtualne platformy Azure mają niezawodne metryki, które obejmują podstawowe informacje, takie jak procesor CPU, we/wy sieci i bajty we/wy. W przypadku zaawansowanych metryk, takich jak Azure Monitor, konieczne będzie wybranie tylko kilku opcji w celu skonfigurowania i użycia określonego konta magazynu.

#### <a name="basic-default-counters"></a>Podstawowe (domyślne) liczniki

  ![Metryki wykresu](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Włączanie Azure Monitor

Po włączeniu metryk Azure Monitor oprogramowanie instaluje rozszerzenie na maszynie wirtualnej, a następnie uruchamia Zbieranie szczegółowych metryk, w tym liczników monitora wydajności.

  ![Konto magazynu diagnostyki](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

**Podstawowe** kategorie licznika są ustawiane jako **domyślne**. Można jednak również ustawić kolekcję **niestandardową** .

  ![Liczniki wydajności](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

Po włączeniu ustawień można wyświetlić te liczniki **gościa** w sekcji **metryki** . Możesz również ustawić **alerty** (w tym wiadomości e-mail), jeśli metryki osiągną pewną wartość progową.

  ![Przestrzeń nazw metryk](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

Aby uzyskać więcej informacji na temat korzystania z usługi Azure monitor do zarządzania maszynami wirtualnymi platformy Azure, zobacz [monitorowanie maszyn wirtualnych platformy Azure przy użyciu Azure monitor](../../azure-monitor/insights/monitor-vm-azure.md).

### <a name="reactive-troubleshooting"></a>Rozwiązywanie problemów z reaktywnymi

Jeśli problem już wystąpił, należy wykryć, co spowodowało problem z dużym procesorem CPU w pierwszym miejscu. Zasobów zakresie Reactive może być kłopotliwa. Tryb zbierania danych nie będzie tak przydatny, ponieważ problem już wystąpił.

Jeśli ten problem był wystąpieniem jednorazowym, może być trudne, aby określić, która aplikacja je spowodowała. Jeśli maszyna wirtualna platformy Azure została skonfigurowana do korzystania z pakietu OMS lub innego śledzenia diagnostyki, nadal możesz uzyskać szczegółowe informacje o tym, co spowodowało problem.

Jeśli chodzi o powtarzający się wzorzec, Zbierz dane w czasie, w którym prawdopodobnie wystąpił problem.

Że program perfinsights nie ma jeszcze **zaplanowanego uruchomienia** . Monitor wydajności można jednak uruchomić i zaplanować za pomocą wiersza polecenia.

### <a name="logman-command"></a>Logman — polecenie

Polecenie **Logman Create Counter** służy do uruchamiania kolekcji perfmon przy użyciu wiersza polecenia, w celu zaplanowania go za pomocą **Menedżera zadań**lub do zdalnego uruchomienia.

**Przykład** (obejmuje tryb kolekcji zdalnej)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

Logman.exe można również uruchomić z komputera równorzędnej maszyny wirtualnej platformy Azure w tej samej sieci wirtualnej.

Aby dowiedzieć się więcej na temat tych parametrów, zobacz [Logman Create Counter](/windows-server/administration/windows-commands/logman-create-counter).

Po zebraniu danych monitora wydajności w trakcie wystąpienia problemu pozostałe kroki w celu przeanalizowania danych są takie same, jak opisane wcześniej.

## <a name="conclusion"></a>Podsumowanie

W przypadku dowolnego problemu z wydajnością zrozumienie obciążenia jest kluczem do rozwiązania problemu. Opcje w różnych jednostkach SKU maszyn wirtualnych i różnych opcjach magazynu dyskowego muszą być oceniane przez utrzymywanie fokusu w obciążeniu produkcyjnym. Proces testowania rozwiązań na różnych maszynach wirtualnych może pomóc w podjęciu najlepszej decyzji.

Ze względu na to, że operacje użytkownika i ilość danych są różne, należy zawsze utrzymywać bufor w możliwościach obliczeniowych, sieciowych i we/wy maszyny wirtualnej. Teraz wszelkie nagłe zmiany obciążenia nie mają dużego efektu.

Jeśli przewidywane jest zwiększenie obciążenia, należy przejść do wyższej jednostki SKU, która ma większą moc obliczeniową. Jeśli obciążenie będzie czasochłonne, należy wybrać jednostki SKU maszyny wirtualnej.