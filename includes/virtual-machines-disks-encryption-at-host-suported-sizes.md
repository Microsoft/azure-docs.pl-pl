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
ms.openlocfilehash: e9d7b39ce267202503b90e84e934d31501d45478
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732236"
---
Wszystkie rozmiary maszyn wirtualnych najnowszej generacji obsługują szyfrowanie na hoście:

|Typ  |Nieobsługiwane  |Obsługiwane  |
|---------|---------|---------|
|Ogólnego przeznaczenia     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4, Ddv4, Ddsv4       |
|Optymalizacja pod kątem obliczeń     |         | Fsv2        |
|Optymalizacja pod kątem pamięci     | Ev3        | Esv3, M, Mv2, Eav4, Easv4, Edv4, Edsv4        |
|Optymalizacja pod kątem magazynu     |         | Ls, Lsv2 (dyski NVMe nie są szyfrowane)        |
|Procesory GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (wersja zapoznawcza)        |
|Obliczenia o wysokiej wydajności     | H        | HB, HC, HBv2        |
|Poprzednie generacje     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
