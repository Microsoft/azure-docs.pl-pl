---
title: Monitorowanie szacunkowych kosztów usług pulpitu wirtualnego systemu Windows — Azure
description: Jak oszacować koszty i ceny korzystania z Azure Monitor dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 099def5a29c8a9b46733ba435befed7210756012
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709986"
---
# <a name="estimate-azure-monitor-costs"></a>Oszacuj koszty Azure Monitor

Dzienniki Azure Monitor to usługa, która zbiera, indeksuje i przechowuje dane wygenerowane przez środowisko. W związku z tym model cen Azure Monitor jest oparty na ilości danych, które zostały wprowadzone do i przetworzone (lub "pozyskiwane") przez obszar roboczy Log Analytics w gigabajtach dziennie. Koszt obszaru roboczego Log Analytics nie jest uzależniony od ilości zebranych danych, ale również wybranego planu płatności platformy Azure oraz sposobu przechowywania danych generowanych przez środowisko.

W tym artykule opisano następujące kwestie, które ułatwią zrozumienie sposobu, w jaki Azure Monitor działa:

- Jak oszacować koszty pozyskiwania i przechowywania danych przed włączeniem tej funkcji
- Sposób mierzenia i kontrolowania pozyskiwania i magazynowania w celu obniżenia kosztów podczas korzystania z tej funkcji

>[!NOTE]
> Wszystkie informacje o rozmiarach i cenach wymienione w tym artykule przedstawiają sposób działania szacowania. Aby uzyskać dokładniejszą ocenę na podstawie modelu cen Log Analytics Azure Monitor i regionu platformy Azure, zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="estimate-data-ingestion-and-storage-costs"></a>Oszacowanie kosztów pozyskiwania i magazynowania danych

Zalecamy używanie wstępnie zdefiniowanego zestawu danych zapisaną jako dzienniki w obszarze roboczym Log Analytics. W poniższym przykładzie szacunków będziemy szukać danych rozliczanych w konfiguracji domyślnej

Wstępnie zdefiniowane zestawy danych dla Azure Monitor dla pulpitu wirtualnego systemu Windows obejmują:

- Liczniki wydajności z hostów sesji
- Dzienniki zdarzeń systemu Windows z hostów sesji
- Diagnostyka pulpitu wirtualnego systemu Windows z infrastruktury usług

Koszty pozyskiwania i magazynowania danych zależą od rozmiaru środowiska, kondycji i użycia. Przykładowe oszacowania, które będziemy używać w tym artykule, aby obliczyć zakresy kosztów, których można oczekiwać, są oparte na zdrowych maszynach wirtualnych z lampą w celu użycia, na podstawie naszych [wytycznych dotyczących rozmiaru maszyn wirtualnych](/remote/remote-desktop-services/virtual-machine-recs), aby obliczyć zakres pozyskiwania danych i koszty magazynu, których można oczekiwać.

Używana w naszym przykładzie maszyna wirtualna o uproszczonym użyciu obejmuje następujące składniki:

- 4 procesorów wirtualnych vCPU, 1 dysk
- 16 sesji dziennie
- Średni czas trwania sesji wynoszący 2 godziny (120 minut)
- 100 procesów na sesję

W naszym przykładzie będzie używana maszyna wirtualna zużycia mocy, która obejmuje następujące składniki:

- 6 procesorów wirtualnych vCPU, 1 dysk
- 6 sesji dziennie
- Średni czas trwania sesji wynoszący 4 godziny (240 minut)
- 200 procesów na sesję

## <a name="estimating-performance-counter-ingestion"></a>Szacowanie pozyskiwania licznika wydajności

Liczniki wydajności pokazują, jak są wykonywane zasoby systemowe. Pozyskiwanie danych licznika wydajności zależy od rozmiaru i użycia środowiska. W większości przypadków liczniki wydajności powinny należeć do 80 do 99% pozyskiwania danych Azure Monitor dla pulpitu wirtualnego systemu Windows.

Przed rozpoczęciem szacowania należy zrozumieć, że każdy licznik wydajności wysyła dane z określoną częstotliwością. Ustawimy domyślną częstotliwość próbkowania na minutę (można również edytować tę stawkę w ustawieniach), ale ta częstotliwość zostanie zastosowana w różnych iloczynach mnożenia w zależności od licznika. Następujące czynniki wpływają na stawkę:

