---
title: Informacje o Azure Files rozliczeń | Microsoft Docs
description: Dowiedz się, jak interpretować modele rozliczeń aprowizowanych i płatności zgodnie z ich płatnością dla udziałów plików platformy Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11d22fd83106bb1802514d0c7d5f67724664464d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788389"
---
# <a name="understand-azure-files-billing"></a>Informacje Azure Files rozliczeń
Azure Files dwa różne modele rozliczeń: aprowizowane i płatność zgodnie z modelem pracy. Aprowizowany model jest dostępny tylko dla udziałów plików w chmurze Premium, które są udziałami plików wdrożonych w rodzaju konta magazynu **FileStorage.** Model płatności zgodnie z rzeczywistym przeznaczenia jest dostępny tylko dla standardowych udziałów plików, które są udziałami plików wdrożonych w typie konta magazynu ogólnego przeznaczenia w wersji **2 (GPv2).** W tym artykule wyjaśniono, jak działają oba modele, aby ułatwić zrozumienie miesięcznego Azure Files rachunku.

Aby Azure Files informacje o cenach, zobacz [Azure Files cennika](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Jednostki magazynu    
Azure Files używa jednostek miary base-2 do reprezentowania pojemności magazynu: KiB, MiB, GiB i TiB. System operacyjny może lub nie może używać tej samej jednostki miary lub systemu zliczania.

### <a name="windows"></a>Windows

Zarówno system operacyjny Windows, jak i Azure Files mierzą pojemność magazynu przy użyciu systemu zliczania base-2, ale istnieje różnica w przypadku jednostek etykietowania. Azure Files pojemności magazynu jest etykietą z jednostkami miary base-2, a system Windows — pojemnością magazynu w jednostkach miary base-10. Podczas raportowania pojemności magazynu system Windows nie konwertuje pojemności magazynu z base-2 na base-10.

|Akronim  |Definicja  |Jednostka  |System Windows jest wyświetlany jako  |
|---------|---------|---------|---------|
|Kib     |1024 bajty         |kibibyte         |KB (kilobajt)         |
|Mib     |1024 KiB (1 048 576 bajtów)         |mebibajt         |MB (megabajt)         |
|Gib     |1024 MiB (1 073 741 824 bajtów)         |gibibajt         |GB (gigabajt)         |
|Tib     |1024 GiB (1 099 511 627 776 bajtów)         |tebibajt         |TB (terabajt)         |

### <a name="macos"></a>macOS

Aby [określić, który](https://support.apple.com/HT201402) system zliczania jest używany, zobacz Jak można określić pojemność magazynu raportów dla systemów iOS i macOS w witrynie internetowej firmy Apple.

### <a name="linux"></a>Linux

Każdy system operacyjny lub poszczególne oprogramowanie może używać innego systemu zliczania. Zapoznaj się z dokumentacją, aby określić sposób zgłaszania pojemności magazynu.

## <a name="provisioned-model"></a>Aprowizowany model
Azure Files aprowizowany model dla udziałów plików w premium. W aprowizowany model biznesowy proaktywnie określasz usłudze Azure Files, jakie są wymagania dotyczące magazynu, a nie na podstawie tego, czego używasz. Jest to podobne do kupowania sprzętu lokalnego, ponieważ podczas aprowizowania udziału plików platformy Azure przy użyciu określonej ilości miejsca płacisz za ten magazyn niezależnie od tego, czy go używasz, czy nie, podobnie jak nie zaczynasz ponosić kosztów nośników fizycznych w środowisku lokalnym, gdy zaczniesz korzystać z miejsca. W przeciwieństwie do zakupu nośnika fizycznego w środowisku lokalnym aprowizowane udziały plików można dynamicznie skalować w górę lub w dół w zależności od charakterystyki wydajności magazynu i we/wy.

Podczas aprowizować udział plików w wersji Premium należy określić liczbę gibów wymaganych przez obciążenie. Każdy zaaprowizowanej liczby gibów uprawnia do dodatkowej liczby IOPS i przepływności na stałym współczynniku. Oprócz podstawowej wartości IOPS, dla której gwarantowane jest, każdy udział plików w stosunku do wersji Premium obsługuje rozszerzenie na podstawie najlepszych nakładów pracy. Formuły dla IOPS i przepływności są następujące:

- Bazowa liczba IOPS = 400 + 1 * aprowizowana GiB. (maksymalnie 100 000 IOPS).
- Limit serii = MAX(4000, 3 * bazowa liczba IOPS).
- Szybkość wychodząca = 60 MiB/s + 0,06 * aprowizowana GiB.
- Szybkość wejdu = 40 MiB/s + 0,04 * aprowizowana GiB.

Zaaprowizowany rozmiar udziału plików można zwiększyć w dowolnym momencie, ale można go zmniejszyć dopiero po 24 godzinach od ostatniego zwiększenia. Po odczekieniu 24 godzin bez zwiększenia limitu przydziału można zmniejszyć limit przydziału udziałów tyle razy, ile chcesz, aż ponownie go zwiększysz. Zmiany skalowania przepływności/IOPS będą obowiązywać w ciągu kilku minut po zmianie aprowizowanej wielkości.

Istnieje możliwość zmniejszenia rozmiaru aprowizowanych udziałów poniżej używanego udziału GiB. Jeśli to zrobisz, nie utracisz danych, ale nadal będą naliczane płatności za użyty rozmiar i otrzymasz wydajność (bazową wartość IOPS, przepływność i wartość burst IOPS) aprowizowanych udziałów, a nie za użyty rozmiar.

W poniższej tabeli przedstawiono kilka przykładów tych formuł dla aprowizowanych rozmiarów udziałów:

|Pojemność (GiB) | Bazowa wartość IOPS | Burst IOPS | Ruch wychodzący (MiB/s) | Ruch przychodzący (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Do 4000     | 66   | 44   |
|500         | 900     | Do 4000  | 90   | 60   |
|1,024       | 1,424   | Do 4000   | 122   | 81   |
|5,120       | 5,520   | Do 15 360  | 368   | 245   |
|10,240      | 10,640  | Do 30 720  | 675   | 450   |
|33,792      | 34,192  | Do 100 000 | 2,088 | 1,392   |
|51,200      | 51,600  | Do 100 000 | 3,132 | 2,088   |
|102,400     | 100 000 | Do 100 000 | 6,204 | 4,136   |

Efektywna wydajność udziału plików podlega limitom sieci maszyn, dostępnej przepustowości sieci, rozmiarom we/wy, równoległości i wielu innym czynnikom. Na przykład na podstawie testowania wewnętrznego z 8 rozmiarami we/wy odczytu/zapisu KiB pojedyncza maszyna wirtualna z systemem Windows bez włączonej funkcji SMB Multichannel, *standardowa F16s_v2*, połączona z udziałem plików Premium za pośrednictwem SMB, może osiągnąć 20 0000 we/wy odczytu i 15 000 000 we/wy zapisu. Dzięki rozmiarom 512 MiB we/wy odczytu i zapisu ta sama maszyna wirtualna może osiągnąć 1,1 GiB/s przepływności wychodzącej i 370 MiB/s dla danych przychodzących. Ten sam klient może osiągnąć do 3 razy więcej wydajności, jeśli funkcja SMB Multichannel jest włączona \~ w udziałach Premium. Aby osiągnąć maksymalną skalę wydajności, [włącz funkcję SMB Multichannel](storage-files-enable-smb-multichannel.md) i rozłóż obciążenie na wiele maszyn wirtualnych. Zapoznaj się z przewodnikiem rozwiązywania problemów i [wydajnością wielokanałowego SMB,](storage-files-smb-multichannel-performance.md) aby [zapoznać](storage-troubleshooting-files-performance.md) się z niektórymi często występującymi problemami z wydajnością i ich obejściami.

### <a name="bursting"></a>Pęknięcie
Jeśli obciążenie wymaga dodatkowej wydajności w celu spełnienia szczytowego zapotrzebowania, twój udział może użyć środków na wzrost wydajności, aby przejść powyżej podstawowego limitu liczby we/wy udziału, aby zaoferować wydajność udziału, która jest wymagana do spełnienia wymagań. Udziały plików w wersji Premium mogą rozsyłać wartość IOPS do 4000 lub do trzech, w zależności od tego, która wartość jest większa. Bursting jest zautomatyzowany i działa w oparciu o system kredytowy. Rozszerzenie działa na zasadzie najlepszego wysiłku, a limit serii nie  jest gwarancją. Udziały plików mogą zostać skrócone do maksymalnego czasu trwania 60 minut.

Środki są gromadzone w zasobniku z serii, gdy ruch dla udziału plików jest poniżej bazowej wartości IOPS. Na przykład udział 100 GiB ma 500 bazowych wartości IOPS. Jeśli rzeczywisty ruch w udziału miał wartość 100 IOPS w określonym interwale 1-sekundowym, to 400 nieużywanych we/wy na sekundę jest środków do zasobnika z serii. Podobnie bezczynny udział 1 TiB nalicza kredyt na wzrost o 1424 IOPS. Te środki będą następnie używane później, gdy operacje przekroczą bazową wartość operacji we/wy na rok.

Za każdym razem, gdy udział przekroczy bazową wartość IOPS i ma środki w zasobniku serii, będzie on przekraczać maksymalną dozwoloną szczytową szybkość serii. Udziały mogą nadal się wydłużać, dopóki są dostępne środki, maksymalnie przez 60 minut, ale jest to oparte na liczbie środków na korzystanie z serii. Każde we/wy poza bazową wartością IOPS zużywa jeden kredyt, a po zużycie wszystkich środków udział zostanie zwrócony do bazowej wartości IOPS.

Środki na udziały mają trzy stany:

- Naliczanie, gdy udział plików używa mniejszej wartości niż podstawowa wartość IOPS.
- Malejąco, gdy udział plików używa więcej niż podstawowa liczba we/wy na rok i jest w trybie serii.
- Stała, gdy udział plików używa dokładnie podstawowej wartości IOPS, nie są naliczane ani wykorzystywane żadne środki.

Nowe udziały plików zaczynają się od pełnej liczby środków w zasobniku serii. Środki na serii nie będą naliczane, jeśli wartość liczby we/wy udziału spadnie poniżej bazowej liczby we/wy nas z powodu ograniczenia wydajności przez serwer.

## <a name="pay-as-you-go-model"></a>Model płatności zgodnie z modelem
Azure Files korzysta z modelu biznesowego płatności zgodnie z potrzebami dla standardowych udziałów plików. W modelu biznesowym z płatnością zgodnie z potrzebami kwota jest określana na podstawie rzeczywiście użycia, a nie na podstawie aprowizowana kwota. Na wysokim poziomie płacisz za ilość danych przechowywanych na dysku, a następnie dodatkowy zestaw transakcji na podstawie użycia tych danych. Model płatności zgodnie z rzeczywistym zużyciem może być opłacalny, ponieważ nie trzeba zbytniej aprowizować, aby uwzględnić przyszłe wymagania dotyczące wzrostu lub wydajności, lub coprowizować, jeśli obciążenie jest zużyciem danych, zmienia się wraz z czasem. Z drugiej strony model płatności zgodnie z użytkownikiem może być również trudny do zaplanowania w ramach procesu budżetowania, ponieważ model rozliczeń z płatnością zgodnie z użytkownikiem jest oparty na zużyciu przez użytkowników końcowych.

### <a name="differences-in-standard-tiers"></a>Różnice w warstwach standardowych
Podczas tworzenia standardowego udziału plików wybiera się między warstwą zoptymalizowaną pod kątem transakcji, gorącą i chłodną. Wszystkie trzy warstwy są przechowywane na tym samym standardowym sprzęcie magazynu. Główną różnicą dla tych trzech warstw są ceny magazynów magazynów danych, które są niższe w chłodniejszych warstwach, oraz ceny transakcji, które są wyższe w chłodniejszych warstwach. Składają się na to następujące elementy:

- Zoptymalizowana pod kątem transakcji, jak wskazuje nazwa, optymalizuje cenę dla dużych obciążeń transakcji. Optymalizacja transakcji ma najwyższą cenę magazynowania danych magazynowych, ale najniższe ceny transakcji.
- Gorąca jest dla aktywnych obciążeń, które nie obejmują dużej liczby transakcji i ma nieco niższą cenę magazynu danych magazynowanych, ale nieco wyższe ceny transakcji w porównaniu z zoptymalizowaną transakcją. Pomyśl o tym jako o środku między warstwami zoptymalizowanym pod kątem transakcji i chłodnym.
- Chłodna optymalizuje cenę dla obciążeń, które nie mają dużej aktywności, oferując najniższą cenę magazynu danych magazynowych, ale najwyższe ceny transakcji.

Jeśli umieścisz rzadko używane obciążenie w warstwie zoptymalizowanej pod kątem transakcji, nie zapłacisz prawie nic za kilka razy w miesiącu, w ramach którego będziesz dokonywać transakcji względem udziału, ale zapłacisz wysoką kwotę za koszty magazynowania danych. Gdyby przenieść ten sam udział do warstwy Chłodna, nadal płacisz prawie nic za koszty transakcji, po prostu dlatego, że transakcje dla tego obciążenia są bardzo rzadko, ale warstwa chłodna ma znacznie niższą cenę magazynu danych. Wybranie odpowiedniej warstwy dla twojego przypadku użycia pozwala znacznie obniżyć koszty. Wybranie odpowiedniej warstwy dla twojego przypadku użycia pozwala znacznie obniżyć koszty.

Podobnie, jeśli umieścisz wysoce dostępne obciążenie w warstwie Chłodna, zapłacisz znacznie więcej za transakcje, ale mniej za koszty magazynowania danych. Może to prowadzić do sytuacji, w której zwiększone koszty wynikające ze wzrostu cen transakcji przeważają nad oszczędnościami z obniżonej ceny magazynu danych, co prowadzi do płacenia większej ilości pieniędzy na chłodnie niż w przypadku optymalizacji transakcji. W przypadku niektórych poziomów użycia istnieje możliwość, że warstwa Gorąca będzie najbardziej opłacaną warstwą, ale warstwa Chłodna będzie droższa niż warstwa zoptymalizowana pod kątem transakcji.

Poziom obciążenia i działania określi najbardziej opłacaną warstwę dla standardowego udziału plików. W praktyce najlepszym sposobem na wybór najbardziej ekonomicznej warstwy jest analizie rzeczywistego zużycia zasobów udziału (przechowywanych danych, transakcji zapisu itp.).

### <a name="what-are-transactions"></a>Co to są transakcje?
Transakcje to operacje lub żądania Azure Files do przekazywania, pobierania lub innego manipulowania zawartością udziału plików. Każda akcja podjęta w ramach udziału plików przekłada się na co najmniej jedną transakcję, a w standardowych udziałach, które korzystają z modelu rozliczeń z płatnością zgodnie z użyciem, co przekłada się na koszty transakcji.

Istnieje pięć podstawowych kategorii transakcji: zapis, lista, odczyt, inne i usuwanie. Wszystkie operacje wykonywane za pośrednictwem interfejsu API REST lub SMB są podzielone na jedną z tych 4 kategorii w następujący sposób:

| Typ operacji | Transakcje zapisu | Lista transakcji | Odczyt transakcji | Inne transakcje | Usuwanie transakcji |
|-|-|-|-|-|-|
| Operacje zarządzania | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Operacje na danych | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> System plików NFS 4.1 jest dostępny tylko dla udziałów plików Premium, które korzystają z aprowizowanych modeli rozliczeń. Transakcje nie wpływają na rozliczenia dla udziałów plików w wersji Premium.

## <a name="see-also"></a>Zobacz też
- [Azure Files cennika](https://azure.microsoft.com/pricing/details/storage/files/).
- [Planowanie wdrożenia Azure Files wdrożenia i](storage-files-planning.md) Planowanie wdrożenia Azure File Sync [wdrożenia.](../file-sync/file-sync-planning.md)
- [Utwórz udział plików i](storage-how-to-create-file-share.md) [Azure File Sync](../file-sync/file-sync-deployment-guide.md).