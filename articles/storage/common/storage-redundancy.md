---
title: Nadmiarowość danych
titleSuffix: Azure Storage
description: Dane na koncie usługi Microsoft Azure Storage są replikowane pod kątem trwałości i wysokiej dostępności. Konfiguracje nadmiarowości obejmują magazynowanie lokalne nadmiarowe (LRS), magazyn strefowy (ZRS), magazyn geograficznie nadmiarowy (GRS), magazyn geograficzny dostępu do odczytu (RA-GRS), magazyn geograficzny nadmiarowy (GZRS) (wersja zapoznawcza) i magazyn geograficzny dostępu do strefy odczytu (RA-GZRS) (wersja zapoznawcza).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 78f7c935e64276e7f4862dad966b99bff6bd246d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481939"
---
# <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage

Usługa Azure Storage zawsze przechowuje wiele kopii danych, dzięki czemu są chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętu, awariami sieci lub zasilania oraz masowymi klęskami żywiołowymi. Nadmiarowość gwarantuje, że twoje konto magazynu spełnia [umowę dotyczącą poziomu usług (SLA) dla usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) nawet w przypadku awarii.

Przy podejmowaniu decyzji, która opcja nadmiarowości jest najlepsza dla twojego scenariusza, należy wziąć pod uwagę kompromisy między niższymi kosztami a wyższą dostępnością i trwałością. Czynniki, które pomagają określić, którą opcję nadmiarowości należy wybrać, obejmują:  

- Sposób replikowania danych w regionie podstawowym
- Czy dane są replikowane do drugiej lokalizacji, która jest geograficznie odległa od regionu podstawowego, aby chronić przed katastrofami regionalnymi
- Czy aplikacja wymaga dostępu do odczytu do zreplikowanych danych w regionie pomocniczym, jeśli region podstawowy staje się niedostępny z jakiegokolwiek powodu

## <a name="redundancy-in-the-primary-region"></a>Nadmiarowość w regionie podstawowym

Dane na koncie usługi Azure Storage są zawsze replikowane trzy razy w regionie podstawowym. Usługa Azure Storage oferuje dwie opcje replikacji danych w regionie podstawowym:

- **Lokalnie nadmiarowy magazyn (LRS)** kopiuje dane synchronicznie trzy razy w jednej lokalizacji fizycznej w regionie podstawowym. LRS jest najtańszą opcją replikacji, ale nie jest zalecane dla aplikacji wymagających wysokiej dostępności.
- **Magazyn nadmiarowy strefy (ZRS)** synchronizuje dane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym. W przypadku aplikacji wymagających wysokiej dostępności firma Microsoft zaleca używanie usługi ZRS w regionie podstawowym, a także replikowanie do regionu pomocniczego.

### <a name="locally-redundant-storage"></a>Magazyn lokalnie nadmiarowy

Lokalnie nadmiarowy magazyn (LRS) replikuje dane trzy razy w jednej lokalizacji fizycznej w regionie podstawowym. LRS zapewnia co najmniej 99.9999999999% (11 dziewiątki) trwałość obiektów w danym roku.

