---
title: Przenoszenie danych do kontenera w chmurze pamięci podręcznej platformy Azure HPC
description: Jak wypełnić usługę Azure Blob Storage do użycia z pamięcią podręczną platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 0da8a4fc1b59976c50cd96f2155715a4cb178cc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87072777"
---
# <a name="move-data-to-azure-blob-storage"></a>Przenoszenie danych do usługi Azure Blob Storage

Jeśli przepływ pracy zawiera przeniesienie danych do usługi Azure Blob Storage, upewnij się, że korzystasz z wydajnej strategii. Można wstępnie załadować dane w nowym kontenerze obiektów BLOB przed zdefiniowaniem go jako obiekt docelowy magazynu lub dodać kontener, a następnie skopiować dane za pomocą usługi Azure HPC cache.

W tym artykule opisano najlepsze sposoby przenoszenia danych do magazynu obiektów BLOB w celu użycia z pamięcią podręczną platformy Azure HPC.

Należy pamiętać o następujących faktach:

* Pamięć podręczna Azure HPC używa wyspecjalizowanego formatu magazynu do organizowania danych w usłudze BLOB Storage. Dlatego obiekt docelowy magazynu obiektów BLOB musi być nowym, pustym kontenerem lub kontenerem obiektów BLOB poprzednio używanym do przechowywania danych w pamięci podręcznej platformy Azure HPC.

* Kopiowanie danych za pomocą pamięci podręcznej platformy Azure HPC do miejsca docelowego magazynu zaplecza jest wydajniejsze w przypadku korzystania z wielu klientów i operacji równoległych. Proste polecenie copy z jednego klienta spowoduje spowolnienie przenoszenia danych.

