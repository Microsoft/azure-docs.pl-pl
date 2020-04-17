---
title: Przenoszenie danych do kontenera chmury pamięci podręcznej HPC usługi Azure
description: Jak wypełnić magazyn obiektów blob platformy Azure do użytku z pamięcią podręczną HPC usługi Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fd21a78d0271f91d334bba5aba748f3770ad38cf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537937"
---
# <a name="move-data-to-azure-blob-storage"></a>Przenoszenie danych do magazynu obiektów blob platformy Azure

Jeśli przepływ pracy obejmuje przenoszenie danych do magazynu obiektów Blob platformy Azure, upewnij się, że używasz wydajnej strategii. Można wstępnie załadować dane w nowym kontenerze obiektów blob przed zdefiniowaniem go jako miejsce docelowe magazynu lub dodać kontener, a następnie skopiować dane przy użyciu pamięci podręcznej HPC usługi Azure.

W tym artykule opisano najlepsze sposoby przenoszenia danych do magazynu obiektów Blob do użytku z pamięcią podręczną HPC usługi Azure.

Należy pamiętać o tych faktach:

* Pamięć podręczna HPC usługi Azure używa specjalistycznego formatu magazynu do organizowania danych w magazynie obiektów Blob. Dlatego miejsce docelowe magazynu obiektów Blob musi być nowym, pustym kontenerem lub kontenerem obiektów Blob, który był wcześniej używany dla danych usługi Azure HPC Cache.

* Kopiowanie danych za pośrednictwem pamięci podręcznej HPC platformy Azure do miejsca docelowego magazynu zaplecza jest bardziej wydajne, gdy używasz wielu klientów i operacji równoległych. Proste polecenie kopiowania z jednego klienta będzie powoli przenosić dane.

