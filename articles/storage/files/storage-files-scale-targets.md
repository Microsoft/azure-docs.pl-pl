---
title: Cele dotyczące skalowalności i wydajności usługi Azure Files
description: Poznaj elementy docelowe skalowalności i wydajności dla Azure Files, w tym pojemność, szybkość żądania oraz limity przepustowości ruchu przychodzącego i wychodzącego.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0ecfbb9053fde4ff332cbbcb6e14a84a5bbeb99a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593156"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności usługi Azure Files
[Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołów protokołu SMB i systemu plików NFS. W tym artykule omówiono elementy docelowe skalowalności i wydajności dla Azure Files i Azure File Sync.

Elementy docelowe skalowalności i wydajności wymienione w tym miejscu są obiektami docelowymi wysokiej klasy, ale mogą mieć wpływ inne zmienne we wdrożeniu. Na przykład przepływność pliku może być również ograniczona przez dostępną przepustowość sieci, a nie tylko serwery obsługujące udziały plików platformy Azure. Zdecydowanie zalecamy testowanie wzorca użycia w celu określenia, czy skalowalność i wydajność Azure Files spełniają Twoje wymagania. Ponadto firma Microsoft dokłada starań, aby zwiększyć te limity w miarę upływu czasu. 

## <a name="azure-files-scale-targets"></a>Cele skalowania usługi Azure Files
Udziały plików platformy Azure są wdrażane na kontach magazynu, które są obiektami najwyższego poziomu reprezentującymi udostępnioną pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików. W związku z tym należy wziąć pod uwagę trzy kategorie: konta magazynu, udziały plików platformy Azure i pliki.

### <a name="storage-account-scale-targets"></a>Cele skali konta magazynu
Platforma Azure obsługuje wiele typów kont magazynu dla różnych klientów scenariuszy magazynu, które mogą mieć, ale istnieją dwa główne typy kont magazynu dla Azure Files. Typ konta magazynu, który należy utworzyć, zależy od tego, czy chcesz utworzyć standardowy udział plików, czy udział plików w warstwie Premium: 

- **Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)**: GPv2 konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach standardowych/twardych. Oprócz przechowywania udziałów plików platformy Azure GPv2 konta magazynu mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. Udziały plików można wdrażać w ramach transakcji zoptymalizowanej (domyślnej), gorąca lub chłodna.

- **FileStorage kont magazynu**: FileStorage konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach Premium/SSD (opartym na dysku półprzewodnikowym). Kont FileStorage można używać tylko do przechowywania udziałów plików platformy Azure. nie można wdrażać innych zasobów magazynu (kontenerów obiektów blob, kolejek, tabel itp.) w ramach konta FileStorage.

| Atrybut | Konta magazynu GPv2 (warstwa standardowa) | Konta magazynu FileStorage (wersja Premium) |
|-|-|-|
| Liczba kont magazynu na region na subskrypcję | 250 | 250 |
| Maksymalna pojemność konta magazynu | 5 PiB<sup>1</sup> | 100 TiB (aprowizacji) |
| Maksymalna liczba udziałów plików | Nieograniczona liczba | Nieograniczony, łączny rozmiar wszystkich udziałów musi być mniejszy niż maksymalny niż maksymalna pojemność konta magazynu |
| Maksymalna liczba współbieżnych żądań | 20 000 IOPS<sup>1</sup> | LICZBA OPERACJI WE/WY 100 000 |
| Maksymalna liczba danych wejściowych | <ul><li>US/Europa: 10 GBP/sek<sup>1</sup></li><li>Inne regiony (LRS/ZRS): 10 GBP/sek<sup>1</sup></li><li>Inne regiony (GRS): 5 GBP/sek<sup>1</sup></li></ul> | 4 136 MiB/s |
| Maksymalna liczba wychodzących | 50 GBP/sek.<sup>1</sup> | 6 204 MiB/s |
| Maksymalna liczba reguł sieci wirtualnej | 200 | 200 |
| Maksymalna liczba reguł adresów IP | 200 | 200 |
| Operacje odczytu zarządzania | 800 za 5 minut | 800 za 5 minut |
| Operacje zapisu zarządzania | 10 na sekundę/1200 na godzinę | 10 na sekundę/1200 na godzinę |
| Operacje na liście zarządzania | 100 za 5 minut | 100 za 5 minut |