Narzędzie oparte na języku Python jest dostępne do załadowania zawartości do kontenera magazynu obiektów BLOB. Zapoznaj się ze [wstępnie załadowanymi danymi w magazynie obiektów BLOB](#pre-load-data-in-blob-storage-with-clfsload) , aby dowiedzieć się więcej.

Jeśli nie chcesz używać narzędzia ładowania lub chcesz dodać zawartość do istniejącego miejsca docelowego magazynu, postępuj zgodnie ze wskazówkami pozyskiwania danych równoległych w temacie [Kopiowanie danych za pomocą pamięci podręcznej platformy Azure HPC](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Wstępne ładowanie danych w magazynie obiektów BLOB za pomocą CLFSLoad

Za pomocą narzędzia avere CLFSLoad można skopiować dane do nowego kontenera magazynu obiektów BLOB przed dodaniem go jako miejsca docelowego magazynu. To narzędzie działa w ramach jednego systemu Linux i zapisuje dane w formacie zastrzeżonym wymaganym przez pamięć podręczną platformy Azure HPC. CLFSLoad to najbardziej wydajny sposób wypełniania kontenera magazynu obiektów BLOB do użycia z pamięcią podręczną.

Narzędzie avere CLFSLoad jest dostępne przez żądanie od zespołu pamięci podręcznej platformy Azure HPC. Poproś o kontakt z zespołem lub Otwórz [bilet pomocy technicznej](hpc-cache-support-ticket.md) , aby uzyskać pomoc.

Ta opcja działa tylko z nowymi pustymi kontenerami. Utwórz kontener przed użyciem avere CLFSLoad.

Szczegółowe informacje znajdują się w dystrybucji CLFSLoad avere, która jest dostępna na żądanie od zespołu pamięci podręcznej platformy Azure HPC.

Ogólny przegląd procesu:

1. Przygotuj system Linux (maszyna wirtualna lub fizyczna) przy użyciu języka Python w wersji 3,6 lub nowszej. Środowisko Python 3,7 jest zalecane w celu zapewnienia lepszej wydajności.
1. Zainstaluj oprogramowanie Avere-CLFSLoad w systemie Linux.
1. Wykonaj transfer z wiersza polecenia systemu Linux.

Narzędzie avere CLFSLoad potrzebuje następujących informacji:

* Identyfikator konta magazynu, który zawiera kontener magazynu obiektów BLOB
* Nazwa pustego kontenera magazynu obiektów BLOB
* Token sygnatury dostępu współdzielonego (SAS), który umożliwia narzędziu zapis do kontenera
* Ścieżka lokalna do źródła danych — katalog lokalny zawierający dane do skopiowania lub ścieżka lokalna do zainstalowanego systemu zdalnego z danymi

## <a name="copy-data-through-the-azure-hpc-cache"></a>Kopiowanie danych za pomocą pamięci podręcznej platformy Azure HPC

Jeśli nie chcesz używać narzędzia avere CLFSLoad, lub jeśli chcesz dodać dużą ilość danych do istniejącego obiektu docelowego magazynu obiektów blob, możesz skopiować go za pomocą pamięci podręcznej. Pamięć podręczna Azure HPC została zaprojektowana w celu jednoczesnego obsłużenia wielu klientów, dlatego w celu skopiowania danych za pośrednictwem pamięci podręcznej należy używać zapisu równoległego z wielu klientów.

![Diagram przedstawiający wiele klientów, przenoszenie danych wielowątkowych: w lewym górnym rogu ikona lokalnego magazynu sprzętu ma wiele strzałek. Strzałki wskazują cztery komputery klienckie. Z każdego komputera klienckiego trzy strzałki wskazują na pamięć podręczną platformy Azure HPC. W pamięci podręcznej platformy Azure HPC wiele strzałek wskazuje na usługę BLOB Storage.](media/hpc-cache-parallel-ingest.png)

``cp``Polecenia lub ``copy`` , które są zwykle używane do transferowania danych z jednego systemu magazynu do innego, to procesy jednowątkowe, które kopiującą tylko jeden plik jednocześnie. Oznacza to, że serwer plików pobiera tylko jeden plik w czasie, który jest odpadami zasobów pamięci podręcznej.

W tej sekcji opisano strategie tworzenia wieloskładnikowego systemu kopiowania plików wielowątkowych do przenoszenia danych do usługi BLOB Storage za pomocą pamięci podręcznej Azure HPC. Objaśniono w nim koncepcje transferu plików i punkty decyzyjne, które mogą być używane do wydajnego kopiowania danych przy użyciu wielu klientów i prostych poleceń kopiowania.

Wyjaśniono również niektóre narzędzia, które mogą pomóc. ``msrsync``Narzędzie może służyć do częściowo automatyzowania procesu dzielenia zestawu danych na przedziały i używania poleceń rsync. ``parallelcp``Skrypt jest innym narzędziem, które odczytuje Katalog źródłowy i automatycznie wystawia polecenia kopiowania.

### <a name="strategic-planning"></a>Planowanie strategiczne

Podczas kompilowania strategii w celu równoległego kopiowania danych należy zrozumieć wady dotyczące wielkości plików, liczby plików i głębokości katalogów.

* Gdy pliki są małe, Metryka jest równa plików na sekundę.
* Gdy pliki są duże (10MiBi lub większe), Metryka jest równa bajty na sekundę.

Każdy proces kopiowania ma stawkę przepływności i szybkość transferu plików, która może być mierzona przez chronometraż czasu polecenia kopiowania i współczynnik rozmiaru pliku i liczby plików. Wyjaśnienie, jak zmierzyć stawki, wykracza poza zakres tego dokumentu, ale jest to konieczne, aby zrozumieć, czy będziesz mieć do czynienia z małymi lub dużymi plikami.

Strategie dotyczące równoległego pozyskiwania danych z użyciem pamięci podręcznej platformy Azure HPC obejmują:

* Kopiowanie ręczne — można ręcznie utworzyć kopię wielowątkową na kliencie, uruchamiając więcej niż jedno polecenie kopiowania w tle względem wstępnie zdefiniowanych zestawów plików lub ścieżek. Aby uzyskać szczegółowe informacje, przeczytaj metodę pozyskiwania [danych w pamięci podręcznej Azure HPC — ręczna kopia](hpc-cache-ingest-manual.md) .

* Częściowe automatyczne kopiowanie za pomocą ``msrsync``  -  ``msrsync`` to narzędzie otoki, które uruchamia wiele procesów równoległych ``rsync`` . Aby uzyskać szczegółowe informacje, Przeczytaj [metodę Azure HPC cache Data pozyskiwania — msrsync](hpc-cache-ingest-msrsync.md).

* Kopiowanie za pomocą skryptu ``parallelcp`` — informacje na temat tworzenia i uruchamiania skryptu kopiowania równoległego w ramach [metody skryptu pozyskiwania danych w pamięci podręcznej Azure HPC](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu magazynu należy dowiedzieć się, jak klienci mogą instalować pamięć podręczną.

* [Dostęp do systemu pamięci podręcznej platformy Azure HPC](hpc-cache-mount.md)