LRS jest opcją nadmiarowości o najniższych kosztach i oferuje najmniejszą trwałość w porównaniu z innymi opcjami. LRS chroni dane przed awariami serwera rack i dysków. Jeśli jednak w centrum danych wystąpi awaria lub powódź, wszystkie repliki konta magazynu przy użyciu lrs mogą zostać utracone lub nieodwracalne. Aby ograniczyć to ryzyko, firma Microsoft zaleca użycie [magazynu nadmiarowego strefy](#zone-redundant-storage) (ZRS), [magazynu geograficznie nadmiarowego](#geo-redundant-storage) (GRS) lub [magazynu geograficznego nadmiarowego (wersja zapoznawcza)](#geo-zone-redundant-storage-preview) (GZRS).

Żądanie zapisu do konta magazynu, który używa LRS dzieje się synchronicznie. Operacja zapisu zwraca pomyślnie tylko po zapisaniu danych do wszystkich trzech replik.

LRS jest dobrym wyborem dla następujących scenariuszy:

- Jeśli aplikacja przechowuje dane, które można łatwo zrekonstruować w przypadku utraty danych, można zdecydować się na LRS.
- Jeśli aplikacja jest ograniczona do replikowania danych tylko w kraju lub regionie ze względu na wymagania dotyczące zarządzania danymi, można wybrać LRS. W niektórych przypadkach sparowane regiony, w których dane są replikowane geograficznie, mogą znajdować się w innym kraju lub regionie. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz [Regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Magazyn strefowo nadmiarowy

Magazyn nadmiarowy strefy (ZRS) synchronizuje dane usługi Azure Storage synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym. Każda strefa dostępności jest oddzielną lokalizacją fizyczną z niezależnym zasilaniem, chłodzeniem i siecią. Usługa ZRS oferuje trwałość obiektów danych usługi Azure Storage o wartości co najmniej 99,999999999999% (12 9) w danym roku.

Dzięki zurzy z usługi ZRS dane są nadal dostępne dla operacji odczytu i zapisu, nawet jeśli strefa stanie się niedostępna. Jeśli strefa stanie się niedostępna, platforma Azure podejmuje aktualizacje sieci, takie jak ponowne wskazywanie DNS. Te aktualizacje mogą mieć wpływ na aplikację, jeśli dostęp do danych przed zakończeniem aktualizacji. Podczas projektowania aplikacji dla ZRS, postępuj zgodnie z praktykami obsługi błędów przejściowych, w tym implementowanie zasad ponawiania prób z wykładniczym wycofywaniem.

Żądanie zapisu na konto magazynu, który używa ZRS dzieje się synchronicznie. Operacja zapisu zwraca pomyślnie tylko po zapisaniu danych do wszystkich replik w trzech strefach dostępności.

Firma Microsoft zaleca używanie usługi ZRS w regionie podstawowym w scenariuszach wymagających spójności, trwałości i wysokiej dostępności. Usługa ZRS zapewnia doskonałą wydajność, małe opóźnienia i odporność danych, jeśli staną się tymczasowo niedostępne. Jednak ZRS sam w sobie może nie chronić danych przed regionalną katastrofą, w której trwale wpływa wiele stref. Aby chronić przed awariami regionalnymi, firma Microsoft zaleca użycie [magazynu nadmiarowego strefy geograficznej](#geo-zone-redundant-storage-preview) (GZRS), który używa usługi ZRS w regionie podstawowym, a także replikuje dane geograficzne do regionu pomocniczego.

W poniższej tabeli przedstawiono, które typy kont magazynu obsługują zrs, w których regionach:

|    Typ konta magazynu    |    Obsługiwane regiony    |    Obsługiwane usługi    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Przeznaczenie ogólne v2<sup>1</sup>    | Azja Południowo-Wschodnia<br /> Australia Wschodnia<br /> Europa Północna<br />  Europa Zachodnia<br /> Francja Środkowa<br /> Japonia Wschodnia<br /> Republika Południowej Afryki Północ<br /> Południowe Zjednoczone Królestwo<br /> Środkowe stany USA<br /> Wschodnie stany USA<br /> Wschodnie stany USA 2<br /> Zachodnie stany USA 2    |    Blokowe obiekty blob<br /> Obiekty blob<sup>stron 2</sup><br /> Udziały plików (standardowe)<br /> Tabele<br /> Kolejki<br /> |
|    BlockBlobStorage<sup>1</sup>    | Europa Zachodnia<br /> Wschodnie stany USA    |    Blokuj tylko obiekty blob    |
|    FileStorage (FileStorage)    | Europa Zachodnia<br /> Wschodnie stany USA    |    Tylko pliki platformy Azure    |

<sup>1</sup> Warstwa archiwum nie jest obecnie obsługiwana dla kont ZRS.<br />
<sup>2</sup> Konta magazynu, które zawierają dyski zarządzane platformy Azure dla maszyn wirtualnych zawsze używają lrs. Dyski niezarządzane platformy Azure powinny również używać lrs. Istnieje możliwość utworzenia konta magazynu dla dysków niezarządzanych platformy Azure, który używa GRS, ale nie jest zalecane ze względu na potencjalne problemy ze spójnością dla replikacji geograficznej asynchronicznego. Ani dyski zarządzane, ani niezarządzane nie obsługują ZRS ani GZRS. Aby uzyskać więcej informacji na temat dysków zarządzanych, zobacz [Cennik dla dysków zarządzanych platformy Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

Aby uzyskać informacje o regionach obsługiwanych przez usługę ZRS, zobacz **Pomoc techniczna dla usług według regionów** w obszarze Jakie są strefy dostępności platformy [Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Nadmiarowość w regionie pomocniczym

W przypadku aplikacji wymagających wysokiej dostępności można dodatkowo skopiować dane na koncie magazynu do regionu pomocniczego oddalonego o setki mil od regionu podstawowego. Jeśli konto magazynu jest kopiowane do regionu pomocniczego, dane są trwałe nawet w przypadku całkowitej awarii regionalnej lub awarii, w której regionu podstawowego nie można odzyskać.

Podczas tworzenia konta magazynu należy wybrać region podstawowy dla konta. Sparowany region pomocniczy jest określany na podstawie regionu podstawowego i nie można go zmienić. Aby uzyskać więcej informacji na temat regionów obsługiwanych przez platformę Azure, zobacz [Regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).

Usługa Azure Storage oferuje dwie opcje kopiowania danych do regionu pomocniczego:

- **Magazyn geograficznie nadmiarowy (GRS)** kopiuje dane synchronicznie trzy razy w jednej lokalizacji fizycznej w regionie podstawowym przy użyciu lrs. Następnie kopiuje dane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym.
- **Magazyn nadmiarowy strefy geograficznej (GZRS)** (wersja zapoznawcza) kopiuje dane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym przy użyciu usługi ZRS. Następnie kopiuje dane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym.

Podstawową różnicą między GRS i GZRS jest sposób replikowania danych w regionie podstawowym. W lokalizacji dodatkowej dane są zawsze replikowane synchronicznie trzy razy przy użyciu lrs.

W przypadku grs lub GZRS dane w lokalizacji pomocniczej nie są dostępne dla dostępu do odczytu lub zapisu, chyba że istnieje praca awaryjna w regionie pomocniczym. Aby uzyskać dostęp do odczytu do lokalizacji dodatkowej, skonfiguruj konto magazynu do używania magazynu geograficznego dostępu do odczytu (RA-GRS) lub magazynu geograficznego dostępu do odczytu (RA-GZRS). Aby uzyskać więcej informacji, zobacz [Dostęp do odczytu danych w regionie pomocniczym](#read-access-to-data-in-the-secondary-region).

Jeśli region podstawowy stanie się niedostępny, można wybrać opcję pracy awaryjnej w regionie pomocniczym (wersja zapoznawcza). Po zakończeniu pracy awaryjnej region pomocniczy staje się regionem podstawowym i można ponownie odczytywać i zapisywać dane. Aby uzyskać więcej informacji na temat odzyskiwania po awarii i dowiedzieć się, jak awaryjnie w regionie pomocniczym, zobacz [Odzyskiwanie po awarii i tryb failover konta (wersja zapoznawcza)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Ponieważ dane są replikowane do pomocniczego regionu asynchronicznie, błąd, który wpływa na region podstawowy może spowodować utratę danych, jeśli nie można odzyskać regionu podstawowego. Interwał między ostatni zapis w regionie podstawowym i ostatni zapis w regionie pomocniczym jest znany jako cel punktu odzyskiwania (RPO). Punkt punktu wskazuje punkt w czasie, do którego dane mogą być odzyskane. Usługa Azure Storage zazwyczaj ma obiekt RPO krótszy niż 15 minut, chociaż obecnie nie ma umowy SLA na czas replikowania danych do regionu pomocniczego.

### <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

Magazyn geograficznie nadmiarowy (GRS) kopiuje dane synchronicznie trzy razy w jednej lokalizacji fizycznej w regionie podstawowym przy użyciu lrs. Następnie kopiuje dane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym, który znajduje się setki mil od regionu podstawowego. GRS oferuje trwałość dla obiektów danych usługi Azure Storage co najmniej 99.99999999999999999999% (16 9's) w ciągu danego roku.

Operacja zapisu jest najpierw zatwierdzona do lokalizacji podstawowej i replikowana przy użyciu lrs. Aktualizacja jest następnie replikowana asynchronicznie do regionu pomocniczego. Gdy dane są zapisywane w lokalizacji pomocniczej, jest również replikowane w tej lokalizacji przy użyciu LRS.

### <a name="geo-zone-redundant-storage-preview"></a>Magazyn nadmiarowy ze strefy geograficznej (wersja zapoznawcza)

Magazyn geograficznie nadmiarowy (GZRS) (wersja zapoznawcza) łączy wysoką dostępność zapewnianą przez nadmiarowość w strefach dostępności z ochroną przed regionalnymi awariami zapewnianymi przez replikację geograficzną. Dane na koncie magazynu GZRS są kopiowane przez trzy [strefy dostępności platformy Azure](../../availability-zones/az-overview.md) w regionie podstawowym i są również replikowane do pomocniczego regionu geograficznego w celu ochrony przed awariami regionalnymi. Firma Microsoft zaleca używanie GZRS dla aplikacji wymagających maksymalnej spójności, trwałości i dostępności, doskonałej wydajności i odporności na odzyskiwanie po awarii.

Za pomocą konta magazynu GZRS można kontynuować odczytywanie i zapisywanie danych, jeśli strefa dostępności stanie się niedostępna lub nie można jej nieodwracalnie odzyskiwać. Ponadto dane są również trwałe w przypadku całkowitej awarii regionalnej lub awarii, w której regionu podstawowego nie można odzyskać. GZRS został zaprojektowany, aby zapewnić co najmniej 99.9999999999999999% (16 9's) trwałość obiektów w danym roku.

Tylko konta magazynu ogólnego przeznaczenia w wersji 2 obsługują GZRS i RA-GZRS. Aby uzyskać więcej informacji dotyczących typów kont magazynu, zobacz temat [Przegląd konta usługi Azure Storage](storage-account-overview.md). GZRS i RA-GZRS obsługują blokowe obiekty blob, obiekty blob stron (z wyjątkiem dysków VHD), pliki, tabele i kolejki.

GZRS i RA-GZRS są obecnie dostępne do podglądu w następujących regionach:

- Azja Południowo-Wschodnia
- Europa Północna
- Europa Zachodnia
- Japonia Wschodnia
- Południowe Zjednoczone Królestwo
- Wschodnie stany USA
- Wschodnie stany USA 2
- Środkowe stany USA
- Zachodnie stany USA 2

Firma Microsoft nadal włącza GZRS i RA-GZRS w dodatkowych regionach platformy Azure. Regularnie sprawdzaj stronę [Aktualizacje usługi platformy Azure, aby](https://azure.microsoft.com/updates/) uzyskać informacje na temat obsługiwanych regionów.

Aby uzyskać informacje na temat cen w wersji zapoznawczej, zobacz GZRS ceny wersji zapoznawczej dla [obiektów Blobs,](https://azure.microsoft.com/pricing/details/storage/blobs) [Files](https://azure.microsoft.com/pricing/details/storage/files/), [Queues](https://azure.microsoft.com/pricing/details/storage/queues/)i [Tables](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Firma Microsoft zaleca, aby nie używać funkcji w wersji zapoznawczej dla obciążeń produkcyjnych.

## <a name="read-access-to-data-in-the-secondary-region"></a>Dostęp do odczytu danych w regionie pomocniczym

Magazyn geograficznie nadmiarowy (z GRS lub GZRS) replikuje dane do innej fizycznej lokalizacji w regionie pomocniczym w celu ochrony przed regionalnymi awariami. Jednak te dane są dostępne do odczytu tylko wtedy, gdy klient lub firma Microsoft inicjuje pracy awaryjnej z regionu podstawowego do pomocniczego. Po włączeniu dostępu do odczytu do regionu pomocniczego, dane są dostępne do odczytu, jeśli region podstawowy staje się niedostępny. Aby uzyskać dostęp do odczytu do regionu pomocniczego, włącz magazyn geograficzny dostępu do odczytu (RA-GRS) lub magazyn geograficzny dostępu do odczytu (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Projektowanie aplikacji w celu uzyskania dostępu do odczytu do

Jeśli konto magazynu jest skonfigurowany do odczytu dostępu do regionu pomocniczego, następnie można zaprojektować aplikacje, aby bezproblemowo przejść do odczytu danych z regionu pomocniczego, jeśli region podstawowy staje się niedostępny z jakiegokolwiek powodu. Region pomocniczy jest zawsze dostępny dla dostępu do odczytu, dzięki czemu można przetestować aplikację, aby upewnić się, że będzie odczytywana z pomocniczego w przypadku awarii. Aby uzyskać więcej informacji na temat projektowania aplikacji pod kątem wysokiej dostępności, zobacz [Projektowanie aplikacji o wysokiej dostępności przy użyciu pamięci masowej geograficznie nadmiarowej z dostępem do odczytu.](storage-designing-ha-apps-with-ragrs.md)

Gdy dostęp do odczytu do pomocniczego jest włączony, dane mogą być odczytywane z pomocniczego punktu końcowego, a także z podstawowego punktu końcowego dla konta magazynu. Pomocniczy punkt końcowy dołącza sufiks *-pomocniczy* do nazwy konta. Na przykład jeśli podstawowym punktem końcowym `myaccount.blob.core.windows.net`dla magazynu obiektów Blob jest , to pomocniczy punkt końcowy jest `myaccount-secondary.blob.core.windows.net`. Klucze dostępu do konta dla konta magazynu są takie same dla podstawowych i pomocniczych punktów końcowych.

### <a name="check-the-last-sync-time-property"></a>Sprawdzanie właściwości czasu ostatniej synchronizacji

Ponieważ dane są replikowane do pomocniczego regionu asynchronicznie, pomocniczy region jest często za regionu podstawowego. Jeśli wystąpi błąd w regionie podstawowym, jest prawdopodobne, że wszystkie zapisy do podstawowej nie zostały jeszcze zreplikowane do pomocniczego.

Aby ustalić, które operacje zapisu zostały zreplikowane do regionu pomocniczego, aplikacja może sprawdzić właściwość **Czas ostatniej synchronizacji** dla konta magazynu. Wszystkie operacje zapisu zapisane w regionie podstawowym przed ostatnim czasem synchronizacji zostały pomyślnie zreplikowane do regionu pomocniczego, co oznacza, że są one dostępne do odczytu z pomocniczego. Wszelkie operacje zapisu zapisane w regionie podstawowym po ostatnim czasie synchronizacji mogą lub nie zostały zreplikowane do regionu pomocniczego, co oznacza, że mogą nie być dostępne dla operacji odczytu.

Można zbadać wartość właściwości **Czas ostatniej synchronizacji** przy użyciu usługi Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub jednej z bibliotek klienta usługi Azure Storage. **Właściwość Czas ostatniej synchronizacji** jest wartością daty/godziny GMT. Aby uzyskać więcej informacji, zobacz [Sprawdzanie właściwości Ostatni czas synchronizacji dla konta magazynu](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Podsumowanie opcji nadmiarowości

W poniższej tabeli przedstawiono, jak trwałe i dostępne dane są w danym scenariuszu, w zależności od tego, jaki typ nadmiarowości obowiązuje dla konta magazynu:

| Scenariusz                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (wersja zapoznawcza)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Węzeł w centrum danych staje się niedostępny                                                                 | Tak                             | Tak                              | Tak                                  | Tak                                  |
| Całe centrum danych (strefowe lub nieokałowe) staje się niedostępne                                           | Nie                              | Tak                              | Tak                                  | Tak                                  |
| Występuje awaria w całym regionie                                                                                     | Nie                              | Nie                               | Tak                                  | Tak                                  |
| Dostęp do odczytu danych w regionie pomocniczym, jeśli region podstawowy stanie się niedostępny | Nie                              | Nie                               | Tak (z RA-GRS)                                   | Tak (z RA-GZRS)                                 |
| Procentowa trwałość obiektów w danym roku<sup>1</sup>                                          | co najmniej 99,999999999% (11 9 lat) | co najmniej 99,9999999999% (12 9 lat) | co najmniej 99,999999999999999% (16 9)at least 99.99999999999999999999% (16 9's) | co najmniej 99,999999999999999% (16 9)at least 99.99999999999999999999% (16 9's) |
| Obsługiwane konta magazynu typy<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Dostępność umowy SLA dla żądań odczytu<sup>1</sup>  | Co najmniej 99,9% (99% dla warstwy chłodnego dostępu) | Co najmniej 99,9% (99% dla warstwy chłodnego dostępu) | Co najmniej 99,9% (99% dla warstwy chłodnego dostępu) dla GRS<br /><br />Co najmniej 99,99% (99,9% dla warstwy chłodnego dostępu) dla RA-GRS | Co najmniej 99,9% (99% dla chłodnej warstwy dostępu) dla GZRS<br /><br />Co najmniej 99,99% (99,9% dla chłodnej warstwy dostępu) dla RA-GZRS |
| Dostępność umowy SLA dla żądań zapisu<sup>1</sup>  | Co najmniej 99,9% (99% dla warstwy chłodnego dostępu) | Co najmniej 99,9% (99% dla warstwy chłodnego dostępu) | Co najmniej 99,9% (99% dla warstwy chłodnego dostępu) | Co najmniej 99,9% (99% dla warstwy chłodnego dostępu) |

<sup>1</sup> Aby uzyskać informacje na temat gwarancji usługi Azure Storage dotyczących trwałości i dostępności, zobacz [umowy SLA usługi Azure Storage.](https://azure.microsoft.com/support/legal/sla/storage/)

<sup>2</sup> Aby uzyskać informacje dotyczące typów kont magazynu, zobacz [Omówienie konta magazynu](storage-account-overview.md).

Wszystkie dane dla wszystkich typów kont magazynu i [wszystkich warstw (w tym archiwum)](../blobs/storage-blob-storage-tiers.md) są kopiowane zgodnie z opcją nadmiarowości dla konta magazynu. Obiekty, w tym blokowe obiekty blob, dołączanie obiektów blob, stronicowych obiektów blob, kolejek, tabel i plików są kopiowane.

Aby uzyskać informacje o cenach dla każdej opcji nadmiarowości, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Usługa Azure Premium Disk Storage obsługuje obecnie tylko lokalnie nadmiarowy magazyn (LRS). Konta magazynu obiektów blob obsługują lokalnie nadmiarowy magazyn (LRS) i magazyn strefowy nadmiarowy (ZRS) w niektórych regionach.

## <a name="data-integrity"></a>Integralność danych

Usługa Azure Storage regularnie weryfikuje integralność danych przechowywanych przy użyciu cyklicznych kontroli nadmiarowości (CRC). Jeśli zostanie wykryte uszkodzenie danych, jest naprawiany przy użyciu nadmiarowych danych. Usługa Azure Storage oblicza również sumy kontrolne dla całego ruchu sieciowego w celu wykrycia uszkodzenia pakietów danych podczas przechowywania lub pobierania danych.

## <a name="see-also"></a>Zobacz też

- [Sprawdź właściwość Czas ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md)
- [Zmienianie opcji nadmiarowości dla konta magazynu](redundancy-migration.md)
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu pamięci masowej RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Odzyskiwanie po awarii i przewijanie awaryjne konta (wersja zapoznawcza)](storage-disaster-recovery-guidance.md)
