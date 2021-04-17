---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e8ec7faf1562381288aeef630bf2076ce413017a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531529"
---
Wszystkie rozmiary maszyn wirtualnych najnowszej generacji obsługują szyfrowanie na hoście:

|Typ  |Nieobsługiwane  |Obsługiwane  |
|---------|---------|---------|
|Ogólnego przeznaczenia     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4        |
|Optymalizacja pod kątem obliczeń     |         | Fsv2        |
|Optymalizacja pod kątem pamięci     | Ev3        | DSv2, Esv3, M, Mv2, Eav4, Easv4        |
|Optymalizacja pod kątem magazynu     |         | Ls, Lsv2 (dyski NVMe nie są szyfrowane)        |
|Procesory GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (wersja zapoznawcza)        |
|Obliczenia o wysokiej wydajności     | H        | HB, HC, HBv2        |
|Poprzednie generacje     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
