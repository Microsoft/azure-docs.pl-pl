---
title: Informacje na temat rozliczeń Azure Files | Microsoft Docs
description: Dowiedz się, jak interpretować udostępniane modele rozliczeń i płatności zgodnie z rzeczywistym użyciem dla udziałów plików platformy Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bb608492327baae958c32be05d8f2a1bb4dbfbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226645"
---
# <a name="understand-azure-files-billing"></a>Informacje o rozliczeniach Azure Files
Azure Files oferuje dwa odrębne modele rozliczeń: obsługiwane i płatność zgodnie z rzeczywistym użyciem. Model aprowizacji jest dostępny tylko dla udziałów plików w warstwie Premium, które są udziałami plików wdrożonymi w rodzaju konta magazynu **FileStorage** . Model płatność zgodnie z rzeczywistym użyciem jest dostępny tylko dla standardowych udziałów plików, czyli udziałów plików wdrożonych w ramach konta magazynu **ogólnego przeznaczenia w wersji 2 (GPv2)** . W tym artykule wyjaśniono, jak działają oba modele, aby pomóc Ci zrozumieć miesięczne Azure Files.

Aby uzyskać informacje o cenach Azure Files, zobacz [stronę cennika Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Jednostki magazynu    
Azure Files używa jednostek miary z podstawową 2, aby reprezentować pojemność magazynu: KiB, MiB, GiB i TiB. System operacyjny może lub nie może korzystać z tej samej jednostki pomiaru lub systemu liczenia.

### <a name="windows"></a>Windows

Zarówno system operacyjny Windows, jak i Azure Files mierzą pojemność magazynu przy użyciu systemu inwentaryzacji 2, ale istnieje różnica w przypadku etykietowania jednostek. Azure Files etykiety pojemności magazynu z podstawową jednostką miary, podczas gdy system Windows oznacza, że pojemność magazynu jest podzielna na jednostki miary w podstawowej części 10. Podczas raportowania pojemności magazynu system Windows nie konwertuje pojemności magazynu z Base-2 do Base-10.

|Wytycznym dotyczącym skrótowców  |Definicja  |Jednostka  |System Windows wyświetla jako  |
|---------|---------|---------|---------|
|KiB     |1 024 bajtów         |kibibyte         |KB (w kilobajtach)         |
|-     |1 024 KiB (1 048 576 bajtów)         |mebibyte         |MB (MB)         |
|GiB     |1024 MiB (1 073 741 824 bajtów)         |nazywana gigabajtem i         |GB (GB)         |
|TiB     |1024 GiB (1 099 511 627 776 bajtów)         |tebibyte         |TB (terabajt)         |

### <a name="macos"></a>macOS

Zobacz, [jak system iOS i macOS pojemność magazynu raportów](https://support.apple.com/HT201402) w witrynie sieci Web firmy Apple, aby określić, który z nich jest używany.

### <a name="linux"></a>Linux

Każdy system operacyjny lub pojedyncze oprogramowanie może używać innego systemu zliczania. Zapoznaj się z dokumentacją, aby określić sposób zgłaszania pojemności magazynu.

## <a name="provisioned-model"></a>Model aprowizacji
Azure Files korzysta z modelu aprowizacji dla udziałów plików w warstwie Premium. W modelu biznesowym z obsługą administracyjną można aktywnie określić usługi Azure Files, jakie są wymagania dotyczące magazynu, a nie na podstawie ich użycia. Jest to podobne do kupowania sprzętu lokalnie, w tym przypadku gdy udostępniasz udział plików platformy Azure z określoną ilością pamięci, płacisz za ten magazyn, bez względu na to, czy używasz tego magazynu, czy nie, tak jak w przypadku, gdy zaczniesz korzystać z przestrzeni dyskowej w środowisku lokalnym. W przeciwieństwie do kupowania multimediów fizycznych lokalnie udostępnione udziały plików można dynamicznie skalować w górę lub w dół w zależności od charakterystyki magazynu i wydajności operacji we/wy.

Gdy udostępniasz udział plików w warstwie Premium, możesz określić liczbę GiBs wymaganych do obciążenia. Każdy GiB, który zastrzega sobie, uprawnia do dodatkowych IOPS i przepływności na stałym wskaźniku. Oprócz operacji wejścia/wyjścia linii bazowej, dla których użytkownik jest gwarantowany, każdy udział plików w warstwie Premium obsługuje maksymalnie duże nakłady pracy. Formuły dla operacji we/wy i przepływności są następujące:

- Liczba operacji we/wy dla linii bazowej = 400 + 1 * zainicjowana GiB. (Maksymalnie 100 000 operacji we/wy).
- Limit serii = MAX (4000, 3 * operacji wejścia/wyjścia na sekundę).
- Szybkość ruchu wychodzącego = 60 MiB/s + 0,06 * aprowizacji GiB.
- Transfer danych przychodzących = 40 MiB/s + 0,04 * aprowizacji GiB.

Udostępniony rozmiar udziału plików można zwiększyć w dowolnym momencie, ale może zostać zmniejszony dopiero po 24 godzinach od momentu ostatniego wzrostu. Po odczekaniu przez 24 godziny bez zwiększenia limitu przydziału można zmniejszyć przydział udziału dowolną liczbę razy, aż do momentu jego zwiększenia. Zmiany skali operacji we/wy na sekundę będą obowiązywać w ciągu kilku minut od zmiany rozmiaru aprowizacji.

Istnieje możliwość zmniejszenia rozmiaru udziału przystosowanego poniżej użytych GiB. Jeśli to zrobisz, nie utracisz danych, ale nadal będą naliczane opłaty za używany rozmiar i zostanie uzyskana wydajność (liczba operacji wejścia/wyjścia na sekundę, przepływność i liczba IOPS dla operacji wejścia/wyjścia na sekundę) udziału, który nie jest używany.

W poniższej tabeli przedstawiono kilka przykładów tych wzorów dla rozmiarów udostępnianych udziałów:

|Pojemność (GiB) | Liczba IOPS linii bazowej | Operacje we/wy na sekundę | Ruch wychodzący (MiB/s) | Ruch przychodzący (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Do 4 000     | 66   | 44   |
|500         | 900     | Do 4 000  | 90   | 60   |
|1 024       | 1 424   | Do 4 000   | 122   | 81   |
|5 120       | 5 520   | Do 15 360  | 368   | 245   |
|10 240      | 10 640  | Do 30 720  | 675   | 450   |
|33 792      | 34 192  | Do 100 000 | 2 088 | 1 392   |
|51 200      | 51 600  | Do 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Do 100 000 | 6 204 | 4 136   |

Efektywna wydajność udziałów plików zależy od ograniczeń sieci maszynowych, dostępnej przepustowości sieci, rozmiarów operacji we/wy, równoległości, między wieloma innymi czynnikami. Na przykład w oparciu o testowanie wewnętrzne z 8 KiB rozmiaru operacji we/wy odczytu/zapisu, Pojedyncza maszyna wirtualna z systemem Windows bez włączonego tworzenia wielokanałowości SMB, *standardowa F16s_v2*, połączona z udziałem plików w warstwie Premium za pośrednictwem protokołu SMB może osiągnąć 20 000 odczyt operacji wejścia/wyjścia na sekundę i 10 tys. W przypadku rozmiaru operacji we/wy odczytu i zapisu na 512 MiB ta sama maszyna wirtualna może osiągnąć 1,1 GiB/s ruch wychodzący i 370 przepływność transferu danych (MiB/s). Ten sam Klient może osiągnąć do \~ 3. wydajności, jeśli włączono funkcję SMB wielokanałowe w udziałach w warstwie Premium. Aby osiągnąć maksymalną skalę wydajności, [Włącz Wielokanałowość SMB](storage-files-enable-smb-multichannel.md) i rozłożenie obciążenia na wiele maszyn wirtualnych. W przypadku niektórych typowych problemów z wydajnością i obejść zapoznaj się z przewodnikiem dotyczącym wydajności i [rozwiązywania problemów](storage-troubleshooting-files-performance.md) w protokole [SMB](storage-files-smb-multichannel-performance.md)

### <a name="bursting"></a>Rozszerzanie możliwości
Jeśli obciążenie wymaga dodatkowej wydajności w celu zaspokojenia szczytowego zapotrzebowania, udział może korzystać z punktów końcowych w celu przekroczenia limitu liczby operacji wejścia/wyjścia na sekundę, aby zaoferować wydajność udziału potrzebną do osiągnięcia zapotrzebowania. Udziały plików w warstwie Premium mogą przekroczyć liczbę operacji we/wy do 4 000 lub maksymalnie na dwa czynniki, w zależności od tego, która wartość jest wyższa. Rozliczanie jest zautomatyzowane i działa na podstawie systemu kredytowego. Seria zadań działa w oparciu o najlepszy nakład pracy, a limit graniczny nie jest gwarancją, udziały plików mogą przekroczyć limit *przez maksymalnie 60* minut.

Kredyty są gromadzone w zasobniku, gdy ruch dla udziału plików jest poniżej liczby operacji wejścia/wyjścia na sekundę. Na przykład udział GiB 100 ma 500 liczby operacji wejścia/wyjścia na sekundę. Jeśli rzeczywisty ruch w udziale był 100 operacji we/wy dla określonego interwału 1 sekund, wówczas liczba nieużywanych operacji 400 we/wy na sekundę jest księgowana w zasobniku serii. Podobnie udział TiB bezczynny, naliczy kredyt na 1 424 operacji we/wy na sekundę. Te kredyty zostaną następnie użyte później, gdy operacje przekroczą liczbę IOPS linii bazowej.

Za każdym razem, gdy udział przekracza liczbę operacji we/wy na sekundę i ma kredyty w zasobniku szeregowym, zostanie osiągnięty limit maksymalnej dozwolonej szczytowej szybkości szeregowej. Udziały mogą w dalszym ciągu przekroczyć czas trwania wszystkich kredytów, maksymalnie 60 minut, ale jest to uzależnione od liczby naliczanych kredytów na korzystanie z serii. Wszystkie operacje we/wy poza linią bazową zużywają jedno środki, a po wykorzystaniu wszystkich kredytów, udział zwróci wartość operacji we/wy na sekundę.

Kredyty na udostępnianie mają trzy stany:

- Naliczanie, gdy udział plików używa mniejszej niż liczba operacji we/wy na sekundę.
- Odrzucanie, gdy udział plików używa więcej niż operacji we/wy na sekundę i w trybie rozruchowym.
- Stała, gdy udział plików używa dokładnie liczby operacji we/wy na sekundę, nie są naliczane ani wykorzystane środki.

Nowe udziały plików zaczynają się od pełnej liczby kredytów w swoim zasobniku. Środki na korzystanie z serii nie zostaną naliczone, jeśli liczba operacji we/wy udziałów spadnie poniżej liczby operacji wejścia/wyjścia na sekundę z powodu ograniczenia przez serwer.

## <a name="pay-as-you-go-model"></a>Model płatność zgodnie z rzeczywistym użyciem
W przypadku standardowych udziałów plików Azure Files jest stosowany model biznesowy z opcją płatność zgodnie z rzeczywistym użyciem. W modelu biznesowym z opcją płatność zgodnie z rzeczywistym użyciem kwota płatna jest określana na podstawie ilości faktycznie używanej zamiast zainicjowanej. Na wysokim poziomie opłaty są nanoszone za ilość danych przechowywanych na dysku, a następnie dodatkowy zestaw transakcji oparty na użyciu tych danych. Model płatność zgodnie z rzeczywistym użyciem może być opłacalny, ponieważ nie jest konieczne przeprowadzenie nadmiernej aprowizacji w celu uwzględnienia w przyszłości wymagań dotyczących wzrostu lub wydajności lub anulowania aprowizacji, jeśli obciążenie jest różne w czasie. Z drugiej strony model płatności zgodnie z rzeczywistym użyciem może być trudny do zaplanowania w ramach procesu tworzenia budżetu, ponieważ model rozliczeń z płatnością zgodnie z rzeczywistym użyciem jest oparty na zużyciu przez użytkownika końcowego.

### <a name="differences-in-standard-tiers"></a>Różnice w warstwach standardowych
Podczas tworzenia standardowego udziału plików można wybierać między warstwami zoptymalizowanymi, gorącą i chłodną. Wszystkie trzy warstwy są przechowywane na dokładnie tym samym standardowym sprzęcie pamięci masowej. Główną różnicą między tymi trzema warstwami są ceny za magazyn danych w czasie spoczynku, które są niższe w warstwach usługi chłodziark i ceny transakcji, które są wyższe w warstwach chłodnicy. Składają się na to następujące elementy:

- Transakcja zoptymalizowana pod względem nazwy wskazuje, że optymalizuje ceny za duże obciążenia transakcji. Transakcja jest zoptymalizowana pod kątem najwyższej ceny za magazyn w spoczynku, ale najniższych cen transakcji.
- Gorąca jest dla aktywnych obciążeń, które nie obejmują dużej liczby transakcji, i ma nieco niższe ceny za magazyn w spoczynku, ale znacznie wyższe ceny transakcji w porównaniu z optymalizacją transakcji. Należy traktować go jako środek środkowy między warstwami zoptymalizowanymi pod kątem transakcji i chłodnymi.
- Chłodna optymalizuje ceny dla obciążeń, które nie mają dużo aktywności, oferując najniższą cenę za przechowywanie danych w spoczynku, ale najwyższe ceny transakcji.

Jeśli wyłączysz rzadko używane obciążenia w warstwie zoptymalizowanej pod kątem transakcji, będziesz płacić niemal nie tylko przez kilka razy w miesiącu, w którym będziesz wprowadzać transakcje względem Twojego udziału, ale będziesz płacić za duże ilości danych. Jeśli przeniesiesz ten sam udział do warstwy chłodna, nadal płacisz za koszty transakcji, wystarczy, że bardzo rzadko trwają transakcje dla tego obciążenia, ale warstwa chłodna ma znacznie tańszą cenę magazynu danych. Wybranie odpowiedniej warstwy dla przypadku użycia umożliwia znaczne zmniejszenie kosztów. Wybranie odpowiedniej warstwy dla przypadku użycia umożliwia znaczne zmniejszenie kosztów.

Podobnie, jeśli umieścisz obciążenie o wysokiej dostępności w warstwie chłodna, będziesz płacić dużo więcej kosztów transakcji, ale nie tylko za koszty magazynowania danych. Może to prowadzić do sytuacji, w której zwiększone koszty z cen transakcji zwiększają się znacznie oszczędności wynikające z obniżonej ceny za magazyn danych, co prowadzi do płacenia pieniędzy w chłodnym czasie niż w przypadku optymalizacji transakcji. W przypadku niektórych poziomów użycia, gdy warstwa gorąca będzie najbardziej opłacalną warstwą, warstwa chłodna będzie tańsza niż Optymalizacja transakcji.

Twoje obciążenie i poziom aktywności określają najbardziej opłacalną warstwę dla standardowego udziału plików. W przypadku najlepszym sposobem na wybranie najbardziej opłacalnej warstwy jest poszukiwanie rzeczywistego zużycia zasobów udziału (dane przechowywane, transakcje zapisu itd.).

### <a name="what-are-transactions"></a>Co to są transakcje?
Transakcje są operacjami lub żądaniami względem Azure Files w celu przekazywania, pobierania lub manipulowania zawartością udziału plików. Każda Akcja podejmowana na udziale plików jest przetłumaczyć na co najmniej jedną transakcję i w standardowych udziałach korzystających z modelu rozliczania płatności zgodnie z rzeczywistym użyciem, który tłumaczy koszty transakcji.

Istnieje pięć podstawowych kategorii transakcji: Write, list, Read, Other i DELETE. Wszystkie operacje wykonywane za pośrednictwem interfejsu API REST lub SMB są przełączone do jednej z czterech kategorii w następujący sposób:

| Typ operacji | Transakcje zapisu | Wyświetl listę transakcji | Odczytaj transakcje | Inne transakcje | Usuń transakcje |
|-|-|-|-|-|-|
| Operacje zarządzania | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Operacje na danych | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> System plików NFS 4,1 jest dostępny tylko dla udziałów plików w warstwie Premium, które korzystają z modelu rozliczania z zainicjowaną obsługą, transakcje nie wpływają na rozliczenia udziałów plików w warstwie Premium.

## <a name="see-also"></a>Zobacz też
- [Strona cennika Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).
- [Planowanie wdrożenia Azure Files](./storage-files-planning.md) i [Planowanie wdrożenia Azure File Sync](./storage-sync-files-planning.md).
- [Utwórz udział plików](./storage-how-to-create-file-share.md) i [Wdróż Azure File Sync](./storage-sync-files-deployment-guide.md).