<sup>1</sup> konto magazynu ogólnego przeznaczenia w wersji 2 obsługuje wyższe limity pojemności i wyższe limity dotyczące ruchu przychodzącego przez żądanie. Aby zażądać zwiększenia limitów kont, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Cele skalowania udziału plików platformy Azure
| Atrybut | Standardowe udziały plików<sup>1</sup> | Udziały plików w warstwie Premium |
|-|-|-|
| Minimalny rozmiar udziału plików | Brak wartości minimalnej | 100 GiB (aprowizacji) |
| Jednostka zwiększania/zmniejszania rozmiaru aprowizacji | Nie dotyczy | 1 GiB |
| Maksymalny rozmiar udziału plików | <ul><li>100 TiB, z włączoną funkcją dużego udziału plików<sup>2</sup></li><li>5 TiB, domyślnie</li></ul> | 100 TiB |
| Maksymalna liczba plików w udziale plików | Bez ograniczeń | Bez ograniczeń |
| Maksymalna liczba żądań (maksymalna liczba IOPS) | <ul><li>10 000 z włączoną funkcją dużego udziału plików<sup>2</sup></li><li>żądania 1 000 lub 100 na 100 ms, domyślne</li></ul> | <ul><li>Wejścia/wyjścia linii bazowej: 400 + 1 Liczba operacji we/wy na sekundę na GiB, do 100 000</li><li>Rozerwanie operacji we/wy: maks. (4000, 1 razy dziennie operacji we/wy na GiB), do 100 000</li></ul> |
| Maksymalna liczba danych wejściowych dla pojedynczego udziału plików | <ul><li>Do 300 MiB/s z włączoną funkcją dużego udziału plików<sup>2</sup></li><li>Do 60 MiB/s, domyślnie</li></ul> | 40 MiB/s + 0,04 * GiB aprowizacji |
| Maksymalna liczba ruchu wychodzącego dla pojedynczego udziału plików | <ul><li>Do 300 MiB/s z włączoną funkcją dużego udziału plików<sup>2</sup></li><li>Do 60 MiB/s, domyślnie</li></ul> | 60 MiB/s + 0,06 * GiB aprowizacji |
| Maksymalna liczba migawek udziałów | 200 migawek | 200 migawek |
| Maksymalna długość nazwy obiektu (katalogów i plików) | 2 048 znaków | 2 048 znaków |
| Maksymalna część ścieżki składnika (w ścieżce \A\B\C\D każda litera jest składnikiem) | 255 znaków | 255 znaków |
| Limit linków twardych (tylko system plików NFS) | Nie dotyczy | 178 |
| Maksymalna liczba kanałów wielokanałowe protokołu SMB | Nie dotyczy | 4 |
| Maksymalna liczba przechowywanych zasad dostępu na udział plików | 5 | 5 |

<sup>1</sup> limity dla standardowych udziałów plików mają zastosowanie do wszystkich trzech warstw dostępnych dla standardowych udziałów plików: transakcja zoptymalizowana, gorąca i chłodna.

<sup>2</sup> domyślne udziały plików w warstwie Standardowa to 5 TIB, zobacz temat [Włączanie i tworzenie dużych udziałów plików](./storage-files-how-to-create-large-file-share.md) , aby uzyskać szczegółowe informacje na temat zwiększania rozmiaru standardowych udziałów plików do 100 TIB.

### <a name="file-scale-targets"></a>Cele skalowania plików
| Atrybut | Pliki w standardowych udziałach plików  | Pliki w Premium udziałach plików  |
|-|-|-|
| Maksymalna wielkość pliku | 4 TiB | 4 TiB |
| Maksymalna liczba współbieżnych żądań | LICZBA OPERACJI WE/WY 1 000 | Do 8 000<sup>1</sup> |
| Maksymalna ilość danych wejściowych dla pliku | 60 MiB/s | 200 MiB/s (do 1 GiB/s przy użyciu funkcji SMB wielokanałowe Preview)<sup>2</sup>|
| Maksymalna wartość wyjściowa pliku | 60 MiB/s | 300 MiB/s (do 1 GiB/s przy użyciu funkcji SMB wielokanałowe Preview)<sup>2</sup> |
| Maksymalna liczba równoczesnych dojść | 2 000 dojść | 2 000 dojść  |

