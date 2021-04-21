---
title: Cele dotyczące skalowalności i wydajności usługi Azure Files
description: Dowiedz się więcej o celach dotyczących skalowalności i wydajności dla Azure Files, w tym o pojemności, szybkości żądań oraz limitach przepustowości dla ruchu przychodzącego i wychodzącego.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bdcee6b8ced45dba34309724e5a634cbb60a6d37
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818822"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności usługi Azure Files
[Azure Files](storage-files-introduction.md) udostępnia w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołów SMB i NFS systemu plików. W tym artykule omówiono cele dotyczące skalowalności i wydajności dla Azure Files i Azure File Sync.

Cele dotyczące skalowalności i wydajności wymienione w tym miejscu są celami wysokiej klasy, ale mogą mieć na nie wpływ inne zmienne we wdrożeniu. Na przykład przepływność pliku może być również ograniczona przez dostępną przepustowość sieci, a nie tylko serwery hostowane w udziałach plików platformy Azure. Zdecydowanie zalecamy przetestowanie wzorca użycia w celu określenia, czy skalowalność i wydajność Azure Files spełnia Twoje wymagania. Dokładamy również starań, aby zwiększyć te limity wraz z czasem. 

## <a name="azure-files-scale-targets"></a>Cele skalowania usługi Azure Files
Udziały plików platformy Azure są wdrażane na kontach magazynu, które są obiektami najwyższego poziomu, które reprezentują udostępnioną pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików. Dlatego należy wziąć pod uwagę trzy kategorie: konta magazynu, udziały plików platformy Azure i pliki.

### <a name="storage-account-scale-targets"></a>Cele skalowania konta magazynu
Platforma Azure obsługuje wiele typów kont magazynu dla różnych scenariuszy magazynowania, które mogą mieć klienci, ale istnieją dwa główne typy kont magazynu dla Azure Files. Typ konta magazynu, który należy utworzyć, zależy od tego, czy chcesz utworzyć standardowy udział plików, czy udział plików w chmurze Premium: 

- Konta magazynu ogólnego przeznaczenia w wersji **2 (GPv2):** konta magazynu GPv2 umożliwiają wdrażanie udziałów plików platformy Azure na standardowym/twardym sprzęcie (opartym na dyskach twardych). Oprócz przechowywania udziałów plików platformy Azure konta magazynu GPv2 mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. Udziały plików można wdrażać w warstwach zoptymalizowanych pod kątem transakcji (ustawienie domyślne), gorąca lub chłodna.

- **Konta magazynu FileStorage:** konta magazynu FileStorage umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach SSD w warstwie Premium/SSD. Konta FileStorage mogą być używane tylko do przechowywania udziałów plików platformy Azure; Na koncie FileStorage nie można wdrożyć żadnych innych zasobów magazynu (kontenerów obiektów blob, kolejek, tabel itp.).

| Atrybut | Konta magazynu GPv2 (standardowe) | FileStorage storage accounts (premium) |
|-|-|-|
| Liczba kont magazynu na region na subskrypcję | 250 | 250 |
| Maksymalna pojemność konta magazynu | 5 PiB<sup>1</sup> | 100 TiB (aprowizowane) |
| Maksymalna liczba udziałów plików | Nieograniczona liczba | Nieograniczony, całkowity aprowizowany rozmiar wszystkich udziałów musi być mniejszy niż maksymalna pojemność konta magazynu |
| Maksymalna współbieżna liczba żądań | 20 000 IOPS<sup>1</sup> | 100 000 IOPS |
| Maksymalny ruch przychodzący | <ul><li>USA/Europa: 9536 MiB/s<sup>1</sup></li><li>Inne regiony (LRS/ZRS): 9536 MiB/s<sup>1</sup></li><li>Inne regiony (GRS): 4768 GiB/s<sup>1</sup></li></ul> | 4136 MiB/s |
| Maksymalny ruch wychodzący | 47 683 MiB/s<sup>1</sup> | 6204 MiB/s |
| Maksymalna liczba reguł sieci wirtualnej | 200 | 200 |
| Maksymalna liczba reguł adresów IP | 200 | 200 |
| Operacje odczytu zarządzania | 800 na 5 minut | 800 na 5 minut |
| Operacje zapisu zarządzania | 10 na sekundę/1200 na godzinę | 10 na sekundę/1200 na godzinę |
| Operacje listy zarządzania | 100 na 5 minut | 100 na 5 minut |

