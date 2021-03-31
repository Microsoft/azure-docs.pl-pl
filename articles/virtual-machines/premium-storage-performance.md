---
title: 'Azure Premium Storage: projektowanie pod kątem wysokiej wydajności'
description: Projektuj aplikacje o wysokiej wydajności przy użyciu dysków zarządzanych w usłudze Azure Premium SSD. Premium Storage oferuje wysoką wydajność, obsługę dysków o niskich opóźnieniach dla obciążeń intensywnie korzystających z operacji we/wy działających na platformie Azure Virtual Machines.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7e93c659ad58db8d82e68380ab6a0855af27e1bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882386"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: projektowanie pod kątem wysokiej wydajności

Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Możesz użyć instrukcji przedstawionych w tym dokumencie wraz z najlepszymi rozwiązaniami dotyczącymi wydajności mającymi zastosowanie do technologii używanych przez aplikację. Aby zilustrować wskazówki, firma Microsoft użyła SQL Server działającego na Premium Storage na przykład w tym dokumencie.

Chociaż w tym artykule opisano scenariusze wydajności dla warstwy magazynowania, należy zoptymalizować warstwę aplikacji. Na przykład w przypadku hostowania farmy programu SharePoint w usłudze Azure Premium Storage można użyć przykładów SQL Server z tego artykułu, aby zoptymalizować serwer bazy danych. Ponadto Zoptymalizuj serwer sieci Web i serwer aplikacji farmy programu SharePoint w celu uzyskania najwyższej wydajności.

Ten artykuł pomoże odpowiedzieć na często zadawane pytania dotyczące optymalizowania wydajności aplikacji na platformie Azure Premium Storage,

* Jak zmierzyć wydajność aplikacji?  
* Dlaczego nie widzisz oczekiwanej wysokiej wydajności?  
* Jakie czynniki wpływają na wydajność aplikacji na Premium Storage?  
* Jak te czynniki wpływają na wydajność aplikacji na Premium Storage?  
* Jak można zoptymalizować na potrzeby operacji wejścia/wyjścia na sekundę, przepustowości i opóźnień?  

Te wytyczne zostały podane w odniesieniu do Premium Storage, ponieważ obciążenia działające na Premium Storage są wysoce wrażliwe na wydajność. W razie potrzeby podano przykłady. Niektóre z tych wytycznych można także zastosować do aplikacji uruchamianych na maszynach wirtualnych IaaS przy użyciu dysków magazynu w warstwie Standardowa.