<sup>1 ma zastosowanie do odczytu i zapisu systemu iOS (zazwyczaj mniejsze rozmiary we/wy mniejsze niż lub równe 64 KiB). Operacje na metadanych, inne niż odczyty i zapisy, mogą być mniejsze.</sup> 
 <sup>2 z uwzględnieniem limitów sieci maszyn, dostępnej przepustowości, rozmiarów operacji we/wy, głębokości kolejki i innych czynników. Aby uzyskać szczegółowe informacje, zobacz [wydajność protokołu SMB wielokanałowe](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync
Poniższa tabela wskazuje granice testowania firmy Microsoft, a także wskazuje, które cele są sztywne:

| Zasób | Cel | Limit sztywny |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na region | Usługi synchronizacji magazynu 100 | Tak |
| Grupy synchronizacji na usługę synchronizacji magazynu | 200 grup synchronizacji | Tak |
| Zarejestrowane serwery na usługę synchronizacji magazynu | serwery 99 | Tak |
| Punkty końcowe chmury na grupę synchronizacji | 1 punkt końcowy w chmurze | Tak |
| Punkty końcowe serwera dla grupy synchronizacji | punkty końcowe serwera 100 | Tak |
| Punkty końcowe serwera na serwer | 30 punktów końcowych serwera | Tak |
| Obiekty systemu plików (Katalogi i pliki) dla każdej grupy synchronizacji | 100 000 000 obiektów | Nie |
| Maksymalna liczba obiektów systemu plików (katalogów i plików) w katalogu | 5 000 000 obiektów | Tak |
| Maksymalny rozmiar deskryptora zabezpieczeń obiektu (katalogów i plików) | 64 KiB | Tak |
| Rozmiar pliku | 100 GiB | Nie |
| Minimalny rozmiar pliku do warstwowego | V9 i nowsze: w zależności od rozmiaru klastra systemu plików (rozmiar klastra o podwójnym rozmiarze systemu plików). Jeśli na przykład rozmiar klastra systemu plików to 4 KiB, minimalny rozmiar pliku wynosi 8 KiB.<br> V8 i starsze: 64 KiB  | Tak |

> [!Note]  
> Punkt końcowy Azure File Sync może skalować w górę do rozmiaru udziału plików platformy Azure. Jeśli osiągnięto limit rozmiaru udziału plików platformy Azure, synchronizacja nie będzie mogła działać.

### <a name="azure-file-sync-performance-metrics"></a>Metryki wydajności usługi Azure File Sync
Ponieważ agent Azure File Sync działa na komputerze z systemem Windows Server, który nawiązuje połączenie z udziałami plików platformy Azure, efektywna wydajność synchronizacji zależy od wielu czynników w infrastrukturze: system Windows Server i podstawowa Konfiguracja dysku, przepustowość sieci między serwerem a magazynem platformy Azure, rozmiar pliku, całkowity rozmiar zestawu danych i działanie w zestawie danych. Ponieważ Azure File Sync działa na poziomie pliku, charakterystyka wydajności rozwiązania opartego na Azure File Sync jest lepiej mierzona w liczbie obiektów (plików i katalogów) przetworzonych na sekundę.

W przypadku Azure File Sync wydajność ma kluczowe znaczenie w dwóch etapach:

1. **Początkowe wstępne Inicjowanie obsługi**: aby zoptymalizować wydajność początkowej aprowizacji, zapoznaj się z tematem [dołączania do Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) , aby uzyskać szczegółowe informacje na temat optymalnego wdrożenia.
2. Aktualna **Synchronizacja**: po początkowym umieszczeniu danych w udziałach plików platformy Azure Azure File Sync synchronizuje wiele punktów końcowych.

Aby ułatwić Planowanie wdrożenia dla każdego z etapów, poniżej przedstawiono wyniki zaobserwowane podczas wewnętrznego testowania w systemie z konfiguracją

| Konfiguracja systemu | Szczegóły |
|-|-|
| Procesor CPU | 64 rdzeni wirtualnych z 64 MiB L3 pamięci podręcznej |
| Pamięć | 128 GiB |
| Dysk | Dyski SAS z macierzą RAID 10 z zapasową pamięcią podręczną |
| Sieć | Sieć 1 GB/s |
| Obciążenie | Ogólnego przeznaczenia serwera plików|

| Początkowe Inicjowanie obsługi po raz pierwszy  | Szczegóły |
|-|-|
| Liczba obiektów | 25 000 000 obiektów |
| Rozmiar zestawu danych| ~ 4,7 TiB |
| Średni rozmiar pliku | ~ 200 KiB (największy plik: 100 GiB) |
| Początkowe Wyliczenie zmian w chmurze | 20 obiektów na sekundę  |
| Przepływność przekazywania | 20 obiektów na sekundę na grupę synchronizacji |
| Przepływność pobierania przestrzeni nazw | 400 obiektów na sekundę |

### <a name="initial-one-time-provisioning"></a>Początkowe Inicjowanie obsługi po raz pierwszy

**Początkowe Wyliczenie zmian w chmurze**: po utworzeniu nowej grupy synchronizacji, początkowym wyliczeniem zmian w chmurze jest pierwszy krok, który zostanie wykonany. W tym procesie system wylicza wszystkie elementy w udziale plików platformy Azure. W trakcie tego procesu nie będzie żadnych działań synchronizacji, co oznacza, że żadne elementy nie zostaną pobrane z punktu końcowego w chmurze do punktu końcowego serwera i żadne elementy nie zostaną przekazane z punktu końcowego serwera do punktu końcowego w chmurze. Działanie synchronizacji zostanie wznowione po zakończeniu początkowego wyliczenia zmian w chmurze.
Szybkość wydajności to 20 obiektów na sekundę. Klienci mogą oszacować czas trwania wstępnego wyliczania zmian w chmurze, określając liczbę elementów w udziale chmury i korzystając z następującej formuły, aby uzyskać czas w dniach. 

   **Czas (w dniach) dla początkowej wyliczenia chmury = (liczba obiektów w punkcie końcowym w chmurze)/(20 * 60 * 60 * 24)**

**Początkowa synchronizacja danych z systemu Windows Server do udziału plików platformy Azure**: wiele wdrożeń Azure File Sync rozpoczyna się od pustego udziału plików platformy Azure, ponieważ wszystkie dane są w systemie Windows Server. W takich przypadkach początkowe Wyliczenie zmian w chmurze jest szybkie i większość czasu będzie poświęcana na synchronizację zmian z systemu Windows Server w udziałach plików platformy Azure. 

Podczas synchronizacji przekazuje dane do udziału plików platformy Azure, nie ma żadnego przestoju na lokalnym serwerze plików, a administratorzy mogą [skonfigurować limity sieciowe](./storage-sync-files-server-registration.md#set-azure-file-sync-network-limits) w celu ograniczenia przepustowości używanej do przekazywania danych w tle.

Synchronizacja początkowa jest zwykle ograniczona przez początkową szybkość przekazywania 20 plików na sekundę dla każdej grupy synchronizacji. Klienci mogą oszacować czas przekazywania wszystkich danych na platformę Azure, korzystając z następującej formuły, aby uzyskać czas w dniach:  

   **Czas (w dniach) przekazywania plików do grupy synchronizacji = (liczba obiektów w punkcie końcowym serwera)/(20 * 60 * 60 * 24)**

Dzielenie danych na wiele punktów końcowych serwera i grup synchronizacji może przyspieszyć to wstępne przekazywanie danych, ponieważ przekazywanie może odbywać się równolegle dla wielu grup synchronizacji z częstotliwością 20 elementów na sekundę. Tak więc dwie grupy synchronizacji byłyby uruchomione ze łączną szybkością 40 elementów na sekundę. Łączny czas do ukończenia to oszacowanie czasu dla grupy synchronizacji z największą liczbą plików do zsynchronizowania.

**Przepływność pobierania przestrzeni nazw** Po dodaniu nowego punktu końcowego serwera do istniejącej grupy synchronizacji Agent Azure File Sync nie pobiera żadnej zawartości pliku z punktu końcowego w chmurze. Najpierw synchronizuje pełną przestrzeń nazw, a następnie wyzwala odwołanie w tle w celu pobrania plików, w całości lub, jeśli włączono obsługę warstw w chmurze, do zasad obsługi warstw w chmurze ustawionych w punkcie końcowym serwera.

| Synchronizacja bieżąca  | Szczegóły  |
|-|--|
| Liczba zsynchronizowanych obiektów| 125 000 obiektów (~ 1% zmian) |
| Rozmiar zestawu danych| 50 GiB |
| Średni rozmiar pliku | ~ 500 KiB |
| Przepływność przekazywania | 20 obiektów na sekundę na grupę synchronizacji |
| Przepływność pełnego pobierania * | 60 obiektów na sekundę |

* Jeśli włączono obsługę warstw w chmurze, prawdopodobnie zobaczysz lepszą wydajność, ponieważ pobierane są tylko niektóre z danych plików. Azure File Sync pobiera tylko dane plików z pamięci podręcznej, gdy zostaną zmienione na dowolnym z punktów końcowych. W przypadku wszystkich warstwowych lub nowo utworzonych plików Agent nie pobiera danych plików, a zamiast tego synchronizuje przestrzeń nazw ze wszystkimi punktami końcowymi serwera. Agent obsługuje również częściowe pobieranie plików warstwowych, które są dostępne dla użytkownika. 

> [!Note]  
> Powyższe numery nie są wskaźnikami wydajności, które będą używane. Rzeczywista wydajność będzie zależeć od wielu czynników, jak opisano na początku tej sekcji.

Ogólnie rzecz biorąc, należy pamiętać o kilku kwestiach:

- Przepływność obiektu jest około skalowana proporcjonalnie do liczby grup synchronizacji na serwerze. Dzielenie danych na wiele grup synchronizacji na serwerze daje lepszą przepływność, co jest również ograniczone przez serwer i sieć.
- Przepływność obiektu jest odwrotnie proporcjonalna do przepływności MiB na sekundę. W przypadku mniejszych plików można zwiększyć przepływność w zakresie liczby obiektów przetworzonych na sekundę, ale mniejszą przepływność MiB na sekundę. Z kolei w przypadku większych plików można uzyskać mniej obiektów przetworzonych na sekundę, ale wyższą przepływność MiB na sekundę. Przepływność MiB na sekundę jest ograniczona przez elementy docelowe skalowania Azure Files.

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)