<sup>1 Konta</sup> magazynu ogólnego przeznaczenia w wersji 2 obsługują wyższe limity pojemności i wyższe limity dla danych przychodzących przez żądanie. Aby zażądać zwiększenia limitów kont, skontaktuj się z [pomoc techniczna platformy Azure](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Cele skalowania udziałów plików platformy Azure
| Atrybut | Standardowe udziały plików<sup>1</sup> | Udziały plików Premium |
|-|-|-|
| Minimalny rozmiar udziału plików | Brak minimum | 100 GiB (aprowizowana) |
| Aprowizowana jednostka zwiększenia/zmniejszenia rozmiaru | Nie dotyczy | 1 GiB |
| Maksymalny rozmiar udziału plików | <ul><li>100 TiB z włączoną funkcją dużego udziału plików<sup>2</sup></li><li>5 TiB, wartość domyślna</li></ul> | 100 TiB |
| Maksymalna liczba plików w udziałach plików | Bez ograniczeń | Bez ograniczeń |
| Maksymalna liczba żądań (maksymalna liczba IOPS) | <ul><li>10 000 z włączoną funkcją dużego udziału plików<sup>2</sup></li><li>1000 lub 100 żądań na 100 ms, wartość domyślna</li></ul> | <ul><li>Podstawowa wartość IOPS: 400 + 1 IOPS na GiB, do 100 000</li><li>IOPS bursting :Max (4000,3x IOPS per GiB), up to 100,000</li></ul> |
| Maksymalny ruch przychodzący dla pojedynczego udziału plików | <ul><li>Do 300 MiB/s z włączoną funkcją dużego udziału plików<sup>2</sup></li><li>Do 60 MiB/s, wartość domyślna</li></ul> | 40 MiB/s + 0,04 * aprowizowana GiB |
| Maksymalny ruch wychodzący dla pojedynczego udziału plików | <ul><li>Do 300 MiB/s z włączoną funkcją dużego udziału plików<sup>2</sup></li><li>Do 60 MiB/s, wartość domyślna</li></ul> | 60 MiB/s + 0,06 * aprowizowana GiB |
| Maksymalna liczba migawek udziałów | 200 migawek | 200 migawek |
| Maksymalna długość nazwy obiektu (katalogów i plików) | 2048 znaków | 2048 znaków |
| Składnik maksymalnej nazwy ścieżki (w ścieżce \A\B\C\D każda litera jest składnikiem) | 255 znaków | 255 znaków |
| Limit twardych linków (tylko NFS) | Nie dotyczy | 178 |
| Maksymalna liczba kanałów SMB Multichannel | Nie dotyczy | 4 |
| Maksymalna liczba przechowywanych zasad dostępu na udział plików | 5 | 5 |

<sup>1</sup> Limity dla standardowych udziałów plików mają zastosowanie do wszystkich trzech warstw dostępnych dla standardowych udziałów plików: zoptymalizowanych pod kątem transakcji, gorących i chłodnych.

<sup>2</sup> Domyślne w standardowych udziałach plików [](./storage-files-how-to-create-large-file-share.md) to 5 TiB. Aby uzyskać szczegółowe informacje na temat zwiększania skali standardowych udziałów plików do 100 TiB, zobacz Włączanie i tworzenie dużych udziałów plików.

### <a name="file-scale-targets"></a>Cele skalowania plików
| Atrybut | Pliki w standardowych udziałach plików  | Pliki w udziałach plików Premium  |
|-|-|-|
| Maksymalna wielkość pliku | 4 TiB | 4 TiB |
| Maksymalna współbieżna liczba żądań | 1000 IOPS | Do 8000<sup>1</sup> |
| Maksymalny ruch przychodzący dla pliku | 60 MiB/s | 200 MiB/s (do 1 GiB/s z SMB Multichannel w wersji<sup>zapoznawczej) 2</sup>|
| Maksymalny ruch wychodzący dla pliku | 60 MiB/s | 300 MiB/s (do 1 GiB/s z SMB Multichannel w wersji<sup>zapoznawczej) 2</sup> |
| Maksymalna liczba współbieżnych dojść | 2000 dojść | 2000 dojść  |

1 Dotyczy odczytu i zapisu we/wy (zazwyczaj mniejsze rozmiary we/wy mniejsze niż lub równe <sup>64 KiB). Operacje na metadanych, inne niż operacje odczytu i zapisu, mogą być mniejsze.</sup> 
 <sup>2 Podlega limitom sieci maszyn, dostępnej przepustowości, rozmiarom we/wy, głębokości kolejki i innym czynnikom. Aby uzyskać szczegółowe informacje, [zobacz SMB Multichannel performance (Wydajność wielu kanałów SMB).](./storage-files-smb-multichannel-performance.md)</sup>

## <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync
W poniższej tabeli przedstawiono granice testowania firmy Microsoft, a także o tym, które cele są twardymi limitami:

| Zasób | Cel | Limit twardy |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na region | 100 usług synchronizacji magazynu | Tak |
| Grupy synchronizacji na usługę synchronizacji magazynu | 200 grup synchronizacji | Tak |
| Zarejestrowane serwery na usługę synchronizacji magazynu | 99 serwerów | Tak |
| Punkty końcowe w chmurze na grupę synchronizacji | 1 punkt końcowy w chmurze | Tak |
| Punkty końcowe serwera na grupę synchronizacji | 100 punktów końcowych serwera | Tak |
| Punkty końcowe serwera na serwer | 30 punktów końcowych serwera | Tak |
| Obiekty systemu plików (katalogi i pliki) na grupę synchronizacji | 100 milionów obiektów | Nie |
| Maksymalna liczba obiektów systemu plików (katalogów i plików) w katalogu | 5 milionów obiektów | Tak |
| Maksymalny rozmiar deskryptora zabezpieczeń obiektu (katalogów i plików) | 64 KiB | Tak |
| Rozmiar pliku | 100 GiB | Nie |
| Minimalny rozmiar pliku, który ma być warstwowy | Wersja 9 i nowsze: na podstawie rozmiaru klastra systemu plików (rozmiar klastra podwójnego systemu plików). Jeśli na przykład rozmiar klastra systemu plików wynosi 4 KiB, minimalny rozmiar pliku to 8 KiB.<br> Wersja 8 i starsze: 64 KiB  | Tak |

> [!Note]  
> Punkt Azure File Sync można skalować w górę do rozmiaru udziału plików platformy Azure. Jeśli limit rozmiaru udziału plików platformy Azure zostanie osiągnięty, synchronizacja nie będzie mogła działać.

### <a name="azure-file-sync-performance-metrics"></a>Metryki wydajności usługi Azure File Sync
Ponieważ agent usługi Azure File Sync działa na maszynie z systemem Windows Server, która łączy się z udziałami plików platformy Azure, efektywna wydajność synchronizacji zależy od wielu czynników w infrastrukturze: systemu Windows Server i podstawowej konfiguracji dysku, przepustowości sieci między serwerem a magazynem platformy Azure, rozmiaru pliku, całkowitego rozmiaru zestawu danych i aktywności na zestawie danych. Ponieważ Azure File Sync działa na poziomie plików, charakterystyki wydajności rozwiązania opartego na Azure File Sync są lepiej mierzone w liczbie przetworzonych obiektów (plików i katalogów) na sekundę.

W Azure File Sync wydajność jest krytyczna w dwóch etapach:

1. **Początkowa aprowizowanie raz:** aby zoptymalizować wydajność początkowego inicjowania obsługi administracyjnej, zapoznaj się z tematem [Onboarding with Azure File Sync](../file-sync/file-sync-deployment-guide.md#onboarding-with-azure-file-sync) (Dołączanie z Azure File Sync), aby uzyskać optymalne szczegóły wdrożenia.
2. **Bieżąca synchronizacja:** po początkowym inicjaleniu danych w udziałach plików platformy Azure Azure File Sync wiele punktów końcowych w synchronizacji.

Aby ułatwić planowanie wdrożenia dla każdego z etapów, poniżej przedstawiono wyniki zaobserwowane podczas wewnętrznych testów w systemie za pomocą konfiguracji

| Konfiguracja systemu | Szczegóły |
|-|-|
| Procesor CPU | 64 rdzenie wirtualne z 64 pamięciami podręcznymi MiB L3 |
| Pamięć | 128 GiB |
| Dysk | Dyski SAS z macierzą RAID 10 z zapasową pamięcią podręczną z baterii |
| Sieć | Sieć 1 Gb/s |
| Obciążenie | Ogólnego przeznaczenia serwera plików|

| Początkowa aprowizowanie raz  | Szczegóły |
|-|-|
| Liczba obiektów | 25 milionów obiektów |
| Rozmiar zestawu danych| Ok. 4,7 TiB |
| Średni rozmiar pliku | ~200 KiB (największy plik: 100 GiB) |
| Wyliczenie początkowej zmiany w chmurze | 20 obiektów na sekundę  |
| Przekazywanie przepływności | 20 obiektów na sekundę na grupę synchronizacji |
| Przepływność pobierania przestrzeni nazw | 400 obiektów na sekundę |

### <a name="initial-one-time-provisioning"></a>Początkowa aprowizowanie raz

**Początkowe wyliczanie zmian w** chmurze: po utworzeniu nowej grupy synchronizacji pierwszym krokiem, który zostanie wykonany, jest wyliczenie początkowej zmiany w chmurze. W tym procesie system wyliczy wszystkie elementy w udziałach plików platformy Azure. W trakcie tego procesu nie będzie żadnych działań synchronizacji, tj. żadne elementy nie zostaną pobrane z punktu końcowego chmury do punktu końcowego serwera i żadne elementy nie zostaną przekazane z punktu końcowego serwera do punktu końcowego w chmurze. Działanie synchronizacji zostanie wznowione po zakończeniu początkowego wyliczania zmian w chmurze.
Szybkość działania wynosi 20 obiektów na sekundę. Klienci mogą oszacować czas ukończenia początkowego wyliczania zmian w chmurze, określając liczbę elementów w udziałach w chmurze i używając następującej formuły, aby uzyskać czas w dniach. 

   **Czas (w dniach) dla początkowego wyliczenia chmury = (liczba obiektów w punkcie końcowym chmury)/(20 * 60 * 60 * 24)**

**Początkowa synchronizacja** danych z systemu Windows Server do udziału plików platformy Azure: wiele wdrożeń Azure File Sync rozpoczyna się od pustego udziału plików platformy Azure, ponieważ wszystkie dane są w systemie Windows Server. W takich przypadkach początkowe wyliczanie zmian w chmurze jest szybkie, a większość czasu będzie spędzana na synchronizowanie zmian z systemu Windows Server z udziałami plików platformy Azure. 

Podczas gdy synchronizacja przesyła dane do udziału plików platformy Azure, nie [](../file-sync/file-sync-server-registration.md#set-azure-file-sync-network-limits) ma żadnych przestojów na lokalnym serwerze plików, a administratorzy mogą skonfigurować limity sieci w celu ograniczenia przepustowości używanej do przekazywania danych w tle.

Synchronizacja początkowa jest zwykle ograniczona przez szybkość początkowego przekazywania 20 plików na sekundę na grupę synchronizacji. Klienci mogą oszacować czas przekazywania wszystkich swoich danych na platformę Azure przy użyciu następującej formuły, aby uzyskać czas w dniach:  

   **Czas (w dniach) przekazywania plików do grupy synchronizacji = (liczba obiektów w punkcie końcowym serwera)/(20 * 60 * 60 * 24)**

Podzielenie danych na wiele punktów końcowych serwera i grup synchronizacji może przyspieszyć to początkowe przekazywanie danych, ponieważ przekazywanie może odbywać się równolegle dla wielu grup synchronizacji z szybkością 20 elementów na sekundę. W związku z tym dwie grupy synchronizacji byłyby uruchomione z łączona szybkością 40 elementów na sekundę. Łączny czas do ukończenia to szacowany czas dla grupy synchronizacji z najwięcej plików do zsynchronizowania.

**Przepływność pobierania przestrzeni nazw** Po dodaniu nowego punktu końcowego serwera do istniejącej grupy synchronizacji agent usługi Azure File Sync nie pobiera żadnej zawartości pliku z punktu końcowego w chmurze. Najpierw synchronizuje pełną przestrzeń nazw, a następnie wyzwala funkcję odwoływania w tle, aby pobrać pliki w całości lub, jeśli włączono obsługę warstw w chmurze, z zasadami obsługi warstw w chmurze ustawionymi w punkcie końcowym serwera.

| Bieżąca synchronizacja  | Szczegóły  |
|-|--|
| Liczba zsynchronizowanych obiektów| 125 000 obiektów (ok. 1% zmian) |
| Rozmiar zestawu danych| 50 GiB |
| Średni rozmiar pliku | ~500 KiB |
| Przekazywanie przepływności | 20 obiektów na sekundę na grupę synchronizacji |
| Pełna przepływność pobierania* | 60 obiektów na sekundę |

*Jeśli włączono obsługę warstw w chmurze, prawdopodobnie będziesz obserwować lepszą wydajność, ponieważ pobierane są tylko niektóre dane plików. Azure File Sync pobiera dane buforowanych plików tylko wtedy, gdy zostaną zmienione w dowolnym punkcie końcowym. W przypadku plików warstwowych lub nowo utworzonych agent nie pobiera danych pliku, a zamiast tego synchronizuje przestrzeń nazw tylko ze wszystkimi punktami końcowymi serwera. Agent obsługuje również częściowe pobieranie plików warstwowych, gdy użytkownik uzyskuje do nich dostęp. 

> [!Note]  
> Powyższe liczby nie wskazują wydajności, która będzie się świadczyć. Rzeczywista wydajność będzie zależeć od wielu czynników, jak opisano na początku tej sekcji.

W ogólnym przewodniku po wdrożeniu należy pamiętać o kilku kwestiach:

- Przepływność obiektu jest w przybliżeniu skalowana proporcjonalnie do liczby grup synchronizacji na serwerze. Dzielenie danych na wiele grup synchronizacji na serwerze zapewnia lepszą przepływność, która jest również ograniczona przez serwer i sieć.
- Przepływność obiektu jest odwrotnie proporcjonalna do przepływności MiB na sekundę. W przypadku mniejszych plików będziesz odczuwać wyższą przepływność pod względem liczby przetworzonych obiektów na sekundę, ale mniejszą przepływność MiB na sekundę. Z kolei w przypadku większych plików będzie przetwarzanych mniej obiektów na sekundę, ale większa przepływność MiB na sekundę. Przepływność MiB na sekundę jest ograniczona przez Azure Files skalowania.

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Planowanie wdrażania usługi Azure File Sync](../file-sync/file-sync-planning.md)