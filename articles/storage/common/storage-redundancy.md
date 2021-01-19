---
title: Nadmiarowość danych
titleSuffix: Azure Storage
description: Zrozumienie nadmiarowości danych w usłudze Azure Storage. Dane na koncie Microsoft Azure Storage są replikowane w celu zapewnienia trwałości i wysokiej dostępności.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 83a4a2aa8328a6e3de9eab44bbf19fc76921b128
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573363"
---
# <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage

Usługa Azure Storage zawsze przechowuje wiele kopii danych w taki sposób, aby była chroniona przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi klęskami żywiołowymi. Nadmiarowość gwarantuje, że Twoje konto magazynu spełnia jego dostępność i cele trwałości nawet w przypadku awarii.

Decydując o tym, która opcja nadmiarowości jest Najlepsza dla danego scenariusza, należy wziąć pod uwagę kompromisy między niższymi kosztami i wyższą dostępnością. Czynniki pomagające w ustaleniu, która opcja nadmiarowości należy wybrać:  

- Jak dane są replikowane w regionie podstawowym
- Bez względu na to, czy dane są replikowane do drugiego regionu, który jest geograficznie odległy do regionu podstawowego, aby chronić przed awariami regionalnymi
- Czy aplikacja wymaga dostępu do odczytu replikowanych danych w regionie pomocniczym, jeśli region podstawowy stał się niedostępny z dowolnego powodu

## <a name="redundancy-in-the-primary-region"></a>Nadmiarowość w regionie podstawowym

Dane na koncie usługi Azure Storage są zawsze replikowane trzy razy w regionie podstawowym. Usługa Azure Storage oferuje dwie metody replikowania danych w regionie podstawowym:

- **Magazyn lokalnie nadmiarowy (LRS)** kopiuje dane synchronicznie trzykrotnie w jednej lokalizacji fizycznej w regionie podstawowym. LRS to najmniej kosztowna opcja replikacji, ale nie jest zalecana w przypadku aplikacji wymagających wysokiej dostępności.
- **Magazyn strefowo nadmiarowy (ZRS)** kopiuje dane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym. W przypadku aplikacji wymagających wysokiej dostępności firma Microsoft zaleca użycie ZRS w regionie podstawowym, a także replikowanie do regionu pomocniczego.

### <a name="locally-redundant-storage"></a>Magazyn lokalnie nadmiarowy

Magazyn lokalnie nadmiarowy (LRS) replikuje dane trzy razy w jednym centrum danych w regionie podstawowym. LRS zapewnia co najmniej 99,999999999% (11 dziewięciu) trwałości obiektów w danym roku.

