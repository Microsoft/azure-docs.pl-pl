---
title: Nadmiarowość danych
titleSuffix: Azure Storage
description: Informacje o nadmiarowości danych w usłudze Azure Storage. Dane na koncie Microsoft Azure Storage są replikowane w celu zapewnienia trwałości i wysokiej dostępności.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3665421ddbdd9cf079ff4aab9377fc9164a1599c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575364"
---
# <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage

Usługa Azure Storage zawsze przechowuje wiele kopii danych, dzięki czemu są chronione przed planowanymi i nieplanowanych zdarzeniami, w tym przejściowymi awariami sprzętu, awariami sieci lub zasilania oraz ogromną klęską żywiołową. Nadmiarowość zapewnia, że konto magazynu spełnia cele dotyczące dostępności i trwałości nawet w przypadku awarii.

Podejmując decyzję o tym, która opcja nadmiarowości jest najlepsza dla Twojego scenariusza, weź pod uwagę kompromisy między niższymi kosztami i wyższą dostępnością. Czynniki, które pomagają określić, którą opcję nadmiarowości należy wybrać, obejmują:  

- Sposób replikowania danych w regionie podstawowym
- Określa, czy dane są replikowane do drugiego regionu geograficznie odległego od regionu podstawowego w celu ochrony przed awariami regionalnymi
- Określa, czy aplikacja wymaga dostępu do odczytu replikowanych danych w regionie pomocniczym, jeśli region podstawowy stanie się niedostępny z jakiegokolwiek powodu

## <a name="redundancy-in-the-primary-region"></a>Nadmiarowość w regionie podstawowym

Dane na koncie usługi Azure Storage są zawsze replikowane trzy razy w regionie podstawowym. Usługa Azure Storage oferuje dwie metody replikowania danych w regionie podstawowym:

- **Magazyn lokalnie nadmiarowy (LRS)** kopiuje dane synchronicznie trzy razy w obrębie jednej lokalizacji fizycznej w regionie podstawowym. LRS jest najmniej kosztowną opcją replikacji, ale nie jest zalecana w przypadku aplikacji wymagających wysokiej dostępności.
- **Magazyn strefowo nadmiarowy (ZRS)** kopiuje dane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym. W przypadku aplikacji wymagających wysokiej dostępności firma Microsoft zaleca używanie magazynu ZRS w regionie podstawowym, a także replikację do regionu pomocniczego.

> [!NOTE]
> Firma Microsoft zaleca używanie usługi ZRS w regionie podstawowym dla Azure Data Lake Storage Gen2 obciążeń.

### <a name="locally-redundant-storage"></a>Magazyn lokalnie nadmiarowy

Magazyn lokalnie nadmiarowy (LRS) replikuje dane trzy razy w jednym centrum danych w regionie podstawowym. LRS zapewnia trwałość obiektów na poziomie co najmniej 99,9999999999% (11 dziewiątek) w danym roku.