Narzędzie oparte na języku Python jest dostępne do ładowania zawartości do kontenera magazynu obiektów Blob. Przeczytaj [wstępne ładowanie danych w magazynie obiektów Blob,](#pre-load-data-in-blob-storage-with-clfsload) aby dowiedzieć się więcej.

Jeśli nie chcesz korzystać z narzędzia ładowania lub jeśli chcesz dodać zawartość do istniejącego miejsca docelowego magazynu, postępuj zgodnie z równoległymi wskazówkami dotyczącymi pozyskiwania danych w [obszarze Kopiowanie danych za pośrednictwem pamięci podręcznej HPC usługi Azure.](#copy-data-through-the-azure-hpc-cache)

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Wstępne ładowanie danych w magazynie obiektów Blob za pomocą programu CLFSLoad

Narzędzie Avere CLFSLoad służy do kopiowania danych do nowego kontenera magazynu obiektów Blob przed dodaniem go jako miejsca docelowego magazynu. To narzędzie działa na jednym systemie Linux i zapisuje dane w zastrzeżonym formacie potrzebnym do pracy w pamięci podręcznej HPC platformy Azure. CLFSLoad jest najbardziej efektywnym sposobem wypełniania kontenera magazynu obiektów Blob do użycia z pamięcią podręczną.

Narzędzie Avere CLFSLoad jest dostępne na żądanie zespołu pamięci podręcznej HPC platformy Azure. Poproś o kontakt z zespołem lub otwórz [bilet pomocy technicznej,](hpc-cache-support-ticket.md) aby poprosić o pomoc.

Ta opcja działa tylko z nowymi, pustymi kontenerami. Utwórz kontener przed użyciem Avere CLFSLoad.

Szczegółowe informacje znajdują się w dystrybucji Avere CLFSLoad, która jest dostępna na żądanie zespołu pamięci podręcznej HPC platformy Azure.

Ogólny przegląd procesu:

1. Przygotuj system Linux (VM lub fizyczny) w wersji Python 3.6 lub nowszej. Python 3.7 jest zalecany dla lepszej wydajności.
1. Zainstaluj oprogramowanie Avere-CLFSLoad w systemie Linux.
1. Wykonaj transfer z wiersza polecenia Linuksa.

Narzędzie Avere CLFSLoad wymaga następujących informacji:

* Identyfikator konta magazynu zawierający kontener magazynu obiektów Blob
* Nazwa pustego kontenera magazynu obiektów Blob
* Token sygnatury dostępu współdzielonego (SAS), który umożliwia narzędziu zapisywanie w kontenerze
* Ścieżka lokalna do źródła danych — katalog lokalny zawierający dane do skopiowania lub ścieżka lokalna do zamontowanego systemu zdalnego z danymi

## <a name="copy-data-through-the-azure-hpc-cache"></a>Kopiowanie danych za pośrednictwem pamięci podręcznej HPC platformy Azure

Jeśli nie chcesz używać narzędzia Avere CLFSLoad lub jeśli chcesz dodać dużą ilość danych do istniejącego miejsca docelowego magazynu obiektów Blob, możesz skopiować go za pośrednictwem pamięci podręcznej. Usługa Azure HPC Cache jest przeznaczony do obsługi wielu klientów jednocześnie, więc do kopiowania danych za pośrednictwem pamięci podręcznej, należy użyć zapisów równoległych z wielu klientów.

![Diagram przedstawiający wielonakładnikowy, wielowątkowy ruch danych: W lewym górnym rogu ikona lokalnego magazynu sprzętu ma wiele strzałek pochodzących z niego. Strzałki wskazują na cztery maszyny klienckie. Z każdego komputera klienckiego trzy strzałki wskazują w kierunku pamięci podręcznej HPC platformy Azure. Z pamięci podręcznej HPC platformy Azure wiele strzałek wskazuje magazyn obiektów Blob.](media/hpc-cache-parallel-ingest.png)

Polecenia ``cp`` ``copy`` lub polecenia, które są zwykle używane do przesyłania danych z jednego systemu magazynu do innego są procesami jednowątkowymi, które kopiują tylko jeden plik naraz. Oznacza to, że serwer plików jest pozyskiwania tylko jeden plik na raz - co jest stratą zasobów pamięci podręcznej.

W tej sekcji opisano strategie tworzenia wielowątkowy, wielowątkowy system kopiowania plików, aby przenieść dane do magazynu obiektów Blob z pamięcią podręczną HPC Azure. Wyjaśniono pojęcia transferu plików i punkty decyzyjne, które mogą być używane do wydajnego kopiowania danych przy użyciu wielu klientów i prostych poleceń kopiowania.

Wyjaśnia również niektóre narzędzia, które mogą pomóc. Narzędzie ``msrsync`` może być używane do częściowego zautomatyzowania procesu dzielenia zestawu danych na zasobniki i używania poleceń rsync. Skrypt ``parallelcp`` jest innym narzędziem, które odczytuje katalog źródłowy i automatycznie wydaje polecenia kopiowania.

### <a name="strategic-planning"></a>Planowanie strategiczne

Podczas tworzenia strategii kopiowania danych równolegle, należy zrozumieć kompromisy w rozmiarze pliku, liczby plików i głębokości katalogu.

* Gdy pliki są małe, metryką zainteresowania są pliki na sekundę.
* Gdy pliki są duże (10MiBi lub większa), metryka zainteresowania jest bajtów na sekundę.

Każdy proces kopiowania ma szybkość przepływności i szybkość przesyłania plików, które mogą być mierzone przez czas długości polecenia kopiowania i faktoringu rozmiaru pliku i liczby plików. Wyjaśnienie, jak mierzyć stawki, wykracza poza zakres tego dokumentu, ale konieczne jest zrozumienie, czy będziesz mieć do czynienia z małymi czy dużymi plikami.

Strategie równoległego pozyskiwania danych za pomocą usługi Azure HPC Cache obejmują:

* Kopiowanie ręczne — można ręcznie utworzyć kopię wielowątkową na kliencie, uruchamiając więcej niż jedno polecenie kopiowania jednocześnie w tle względem wstępnie zdefiniowanych zestawów plików lub ścieżek. Przeczytaj [źródło danych usługi Azure HPC Cache — metoda ręcznej kopiowania, aby](hpc-cache-ingest-manual.md) uzyskać szczegółowe informacje.

* Częściowo automatyczne kopiowanie ``msrsync``  -  ``msrsync`` za pomocą jest narzędziem ``rsync`` otoki, które uruchamia wiele procesów równoległych. Aby uzyskać szczegółowe informacje, przeczytaj [artykuł Pozyskiwania danych usługi Azure HPC Cache — metoda msrsync](hpc-cache-ingest-msrsync.md).

* Kopiowanie skryptów ``parallelcp`` za pomocą — dowiedz się, jak utworzyć i uruchomić skrypt kopiowania równoległego w [ujmowaniu danych usługi Azure HPC Cache — metoda skryptu kopiowania równoległego](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu magazynu dowiedz się, jak klienci mogą zainstalować pamięć podręczną.

* [Dostęp do systemu pamięci podręcznej HPC azure](hpc-cache-mount.md)
