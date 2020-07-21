---
title: Używanie zagregowanej przestrzeni nazw usługi Azure HPC cache
description: Jak zaplanować wirtualną przestrzeń nazw dla pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: c16d2f9e9c94603361d9a096f33d559105f2d28d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497033"
---
# <a name="plan-the-aggregated-namespace"></a>Planowanie zagregowanej przestrzeni nazw

Pamięć podręczna Azure HPC umożliwia klientom dostęp do różnych systemów magazynowania za pomocą wirtualnej przestrzeni nazw, która ukrywa szczegóły systemu magazynu zaplecza.

Podczas dodawania miejsca docelowego magazynu należy ustawić ścieżkę pliku dostępną dla klienta. Komputery klienckie instalują tę ścieżkę pliku i mogą wprowadzać żądania odczytu plików do pamięci podręcznej, zamiast bezpośrednio instalować system przechowywania.

Ponieważ pamięć podręczna platformy Azure HPC zarządza tym wirtualnym systemem plików, można zmienić miejsce docelowe magazynu bez zmiany ścieżki skierowanej do klienta. Można na przykład zastąpić sprzętowy system magazynowania z magazynem w chmurze bez konieczności ponownego zapisywania procedur związanych z klientem.

## <a name="aggregated-namespace-example"></a>Przykład zagregowanego przestrzeni nazw

Zaplanuj zagregowaną przestrzeń nazw, tak aby komputery klienckie mogły wygodnie uzyskać dostęp do potrzebnych informacji, dzięki czemu administratorzy i inżynierowie przepływów pracy mogą łatwo odróżnić ścieżki.

Rozważmy na przykład system, w którym wystąpienie pamięci podręcznej platformy Azure HPC jest używane do przetwarzania danych przechowywanych w obiekcie blob platformy Azure. Analiza wymaga plików szablonów przechowywanych w lokalnym centrum danych.

Dane szablonu są przechowywane w centrum danych, a informacje wymagające tego zadania są przechowywane w następujących podkatalogach:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

System magazynu centrum danych ujawnia następujące eksporty:

* */*
* */goldline*
* */goldline/templates*

Dane, które mają zostać poddane analizie, zostały skopiowane do kontenera magazynu obiektów blob platformy Azure o nazwie "SourceCollection" przy użyciu [Narzędzia CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Aby zapewnić łatwy dostęp za pomocą pamięci podręcznej, należy rozważyć utworzenie obiektów docelowych magazynu przy użyciu tych ścieżek wirtualnych przestrzeni nazw:

| System magazynu zaplecza <br/> (Ścieżka pliku NFS lub kontener obiektów BLOB) | Ścieżka wirtualnej przestrzeni nazw |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| obiekcie SourceCollection wprowadzanych                        | /source               |

Obiekt docelowy magazynu NFS może mieć wiele ścieżek przestrzeni nazw wirtualnych, o ile każda z nich odwołuje się do unikatowej ścieżki eksportu.

Ponieważ ścieżki źródłowe NFS są podkatalogami tego samego eksportu, należy zdefiniować wiele ścieżek przestrzeni nazw z tego samego miejsca docelowego magazynu.

| Nazwa hosta docelowego magazynu  | Ścieżka eksportowania NFS     | Ścieżka podkatalogu | Ścieżka przestrzeni nazw    |
|--------------------------|---------------------|-------------------|-------------------|
| *Adres IP lub nazwa hosta* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *Adres IP lub nazwa hosta* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Aplikacja kliencka może zainstalować pamięć podręczną i łatwo uzyskać dostęp do zagregowanych ścieżek plików przestrzeni nazw, ``/source`` ``/templates/sku798`` i ``/templates/sku980`` .

## <a name="next-steps"></a>Następne kroki

Po podjęciu decyzji o sposobie konfigurowania wirtualnego systemu plików należy [utworzyć miejsce docelowe magazynu](hpc-cache-add-storage.md) w celu zamapowania magazynu zaplecza na ścieżki do pliku wirtualnego po stronie klienta.