> [!NOTE]
> Czasami problem z wydajnością dysku to w rzeczywistości wąskie gardło sieci. W takich sytuacjach należy zoptymalizować [wydajność sieci](../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Jeśli chcesz przeprowadzić test porównawczy dysku, zapoznaj się z naszymi artykułami dotyczącymi tworzenia wzorców dysków:
>
> * Dla systemu Linux: [test porównawczy aplikacji na Azure Disk Storage](./disks-benchmarks.md)
> * Dla systemu Windows: przeprowadzenie [testu porównawczego dysku](./disks-benchmarks.md).
>
> Jeśli maszyna wirtualna obsługuje przyspieszone sieci, należy upewnić się, że jest ona włączona. Jeśli nie jest włączona, można ją włączyć dla już wdrożonych maszyn wirtualnych w [systemach Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Przed rozpoczęciem, jeśli dopiero zaczynasz Premium Storage, najpierw Przeczytaj artykuł [Wybieranie typu dysku platformy Azure dla maszyn wirtualnych IaaS](disks-types.md) i [elementów docelowych skalowalności dla kont usługi BLOB Storage na stronie Premium](../storage/blobs/scalability-targets-premium-page-blobs.md).

## <a name="application-performance-indicators"></a>Wskaźniki wydajności aplikacji

Oceniamy, czy aplikacja działa prawidłowo, czy nie używa wskaźników wydajności, takich jak szybka aplikacja przetwarza żądanie użytkownika, ile danych jest przetwarzanych przez aplikację na żądanie, ile żądań jest przetwarzanych przez aplikację w określonym przedziale czasu, jak długo użytkownik musi oczekiwać na odpowiedź po przesłaniu żądania. Warunki techniczne dla tych wskaźników wydajności to między innymi operacje we/wy na sekundę, przepływności lub przepustowość oraz opóźnienia.

W tej sekcji omówiono typowe wskaźniki wydajności w kontekście Premium Storage. W poniższej sekcji, zbierając wymagania dotyczące aplikacji, dowiesz się, jak zmierzyć te wskaźniki wydajności dla aplikacji. Później w celu optymalizacji wydajności aplikacji można dowiedzieć się więcej na temat czynników wpływających na te wskaźniki wydajności i zalecenia w celu ich zoptymalizowania.

## <a name="iops"></a>Liczba operacji we/wy na sekundę

Operacje we/wy lub wejścia/wyjścia na sekundę to liczba żądań wysyłanych przez aplikację do dysków magazynu w jednej sekundzie. Operacja wejścia/wyjścia może mieć Odczyt lub zapis, sekwencyjne lub losowe. Aplikacje przetwarzania transakcji online (OLTP), takie jak witryna sieci Web w trybie online, muszą natychmiast przetwarzać wiele równoczesnych żądań użytkowników. Żądania użytkownika są wstawiane i aktualizowane z dużą ilością transakcji bazy danych, które aplikacja musi szybko przetwarzać. W związku z tym aplikacje OLTP wymagają bardzo dużej liczby operacji we/wy. Takie aplikacje obsługują miliony małych i losowych żądań we/wy. Jeśli masz taką aplikację, musisz zaprojektować infrastrukturę aplikacji, aby zoptymalizować ją pod kątem operacji we/wy na sekundę. W dalszej części, *Optymalizacja wydajności aplikacji* pozwala szczegółowo omówić wszystkie czynniki, które należy wziąć pod uwagę w celu uzyskania dużej liczby operacji we/wy na sekundę.

Po dołączeniu dysku magazynu premium do maszyny wirtualnej o dużej skali platforma Azure aprowizuje dla Ciebie gwarantowaną liczbę operacji we/wy zgodnie ze specyfikacją dysku. Na przykład dysk P50 aprowizuje 7500 IOPS. Każdy rozmiar maszyny wirtualnej o dużej skali ma również określony limit IOPS, jaki może utrzymać. Na przykład Standardowa maszyna wirtualna GS5 ma limit liczby operacji we/wy 80 000.

## <a name="throughput"></a>Przepływność

Przepływność lub przepustowość to ilość danych wysyłanych przez aplikację do dysków magazynu w określonym interwale. Jeśli aplikacja wykonuje operacje wejścia/wyjścia z dużymi rozmiarami jednostek we/wy, wymaga dużej przepływności. Aplikacje magazynu danych mogą wystawiać intensywne operacje skanowania, które uzyskują dostęp do dużych porcji danych jednocześnie i często wykonują operacje zbiorcze. Innymi słowy, takie aplikacje wymagają większej przepływności. Jeśli masz taką aplikację, musisz zaprojektować jej infrastrukturę w celu optymalizacji pod kątem przepływności. W następnej sekcji omawiamy szczegółowo czynniki, które należy dostosować w celu osiągnięcia tego celu.

Po dołączeniu dysku magazynu w warstwie Premium do maszyny wirtualnej o dużej skali platforma Azure udostępnia przepływność zgodnie z tą specyfikacją dysku. Na przykład dysk P50 aprowizuje przepływność dysku wynoszącą 250 MB na sekundę. Każdy rozmiar maszyny wirtualnej o dużej skali ma również określony limit przepływności, jaki może utrzymać. Na przykład maszyna wirtualna Standard GS5 ma maksymalną przepływność wynoszącą 2000 MB na sekundę.

Istnieje relacja między przepływem i liczbą operacji wejścia/wyjścia, jak pokazano w poniższej formule.

![Relacja IOPS i przepływność](linux/media/premium-storage-performance/image1.png)

W związku z tym ważne jest, aby określić optymalną przepływność i wartości IOPS wymagane przez aplikację. W trakcie próby optymalizacji jeden ma również wartość inne. W późniejszej sekcji *Optymalizacja wydajności aplikacji* zawiera szczegółowe informacje na temat optymalizowania operacji we/wy i przepływności.

## <a name="latency"></a>Opóźnienie

Opóźnienie to czas, przez który aplikacja otrzymuje pojedyncze żądanie, wysyła ją do dysków magazynu i wysyła odpowiedź do klienta programu. Jest to krytyczna miara wydajności aplikacji oprócz operacji we/wy i przepływności. Opóźnienie dysku magazynu w warstwie Premium to czas potrzebny do pobrania informacji dla żądania i przekazania go z powrotem do aplikacji. Premium Storage zapewnia spójne małe opóźnienia. Dyski w warstwie Premium zostały zaprojektowane w taki sposób, aby zapewniały milisekundowe opóźnienia w przypadku większości operacji we/wy. Jeśli włączysz buforowanie hosta tylko do odczytu na dyskach magazynu w warstwie Premium, możesz uzyskać znacznie mniejsze opóźnienie odczytu. Szczegółowo omówiono buforowanie dysków w dalszej części w sekcji *Optymalizacja wydajności aplikacji*.

Optymalizacja aplikacji pozwala uzyskać większą liczbę operacji we/wy na sekundę, co wpłynie na opóźnienie aplikacji. Po dostrojeniu wydajności aplikacji należy zawsze oszacować opóźnienia aplikacji, aby uniknąć nieoczekiwanego dużego opóźnienia.

Następujące operacje płaszczyzny kontroli na Managed Disks mogą polegać na przemieszczaniu dysku z jednej lokalizacji magazynu do innej. Jest to zorganizowane za pośrednictwem kopii w tle danych, które mogą potrwać kilka godzin, zwykle krócej niż 24 godziny, w zależności od ilości danych na dyskach. W tym czasie aplikacja może korzystać z wyższego poziomu niż zwykłe opóźnienie odczytu, ponieważ niektóre odczyty mogą zostać przekierowane do oryginalnej lokalizacji i może trwać dłużej. Nie ma to wpływu na opóźnienie zapisu w tym okresie.

- Zaktualizuj typ magazynu.
- Odłącz i Dołącz dysk z jednej maszyny wirtualnej do innej.
- Utwórz dysk zarządzany na podstawie dysku VHD.
- Utwórz dysk zarządzany na podstawie migawki.
- Konwertowanie dysków niezarządzanych na dyski zarządzane.

## <a name="performance-application-checklist-for-disks"></a>Lista kontrolna aplikacji wydajności dla dysków

Pierwszym krokiem projektowania aplikacji o wysokiej wydajności działającej na platformie Azure Premium Storage jest zrozumienie wymagań dotyczących wydajności aplikacji. Po zebraniu wymagań dotyczących wydajności można zoptymalizować aplikację, aby osiągnąć optymalną wydajność.

W poprzedniej sekcji wyjaśniono typowe wskaźniki wydajności, operacje we/wy na sekundę, przepływność i opóźnienie. Należy określić, które z tych wskaźników wydajności mają kluczowe znaczenie dla aplikacji, aby zapewnić odpowiednie środowisko użytkownika. Na przykład duże liczby operacji we/wy są największe w przypadku aplikacji OLTP przetwarzających miliony transakcji w drugim. Wysoka przepływność jest niezwykle ważna w przypadku aplikacji magazynu danych przetwarzających duże ilości danych w drugim. Bardzo małe opóźnienia są kluczowe dla aplikacji w czasie rzeczywistym, takich jak internetowe przesyłanie strumieniowe wideo na żywo.

Następnie Zmierz maksymalne wymagania dotyczące wydajności aplikacji przez cały okres istnienia. Użyj poniższej listy kontrolnej poniżej jako początku. Zanotuj maksymalne wymagania dotyczące wydajności podczas normalnych, szczytowych i czasowych okresów obciążenia. Identyfikując wymagania dla wszystkich poziomów obciążeń, będzie można określić ogólne wymagania dotyczące wydajności aplikacji. Na przykład normalne obciążenie witryny internetowej handlu elektronicznego będzie transakcjami, które są obsługiwane w ciągu ostatnich dni w roku. Szczytowe obciążenie witryny sieci Web będzie transakcjami, które są obsługiwane przez okres urlopowy lub specjalne wydarzenia sprzedaży. Obciążenie szczytowe jest zwykle wykonywane przez ograniczony czas, ale może wymagać, aby aplikacja mogła skalować dwie lub więcej razy swoją normalną operację. Dowiedz się więcej o 50 percentylu, 90 percentylu i 99 wymagania dotyczące percentylu. Pozwala to na odfiltrowanie wszelkich wartości odstających w wymaganiach dotyczących wydajności i skoncentrowanie się na optymalizacji pod kątem odpowiednich wartooci.

## <a name="application-performance-requirements-checklist"></a>Lista kontrolna wymagań dotyczących wydajności aplikacji

| **Wymagania dotyczące wydajności** | **50 percentyl** | **90 percentyl** | **99 percentyl** |
| --- | --- | --- | --- |
| Maksymalnie z Liczba transakcji na sekundę | | | |
| Operacje odczytu (%) | | | |
| Operacje zapisu% | | | |
| % Operacji losowych | | | |
| % Operacji sekwencyjnych | | | |
| Rozmiar żądania we/wy | | | |
| Średnia przepływność | | | |
| Maksymalnie z Przepływność | | | |
| Długości. Opóźnienie | | | |
| Średnie opóźnienie | | | |
| Maksymalnie z Procesor CPU | | | |
| Średni procesor CPU | | | |
| Maksymalnie z Pamięć | | | |
| Średnia pamięć | | | |
| Głębokość kolejki | | | |

> [!NOTE]
> Należy rozważyć skalowanie tych numerów w zależności od oczekiwanego przyszłego wzrostu aplikacji. Dobrym pomysłem jest zaplanowanie wzrostu czasu, ponieważ może to utrudnić zmianę infrastruktury w celu zwiększenia wydajności.

Jeśli masz istniejącą aplikację i chcesz przenieść ją do Premium Storage, najpierw Skompiluj listę kontrolną powyżej dla istniejącej aplikacji. Następnie utwórz prototyp aplikacji na Premium Storage i Zaprojektuj aplikację na podstawie wytycznych opisanych w sekcji *Optymalizacja wydajności aplikacji* w dalszej części tego dokumentu. W następnym artykule opisano narzędzia, za pomocą których można zbierać pomiary wydajności.

### <a name="counters-to-measure-application-performance-requirements"></a>Liczniki do mierzenia wymagań dotyczących wydajności aplikacji

Najlepszym sposobem mierzenia wymagań dotyczących wydajności aplikacji jest użycie narzędzi do monitorowania wydajności dostarczonych przez system operacyjny serwera programu. Możesz użyć Monitora wydajności dla systemów Windows i iostat dla systemu Linux. Te narzędzia przechwytują liczniki odpowiadające każdej mierzeniu opisanej w powyższej sekcji. Wartości tych liczników należy przechwycić, gdy w aplikacji działają normalne, szczytowe i wolne obciążenia.

Liczniki Monitora wydajności są dostępne dla procesora, pamięci i każdego dysku logicznego i dysku fizycznego serwera programu. W przypadku korzystania z dysków magazynu Premium Storage z maszyną wirtualną liczniki dysku fizycznego są dla każdego dysku magazynu w warstwie Premium, a liczniki dysku logicznego są dla każdego woluminu utworzonego na dyskach magazynu w warstwie Premium. Należy przechwycić wartości dysków, które obsługują obciążenie aplikacji. Jeśli istnieje jedno do jednego mapowanie między dyskami logicznymi i fizycznymi, można odwoływać się do liczników dysków fizycznych. w przeciwnym razie odwołaj się do liczników dysku logicznego. W systemie Linux polecenie iostat generuje raport użycia procesora CPU i dysku. Raport o wykorzystaniu dysku zawiera statystyki na fizyczne urządzenie lub partycję. Jeśli masz serwer bazy danych z danymi i dzienniki na oddzielnych dyskach, Zbierz te dane dla obu dysków. W poniższej tabeli opisano liczniki dotyczące dysków, procesorów i pamięci:

| Licznik | Opis | Monitora wydajności | Iostat |
| --- | --- | --- | --- |
| **Liczba operacji we/wy na sekundę** |Liczba żądań we/wy wystawionych na dysk magazynujący na sekundę. |Odczyty dysku/s <br> Zapisy dysku/s |TPS <br> r/s <br> w/s |
| **Odczyty i zapisy z dysku** |% operacji odczytu i zapisu wykonanych na dysku. |Czas odczytu dysku (%) <br> Czas zapisu na dysku (%) |r/s <br> w/s |
| **Przepływność** |Ilość danych odczytywanych lub zapisywana na dysku na sekundę. |Bajty odczytu dysku/s <br> Bajty zapisu dysku/s |kB_read/s <br> kB_wrtn/s |
| **Opóźnienie** |Łączny czas wykonywania żądania we/wy dysku. |Średni czas dysku w s/odczyt <br> Średni czas dysku w s/zapis |await <br> svctm |
| **Rozmiar we/wy** |Rozmiar żądań we/wy dotyczy dysków magazynu. |Średnia liczba bajtów dysku/odczyt <br> Średnia liczba bajtów dysku/zapis |avgrq — sz |
| **Głębokość kolejki** |Liczba oczekujących żądań we/wy oczekujących na odczytanie lub zapis na dysku magazynu. |Bieżąca długość kolejki dysku |avgqu — sz |
| **Maks. pamięć** |Ilość pamięci wymaganej do bezproblemowego uruchamiania aplikacji |Zadeklarowane bajty w użyciu (%) |Użyj vmstat |
| **Max. CPU** |Ilość czasu wymagana do bezproblemowego uruchamiania aplikacji |Czas procesora (%) |% util |

Dowiedz się więcej na temat [iostat](https://linux.die.net/man/1/iostat) i [monitora wydajności](/windows/win32/perfctrs/performance-counters-portal).



## <a name="optimize-application-performance"></a>Optymalizowanie wydajności aplikacji

Główne czynniki wpływające na wydajność aplikacji działającej na Premium Storage są charakterem żądań we/wy, rozmiarem maszyny wirtualnej, rozmiarem dysku, liczbą dysków, buforowaniem dysków, wielowątkowości i głębokości kolejki. Niektóre z tych czynników można kontrolować za pomocą pokręteł dostarczonych przez system. Większość aplikacji może nie dawać opcji, aby zmienić rozmiar we/wy i głębokość kolejki bezpośrednio. Na przykład jeśli używasz SQL Server, nie możesz wybrać rozmiaru operacji we/wy i głębokości kolejki. SQL Server wybiera optymalną wartość we/wy i głębokość kolejki w celu uzyskania najwyższej wydajności. Ważne jest, aby zrozumieć wpływ obu typów czynników na wydajność aplikacji, dzięki czemu można udostępnić odpowiednie zasoby w celu spełnienia wymagań dotyczących wydajności.

W tej sekcji zapoznaj się z utworzoną listą kontrolną dotyczącą wymagań aplikacji, aby sprawdzić, ile jest potrzebnych do zoptymalizowania wydajności aplikacji. W zależności od tego można określić, które czynniki z tej sekcji należy dostosować. Aby wypróbować wpływ każdego czynnika na wydajność aplikacji, Uruchom narzędzia do testów porównawczych w konfiguracji aplikacji. Zapoznaj się z artykułem dotyczącym testów porównawczych połączonym na końcu, aby zapoznać się z krokami uruchamiania wspólnych narzędzi testowych na maszynach wirtualnych z systemami Windows i Linux.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Błyskawiczne Optymalizowanie operacji we/wy, przepływności i opóźnień

W poniższej tabeli zestawiono czynniki wydajności i kroki niezbędne do optymalizacji operacji we/wy na sekundę, przepływności i opóźnienia. W poniższych sekcjach opisano każdy czynnik jest znacznie bardziej szczegółowy.

Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych oraz o liczbie operacji we/wy na sekundę, przepływności i opóźnieniu dostępnych dla każdego typu maszyny wirtualnej, zobacz [rozmiary dla maszyny wirtualnej na platformie Azure](sizes.md).

| | **Liczba operacji we/wy na sekundę** | **Przepływność** | **Opóźnienie** |
| --- | --- | --- | --- |
| **Przykładowy scenariusz** |Aplikacja OLTP przedsiębiorstwa wymagająca bardzo dużej liczby transakcji na sekundę. |Przetwarzanie dużych ilości danych przez aplikację do magazynowania danych przedsiębiorstwa. |Aplikacje niemal w czasie rzeczywistym wymagające natychmiastowych odpowiedzi na żądania użytkowników, takie jak gry online. |
| **Czynniki wydajności** | &nbsp; | &nbsp; | &nbsp; |
| **Rozmiar we/wy** |Mniejszy rozmiar we/wy daje większe liczby IOPS. |Większy rozmiar we/wy w celu uzyskania większej przepływności. | &nbsp;|
| **Rozmiar maszyny wirtualnej** |Użyj rozmiaru maszyny wirtualnej, który oferuje operacje we/wy większe niż wymagania aplikacji. |Użyj rozmiaru maszyny wirtualnej z limitem przepływności większym niż wymaganie aplikacji. |Użyj rozmiaru maszyny wirtualnej, który oferuje limity skalowania większe niż wymagania aplikacji. |
| **Rozmiar dysku** |Użyj rozmiaru dysku, który oferuje operacje we/wy większe niż wymagania aplikacji. |Użyj rozmiaru dysku z limitem przepływności większym niż wymaganie aplikacji. |Użyj rozmiaru dysku, który oferuje limity skalowania większe niż wymagania aplikacji. |
| **Limity skalowania maszyn wirtualnych i dysków** |Limit liczby operacji we/wy wybranego rozmiaru maszyny wirtualnej powinien być większy niż łączna liczba operacji we/wy, które są dołączone do dysku magazynu. |Limit przepływności wybranego rozmiaru maszyny wirtualnej powinien być większy niż całkowita przepływność obsługiwana przez dyski magazynu Premium Storage. |Limity skalowania wybranego rozmiaru maszyny wirtualnej muszą być większe niż całkowite limity skali dołączonych dysków magazynu w warstwie Premium. |
| **Buforowanie dysku** |Włącz pamięć podręczną tylko do odczytu na dyskach magazynu w warstwie Premium, aby uzyskać większą liczbę operacji we/wy odczytu. | &nbsp; |Włącz pamięć podręczną tylko do odczytu na dyskach magazynu w warstwie Premium dzięki gotowym dużym operacjom na uzyskanie bardzo niskich opóźnień odczytu. |
| **Rozkładanie dysku** |Użyj wielu dysków i Rozłącz je w celu uzyskania większej liczby operacji we/wy na sekundę. Łączny limit na maszynę wirtualną powinien być wyższy niż łączne limity podłączonych dysków w warstwie Premium. | &nbsp; | &nbsp; |
| **Rozmiar woluminu rozłożonego** |Mniejszy rozmiar paska dla losowego wzorca we/wy widoczny w aplikacjach OLTP. Na przykład Użyj rozmiaru rozłożonego 64 KB dla aplikacji SQL Server OLTP. |Większy rozmiar pasków dla dużych wzorców operacji we/wy widzianych w aplikacjach magazynu danych. Na przykład dla SQL Server aplikacji magazynu danych Użyj rozmiaru woluminu 256 KB. | &nbsp; |
| **Wielowątkowość** |Używaj wielowątkowości, aby wypchnąć większą liczbę żądań do Premium Storage, które będą prowadzić do większej liczby operacji we/wy na sekundę. Na przykład na SQL Server ustawić wysoką wartość MAXDOP, aby przydzielić większy procesor CPU do SQL Server. | &nbsp; | &nbsp; |
| **Głębokość kolejki** |Większa głębokość kolejki daje większe liczby operacji we/wy na sekundę. |Większa głębokość kolejki zapewnia wyższą przepływność. |Mniejsza głębokość kolejki daje mniejsze opóźnienia. |

## <a name="nature-of-io-requests"></a>Charakter żądań we/wy

Żądanie we/wy jest jednostką operacji wejścia/wyjścia, która będzie wykonywana przez aplikację. Zidentyfikowanie charakteru żądań we/wy, losowych lub sekwencyjnych, Odczyt, zapis, mały lub duży, pomoże określić wymagania dotyczące wydajności aplikacji. Ważne jest zrozumienie charakteru żądań we/wy, aby podejmować odpowiednie decyzje podczas projektowania infrastruktury aplikacji. System IOs musi być równomiernie dystrybuowany w celu osiągnięcia najlepszej możliwej wydajności.

Rozmiar we/wy to jeden z ważniejszych czynników. Rozmiar we/wy to rozmiar żądania operacji wejścia/wyjścia wygenerowanego przez aplikację. Rozmiar we/wy ma znaczny wpływ na wydajność, szczególnie w przypadku operacji wejścia/wyjścia aplikacji. Poniższa formuła przedstawia relację między operacjami IOPS, rozmiarem we/wy i przepustowością/przepływność.  
    ![Diagram pokazujący, że liczba operacji we/wy O rozmiarze P jest równa przepływności.](media/premium-storage-performance/image1.png)

Niektóre aplikacje umożliwiają zmianę rozmiaru operacji we/wy, podczas gdy niektóre aplikacje nie są. Na przykład SQL Server określa optymalny rozmiar we/wy i nie zapewnia użytkownikom żadnych pokrętłów, aby je zmienić. Z drugiej strony firma Oracle udostępnia parametr o nazwie [ \_ DataBlock \_ size](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) , za pomocą którego można skonfigurować rozmiar żądania we/wy bazy danych.

Jeśli używasz aplikacji, która nie pozwala na zmianę rozmiaru we/wy, Skorzystaj z wytycznych w tym artykule, aby zoptymalizować wskaźnik KPI wydajności, który jest najbardziej odpowiedni dla aplikacji. Na przykład

* Aplikacja OLTP generuje miliony małych i losowych żądań we/wy. Aby obsłużyć te typy żądań we/wy, należy zaprojektować infrastrukturę aplikacji w celu uzyskania większych liczb IOPS.  
* Aplikacja do magazynowania danych generuje duże i sekwencyjne żądania we/wy. Aby obsługiwać te typy żądań we/wy, należy zaprojektować infrastrukturę aplikacji w celu uzyskania większej przepustowości lub przepływności.

Jeśli używasz aplikacji, która pozwala na zmianę rozmiaru we/wy, Użyj tej reguły miniatury dla rozmiaru we/wy oprócz innych wytycznych dotyczących wydajności,

* Mniejszy rozmiar we/wy, aby uzyskać większą liczbę IOPS. Na przykład 8 KB dla aplikacji OLTP.  
* Większy rozmiar we/wy w celu uzyskania większej przepustowości/przepływności. Na przykład 1024 KB dla aplikacji magazynu danych.

Oto przykład sposobu obliczania liczby operacji we/wy i przepływności/przepustowości dla aplikacji. Rozważ zastosowanie aplikacji przy użyciu dysku P30. Maksymalna liczba operacji we/wy i przepływność/przepustowość na dysku P30 to 5000 operacji we/wy i 200 MB na sekundę. Jeśli aplikacja wymaga maksymalnej liczby IOPS z dysku P30 i używasz mniejszego rozmiaru we/wy, takiego jak 8 KB, uzyskana przepustowość będzie dostępna na 40 MB na sekundę. Jeśli jednak aplikacja wymaga maksymalnej przepływności/przepustowości z dysku P30 i używasz większego rozmiaru we/wy, takiego jak 1024 KB, wyniki IOPS będą mniejsze, 200 IOPS. W związku z tym Dostosuj rozmiar operacji we/wy, tak aby spełniał zarówno dane IOPS aplikacji, jak i przepływność/przepustowość. W poniższej tabeli zestawiono różne rozmiary we/wy oraz odpowiadające im IOPS i przepływność dla dysku P30.

| Wymaganie aplikacji | Rozmiar we/wy | Liczba operacji we/wy na sekundę | Przepływność/przepustowość |
| --- | --- | --- | --- |
| Maks. liczba operacji we/wy na sekundę |8 KB |5000 |40 MB na sekundę |
| Maksymalna przepływność |1024 KB |200 |200 MB na sekundę |
| Maksymalna przepływność i duże liczby operacji we/wy |64 KB |3 200 |200 MB na sekundę |
| Maksymalna liczba IOPS i Wysoka przepływność |32 KB |5000 |160 MB na sekundę |

Aby uzyskać liczbę operacji we/wy i przepustowość wyższą niż maksymalna wartość jednego dysku magazynu Premium Storage, należy użyć wielu dysków w warstwie Premium. Na przykład można rozdzielić dwa dyski P30, aby uzyskać łączną liczbę operacji we/wy 10 000 IOPS lub łączną przepływność równą 400 MB na sekundę. Zgodnie z opisem w następnej sekcji należy użyć rozmiaru maszyny wirtualnej, który obsługuje łączenie operacji we/wy na dysku.

> [!NOTE]
> W miarę zwiększania liczby operacji we/wy lub przepływności inne również zwiększają się, pamiętaj, aby nie trafiać przepływności ani limitów liczby operacji we/wy dysku lub maszyny wirtualnej podczas jej wzrostu.

Aby posłużyć do monitorowania wpływu rozmiaru operacji we/wy na wydajność aplikacji, można uruchomić narzędzia do oceny porównawczej na maszynie wirtualnej i na dyskach. Utwórz wiele przebiegów testowych i użyj innego rozmiaru we/wy dla każdego przebiegu, aby zobaczyć wpływ. Aby uzyskać więcej informacji, zapoznaj się z artykułem dotyczącym testów porównawczych.

## <a name="high-scale-vm-sizes"></a>Rozmiary maszyn wirtualnych o dużej skali

Po rozpoczęciu projektowania aplikacji należy wybrać maszynę wirtualną do hostowania aplikacji. Premium Storage obejmuje rozmiary maszyn wirtualnych o dużej skali, które mogą uruchamiać aplikacje wymagające większej mocy obliczeniowej i wysoką wydajność we/wy dysku lokalnego. Te maszyny wirtualne zapewniają szybszy procesor, większy stosunek pamięci do rdzenia i dysk Solid-State (SSD) dla dysku lokalnego. Przykładami maszyn wirtualnych o dużej skali obsługujących Premium Storage są maszyny wirtualne z serii DS i GS.

Maszyny wirtualne o wysokiej skali są dostępne w różnych rozmiarach przy użyciu innej liczby rdzeni procesora CPU, pamięci, systemu operacyjnego i tymczasowego rozmiaru dysku. Każdy rozmiar maszyny wirtualnej ma również maksymalną liczbę dysków z danymi, które można dołączyć do maszyny wirtualnej. W związku z tym wybrany rozmiar maszyny wirtualnej będzie miał wpływ na to, jak dużo pamięci masowej i pojemności magazynu są dostępne dla aplikacji. Ma to również wpływ na koszt obliczeń i magazynu. Na przykład poniżej przedstawiono specyfikacje największego rozmiaru maszyny wirtualnej w serii DS i serii GS:

| Rozmiar maszyny wirtualnej | Rdzenie procesora CPU | Pamięć | Rozmiary dysków maszyny wirtualnej | Maksymalnie z dyski danych | Rozmiar pamięci podręcznej | Liczba operacji we/wy na sekundę | Limity operacji we/wy pamięci podręcznej przepustowości |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS14 |16 |112 GB |SYSTEM OPERACYJNY = 1023 GB <br> Lokalny dysk SSD = 224 GB |32 |576 GB |LICZBA OPERACJI WE/WY 50 000 <br> 512 MB na sekundę |4 000 operacji we/wy i 33 MB na sekundę |
| Standardowa_GS5 |32 |448 GB |SYSTEM OPERACYJNY = 1023 GB <br> Lokalny dysk SSD = 896 GB |64 |4224 GB |LICZBA OPERACJI WE/WY 80 000 <br> 2 000 MB na sekundę |5 000 operacji we/wy i 50 MB na sekundę |

Aby wyświetlić pełną listę wszystkich dostępnych rozmiarów maszyn wirtualnych platformy Azure, zapoznaj się z tematem [rozmiary maszyny wirtualnej na platformie Azure](sizes.md) lub. Wybierz rozmiar maszyny wirtualnej, który można spełnić i skalować do żądanych wymagań dotyczących wydajności aplikacji. Oprócz tego należy wziąć pod uwagę następujące ważne zagadnienia podczas wybierania rozmiarów maszyn wirtualnych.

*Limity skalowania*  
Maksymalne limity IOPS na maszynę wirtualną i na dysk są różne i niezależne od siebie. Upewnij się, że aplikacja prowadzi działania w ramach limitów maszyn wirtualnych, a także dołączone do niej dyski w warstwie Premium. W przeciwnym razie wydajność aplikacji zostanie ograniczona.

Załóżmy na przykład, że wymaganie aplikacji jest maksymalnie 4 000 operacji we/wy na sekundę. Aby to osiągnąć, należy udostępnić dysk P30 na maszynie wirtualnej DS1. Dysk P30 może dostarczyć do 5 000 operacji we/wy na sekundę. Jednak maszyna wirtualna DS1 jest ograniczona do 3 200 operacji we/wy na sekundę. W związku z tym wydajność aplikacji będzie ograniczona limitem maszyn wirtualnych równym 3 200 operacji we/wy na sekundę, co spowoduje spadek wydajności. Aby uniknąć tej sytuacji, wybierz maszynę wirtualną i rozmiar dysku, który będzie spełniał wymagania aplikacji.

*Koszt operacji*  
W wielu przypadkach istnieje możliwość, że całkowity koszt operacji przy użyciu Premium Storage jest niższy niż przy użyciu magazynu w warstwie Standardowa.

Rozważmy na przykład aplikację wymagającą 16 000 operacji we/wy na sekundę. Aby osiągnąć tę wydajność, potrzebna jest standardowa \_ maszyna wirtualna D14 Azure IaaS, która może zapewnić maksymalną liczbę IOPS 16 000 32 przy użyciu dysków magazynu w warstwie Standardowa 1 TB. Każdy dysk magazynu o pojemności 1 TB może osiągać maksymalnie 500 operacji we/wy na sekundę. Szacowany koszt tej maszyny wirtualnej na miesiąc to $1 570. Miesięczny koszt 32 dysków magazynu w warstwie Standardowa to $1 638. Szacowany łączny koszt miesięczny wynosi $3 208.

Jeśli jednak ta sama aplikacja jest hostowana na Premium Storage, potrzebny będzie mniejszy rozmiar maszyny wirtualnej i mniej dysków magazynu Premium Storage, co zmniejsza całkowity koszt. Standardowa \_ maszyna wirtualna DS13 może spełnić wymagania dotyczące liczby operacji we/wy 16 000 przy użyciu czterech dysków P30. Maszyna wirtualna DS13 ma maksymalną liczbę IOPS 25 600, a każdy dysk P30 ma maksymalną liczbę IOPS 5 000. Ogólnie Konfiguracja ta może osiągnąć 5 000 x 4 20 000 = liczba operacji we/wy na sekundę. Szacowany koszt tej maszyny wirtualnej na miesiąc to $1 003. Miesięczny koszt czterech dysków magazynu P30 Premium będzie $544,34. Szacowany łączny koszt miesięczny wynosi $1 544.

W poniższej tabeli zestawiono podział kosztów tego scenariusza dla standardów i Premium Storage.

| &nbsp; | **Standardowa** | **Premium** |
| --- | --- | --- |
| **Koszt maszyny wirtualnej miesięcznie** |$1 570,58 (standardowa \_ D14) |$1 003,66 (standardowa \_ DS13) |
| **Koszt dysków miesięcznie** |$1 638,40 (32 x 1 TB dysków) |$544,34 (4 x P30 dysków) |
| **Łączny koszt miesięcznie** |$3 208,98 |$1 544,34 |

*Dystrybucje systemu Linux*  

Dzięki platformie Azure Premium Storage uzyskasz ten sam poziom wydajności dla maszyn wirtualnych z systemami Windows i Linux. Obsługujemy wiele rodzajów systemu Linux dystrybucje. pełną listę można zobaczyć [tutaj](linux/endorsed-distros.md). Należy pamiętać, że różne dystrybucje są lepiej dopasowane do różnych typów obciążeń. Zobaczysz różne poziomy wydajności w zależności od dystrybucji, w którym działa obciążenie. Przetestuj dystrybucje systemu Linux przy użyciu aplikacji i wybierz ten, który działa najlepiej.

Podczas uruchamiania systemu Linux z Premium Storage Sprawdź najnowsze aktualizacje dotyczące wymaganych sterowników, aby zapewnić wysoką wydajność.

## <a name="premium-storage-disk-sizes"></a>Rozmiary dysków magazynu w warstwie Premium

Usługa Azure Premium Storage oferuje różne rozmiary, dzięki czemu możesz wybrać jeden z nich, który najlepiej odpowiada Twoim potrzebom. Każdy rozmiar dysku ma inny limit skalowania dla operacji we/wy na sekundę, przepustowości i magazynu. Wybierz odpowiedni Premium Storage rozmiar dysku w zależności od wymagań aplikacji i rozmiaru maszyny wirtualnej o dużej skali. W poniższej tabeli przedstawiono rozmiary dysków i ich możliwości. Rozmiary P4, P6, P15, P60, P70 i P80 są obecnie obsługiwane tylko dla Managed Disks.

[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Liczba wybranych dysków zależy od wybranego rozmiaru dysku. Aby spełnić wymagania aplikacji, można użyć jednego dysku P50 lub wielu dysków P10. Po dokonaniu wyboru należy wziąć pod uwagę poniższe kwestie.

*Limity skalowania (IOPS i przepływność)*  
Limity liczby operacji we/wy i przepływności dla każdego rozmiaru dysku Premium są różne i niezależne od limitów skali maszyny wirtualnej. Upewnij się, że łączna liczba operacji we/wy i przepływność na dyskach są ograniczone do zakresu skali wybranego rozmiaru maszyny wirtualnej.

Na przykład, jeśli wymaganie aplikacji jest maksymalnie 250 MB/s i jest używana maszyna wirtualna DS4 z pojedynczym dyskiem P30. Maszyna wirtualna DS4 może zapewnić przepustowość do 256 MB/s. Jednak pojedynczy dysk P30 ma limit przepływności równy 200 MB/s. W związku z tym aplikacja zostanie ograniczona o 200 MB/s z powodu limitu dysku. Aby wyeliminować ten limit, Zainicjuj obsługę więcej niż jednego dysku danych na maszynie wirtualnej lub Zmień rozmiar dysków na P40 lub P50.

> [!NOTE]
> Odczyty obsługiwane przez pamięć podręczną nie są uwzględniane w operacjach IOPS dysku i przepływności, dlatego nie podlegają limitom dysków. Pamięć podręczna ma oddzielny limit operacji we/wy na maszynę wirtualną.
>
> Na przykład początkowo odczyty i zapisy są 60MB/s i 40MB/s odpowiednio. W miarę upływu czasu pamięć podręczna jest rozgrzana i obsługuje więcej operacji odczytu z pamięci podręcznej. Następnie można uzyskać większą przepływność zapisu z dysku.

*Liczba dysków*  
Określ liczbę dysków, które będą potrzebne, oceniając wymagania aplikacji. Każdy rozmiar maszyny wirtualnej ma również limit liczby dysków, które można dołączyć do maszyny wirtualnej. Zwykle jest to dwukrotnie liczba rdzeni. Upewnij się, że wybrany rozmiar maszyny wirtualnej może obsługiwać wymaganą liczbę dysków.

Należy pamiętać, że dyski Premium Storage mają wyższe możliwości wydajności w porównaniu z dyskami magazynu w warstwie Standardowa. W związku z tym, Jeśli migrujesz aplikację z maszyny wirtualnej IaaS platformy Azure przy użyciu magazynu w warstwie Standardowa do Premium Storage, prawdopodobnie potrzebujesz mniej dysków w warstwie Premium, aby osiągnąć tę samą lub wyższą wydajność aplikacji.

## <a name="disk-caching"></a>Buforowanie dysku

Maszyny wirtualne o wysokiej skali wykorzystujące platformę Azure Premium Storage mają wielowarstwową technologię buforowania o nazwie BlobCache. BlobCache używa kombinacji pamięci RAM hosta i lokalnego dysku SSD na potrzeby buforowania. Ta pamięć podręczna jest dostępna dla dysków trwałych Premium Storage i lokalnych dysków maszyny wirtualnej. Domyślnie to ustawienie pamięci podręcznej jest ustawione na odczyt/zapis dla dysków systemu operacyjnego i tylko do odczytu dla dysków danych hostowanych na Premium Storage. Gdy buforowanie dysków jest włączone na dyskach Premium Storage, maszyny wirtualne o wysokiej skali mogą uzyskać bardzo wysokie poziomy wydajności, które przekraczają wydajność dysku podstawowego.

> [!WARNING]
> Buforowanie dysków nie jest obsługiwane w przypadku dysków 4 TiB i większych. Jeśli do maszyny wirtualnej są dołączone wiele dysków, każdy dysk o rozmiarze mniejszym niż 4 TiB będzie obsługiwał buforowanie.
>
> Zmienianie ustawień pamięci podręcznej dysku platformy Azure powoduje odłączenie i ponowne dołączenie dysku docelowego. Jeśli jest to dysk systemu operacyjnego, maszyna wirtualna zostanie ponownie uruchomiona. Zatrzymasz wszystkie aplikacje/usługi, na które może mieć wpływ to zakłócenie, przed zmianą ustawień pamięci podręcznej dysku. Nie poniższe zalecenia mogą prowadzić do uszkodzenia danych.

Aby dowiedzieć się więcej o tym, jak działa BlobCache, zapoznaj się z wpisem w blogu na [platformie Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

Ważne jest włączenie pamięci podręcznej w odpowiednim zestawie dysków. Określa, czy należy włączyć buforowanie dysku na dysku w warstwie Premium, czy nie będzie zależeć od wzorca obciążenia, który będzie obsługiwał ten dysk. W poniższej tabeli przedstawiono domyślne ustawienia pamięci podręcznej dla dysków systemu operacyjnego i danych.

| **Typ dysku** | **Domyślne ustawienie pamięci podręcznej** |
| --- | --- |
| Dysk systemu operacyjnego |Odczyt/zapis |
| Dysk danych |ReadOnly |

Poniżej przedstawiono zalecane ustawienia pamięci podręcznej dysków dla dysków z danymi,

| **Ustawienie buforowania dysku** | **zalecenie dotyczące sytuacji, w których należy używać tego ustawienia** |
| --- | --- |
| Brak |Skonfiguruj pamięć podręczną hosta jako brak dla dysków tylko do zapisu i zapisu z dużym obciążeniem. |
| ReadOnly |Skonfiguruj pamięć podręczną hosta jako ReadOnly dla dysków tylko do odczytu i zapisu. |
| Odczyt/zapis |Skonfiguruj pamięć podręczną hosta jako ReadWrite tylko wtedy, gdy aplikacja prawidłowo obsługuje zapisywanie danych w pamięci podręcznej na dyskach trwałych w razie potrzeby. |

*Trybie*  
Konfigurując buforowanie w trybie tylko do odczytu na Premium Storage dyskach danych, można osiągnąć niskie opóźnienie i uzyskać bardzo wysokie operacje we/wy odczytu i przepływność aplikacji. Dzieje się tak z dwóch powodów,

1. Operacje odczytu z pamięci podręcznej, która znajduje się na pamięci maszyn wirtualnych i lokalnym dysku SSD, są znacznie szybsze niż odczyt z dysku danych, który znajduje się w usłudze Azure Blob Storage.  
1. Premium Storage nie liczy odczytów obsłużonych z pamięci podręcznej, w tym operacji we/wy na dysku i przepływności. W związku z tym aplikacja może osiągnąć większą liczbę operacji we/wy na sekundę.

*ReadWrite*  
Domyślnie dyski systemu operacyjnego mają włączone buforowanie ReadWrite. Niedawno dodaliśmy obsługę buforowania ReadWrite na dyskach danych. Jeśli używasz buforowania ReadWrite, musisz mieć właściwy sposób zapisu danych z pamięci podręcznej na dyskach trwałych. Na przykład SQL Server obsługuje zapisywanie w pamięci podręcznej danych na trwałych dyskach magazynu. Używanie pamięci podręcznej ReadWrite z aplikacją, która nie obsługuje utrwalania wymaganych danych może spowodować utratę danych, jeśli maszyna wirtualna ulegnie awarii.

*Brak*  
Obecnie **żadna wartość nie** jest obsługiwana tylko na dyskach danych. Nie jest on obsługiwany na dyskach z systemem operacyjnym. Jeśli na dysku systemu operacyjnego nie zostanie ustawiona **żadna wartość Brak** , spowoduje to zastąpienie wewnętrznie i ustawienie wartości **tylko do odczytu**.

Na przykład można zastosować te wytyczne do SQL Server uruchamiania na Premium Storage, wykonując następujące czynności:

1. Skonfiguruj pamięć podręczną "ReadOnly" na dyskach usługi Premium Storage, na których znajdują się pliki danych.  
   a.  Szybkie odczyty z pamięci podręcznej obniżają SQL Server czasu zapytania, ponieważ strony danych są znacznie szybsze, od pamięci podręcznej w porównaniu bezpośrednio z dyskami danych.  
   b.  Obsługa odczytów z pamięci podręcznej oznacza, że dostępna jest dodatkowa przepływność z dysków danych w warstwie Premium. SQL Server może korzystać z tej dodatkowej przepływności w celu pobierania kolejnych stron danych i innych operacji, takich jak tworzenie kopii zapasowej/przywracanie, ładowanie wsadowe i ponowne kompilacje indeksów.  
1. Skonfiguruj pamięć podręczną "none" na dyskach usługi Premium Storage obsługujących pliki dziennika.  
   a.  Pliki dziennika mają głównie operacje zapisujące duże ilości. W związku z tym nie korzystają z pamięci podręcznej tylko do odczytu.

## <a name="optimize-performance-on-linux-vms"></a>Optymalizowanie wydajności na maszynach wirtualnych z systemem Linux

W przypadku wszystkich dysków SSD Premium lub Ultra dysków można wyłączyć "bariery" dla systemów plików na dysku w celu zwiększenia wydajności, gdy wiadomo, że nie ma żadnych pamięci podręcznych, które mogą spowodować utratę danych.  Jeśli buforowanie dysku platformy Azure ma wartość tylko do odczytu lub brak, można wyłączyć bariery.  Ale jeśli buforowanie jest ustawione na ReadWrite, bariery powinny pozostać włączone, aby zapewnić trwałość zapisu.  Bariery są zwykle domyślnie włączone, ale można wyłączyć bariery, korzystając z jednej z następujących metod, w zależności od typu systemu plików:

* W przypadku **reiserFS** należy użyć opcji "bariera = brak instalacji", aby wyłączyć bariery.  Aby jawnie włączyć bariery, należy użyć bariery = Flush.
* Aby wyłączyć bariery dla **ext3/ext4**, należy użyć opcji instalacji barier = 0.  Aby jawnie włączyć bariery, należy użyć bariery = 1.
* W przypadku usługi **XFS** należy użyć opcji instalacji bez bariery, aby wyłączyć bariery.  Aby jawnie włączyć bariery, należy użyć bariery.  Należy pamiętać, że w późniejszych wersjach jądra systemu Linux konstrukcja system plików XFS zapewnia trwałość, a wyłączenie barier nie przynosi żadnego efektu.  

## <a name="disk-striping"></a>Rozkładanie dysku

Jeśli maszyna wirtualna o dużej skali jest dołączona do kilku dysków trwałych usługi Premium Storage, dyski można rozmieścić razem w celu zagregowania ich liczby operacji we/wy na sekundę, przepustowości i pojemności magazynu.

W systemie Windows można używać funkcji miejsca do magazynowania w celu rozdzielenia dysków. Dla każdego dysku w puli należy skonfigurować jedną kolumnę. W przeciwnym razie ogólna wydajność woluminu rozłożonego może być niższa niż oczekiwana, ze względu na nierównomierny rozkład ruchu między dyskami.

Ważne: przy użyciu Menedżer serwera interfejsu użytkownika można ustawić całkowitą liczbę kolumn do 8 dla woluminu rozłożonego. W przypadku dołączania więcej niż ośmiu dysków należy utworzyć wolumin przy użyciu programu PowerShell. Za pomocą programu PowerShell można ustawić liczbę kolumn równą liczbie dysków. Na przykład, jeśli na jednym zestawie rozłożonym znajduje się 16 dysków; Określ 16 kolumn w parametrze *NumberOfColumns* polecenia cmdlet *New-VirtualDisk* programu PowerShell.

W systemie Linux Użyj narzędzia MDADM, aby połączyć dyski ze sobą. Aby uzyskać szczegółowe instrukcje dotyczące dysków rozłożonych w systemie Linux, zobacz [Konfigurowanie RAID oprogramowania w systemie Linux](/previous-versions/azure/virtual-machines/linux/configure-raid).

*Rozmiar woluminu rozłożonego*  
Ważną konfiguracją w obłożonym dysku jest rozmiar paska. Rozmiar obszaru rozłożonego lub blok jest najmniejszym fragmentem danych, które aplikacja może rozwiązać na woluminie rozłożonym. Skonfigurowany rozmiar obszaru rozłożonego zależy od typu aplikacji i jego wzorca żądania. W przypadku wybrania niewłaściwego rozmiaru paska może wystąpić niezgodność we/wy, co prowadzi do obniżenia wydajności aplikacji.

Na przykład jeśli żądanie we/wy wygenerowane przez aplikację jest większe niż rozmiar rozłożonego dysku, system magazynu zapisuje go między granicami jednostki na więcej niż jednym dysku. Gdy dostęp do tych danych jest czas, będzie musiał poszukiwać więcej niż jednej jednostki rozłożonej, aby wykonać żądanie. Skumulowany efekt takiego zachowania może prowadzić do znacznego obniżenia wydajności. Z drugiej strony, jeśli rozmiar żądania we/wy jest mniejszy niż rozmiar paska, a jeśli ma charakter losowy, żądania we/wy mogą zostać dodane na tym samym dysku powodującym wąskie gardło i ostatecznie spadek wydajności operacji we/wy.

W zależności od typu obciążenia, w którym działa aplikacja, wybierz odpowiedni rozmiar paska. W przypadku bardzo małych żądań we/wy Użyj mniejszego rozmiaru. W przypadku duże sekwencyjne żądania we/wy używają większego rozmiaru paska. Zapoznaj się z zaleceniami dotyczącymi rozmiaru paska aplikacji, które będą działać na Premium Storage. W przypadku SQL Server Skonfiguruj rozmiar rozłożenia 64 KB dla obciążeń OLTP i 256 KB na potrzeby obciążeń związanych z magazynowaniem danych. Aby dowiedzieć się więcej, zobacz [najlepsze rozwiązania w zakresie wydajności dotyczące SQL Server na maszynach wirtualnych platformy Azure](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md#disks-guidance) .

> [!NOTE]
> Na maszynach wirtualnych serii z serii GS można rozdzielić maksymalnie 32 dysków usługi Premium Storage 64, a także dyski magazynu w warstwie Premium

## <a name="multi-threading"></a>Wielowątkowość

Platforma Azure zaprojektowano Premium Storage platformę, aby była wysoce równoległa. W związku z tym aplikacja wielowątkowa osiąga znacznie wyższą wydajność niż aplikacja jednowątkowa. Aplikacja wielowątkowa dzieli swoje zadania na wiele wątków i zwiększa wydajność jego wykonywania, wykorzystując do tego maksymalną ilość zasobów maszyny wirtualnej i dysku.

Na przykład jeśli aplikacja działa na pojedynczej podstawowej maszynie wirtualnej z dwoma wątkami, procesor CPU może przełączać się między dwoma wątkami w celu uzyskania wydajności. Chociaż jeden wątek oczekuje na wykonanie operacji we/wy na dysku, procesor może przełączać się do innego wątku. W ten sposób dwa wątki mogą wykonywać więcej niż jeden wątek. Jeśli maszyna wirtualna ma więcej niż jeden rdzeń, skraca czas działania, ponieważ poszczególne rdzenie mogą wykonywać równolegle zadania.

Może nie być możliwe zmiana sposobu, w jaki aplikacja poza półką implementuje pojedyncze wątki lub wielowątkowość. Na przykład SQL Server może obsługiwać wiele procesorów i wiele rdzeni. Jednak SQL Server decyduje o tym, jakie warunki będzie korzystać z co najmniej jednego wątku w celu przetworzenia zapytania. Może uruchamiać zapytania i tworzyć indeksy przy użyciu wielowątkowości. W przypadku zapytania, które wymaga przyłączenia dużych tabel i sortowania danych przed zwróceniem do użytkownika, SQL Server prawdopodobnie użyje wielu wątków. Jednak użytkownik nie może kontrolować, czy SQL Server wykonuje zapytanie przy użyciu jednego wątku lub wielu wątków.

Istnieją ustawienia konfiguracji, które można zmienić w celu wywierania wpływu na wielowątkowość lub przetwarzanie równoległe aplikacji. Na przykład w przypadku SQL Server jest to maksymalny stopień równoległości konfiguracji. To ustawienie o nazwie MAXDOP umożliwia skonfigurowanie maksymalnej liczby procesorów, SQL Server mogą być używane podczas przetwarzania równoległego. MAXDOP można skonfigurować dla poszczególnych zapytań lub operacji indeksowania. Jest to przydatne, gdy chcesz zrównoważyć zasoby systemu dla krytycznej wydajności aplikacji.

Załóżmy na przykład, że aplikacja używa SQL Server wykonuje duże zapytania i operację indeksowania w tym samym czasie. Załóżmy, że chcemy, aby Operacja indeksowania była większa niż w przypadku dużego zapytania. W takim przypadku można ustawić wartość MAXDOP operacji indeksu na wyższą niż wartość MAXDOP zapytania. W ten sposób SQL Server ma więcej procesorów, których może używać dla operacji indeksu w porównaniu z liczbą procesorów, które mogą być dedykowane dla dużego zapytania. Pamiętaj, że nie kontrolujesz liczby wątków, SQL Server będą używane dla każdej operacji. Można kontrolować maksymalną liczbę procesorów dedykowanych dla wielu wątków.

Dowiedz się więcej o [stopniach równoległości](/previous-versions/sql/sql-server-2008-r2/ms188611(v=sql.105)) w SQL Server. Dowiedz się, jakie ustawienia mają wpływ na wiele wątków w aplikacji i ich konfiguracje w celu zoptymalizowania wydajności.

## <a name="queue-depth"></a>Głębokość kolejki

Głębokość kolejki lub długość kolejki lub rozmiar kolejki to liczba oczekujących żądań we/wy w systemie. Wartość głębokości kolejki określa liczbę operacji we/wy, które można wyrównać w aplikacji, w której będą przetwarzane dyski magazynu. Ma to wpływ na wszystkie trzy wskaźniki wydajności aplikacji omówione w tym artykule, tzn. operacje we/wy, przepływność i opóźnienie.

Głębokość kolejki i wiele wątków są ściśle powiązane. Wartość głębokości kolejki wskazuje, ile wielu wątków może zostać osiągniętych przez aplikację. Jeśli głębokość kolejki jest duża, aplikacja może wykonywać więcej operacji współbieżnie, innymi słowy, więcej wielowątkowości. Jeśli głębokość kolejki jest mała, nawet jeśli aplikacja jest wielowątkowa, nie będzie miała wystarczającej liczby żądań w celu jednoczesnego wykonania.

Zwykle poza aplikacjami z półkami nie można zmienić głębokości kolejki, ponieważ w przypadku nieprawidłowego ustawienia będzie bardziej szkodliwy niż dobry. Aplikacje ustawią odpowiednią wartość głębokości kolejki w celu uzyskania optymalnej wydajności. Ważne jest jednak zrozumienie tego koncepcji, aby można było rozwiązać problemy z wydajnością w aplikacji. Możesz również obserwować wpływ głębokości kolejki przez uruchamianie narzędzi testowych w systemie.

Niektóre aplikacje zapewniają ustawienia mające wpływ na głębokość kolejki. Na przykład ustawienie MAXDOP (maksymalny stopień równoległości) w SQL Server wyjaśnione w poprzedniej sekcji. MAXDOP ma wpływ na głębokość kolejki i wiele wątków, chociaż nie zmieniają bezpośrednio wartości głębokości kolejki SQL Server.

*Duża głębokość kolejki*  
Duża głębokość kolejki liniowo więcej operacji na dysku. Dysk wie następne żądanie w swojej kolejce przed czasem. W związku z tym dysk może zaplanować operacje przed czasem i przetwarzać je w optymalną sekwencję. Ponieważ aplikacja wysyła więcej żądań do dysku, dysk może przetwarzać bardziej równoległe systemy IOs. Ostatecznie aplikacja będzie mogła osiągnąć większą liczbę operacji we/wy na sekundę. Ponieważ aplikacja przetwarza więcej żądań, łączna przepływność aplikacji również rośnie.

Zazwyczaj aplikacja może osiągnąć maksymalną przepływność przy użyciu 8-16 + zaległych dysków IOs na dołączonym dysku. Jeśli głębokość kolejki jest taka, aplikacja nie wypychanie wystarczającej liczby urządzeń z systemem IOs do systemu i przetworzy mniejszą ilość w danym okresie. Innymi słowy, mniejsza przepływność.

Na przykład w SQL Server ustawienie wartości MAXDOP dla zapytania na "4" informuje SQL Server, że można użyć maksymalnie czterech rdzeni do wykonania zapytania. SQL Server określi, co to jest Najlepsza wartość głębokości kolejki oraz liczbę rdzeni dla wykonywania zapytania.

*Optymalna głębokość kolejki*  
Bardzo wysoka wartość głębokości kolejki ma także wady. Jeśli wartość głębokości kolejki jest zbyt duża, aplikacja podejmie próbę przeprowadzić bardzo duże liczby operacji we/wy na sekundę. Jeśli aplikacja nie ma dysków trwałych z wystarczającą liczbą operacji we/wy aprowizacji, może to negatywnie wpłynąć na opóźnienia aplikacji. Poniższa formuła przedstawia relację między operacjami IOPS, opóźnienia i kolejki.  
    ![Diagram pokazujący, że opóźnienie I czas opóźnienia są równe głębokości kolejki.](media/premium-storage-performance/image6.png)

Nie należy konfigurować głębokości kolejki do żadnej wysokiej wartości, ale do optymalnej wartości, która może zapewnić wystarczającą liczbę IOPS dla aplikacji bez wpływu na opóźnienia. Na przykład jeśli opóźnienie aplikacji musi wynosić 1 milisekunda, Głębokość kolejki wymagana do osiągnięcia 5 000 operacji we/wy to głębokość kolejki = 5000 x 0,001 = 5.

*Głębokość kolejki dla woluminu rozłożonego*  
W przypadku woluminu rozłożonego należy zachować górną głębokość kolejki, taką jak każdy dysk ma zaszczytową głębokość kolejki. Rozważmy na przykład aplikację, która wypycha głębokość kolejki 2 i zawiera cztery dyski. Dwa żądania we/wy będą kierowane do dwóch dysków, a pozostałe dwa dyski będą bezczynne. W związku z tym Skonfiguruj głębokość kolejki w taki sposób, aby wszystkie dyski mogły być zajęte. W poniższej formule pokazano, jak określić głębokość kolejki woluminów rozłożonych.  
    ![Diagram przedstawiający równanie Q D na dysk razy liczba kolumn na wolumin jest równa Q D woluminu rozłożonego.](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Ograniczanie przepływności

Usługa Azure Premium Storage postanowił określoną liczbę operacji we/wy na sekundę i przepływności w zależności od wybranego rozmiaru maszyny wirtualnej i wybranego rozmiaru dysku. Gdy aplikacja próbuje zwiększyć liczbę operacji we/wy lub przepływności powyżej tych limitów, co może obsłużyć maszyna wirtualna lub dysk, Premium Storage będzie ograniczać ją. Te manifesty mają postać obniżonej wydajności aplikacji. Może to oznaczać większe opóźnienia, niższą przepływność lub mniejsze liczby operacji we/wy na sekundę. Jeśli Premium Storage nie jest ograniczana, aplikacja może zakończyć się niepowodzeniem, przekroczenie możliwości osiągania zasobów. Aby uniknąć problemów z wydajnością ze względu na ograniczenie przepustowości, zawsze Zapewnij odpowiednią ilość zasobów dla aplikacji. Weź pod uwagę to, co omówiono w sekcjach rozmiary maszyn wirtualnych i rozmiary dysków powyżej. Testy porównawcze to najlepszy sposób ustalania zasobów potrzebnych do hostowania aplikacji.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz przeprowadzić test porównawczy dysku, zapoznaj się z naszymi artykułami dotyczącymi tworzenia wzorców dysków:

* Dla systemu Linux: [test porównawczy aplikacji na Azure Disk Storage](./disks-benchmarks.md)
* Dla systemu Windows: przeprowadzenie [testu porównawczego dysku](./disks-benchmarks.md).

Dowiedz się więcej na temat dostępnych typów dysków:

* Dla systemu Linux: [Wybierz typ dysku](disks-types.md)
* Dla systemu Windows: [Wybierz typ dysku](disks-types.md)

Aby uzyskać SQL Server użytkowników, przeczytaj artykuły dotyczące najlepszych rozwiązań w zakresie wydajności dla SQL Server:

* [Najlepsze rozwiązania dotyczące wydajności SQL Server na platformie Azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Usługa Azure Premium Storage zapewnia najwyższą wydajność SQL Server na maszynie wirtualnej platformy Azure](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)