LRS jest opcją najniższego kosztu nadmiarowości i oferuje najmniejszą trwałość w porównaniu z innymi opcjami. LRS chroni dane przed awarią serwera i uszkodzeniem dysku. Jeśli jednak w centrum danych wystąpi awaria, taka jak pożar lub zalanie, wszystkie repliki konta magazynu za pomocą LRS mogą zostać utracone lub nieodwracalne. Aby zmniejszyć to ryzyko, firma Microsoft zaleca korzystanie z [magazynu strefowo](#zone-redundant-storage) nadmiarowego (ZRS), [magazynu geograficznie nadmiarowego](#geo-redundant-storage) (GRS) lub [geograficznie nadmiarowego magazynu](#geo-zone-redundant-storage) (GZRS).

Żądanie zapisu do konta magazynu, które korzysta z LRS, odbywa się synchronicznie. Operacja zapisu jest zwracana pomyślnie tylko po zapisaniu danych we wszystkich trzech replikach.

Na poniższym diagramie pokazano, jak dane są replikowane w jednym centrum danych z LRS:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Diagram przedstawiający sposób replikowania danych w jednym centrum danych za pomocą LRS":::

LRS to dobry wybór w następujących scenariuszach:

- Jeśli aplikacja przechowuje dane, które można łatwo odtworzyć w przypadku utraty danych, można wybrać opcję LRS.
- Jeśli aplikacja jest ograniczona do replikowania danych tylko w danym kraju lub regionie ze względu na wymagania dotyczące zarządzania danymi, możesz wybrać pozycję LRS. W niektórych przypadkach sparowane regiony, w których dane są replikowane geograficznie, mogą znajdować się w innym kraju lub regionie. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Magazyn strefowo nadmiarowy

Magazyn strefowo nadmiarowy (ZRS) replikuje dane usługi Azure Storage synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym. Każda strefa dostępności jest oddzielną lokalizacją fizyczną z niezależnym zasilaniem, chłodzeniem i siecią. ZRS oferuje trwałość dla obiektów danych usługi Azure Storage, co najmniej 99,9999999999% (12 9) w danym roku.

Dzięki ZRS dane są nadal dostępne dla operacji odczytu i zapisu, nawet jeśli strefa przestanie być dostępna. Jeśli strefa przestanie być dostępna, platforma Azure podniesie aktualizacje sieciowe, takie jak ponowne wskazanie DNS. Te aktualizacje mogą mieć wpływ na aplikację, Jeśli uzyskujesz dostęp do danych przed ukończeniem aktualizacji. Podczas projektowania aplikacji dla ZRS, postępuj zgodnie z zaleceniami dotyczącymi obsługi błędów przejściowych, w tym implementowanie zasad ponawiania z wycofywaniem z powrotem.

Żądanie zapisu do konta magazynu, które korzysta z ZRS, odbywa się synchronicznie. Operacja zapisu jest zwracana pomyślnie tylko po zapisaniu danych we wszystkich replikach w trzech strefach dostępności.

Firma Microsoft zaleca korzystanie z usługi ZRS w regionie podstawowym w scenariuszach wymagających spójności, trwałości i wysokiej dostępności. ZRS jest również zalecany do ograniczania replikacji danych do kraju lub regionu w celu spełnienia wymagań dotyczących zarządzania danymi.

Na poniższym diagramie pokazano, jak dane są replikowane w strefach dostępności w regionie podstawowym przy użyciu ZRS:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Diagram przedstawiający sposób replikowania danych w regionie podstawowym przy użyciu ZRS":::

ZRS zapewnia doskonałą wydajność, małe opóźnienia i odporność na dane, jeśli staną się tymczasowo niedostępne. Niemniej jednak ZRS może nie chronić danych przed regionalną awarią, w której mają stałe wpływ na wiele stref. Aby chronić przed awariami regionalnymi, firma Microsoft zaleca używanie [magazynu geograficznie nadmiarowego](#geo-zone-redundant-storage) (GZRS), który używa ZRS w regionie podstawowym, a także geograficznie replikuje dane do regionu pomocniczego.

W poniższej tabeli przedstawiono typy kont magazynu obsługujące ZRS, w których regiony:

| Typ konta magazynu | Obsługiwane regiony | Obsługiwane usługi |
|--|--|--|
| Ogólnego przeznaczenia<sup>w wersji 2</sup> | Azja Południowo-Wschodnia<br /> Australia Wschodnia<br /> Europa Północna<br />  Europa Zachodnia<br /> Francja Środkowa<br /> Japan East<br /> Północna Republika Południowej Afryki<br /> Południowe Zjednoczone Królestwo<br /> Środkowe stany USA<br /> Wschodnie stany USA<br /> Wschodnie stany USA 2<br /> Zachodnie stany USA 2 | Blokowe obiekty blob<br /> Stronicowe obiekty blob<sup>2</sup><br /> Udziały plików (wersja standardowa)<br /> Tabele<br /> Kolejki<br /> |
| BlockBlobStorage<sup>1</sup> | Azja Południowo-Wschodnia<br /> Australia Wschodnia<br /> Europa Północna<br /> Europa Zachodnia<br /> Japan East<br /> Wschodnie stany USA <br /> Wschodnie stany USA 2 <br /> Zachodnie stany USA 2| Tylko blokowe obiekty blob w warstwie Premium |
| FileStorage | Azja Południowo-Wschodnia<br /> Australia Wschodnia<br /> Europa Północna<br /> Europa Zachodnia<br /> Japan East<br /> Wschodnie stany USA <br /> Wschodnie stany USA 2 <br /> Zachodnie stany USA 2 | Tylko udziały plików Premium |

<sup>1</sup> warstwa archiwum nie jest obecnie obsługiwana dla kont ZRS.<br />
<sup>2</sup> konta magazynu zawierające dyski zarządzane przez platformę Azure dla maszyn wirtualnych zawsze używają LRS. W przypadku dysków niezarządzanych platformy Azure należy również użyć LRS. Istnieje możliwość utworzenia konta magazynu dla dysków niezarządzanych platformy Azure korzystających z GRS, ale nie jest to zalecane ze względu na potencjalne problemy ze spójnością w przypadku asynchronicznej replikacji geograficznej. Żadne dyski zarządzane ani niezarządzane nie obsługują ZRS ani GZRS. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Cennik usługi Azure Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

Aby uzyskać informacje o tym, które regiony obsługują ZRS, zobacz temat **Obsługa usług według regionów**  w obszarze [co to jest strefy dostępności platformy Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Nadmiarowość w regionie pomocniczym

W przypadku aplikacji wymagających wysokiej dostępności można wybrać opcję kopiowania danych z konta magazynu do regionu pomocniczego, który ma setki kilometrów od regionu podstawowego. Jeśli konto magazynu jest kopiowane do regionu pomocniczego, dane są trwałe nawet w przypadku pełnej awarii regionalnej lub awarii, w której region podstawowy nie jest możliwy do odzyskania.

Podczas tworzenia konta magazynu należy wybrać region podstawowy dla konta. Sparowany region pomocniczy jest określany na podstawie regionu podstawowego i nie można go zmienić. Aby uzyskać więcej informacji na temat regionów obsługiwanych przez platformę Azure, zobacz [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).

Usługa Azure Storage oferuje dwie opcje kopiowania danych do regionu pomocniczego:

- **Magazyn geograficznie nadmiarowy (GRS)** kopiuje dane synchronicznie trzy razy w ramach jednej lokalizacji fizycznej w regionie podstawowym przy użyciu LRS. Następnie dane są kopiowane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym.
- **Magazyn Geograficznie nadmiarowy (GZRS)** kopiuje dane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym przy użyciu ZRS. Następnie dane są kopiowane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym.

Podstawowa różnica między GRS i GZRS polega na tym, jak dane są replikowane w regionie podstawowym. W regionie pomocniczym dane są zawsze replikowane synchronicznie trzy razy przy użyciu LRS. LRS w regionie pomocniczym chroni dane przed awariami sprzętowymi.

W przypadku GRS lub GZRS dane w regionie pomocniczym nie są dostępne do odczytu lub zapisu, chyba że istnieje przejście w tryb failover do regionu pomocniczego. Aby uzyskać dostęp do odczytu do regionu pomocniczego, skonfiguruj konto magazynu tak, aby korzystało z magazynu geograficznie nadmiarowego do odczytu (RA-GRS) lub strefy geograficznej do odczytu nadmiarowego (RA-GZRS). Aby uzyskać więcej informacji, zobacz [Odczyt dostępu do danych w regionie pomocniczym](#read-access-to-data-in-the-secondary-region).

Jeśli region podstawowy stanie się niedostępny, możesz wybrać opcję przełączenia w tryb failover do regionu pomocniczego. Po zakończeniu pracy w trybie failover region pomocniczy stanie się regionem podstawowym i można ponownie odczytać i zapisać dane. Aby uzyskać więcej informacji na temat odzyskiwania po awarii i dowiedzieć się, jak przejść do trybu failover w regionie pomocniczym, zobacz [odzyskiwanie awaryjne i konto magazynu](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Ponieważ dane są replikowane do regionu pomocniczego asynchronicznie, awaria wpływająca na region podstawowy może spowodować utratę danych, jeśli nie można odzyskać regionu podstawowego. Interwał między ostatnimi zapisami w regionie podstawowym a ostatnim zapisem w regionie pomocniczym jest znany jako cel punktu odzyskiwania (RPO). Cel punktu odzyskiwania wskazuje punkt w czasie, do którego można odzyskać dane. Usługa Azure Storage zazwyczaj ma cel punktu odzyskiwania krótszy niż 15 minut, chociaż obecnie nie ma umowy SLA dotyczącej czasu trwania replikacji danych do regionu pomocniczego.

### <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

Magazyn geograficznie nadmiarowy (GRS) kopiuje dane synchronicznie trzy razy w ramach jednej lokalizacji fizycznej w regionie podstawowym przy użyciu LRS. Następnie dane są kopiowane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym, który znajduje się setki kilometrów od regionu podstawowego. GRS oferuje trwałość dla obiektów danych usługi Azure Storage, co najmniej 99.99999999999999% (16 9) w danym roku.

Operacja zapisu jest najpierw zatwierdzana do lokalizacji podstawowej i replikowana przy użyciu LRS. Aktualizacja jest następnie replikowana asynchronicznie do regionu pomocniczego. Gdy dane są zapisywane w lokalizacji dodatkowej, są również replikowane w tej lokalizacji przy użyciu LRS.

Na poniższym diagramie pokazano, jak dane są replikowane przy użyciu GRS lub RA-GRS:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Diagram przedstawiający sposób replikowania danych za pomocą GRS lub RA-GRS":::

### <a name="geo-zone-redundant-storage"></a>Magazyn geograficznie i strefowo nadmiarowy

Magazyn strefy Geograficznie nadmiarowy (GZRS) łączy wysoką dostępność zapewnianą przez nadmiarowość w strefach dostępności z ochroną od regionalnych awarii zapewnianą przez replikację geograficzną. Dane na koncie magazynu GZRS są kopiowane w trzech [strefach dostępności platformy Azure](../../availability-zones/az-overview.md) w regionie podstawowym, a także replikowane do pomocniczego regionu geograficznego na potrzeby ochrony przed awariami regionalnymi. Firma Microsoft zaleca używanie GZRS dla aplikacji wymagających maksymalnej spójności, trwałości i dostępności, doskonałej wydajności i odporności na odzyskiwanie po awarii.

Za pomocą konta magazynu GZRS można nadal odczytywać i zapisywać dane, jeśli strefa dostępności stanie się niedostępna lub nie można jej odzyskać. Ponadto dane są również trwałe w przypadku kompletnej awarii regionalnej lub awarii, w której region podstawowy nie jest możliwy do odzyskania. GZRS zaprojektowano w celu udostępnienia co najmniej 99.99999999999999% (16 9) trwałości obiektów w danym roku.

Na poniższym diagramie pokazano, jak dane są replikowane przy użyciu GZRS lub RA-GZRS:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Diagram przedstawiający sposób replikowania danych za pomocą GZRS lub RA-GZRS":::

Tylko konta magazynu ogólnego przeznaczenia w wersji 2 obsługują GZRS i RA-GZRS. Aby uzyskać więcej informacji dotyczących typów kont magazynu, zobacz temat [Przegląd konta usługi Azure Storage](storage-account-overview.md). GZRS i RA-GZRS obsługują blokowe obiekty blob, stronicowe obiekty blob (z wyjątkiem dysków VHD), pliki, tabele i kolejki.

GZRS i RA-GZRS są obsługiwane w następujących regionach:

- Azja Południowo-Wschodnia
- Europa Północna
- Europa Zachodnia
- Japan East
- Południowe Zjednoczone Królestwo
- Środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnie stany USA 2

Aby uzyskać informacje na temat cen, zobacz szczegóły cennika [obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs), [plików](https://azure.microsoft.com/pricing/details/storage/files/), [kolejek](https://azure.microsoft.com/pricing/details/storage/queues/)i [tabel](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="read-access-to-data-in-the-secondary-region"></a>Dostęp do odczytu do danych w regionie pomocniczym

Magazyn Geograficznie nadmiarowy (z GRS lub GZRS) replikuje dane do innej lokalizacji fizycznej w regionie pomocniczym, aby zapewnić ochronę przed awarią regionalną. Jednak te dane są dostępne do odczytu tylko wtedy, gdy klient lub firma Microsoft zainicjuje przejście w tryb failover z regionu podstawowego do pomocniczego. Po włączeniu dostępu do odczytu do regionu pomocniczego dane są dostępne do odczytu przez cały czas, łącznie z sytuacją, w której region podstawowy stał się niedostępny. Aby uzyskać dostęp do odczytu do regionu pomocniczego, Włącz magazyn Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS) lub strefę geograficzną z dostępem do odczytu (RA-GZRS).

> [!NOTE]
> Azure Files nie obsługuje magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i geograficznie nadmiarowego do odczytu magazynu (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Projektowanie aplikacji na potrzeby dostępu do odczytu do pomocniczego

Jeśli konto magazynu jest skonfigurowane pod kątem dostępu do odczytu do regionu pomocniczego, można zaprojektować aplikacje w celu bezproblemowego przesunięcia danych z regionu pomocniczego, jeśli region podstawowy stanie się niedostępny z jakiegokolwiek powodu. 

Region pomocniczy jest dostępny do odczytu po włączeniu usługi RA-GRS lub RA-GZRS, dzięki czemu można testować aplikację z wyprzedzeniem, aby upewnić się, że zostanie prawidłowo odczytana z poziomu pomocniczego w przypadku awarii. Aby uzyskać więcej informacji o sposobach projektowania aplikacji pod kątem wysokiej dostępności, zobacz [Korzystanie z nadmiarowości geograficznej w celu projektowania aplikacji o dużym dostępności](geo-redundant-design.md).

Gdy dostęp do odczytu jest włączony, aplikacja może zostać odczytana z pomocniczego punktu końcowego, a także z podstawowego punktu końcowego. Pomocniczy punkt końcowy dołącza sufiks *— pomocniczy* do nazwy konta. Na przykład, jeśli podstawowy punkt końcowy usługi BLOB Storage ma wartość `myaccount.blob.core.windows.net` , pomocniczy punkt końcowy to `myaccount-secondary.blob.core.windows.net` . Klucze dostępu do konta magazynu są takie same dla podstawowych i pomocniczych punktów końcowych.

### <a name="check-the-last-sync-time-property"></a>Sprawdzanie właściwości czasu ostatniej synchronizacji

Ponieważ dane są replikowane do regionu pomocniczego asynchronicznie, region pomocniczy jest często za regionem podstawowym. Jeśli wystąpi awaria w regionie podstawowym, prawdopodobnie wszystkie zapisy w podstawowym nie zostaną jeszcze zreplikowane do pomocniczej bazy danych.

Aby określić, które operacje zapisu zostały zreplikowane do regionu pomocniczego, aplikacja może sprawdzić Właściwość **godzina ostatniej synchronizacji** dla konta magazynu. Wszystkie operacje zapisu zapisane w regionie podstawowym przed upływem czasu ostatniej synchronizacji zostały pomyślnie zreplikowane do regionu pomocniczego, co oznacza, że są dostępne do odczytu z pomocniczego. Wszystkie operacje zapisu zapisane w regionie podstawowym po ostatniej synchronizacji mogły lub nie zostały zreplikowane do regionu pomocniczego, co oznacza, że mogą nie być dostępne dla operacji odczytu.

Można badać wartość **ostatniej właściwości czasu synchronizacji** przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub jednej z bibliotek klienta usługi Azure Storage. Właściwość **czas ostatniej synchronizacji** jest wartością daty/godziny GMT. Aby uzyskać więcej informacji, zobacz [Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Podsumowanie opcji nadmiarowości

W tabelach w poniższych sekcjach opisano opcje nadmiarowości dostępne dla usługi Azure Storage

### <a name="durability-and-availability-parameters"></a>Parametry trwałości i dostępności

W poniższej tabeli opisano parametry klucza dla każdej opcji nadmiarowości:

| Parametr | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Procent trwałości obiektów w danym roku | co najmniej 99,999999999% (11 9) | co najmniej 99,9999999999% (12 9) | co najmniej 99.99999999999999% (16 9) | co najmniej 99.99999999999999% (16 9) |
| Dostępność żądań odczytu | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) dla GRS<br /><br />Co najmniej 99,99% (99,9% dla warstwy dostępu chłodnego) dla usługi RA-GRS | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) dla GZRS<br /><br />Co najmniej 99,99% (99,9% dla warstwy dostępu chłodnego) dla usługi RA-GZRS |
| Dostępność żądań zapisu | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) | Co najmniej 99,9% (99% dla warstwy dostępu chłodnego) |
| Liczba kopii danych przechowywanych w osobnych węzłach | Trzy kopie w jednym regionie | Trzy kopie w różnych strefach dostępności w jednym regionie | Łącznie sześć kopii, w tym trzy w regionie podstawowym i trzy w regionie pomocniczym | Łącznie sześć kopii, w tym trzy w osobnych strefach dostępności w regionie podstawowym oraz trzy lokalnie nadmiarowe kopie w regionie pomocniczym |

### <a name="durability-and-availability-by-outage-scenario"></a>Scenariusz trwałości i dostępności według scenariusza przestojów

Poniższa tabela wskazuje, czy dane są trwałe i dostępne w danym scenariuszu, w zależności od tego, który typ nadmiarowości obowiązuje dla Twojego konta magazynu:

| Scenariusz przestoju | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Węzeł w centrum danych jest niedostępny | Tak | Tak | Tak | Tak |
| Całe centrum danych (zona lub non-Zona) staną się niedostępne | Nie | Tak | Tak<sup>1</sup> | Tak |
| Awaria całego regionu występuje w regionie podstawowym | Nie | Nie | Tak<sup>1</sup> | Tak<sup>1</sup> |
| Dostęp do odczytu do regionu pomocniczego jest dostępny, jeśli region podstawowy stał się niedostępny | Nie | Nie | Tak (z RA-GRS) | Tak (z RA-GZRS) |

<sup>1</sup> przełączenie w tryb failover jest wymagane do przywrócenia dostępności zapisu, jeśli region podstawowy stał się niedostępny. Aby uzyskać więcej informacji, zobacz [odzyskiwanie po awarii i konto magazynu w trybie failover](storage-disaster-recovery-guidance.md).

### <a name="supported-storage-account-types"></a>Obsługiwane typy kont magazynu

W poniższej tabeli przedstawiono, które opcje nadmiarowości są obsługiwane przez każdy typ konta magazynu. Aby uzyskać informacje na temat typów kont magazynu, zobacz [Omówienie konta magazynu](storage-account-overview.md).

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Ogólnego przeznaczenia, wersja 2<br /> Ogólnego przeznaczenia, wersja 1<br /> Blokuj Magazyn obiektów BLOB<br /> Blob Storage<br /> File Storage | Ogólnego przeznaczenia, wersja 2<br /> Blokuj Magazyn obiektów BLOB<br /> File Storage | Ogólnego przeznaczenia, wersja 2<br /> Ogólnego przeznaczenia, wersja 1<br /> Blob Storage | Ogólnego przeznaczenia, wersja 2 |

Wszystkie dane dla wszystkich kont magazynu są kopiowane zgodnie z opcją nadmiarowości dla konta magazynu. Są kopiowane obiekty, w tym blokowych obiektów blob, dołączanie obiektów blob, stronicowych obiektów blob, kolejek, tabel i plików. Kopiowane są dane we wszystkich warstwach, w tym warstwa archiwum. Aby uzyskać więcej informacji na temat warstw obiektów blob, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](../blobs/storage-blob-storage-tiers.md).

Aby uzyskać informacje o cenach dla każdej opcji nadmiarowości, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Usługa Azure Premium Disk Storage obecnie obsługuje tylko Magazyn lokalnie nadmiarowy (LRS). Blokowe konta magazynu obiektów BLOB obsługują Magazyn lokalnie nadmiarowy (LRS) i magazyn strefowo nadmiarowy (ZRS) w określonych regionach.

## <a name="data-integrity"></a>Integralność danych

Usługa Azure Storage regularnie weryfikuje integralność danych przechowywanych przy użyciu cyklicznych testów nadmiarowości (CRCs). Jeśli wykryto uszkodzenie danych, zostanie ono naprawione przy użyciu nadmiarowych danych. Usługa Azure Storage oblicza również sumy kontrolne dla całego ruchu sieciowego w celu wykrycia uszkodzenia pakietów danych podczas przechowywania lub pobierania danych.

## <a name="see-also"></a>Zobacz też

- [Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md)
- [Zmiana opcji nadmiarowości dla konta magazynu](redundancy-migration.md)
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu nadmiarowości geograficznej](geo-redundant-design.md)
- [Odzyskiwanie po awarii i tryb failover konta magazynu](storage-disaster-recovery-guidance.md)
