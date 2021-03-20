---
title: Pobieranie danych z pamięci podręcznej platformy Azure HPC — msrsync
description: Jak używać msrsync do przenoszenia danych do docelowego magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 323ecd6a2dd001c3c8df1b2ec15f0ae8402ec70c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092409"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Pobieranie danych z pamięci podręcznej platformy Azure HPC — Metoda msrsync

Ten artykuł zawiera szczegółowe instrukcje dotyczące korzystania z ``msrsync`` Narzędzia do kopiowania danych do kontenera usługi Azure Blob Storage w celu użycia z pamięcią podręczną platformy Azure HPC.

Aby dowiedzieć się więcej na temat przenoszenia danych do magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC, przeczytaj temat [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

``msrsync``Narzędzie to może służyć do przenoszenia danych do miejsca docelowego magazynu zaplecza dla pamięci podręcznej platformy Azure HPC. To narzędzie służy do optymalizowania użycia przepustowości przez uruchamianie wielu procesów równoległych ``rsync`` . Jest on dostępny w witrynie GitHub pod adresem https://github.com/jbd/msrsync .

``msrsync`` dzieli Katalog źródłowy na oddzielne "zasobniki", a następnie uruchamia poszczególne ``rsync`` procesy w każdym przedziale.

Testowanie wstępne przy użyciu maszyny wirtualnej z czterema rdzeniami wykazało najlepszą wydajność podczas korzystania z 64 procesów. Użyj ``msrsync`` opcji, ``-p`` Aby ustawić liczbę procesów na 64.

Należy pamiętać, że ``msrsync`` można pisać tylko do i z woluminów lokalnych. Źródło i miejsce docelowe muszą być dostępne jako instalacje lokalne na stacji roboczej używanej do wydawania polecenia.

Postępuj zgodnie z poniższymi instrukcjami, aby ``msrsync`` wypełnić usługę Azure Blob Storage za pomocą pamięci podręcznej platformy Azure HPC:

1. Instalacja ``msrsync`` i jej wymagania wstępne ( ``rsync`` i środowisko Python 2,6 lub nowsze)
1. Określ łączną liczbę plików i katalogów, które mają zostać skopiowane.

   Na przykład użyj narzędzia ``prime.py`` z argumentami ```prime.py --directory /path/to/some/directory``` (dostępne przez pobranie <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> ).

   Jeśli nie korzystasz z programu ``prime.py`` , możesz obliczyć liczbę elementów za pomocą ``find`` Narzędzia GNU w następujący sposób:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Podziel liczbę elementów na 64, aby określić liczbę elementów w procesie. Użyj tej liczby z ``-f`` opcją, aby ustawić rozmiar zasobników po uruchomieniu polecenia.

1. Wydaj ``msrsync`` polecenie, aby skopiować pliki:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Na przykład to polecenie jest przeznaczone do przenoszenia plików 11 000 w procesach 64 z/test/Source-Repository do/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