LRS to najtańsza opcja nadmiarowości, która zapewnia najmniejszą trwałość w porównaniu z innymi opcjami. Magazyn LRS chroni dane przed awariami regałów serwerowych i dysków. Jeśli jednak w centrum danych wystąpi awaria, taka jak pożar lub powódź, wszystkie repliki konta magazynu korzystające z magazynu LRS mogą zostać utracone lub nieodwracalne. Aby zmniejszyć to ryzyko, [](#zone-redundant-storage) firma Microsoft zaleca użycie magazynu strefowo nadmiarowego (ZRS), [](#geo-redundant-storage) magazynu geograficznie nadmiarowego (GRS) lub magazynu [geograficznie i strefowo nadmiarowego](#geo-zone-redundant-storage) (GZRS).

Żądanie zapisu do konta magazynu, które korzysta z magazynu LRS, odbywa się synchronicznie. Operacja zapisu zwraca pomyślnie dopiero po zapisie danych we wszystkich trzech replikach.

Na poniższym diagramie przedstawiono sposób replikowania danych w jednym centrum danych przy użyciu LRS:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Diagram przedstawiający sposób replikowania danych w jednym centrum danych za pomocą LRS":::

LRS jest dobrym wyborem w następujących scenariuszach:

- Jeśli aplikacja przechowuje dane, które można łatwo odtworzyć w przypadku utraty danych, możesz wybrać magazyn LRS.
- Jeśli aplikacja jest ograniczona do replikowania danych tylko w obrębie kraju lub regionu ze względu na wymagania dotyczące ładu danych, możesz wybrać LRS. W niektórych przypadkach sparowane regiony, w których dane są replikowane geograficznie, mogą być w innym kraju lub regionie. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz Regiony [platformy Azure.](https://azure.microsoft.com/regions/)

### <a name="zone-redundant-storage"></a>Magazyn strefowo nadmiarowy

Magazyn strefowo nadmiarowy (ZRS) replikuje dane usługi Azure Storage synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym. Każda strefa dostępności jest oddzielną lokalizacją fizyczną z niezależnym zasilaniem, chłodzeniem i siecią. Magazyn ZRS oferuje trwałość obiektów danych usługi Azure Storage na poziomie co najmniej 99,9999999999% (12 9) w danym roku.

Dzięki ZRS dane są nadal dostępne dla operacji odczytu i zapisu, nawet jeśli strefa stanie się niedostępna. Jeśli strefa stanie się niedostępna, platforma Azure podejmie aktualizacje sieciowe, takie jak ponowne wskazanie DNS. Te aktualizacje mogą mieć wpływ na aplikację, jeśli uzyskujesz dostęp do danych przed ich ukończeniem. Podczas projektowania aplikacji dla ZRS należy stosować rozwiązania dotyczące obsługi błędów przejściowych, w tym implementowanie zasad ponawiania przy użyciu wykładniczego wyłączenia.

Żądanie zapisu do konta magazynu, które korzysta z magazynu ZRS, odbywa się synchronicznie. Operacja zapisu zwraca pomyślnie tylko wtedy, gdy dane są zapisywane we wszystkich replikach w trzech strefach dostępności.

Firma Microsoft zaleca korzystanie z usług ZRS w regionie podstawowym w przypadku scenariuszy, które wymagają spójności, trwałości i wysokiej dostępności. ZRS jest również zalecany do ograniczania replikacji danych do wewnątrz kraju lub regionu w celu spełnienia wymagań dotyczących ładu danych.

Na poniższym diagramie przedstawiono sposób replikowania danych między strefami dostępności w regionie podstawowym przy użyciu usługi ZRS:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Diagram przedstawiający sposób replikowania danych w regionie podstawowym przy użyciu systemu ZRS":::

Usługa ZRS zapewnia doskonałą wydajność, małe opóźnienia i odporność danych, jeśli staną się tymczasowo niedostępne. Jednak sama ochrona ZRS może nie chronić danych przed awarią regionalną, która ma trwały wpływ na wiele stref. W celu ochrony przed regionalnymi awariami firma Microsoft zaleca używanie magazynu geograficznie strefowo [nadmiarowego](#geo-zone-redundant-storage) (GZRS), który używa magazynu ZRS w regionie podstawowym, a także replikacji geograficznej danych do regionu pomocniczego.

W poniższej tabeli przedstawiono, które typy kont magazynu obsługują magazyn ZRS w których regionach:

| Typ konta magazynu | Obsługiwane regiony | Obsługiwane usługi |
|--|--|--|
| Ogólnego przeznaczenia, wersja<sup>2 1</sup> | (Afryka) Północna Republika Południowej Afryki<br /> (Azja i Pacyfik) Azja Południowo-Wschodnia<br /> (Azja i Pacyfik) Australia Wschodnia<br /> (Azja i Pacyfik) Japonia Wschodnia<br /> (Kanada) Kanada Środkowa<br /> (Europa) Europa Północna<br /> (Europa) Europa Zachodnia<br /> (Europa) Francja Środkowa<br /> (Europa) Niemcy Zachodnio-środkowe<br /> (Europa) Południowe Zjednoczone Królestwo<br /> (Ameryka Południowa) Brazylia Południowa<br /> (USA) Środkowe usa<br /> (USA) Wschodnie stany USA<br /> (USA) Wschodnie usa 2<br /> (USA) Południowo-środkowe usa<br /> (USA) Zachodnie stany USA<br /> (USA) Zachodnie stany USA 2 | Blokowe obiekty blob<br /> Stronicowe obiekty blob<sup>2</sup><br /> Udziały plików (standardowe)<br /> tabelami<br /> Kolejki<br /> |
| BlockBlobStorage<sup>1</sup> | Azja Południowo-Wschodnia<br /> Australia Wschodnia<br /> Europa Północna<br /> Europa Zachodnia<br /> Francja Środkowa <br /> Japan East<br /> Południowe Zjednoczone Królestwo <br /> Wschodnie stany USA <br /> Wschodnie stany USA 2 <br /> Zachodnie stany USA 2| Tylko blokowe obiekty blob w wersji Premium |
| FileStorage | Azja Południowo-Wschodnia<br /> Australia Wschodnia<br /> Europa Północna<br /> Europa Zachodnia<br /> Francja Środkowa <br /> Japan East<br /> Południowe Zjednoczone Królestwo <br /> Wschodnie stany USA <br /> Wschodnie stany USA 2 <br /> Zachodnie stany USA 2 | Tylko udziały plików Premium |

<sup>1</sup> Warstwa Archiwum nie jest obecnie obsługiwana w przypadku kont magazynu ZRS.<br />
<sup>2 Konta</sup> magazynu zawierające dyski zarządzane platformy Azure dla maszyn wirtualnych zawsze używają magazynu LRS. Dyski nieza zarządzania platformy Azure powinny również używać magazynu LRS. Istnieje możliwość utworzenia konta magazynu dla dysków niezalecanych platformy Azure, które korzysta z magazynu GRS, ale nie jest zalecane z powodu potencjalnych problemów ze spójnością asynchronicznej replikacji geograficznej. Ani dyski zarządzane, ani nieza zarządzanie nie obsługują magazynu ZRS ani GZRS. Aby uzyskać więcej informacji na temat dysków zarządzanych, zobacz [Cennik dysków zarządzanych platformy Azure.](https://azure.microsoft.com/pricing/details/managed-disks/)

Aby uzyskać informacje o regionach, które obsługują usługi ZRS, zobacz Services support by region (Obsługa usług według **regionów)** [w te Strefy dostępności platformy Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Nadmiarowość w regionie pomocniczym

W przypadku aplikacji wymagających wysokiej dostępności można dodatkowo skopiować dane na koncie magazynu do regionu pomocniczego, który znajduje się setki kilometrów od regionu podstawowego. Jeśli konto magazynu jest kopiowane do regionu pomocniczego, dane są trwałe nawet w przypadku całkowitej awarii regionalnej lub awarii, w której nie można odzyskać regionu podstawowego.

Podczas tworzenia konta magazynu należy wybrać region podstawowy dla konta. Sparowany region pomocniczy jest określany na podstawie regionu podstawowego i nie można go zmienić. Aby uzyskać więcej informacji na temat regionów obsługiwanych przez platformę Azure, zobacz [Regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).

Usługa Azure Storage oferuje dwie opcje kopiowania danych do regionu pomocniczego:

- **Magazyn geograficznie nadmiarowy (GRS)** kopiuje dane synchronicznie trzy razy w ramach jednej lokalizacji fizycznej w regionie podstawowym przy użyciu LRS. Następnie dane są kopiowane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym. W regionie pomocniczym dane są kopiowane synchronicznie trzy razy przy użyciu LRS.
- **Magazyn geograficznie i strefowo nadmiarowy (GZRS)** kopiuje dane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym przy użyciu magazynu ZRS. Następnie dane są kopiowane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym. W regionie pomocniczym dane są kopiowane synchronicznie trzy razy przy użyciu LRS.

> [!NOTE]
> Podstawową różnicą między grs i GZRS jest sposób replikowania danych w regionie podstawowym. W regionie pomocniczym dane są zawsze replikowane synchronicznie trzy razy przy użyciu LRS. LRS w regionie pomocniczym chroni dane przed awariami sprzętu.

W przypadku usług GRS lub GZRS dane w regionie pomocniczym nie są dostępne do odczytu lub zapisu, chyba że istnieje trybu failover do regionu pomocniczego. Aby uzyskać dostęp do odczytu do regionu pomocniczego, skonfiguruj konto magazynu tak, aby używało magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) lub magazynu geograficznie strefowo nadmiarowego dostępnego do odczytu (RA-GZRS). Aby uzyskać więcej informacji, zobacz [Odczyt dostępu do danych w regionie pomocniczym](#read-access-to-data-in-the-secondary-region).

Jeśli region podstawowy stanie się niedostępny, możesz wybrać opcję pracy awaryjnej w regionie pomocniczym. Po zakończeniu trybu failover region pomocniczy staje się regionem podstawowym i można ponownie odczytywać i zapisywać dane. Aby uzyskać więcej informacji na temat odzyskiwania po awarii i dowiedzieć się, jak przechować w tryb failover do regionu pomocniczego, zobacz Odzyskiwanie po awarii [i tryb failover konta magazynu](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Ponieważ dane są replikowane asynchronicznie do regionu pomocniczego, awaria wpływająca na region podstawowy może spowodować utratę danych, jeśli region podstawowy nie może zostać odzyskany. Interwał między najnowszymi zapisami w regionie podstawowym i ostatnim zapisem w regionie pomocniczym jest nazywany celem punktu odzyskiwania (RPO). Punkt punktu odzyskania wskazuje punkt w czasie, do którego można odzyskać dane. Usługa Azure Storage zwykle ma celu czasu RPO mniejsze niż 15 minut, chociaż obecnie nie ma umowy SLA dotyczącej czasu replikacji danych do regionu pomocniczego.

### <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

Magazyn geograficznie nadmiarowy (GRS) kopiuje dane synchronicznie trzy razy w ramach jednej lokalizacji fizycznej w regionie podstawowym przy użyciu LRS. Następnie dane są kopiowane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym, który jest setki kilometrów od regionu podstawowego. Magazyn GRS oferuje trwałość obiektów danych usługi Azure Storage na poziomie co najmniej 99,9999999999999% (16 9) w danym roku.

Operacja zapisu jest najpierw zatwierdzona w lokalizacji podstawowej i replikowana przy użyciu LRS. Aktualizacja jest następnie replikowana asynchronicznie do regionu pomocniczego. Gdy dane są zapisywane w lokalizacji pomocniczej, są również replikowane w tej lokalizacji przy użyciu LRS.

Na poniższym diagramie przedstawiono sposób replikowania danych za pomocą grs lub RA-GRS:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Diagram przedstawiający sposób replikowania danych za pomocą grs lub RA-GRS":::

### <a name="geo-zone-redundant-storage"></a>Magazyn geograficznie i strefowo nadmiarowy

Magazyn geograficznie i strefowo nadmiarowy (GZRS, geo-zone-redundant storage) łączy wysoką dostępność zapewnianą przez nadmiarowość w różnych strefach dostępności z ochroną przed regionalnymi błędami zapewnianą przez replikację geograficzną. Dane na koncie magazynu GZRS są kopiowane do trzech stref dostępności platformy [Azure](../../availability-zones/az-overview.md) w regionie podstawowym, a także są replikowane do dodatkowego regionu geograficznego w celu ochrony przed awariami regionalnymi. Firma Microsoft zaleca korzystanie z usług GZRS w przypadku aplikacji wymagających maksymalnej spójności, trwałości i dostępności, doskonałej wydajności i odporności na odzyskiwanie po awarii.

Za pomocą konta magazynu GZRS możesz kontynuować odczytywanie i zapis danych, jeśli strefa dostępności stanie się niedostępna lub nie będzie można jej odwdzięcić. Ponadto dane są również trwałe w przypadku całkowitej awarii regionalnej lub awarii, w której nie można odzyskać regionu podstawowego. GZRS zaprojektowano tak, aby zapewnić trwałość obiektów na poziomie co najmniej 99,9999999999999% (16 9) obiektów w danym roku.

Na poniższym diagramie przedstawiono sposób replikowania danych za pomocą usług GZRS lub RA-GZRS:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Diagram przedstawiający sposób replikowania danych za pomocą usług GZRS lub RA-GZRS":::

Tylko konta magazynu ogólnego przeznaczenia w wersji 2 obsługują magazyny GZRS i RA-GZRS. Aby uzyskać więcej informacji dotyczących typów kont magazynu, zobacz temat [Przegląd konta usługi Azure Storage](storage-account-overview.md). Magazyny GZRS i RA-GZRS obsługują blokowe obiekty blob, stronicowe obiekty blob (z wyjątkiem dysków VHD), pliki, tabele i kolejki.

GZRS i RA-GZRS są obsługiwane w następujących regionach:

- (Afryka) Północna Republika Południowej Afryki
- (Azja i Pacyfik) Azja Wschodnia
- (Azja i Pacyfik) Azja Południowo-Wschodnia
- (Azja i Pacyfik) Australia Wschodnia
- (Azja i Pacyfik) Indie Środkowe
- (Azja i Pacyfik) Japonia Wschodnia
- (Azja i Pacyfik) Korea Środkowa
- (Kanada) Kanada Środkowa
- (Europa) Europa Północna
- (Europa) Europa Zachodnia
- (Europa) Francja Środkowa
- (Europa) Niemcy Zachodnio-środkowe
- (Europa) Holandia Wschodnia
- (Europa) Korea Północna
- (Europa) Południowe Zjednoczone Królestwo
- (Bliski Wschód) Północne Zjednoczone Emiraty Zjednoczone
- (Ameryka Południowa) Brazylia Południowa
- (USA) Środkowe usa
- (USA) Wschodnie stany USA
- (USA) Wschodnie usa 2
- (USA) Północno-środkowe usa
- (USA) Południowo-środkowe usa
- (USA) Zachodnie stany USA
- (USA) Zachodnie stany USA 2

Aby uzyskać informacje o cenach, zobacz szczegóły cennika obiektów [blob,](https://azure.microsoft.com/pricing/details/storage/blobs) [plików,](https://azure.microsoft.com/pricing/details/storage/files/) [kolejek](https://azure.microsoft.com/pricing/details/storage/queues/)i [tabel.](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="read-access-to-data-in-the-secondary-region"></a>Dostęp do odczytu danych w regionie pomocniczym

Magazyn geograficznie nadmiarowy (z magazynem GRS lub GZRS) replikuje dane do innej lokalizacji fizycznej w regionie pomocniczym, aby chronić je przed regionalnymi erudycją. Te dane są jednak dostępne tylko do odczytu, jeśli klient lub firma Microsoft zainicjuje trybu failover z regionu podstawowego do pomocniczego. Po włączeniu dostępu do odczytu do regionu pomocniczego dane są dostępne do odczytu przez cały czas, w tym w sytuacji, gdy region podstawowy staje się niedostępny. Aby uzyskać dostęp do odczytu do regionu pomocniczego, włącz magazyn geograficznie nadmiarowy do odczytu (RA-GRS) lub magazyn geograficznie i strefowo nadmiarowy z dostępem do odczytu (RA-GZRS).

> [!NOTE]
> Azure Files nie obsługuje magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) ani magazynu geograficznie i strefowo nadmiarowego dostępnego do odczytu (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Projektowanie aplikacji pod celu uzyskania dostępu do odczytu do pomocniczej

Jeśli twoje konto magazynu jest skonfigurowane do dostępu do odczytu do regionu pomocniczego, możesz zaprojektować aplikacje tak, aby bezproblemowo przechodziły do odczytu danych z regionu pomocniczego, jeśli region podstawowy stanie się niedostępny z jakiegokolwiek powodu. 

Region pomocniczy jest dostępny do odczytu po włączeniu funkcji RA-GRS lub RA-GZRS, dzięki czemu można z wyprzedzeniem przetestować aplikację, aby upewnić się, że w razie 300 minut będzie ona prawidłowo odczytywana z pomocniczej grupy. Aby uzyskać więcej informacji na temat sposobu projektowania aplikacji w celu zapewnienia wysokiej dostępności, zobacz [Use geo-redundancy to design highly available applications](geo-redundant-design.md)(Projektowanie aplikacji o wysokiej dostępności przy użyciu nadmiarowości geograficznej).

Po włączeniu dostępu do odczytu do pomocniczego punktu końcowego aplikacja może być odczytywana z pomocniczego punktu końcowego, a także z podstawowego punktu końcowego. Pomocniczy punkt końcowy dołącza do nazwy konta sufiks *–secondary.* Jeśli na przykład podstawowym punktem końcowym usługi Blob Storage jest `myaccount.blob.core.windows.net` , pomocniczy punkt końcowy to `myaccount-secondary.blob.core.windows.net` . Klucze dostępu do konta magazynu są takie same dla podstawowego i pomocniczego punktu końcowego.

### <a name="check-the-last-sync-time-property"></a>Sprawdzanie właściwości czasu ostatniej synchronizacji

Ponieważ dane są replikowane asynchronicznie do regionu pomocniczego, region pomocniczy często znajduje się za regionem podstawowym. Jeśli w regionie podstawowym nastąpi awaria, prawdopodobnie wszystkie zapisu w regionie podstawowym nie zostaną jeszcze zreplikowane do pomocniczej bazy danych.

Aby określić, które operacje zapisu zostały zreplikowane do regionu pomocniczego, aplikacja może sprawdzić właściwość **Czas** ostatniej synchronizacji dla konta magazynu. Wszystkie operacje zapisu zapisane w regionie podstawowym przed ostatnią synchronizacją zostały pomyślnie zreplikowane do regionu pomocniczego, co oznacza, że są dostępne do odczytu z regionu pomocniczego. Wszystkie operacje zapisu zapisane w regionie podstawowym po czasie ostatniej synchronizacji mogą być lub nie zostały zreplikowane do regionu pomocniczego, co oznacza, że mogą nie być dostępne dla operacji odczytu.

Możesz wysyłać zapytania o wartość właściwości **Czas** ostatniej synchronizacji przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub jednej z bibliotek klienta usługi Azure Storage. Właściwość **Czas ostatniej synchronizacji** jest wartością daty/godziny GMT. Aby uzyskać więcej informacji, [zobacz Sprawdzanie właściwości Czas ostatniej synchronizacji dla konta magazynu.](last-sync-time-get.md)

## <a name="summary-of-redundancy-options"></a>Podsumowanie opcji nadmiarowości

Tabele w poniższych sekcjach zawierają podsumowanie opcji nadmiarowości dostępnych dla usługi Azure Storage

### <a name="durability-and-availability-parameters"></a>Parametry trwałości i dostępności

W poniższej tabeli opisano kluczowe parametry dla każdej opcji nadmiarowości:

| Parametr | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Procent trwałości obiektów w danym roku | co najmniej 99,9999999999% (11 9) | co najmniej 99,9999999999% (12 9) | co najmniej 99,99999999999999% (16 wartości 9) | co najmniej 99,99999999999999% (16 wartości 9) |
| Dostępność żądań odczytu | Co najmniej 99,9% (99% dla warstwy dostępu Chłodna) | Co najmniej 99,9% (99% dla warstwy dostępu Chłodna) | Co najmniej 99,9% (99% dla warstwy dostępu Chłodna) dla grs<br /><br />Co najmniej 99,99% (99,9% dla warstwy dostępu Chłodna) dla ra-GRS | Co najmniej 99,9% (99% dla warstwy dostępu Chłodna) dla GZRS<br /><br />Co najmniej 99,99% (99,9% dla warstwy dostępu Chłodna) dla usługi RA-GZRS |
| Dostępność żądań zapisu | Co najmniej 99,9% (99% dla warstwy dostępu Chłodna) | Co najmniej 99,9% (99% dla warstwy dostępu Chłodna) | Co najmniej 99,9% (99% dla warstwy dostępu Chłodna) | Co najmniej 99,9% (99% dla warstwy dostępu Chłodna) |
| Liczba kopii danych utrzymywanych w oddzielnych węzłach | Trzy kopie w jednym regionie | Trzy kopie w oddzielnych strefach dostępności w jednym regionie | Łącznie sześć kopii, w tym trzy w regionie podstawowym i trzy w regionie pomocniczym | Łącznie jest sześć kopii, w tym trzy w oddzielnych strefach dostępności w regionie podstawowym i trzy lokalnie nadmiarowe kopie w regionie pomocniczym |

### <a name="durability-and-availability-by-outage-scenario"></a>Trwałość i dostępność według scenariusza outage

W poniższej tabeli przedstawiono, czy dane są trwałe i dostępne w danym scenariuszu, w zależności od tego, jaki typ nadmiarowości obowiązywa dla konta magazynu:

| Scenariusz 3000 000 | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Węzeł w centrum danych staje się niedostępny | Yes | Yes | Yes | Yes |
| Całe centrum danych (strefowe lub inne niż strefowe) staje się niedostępne | Nie | Yes | Tak<sup>1</sup> | Yes |
| W regionie podstawowym występuje błąd w całym regionie | Nie | Nie | Tak<sup>1</sup> | Tak<sup>1</sup> |
| Dostęp do odczytu do regionu pomocniczego jest dostępny, jeśli region podstawowy stanie się niedostępny | Nie | Nie | Tak (z ra-GRS) | Tak (z ra-GZRS) |

<sup>1 Tryb</sup> failover konta jest wymagany do przywrócenia dostępności zapisu, jeśli region podstawowy stanie się niedostępny. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie po awarii i tryb failover konta magazynu](storage-disaster-recovery-guidance.md).

### <a name="supported-azure-storage-services"></a>Obsługiwane usługi Azure Storage

W poniższej tabeli przedstawiono opcje nadmiarowości obsługiwane przez każdą usługę Azure Storage.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br />Dyski zarządzane platformy Azure | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> |

### <a name="supported-storage-account-types"></a>Obsługiwane typy kont magazynu

W poniższej tabeli przedstawiono opcje nadmiarowości obsługiwane przez poszczególne typy kont magazynu. Aby uzyskać informacje dotyczące typów kont magazynu, zobacz [Omówienie konta magazynu.](storage-account-overview.md)

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Ogólnego przeznaczenia, wersja 2<br /> Ogólnego przeznaczenia, wersja 1<br /> BlockBlobStorage<br /> BlobStorage<br /> FileStorage | Ogólnego przeznaczenia, wersja 2<br /> BlockBlobStorage<br /> FileStorage | Ogólnego przeznaczenia, wersja 2<br /> Ogólnego przeznaczenia, wersja 1<br /> BlobStorage | Ogólnego przeznaczenia, wersja 2 |

Wszystkie dane dla wszystkich kont magazynu są kopiowane zgodnie z opcją nadmiarowości konta magazynu. Kopiowane są obiekty, w tym blokowe obiekty blob, uzupełnialne obiekty blob, stronicowe obiekty blob, kolejki, tabele i pliki. Kopiowane są dane we wszystkich warstwach, w tym w warstwie Archiwum. Aby uzyskać więcej informacji na temat warstw obiektów blob, zobacz Azure Blob Storage: warstwy dostępu [Gorąca, Chłodna i Archiwum](../blobs/storage-blob-storage-tiers.md).

Aby uzyskać informacje o cenach poszczególnych opcji nadmiarowości, zobacz [Cennik usługi Azure Storage.](https://azure.microsoft.com/pricing/details/storage/)

> [!NOTE]
> Usługa Azure Premium Disk Storage obecnie obsługuje tylko magazyn lokalnie nadmiarowy (LRS). Konta magazynu blokowych obiektów blob obsługują magazyn lokalnie nadmiarowy (LRS) i magazyn strefowo nadmiarowy (ZRS) w niektórych regionach.

## <a name="data-integrity"></a>Integralność danych

Usługa Azure Storage regularnie weryfikuje integralność przechowywanych danych przy użyciu cyklicznych kontroli nadmiarowości (CPC). Jeśli zostanie wykryte uszkodzenie danych, zostaną naprawione przy użyciu nadmiarowych danych. Usługa Azure Storage oblicza również sumy kontrolne dla całego ruchu sieciowego w celu wykrycia uszkodzenia pakietów danych podczas przechowywania lub pobierania danych.

## <a name="see-also"></a>Zobacz też

- [Sprawdź właściwość Czas ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md)
- [Zmiana opcji nadmiarowości dla konta magazynu](redundancy-migration.md)
- [Używanie nadmiarowości geograficznej do projektowania aplikacji o wysokiej dostępie](geo-redundant-design.md)
- [Odzyskiwanie po awarii i tryb failover konta magazynu](storage-disaster-recovery-guidance.md)