- Dla każdego czynnika maszyny wirtualnej (VM) każdy licznik wysyła dane na maszynę wirtualną w środowisku z domyślną częstotliwością próbkowania na minutę, podczas gdy maszyna wirtualna jest uruchomiona. Można oszacować liczbę rekordów wysyłanych przez te liczniki dziennie przez pomnożenie domyślnej częstotliwości próbkowania na minutę przez liczbę maszyn wirtualnych w środowisku, a następnie pomnożenie tej liczby przez średni czas działania maszyny wirtualnej na dzień.

   Podsumowując:

   Domyślna częstotliwość próbkowania na minutę × liczba rdzeni procesora CPU w jednostce SKU maszyny wirtualnej × liczba maszyn wirtualnych × średni czas działania maszyny wirtualnej na dzień = Liczba rekordów wysłanych dziennie

- Dla każdego czynnika procesora CPU każdy licznik wysyła wartość domyślna częstotliwość próbkowania na minutę na vCPU w każdej maszynie wirtualnej w środowisku, podczas gdy maszyna wirtualna jest uruchomiona. Można oszacować liczbę rekordów, które będą wysyłane przez liczniki dziennie przez pomnożenie domyślnej częstotliwości próbkowania na minutę przez liczbę rdzeni procesora CPU w jednostce SKU maszyny wirtualnej, a następnie pomnożenie tej liczby przez liczbę minut działania maszyny wirtualnej oraz liczbę maszyn wirtualnych w środowisku.

   Podsumowując:
   
   Domyślna częstotliwość próbkowania na minutę × liczba rdzeni procesora CPU w jednostce SKU maszyny wirtualnej × liczba minut działania maszyny wirtualnej × liczba maszyn wirtualnych = liczba rekordów wysłanych dziennie

- Dla każdego czynnika dysku każdy licznik wysyła dane z domyślną częstotliwością próbkowania dla każdego dysku na każdej maszynie wirtualnej w środowisku. Liczba rekordów wysyłanych przez te liczniki na dzień to domyślna częstotliwość próbkowania na minutę pomnożona przez liczbę dysków w jednostce SKU maszyny wirtualnej pomnożona przez 60 minut na godzinę, a na koniec pomnożona przez średnią liczbę aktywnych godzin dla maszyny wirtualnej.

   Podsumowując:

   Domyślna częstotliwość próbkowania na minutę × liczba dysków w jednostce SKU maszyny wirtualnej × 60 minut na godzinę × liczba maszyn wirtualnych × średni czas działania maszyny wirtualnej na dzień = Liczba rekordów wysłanych dziennie

- Dla każdego czynnika sesji każdy licznik wysyła dane z domyślną częstotliwością próbkowania dla każdej sesji w środowisku, gdy sesja jest podłączona. Można oszacować liczbę rekordów, które będą wysyłane przez te liczniki dziennie, przez pomnożenie domyślnej częstotliwości próbkowania na minutę przez średnią liczbę sesji dziennie i średni czas trwania sesji.

   Podsumowując:

   Domyślna częstotliwość próbkowania na minutę x sesji dziennie × średni czas trwania sesji = liczba rekordów wysłanych dziennie

- Dla współczynnika dla poszczególnych procesów każdy licznik wysyła dane przy użyciu domyślnej stawki dla każdego procesu w poszczególnych sesjach w środowisku. Można oszacować liczbę rekordów, które będą wysyłane przez te liczniki dziennie, mnożąc domyślną częstotliwość próbkowania na minutę przez średnią liczbę sesji dziennie, a następnie pomnożenie tego przez średni czas trwania sesji i średnią liczbę procesów na sesję.
  
   Podsumowując:

   Domyślna częstotliwość próbkowania na minutę x sesji dziennie × średni czas trwania sesji × średnia liczba procesów na sesję = liczba rekordów wysłanych dziennie

W poniższej tabeli wymieniono 20 liczników wydajności Azure Monitor na potrzeby zbierania pulpitów wirtualnych systemu Windows i ich domyślnych stawek:

| Nazwa licznika | Domyślna częstotliwość próbkowania | Współczynnik częstotliwości |
|--------------|---------------------|------------------|
| Dysk logiczny (C:) \\ % wolnego miejsca | 60 sekund  | Na dysk             |
| Dysk logiczny (C:) \\ średnia długość kolejki dysku   | 30 sekund    | Na dysk             |
| Dysk logiczny (C:) \\ Średni czas dysku w s/transfer  | 60 sekund       | Na dysk             |
| Dysk logiczny (C:) \\ Bieżąca długość kolejki dysku  | 30 sekund      | Na dysk             |
| Pamięć ( \* ) \\ dostępne MB | 30 sekund    | Na maszynę wirtualną  |
| Pamięć ( \* ) \\ błędy stron/s | 30 sekund   | Na maszynę wirtualną  |
| Pamięć ( \* ) \\ strony/s | 30 sekund   | Na maszynę wirtualną  |
| Pamięć ( \* ) \\ % zadeklarowanych bajtów w użyciu | 30 sekund    | Na maszynę wirtualną  |
| DyskFizyczny ( \* ) \\ średnia długość kolejki dysku | 30 sekund      | Na dysk             |
| DyskFizyczny ( \* ) \\ Średni czas dysku w s/odczyt | 30 sekund  | Na dysk             |
| DyskFizyczny ( \* ) \\ Średni czas dysku w s/transfer | 30 sekund  | Na dysk             |
| DyskFizyczny ( \* ) \\ Średni czas dysku w s/zapis | 30 sekund | Na dysk             |
| Informacje o procesorze (_Total) \\ % czasu procesora | 30 sekund | Na rdzeń/procesor CPU         |
| \*Aktywne sesje usług terminalowych \\          | 60 sekund | Na maszynę wirtualną  |
| \*Nieaktywne sesje usług \\ terminalowych        | 60 sekund | Na maszynę wirtualną  |
| \* \\ Łączna liczba sesji usług terminalowych | 60 sekund | Na maszynę wirtualną  |
| Opóźnienie wejściowe użytkownika na proces ( \* ) \\ maksymalne opóźnienie wejścia         | 30 sekund | Na proces          |
| Opóźnienie wejściowe użytkownika na sesję ( \* ) \\ maksymalne opóźnienie wejściowe        | 30 sekund | Na sesję          |
| Sieć funkcji RemoteFX ( \* ) \\ bieżący RTT TCP | 30 sekund | Na maszynę wirtualną  |
| Sieć funkcji RemoteFX ( \* ) \\ Bieżąca przepustowość UDP     | 30 sekund | Na maszynę wirtualną  |

W przypadku oszacowania rozmiaru każdego rekordu do 200 bajtów Przykładowa maszyna wirtualna z lekkim obciążeniem na domyślną częstotliwość próbkowania wyśle około 90 megabajtów danych licznika wydajności dziennie na maszynę wirtualną. W tym przypadku Przykładowa maszyna wirtualna z uruchomionym obciążeniem energetycznym wyśle około 130 megabajtów danych licznika wydajności dziennie na maszynę wirtualną. Jednak rozmiar rekordu i użycie środowiska mogą się różnić, więc megabajty na dzień używane przez wdrożenie mogą być różne.

