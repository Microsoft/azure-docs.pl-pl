---
title: Pobieranie danych z pamięci podręcznej platformy Azure HPC — msrsync
description: Jak używać msrsync do przenoszenia danych do docelowego magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2e0442b6aa1404ae5f57445179979496faa09863
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194979"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Pobieranie danych z pamięci podręcznej platformy Azure HPC — Metoda msrsync

Ten artykuł zawiera szczegółowe instrukcje dotyczące korzystania z ``msrsync`` narzędzia do kopiowania danych do kontenera usługi Azure Blob Storage w celu użycia z pamięcią podręczną platformy Azure HPC.

Aby dowiedzieć się więcej na temat przenoszenia danych do magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC, przeczytaj temat [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

``msrsync`` Narzędzie to może służyć do przenoszenia danych do miejsca docelowego magazynu zaplecza dla pamięci podręcznej platformy Azure HPC. To narzędzie służy do optymalizowania użycia przepustowości przez uruchamianie wielu procesów ``rsync`` równoległych. Jest on dostępny w witrynie GitHub https://github.com/jbd/msrsyncpod adresem.

``msrsync``dzieli Katalog źródłowy na oddzielne "zasobniki", a następnie uruchamia poszczególne ``rsync`` procesy w każdym przedziale.

Testowanie wstępne przy użyciu maszyny wirtualnej z czterema rdzeniami wykazało najlepszą wydajność podczas korzystania z 64 procesów. Użyj ``msrsync`` opcji ``-p`` , aby ustawić liczbę procesów na 64.

Należy pamiętać ``msrsync`` , że można pisać tylko do i z woluminów lokalnych. Źródło i miejsce docelowe muszą być dostępne jako instalacje lokalne na stacji roboczej używanej do wydawania polecenia.

Postępuj zgodnie z poniższymi instrukcjami, aby ``msrsync`` wypełnić usługę Azure Blob Storage za pomocą pamięci podręcznej platformy Azure HPC:

1. Instalacja ``msrsync`` i jej wymagania wstępne``rsync`` (i środowisko Python 2,6 lub nowsze)
1. Określ łączną liczbę plików i katalogów, które mają zostać skopiowane.

   Na przykład użyj narzędzia ``prime.py`` z argumentami ```prime.py --directory /path/to/some/directory``` (dostępne przez pobranie <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Jeśli nie korzystasz z programu ``prime.py``, możesz obliczyć liczbę elementów za pomocą narzędzia ``find`` GNU w następujący sposób:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Podziel liczbę elementów na 64, aby określić liczbę elementów w procesie. Użyj tej liczby z ``-f`` opcją, aby ustawić rozmiar zasobników po uruchomieniu polecenia.

1. Wydaj polecenie ``msrsync`` , aby skopiować pliki:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Na przykład to polecenie jest przeznaczone do przenoszenia plików 11 000 w procesach 64 z/test/Source-Repository do/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
