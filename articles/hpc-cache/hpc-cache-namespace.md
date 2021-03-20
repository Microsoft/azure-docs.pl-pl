---
title: Informacje na temat zagregowanej przestrzeni nazw pamięci podręcznej platformy Azure HPC
description: Jak zaplanować wirtualną przestrzeń nazw dla pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91612952"
---
# <a name="plan-the-aggregated-namespace"></a>Planowanie zagregowanej przestrzeni nazw

Pamięć podręczna Azure HPC umożliwia klientom dostęp do różnych systemów magazynowania za pomocą wirtualnej przestrzeni nazw, która ukrywa szczegóły systemu magazynu zaplecza.

Po dodaniu miejsca docelowego magazynu należy skonfigurować jedną lub więcej ścieżek przestrzeni nazw związanych z klientem dla miejsca docelowego magazynu. Komputery klienckie instalują tę ścieżkę pliku i mogą wprowadzać żądania odczytu plików do pamięci podręcznej, zamiast bezpośrednio instalować system przechowywania.

Ponieważ pamięć podręczna platformy Azure HPC zarządza tym wirtualnym systemem plików, można zmienić miejsce docelowe magazynu bez zmiany ścieżki skierowanej do klienta. Można na przykład zastąpić sprzętowy system magazynowania z magazynem w chmurze bez konieczności ponownego pisania procedur po stronie klienta.

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

Obiekt docelowy magazynu NFS może mieć wiele ścieżek przestrzeni nazw wirtualnych, o ile każda z nich odwołuje się do unikatowej ścieżki eksportu. (Odczytaj [ścieżki przestrzeni nazw systemu plików NFS](add-namespace-paths.md#nfs-namespace-paths) , aby poznać zalecaną maksymalną liczbę ścieżek przestrzeni nazw na miejsce docelowe magazynu NFS).

Ponieważ ścieżki źródłowe NFS są podkatalogami tego samego eksportu, należy zdefiniować wiele ścieżek przestrzeni nazw z tego samego miejsca docelowego magazynu.

| Nazwa hosta docelowego magazynu  | Ścieżka eksportowania NFS     | Ścieżka podkatalogu | Ścieżka przestrzeni nazw    |
|--------------------------|---------------------|-------------------|-------------------|
| *Adres IP lub nazwa hosta* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *Adres IP lub nazwa hosta* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Aplikacja kliencka może zainstalować pamięć podręczną i łatwo uzyskać dostęp do zagregowanych ścieżek plików przestrzeni nazw, ``/source`` ``/templates/sku798`` i ``/templates/sku980`` .

Alternatywnym rozwiązaniem może być utworzenie ścieżki wirtualnej, takiej jak `/templates` łącza do katalogu nadrzędnego, `acme2017` a następnie przechodzenie klientów do poszczególnych `sku798` `sku980` katalogów po zainstalowaniu pamięci podręcznej. Nie można jednak utworzyć ścieżki przestrzeni nazw, która jest podkatalogiem innej ścieżki przestrzeni nazw. Dlatego w przypadku utworzenia ścieżki do `acme2017` katalogu nie można również utworzyć żadnych ścieżek przestrzeni nazw, aby uzyskać bezpośredni dostęp do jego podkatalogów.

Strona ustawienia **przestrzeni nazw** pamięci podręcznej platformy Azure HPC zawiera system plików z obsługą klienta i umożliwia dodawanie lub edytowanie ścieżek. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [Konfiguracja zagregowanej przestrzeni nazw](add-namespace-paths.md) .

## <a name="next-steps"></a>Następne kroki

Po określeniu sposobu konfigurowania wirtualnego systemu plików wykonaj następujące kroki, aby je utworzyć:

* [Tworzenie docelowych magazynów](hpc-cache-add-storage.md) w celu dodania systemów magazynowania zaplecza do pamięci podręcznej platformy Azure HPC
* [Dodaj ścieżki przestrzeni nazw](add-namespace-paths.md) , aby utworzyć zagregowaną przestrzeń nazw używaną przez komputery klienckie do uzyskiwania dostępu do plików