Aby dowiedzieć się więcej o licznikach wydajności opóźnienia wejścia, zobacz [liczniki wydajności opóźnienia danych wejściowych użytkownika](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="estimating-windows-event-log-ingestion"></a>Szacowanie pozyskiwania dziennika zdarzeń systemu Windows

Dzienniki zdarzeń systemu Windows to źródła danych zbierane przez Log Analytics agentów na maszynach wirtualnych z systemem Windows. Można zbierać zdarzenia z dzienników standardowych, takich jak system i aplikacja, a także niestandardowych dzienników tworzonych przez aplikacje, które muszą być monitorowane.

Są to domyślne zdarzenia systemu Windows dla Azure Monitor dla pulpitu wirtualnego systemu Windows:

- Aplikacja
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/administrator
- Microsoft-Windows-TerminalServices-LocalSessionManager/operacyjny
- System
- Microsoft-FSLogix-Apps/Operational
- Microsoft-FSLogix-Apps/administrator

Zdarzenia systemu Windows wysyłają przy każdym spełnieniu warunków zdarzenia w środowisku. Maszyny w dobrej kondycji będą wysyłać mniej zdarzeń niż w Stanach w złej kondycji. Ze względu na to, że liczba zdarzeń jest nieprzewidywalne, w przypadku tego oszacowania używamy zakresu od 1 000 do 10 000 zdarzeń na maszynę wirtualną na podstawie przykładów. Na przykład, jeśli szacujemy każdy rozmiar rekordu zdarzenia w tym przykładzie na 1 500 bajtów, to zajmie około 2 do 15 megabajtów danych zdarzeń dziennie dla określonego środowiska.

Aby dowiedzieć się więcej na temat zdarzeń systemu Windows, zobacz [Właściwości rekordów zdarzeń systemu Windows](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="estimating-diagnostics-ingestion"></a>Szacowanie pozyskiwania danych diagnostycznych

Usługa diagnostyczna tworzy dzienniki aktywności zarówno dla użytkownika, jak i akcji administracyjnych.

Są to nazwy dzienników aktywności, które są śledzone przez licznik diagnostyczny:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

Usługa wysyła informacje diagnostyczne, gdy środowisko spełnia warunki wymagane do dokonania rekordu. Ze względu na to, że liczba rekordów diagnostycznych jest nieprzewidywalne, użyjemy zakresu od 500 do 1000 zdarzeń na maszynę wirtualną na podstawie przykładów z zdrowego środowiska dla tego oszacowania.

Na przykład jeśli szacujemy każdy rozmiar rekordu diagnostycznego w tym przykładzie na 200 bajtów, łączne dane pozyskiwane byłyby mniej niż 1 MB na maszynę wirtualną dziennie.

Aby dowiedzieć się więcej na temat kategorii dziennika aktywności, zobacz [Diagnostyka pulpitu wirtualnego systemu Windows](diagnostics-log-analytics.md).

## <a name="estimating-total-costs"></a>Szacowanie łącznego kosztu

Na koniec szacujemy łączny koszt. W tym przykładzie Załóżmy, że będziemy mieć następujące wyniki na podstawie przykładowych wartości w poprzednich sekcjach:

| Źródło danych        | Szacowany rozmiar dziennie (w megabajtach)   |
|-------------------------------------|------------------------------------------|
| Liczniki wydajności   | 90-130 |
| Zdarzenia    | 2-15 |
| Diagnostyka pulpitu wirtualnego systemu Windows | \< jedno |

W tym przykładzie całkowita liczba pozyskanych danych dla Azure Monitor dla pulpitu wirtualnego systemu Windows wynosi od 92 do 145 megabajtów na maszynę wirtualną dziennie. Innymi słowy, co 31 dni, każda maszyna wirtualna pobiera około 3 do 5 gigabajtów danych.

Korzystając z domyślnego modelu "płatność zgodnie z rzeczywistym użyciem" dla [log Analytics cennika](https://azure.microsoft.com/pricing/details/monitor/), możesz oszacować Azure monitor do zbierania danych i koszt magazynowania miesięcznie. W zależności od pozyskiwania danych można również rozważyć model rezerwacji pojemności dla Log Analytics cenowych.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Zarządzanie pozyskiwaniem danych w celu obniżenia kosztów

W tej sekcji wyjaśniono, jak mierzyć pozyskiwanie danych i zarządzać nimi w celu obniżenia kosztów.

Aby dowiedzieć się więcej o zarządzaniu prawami i uprawnieniami do skoroszytu, zobacz [Kontrola dostępu](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>Usunięcie punktów danych wpłynie na odpowiadające im wizualizacje w Azure Monitor dla pulpitu wirtualnego systemu Windows.

### <a name="log-analytics-settings"></a>Ustawienia Log Analytics

Poniżej przedstawiono kilka sugestii pozwalających zoptymalizować ustawienia Log Analytics w celu zarządzania pozyskiwaniem danych:

- Za pomocą wydanego obszaru roboczego Log Analytics dla zasobów pulpitu wirtualnego systemu Windows upewnij się, że Log Analytics zbiera tylko liczniki wydajności i zdarzenia dla maszyn wirtualnych w ramach wdrożenia pulpitu wirtualnego systemu Windows.
- Dostosuj ustawienia magazynu Log Analytics, aby zarządzać kosztami. Możesz skrócić okres przechowywania, sprawdzić, czy stała warstwa cenowa magazynu będzie bardziej opłacalna, czy też określić granice ilości danych, które można pozyskać, aby ograniczyć wpływ wdrożenia w złej kondycji. Aby dowiedzieć się więcej, zobacz [Zarządzanie użyciem i kosztami dzienników Azure monitor](../azure-monitor/platform/manage-cost-storage.md).

### <a name="remove-excess-data"></a>Usuń nadmiarowe dane

Konfiguracja domyślna to jedyny zestaw danych, które zalecamy dla Azure Monitor dla pulpitu wirtualnego systemu Windows. Zawsze możesz dodać dodatkowe punkty danych i wyświetlić je w diagnostyce hosta: przeglądarka hosta lub utworzyć niestandardowe wykresy dla nich, jednak dodane dane spowodują zwiększenie kosztu Log Analytics. Można je usunąć, aby zmniejszyć koszty.

### <a name="measure-and-manage-your-performance-counter-data"></a>Mierzenie danych licznika wydajności i zarządzanie nimi

Prawdziwe koszty monitorowania zależą od rozmiaru środowiska, użycia i kondycji. Aby dowiedzieć się, jak zmierzyć pozyskiwanie danych w obszarze roboczym Log Analytics, zobacz [Opis ilości danych dziennika](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)pozyskiwanego.

Liczniki wydajności, które są używane przez hosty sesji, prawdopodobnie będą największe źródło danych pozyskiwanych Azure Monitor dla pulpitu wirtualnego systemu Windows. Następujący niestandardowy szablon zapytania dla obszaru roboczego Log Analytics może śledzić częstotliwość i megabajty pozyskiwane według licznika wydajności w ciągu ostatniego dnia:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Pamiętaj, aby zastąpić wartości symboli zastępczych szablonu wartościami używanymi przez środowisko. w przeciwnym razie zapytanie nie będzie działało.

To zapytanie będzie zawierać wszystkie liczniki wydajności, które zostały włączone w środowisku, a nie tylko domyślne dla Azure Monitor dla pulpitu wirtualnego systemu Windows. Te informacje mogą ułatwić zrozumienie, które obszary mają być celem obniżenia kosztów, takich jak zmniejszenie częstotliwości licznika lub całkowite usunięcie.

Możesz również obniżyć koszty, usuwając liczniki wydajności. Aby dowiedzieć się, jak usunąć liczniki wydajności lub edytować istniejące liczniki w celu zmniejszenia ich częstotliwości, zobacz [Konfigurowanie liczników wydajności](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Zarządzanie dziennikami zdarzeń systemu Windows

Zdarzenia systemu Windows nie mogą spowodować pozyskania danych, gdy wszystkie hosty są w dobrej kondycji. Host w złej kondycji może zwiększyć liczbę zdarzeń wysyłanych do dziennika, ale te informacje mogą być niezbędne do rozwiązania problemów z hostem. Zalecamy ich utrzymywanie. Aby dowiedzieć się więcej na temat zarządzania dziennikami zdarzeń systemu Windows, zobacz [Konfigurowanie dzienników zdarzeń systemu Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

### <a name="manage-diagnostics"></a>Zarządzanie diagnostyką

Diagnostyka pulpitu wirtualnego systemu Windows powinna mieć mniej niż 1% kosztów magazynowania danych, więc nie zalecamy ich usuwania. Aby zarządzać diagnostyką pulpitu wirtualnego systemu Windows, [użyj log Analytics dla funkcji diagnostyki](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Azure Monitor dla pulpitu wirtualnego systemu Windows w następujących artykułach:

- [Aby monitorować wdrożenie, użyj Azure monitor dla pulpitu wirtualnego systemu Windows](azure-monitor.md).
- Aby dowiedzieć się więcej na temat terminów i koncepcji, Skorzystaj z [słownika](azure-monitor-glossary.md) .
- Jeśli wystąpi problem, zapoznaj się z naszym [przewodnikiem rozwiązywania problemów](troubleshoot-azure-monitor.md) , aby uzyskać pomoc.
- Sprawdź [użycie monitorowania i szacowane koszty w Azure monitor](../azure-monitor/usage-estimated-costs.md) , aby dowiedzieć się więcej o zarządzaniu kosztami monitorowania.
