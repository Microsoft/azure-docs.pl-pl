---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86230994"
---
Wszystkie najnowsze generacji rozmiarów maszyn wirtualnych obsługują szyfrowanie na hoście:

|Type  |Nieobsługiwane  |Obsługiwane  |
|---------|---------|---------|
|Zastosowania ogólne     | Dv3, Dav4, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Optymalizacja pod kątem obliczeń     |         | Fsv2        |
|Optymalizacja pod kątem pamięci     | EV3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Optymalizacja pod kątem magazynu     |         | Ls, Lsv2 (dyski interfejsu NVMe nie są szyfrowane)        |
|Procesory GPU     | NC, NV        | NCv2, Seria NCV3, ND, NVv3, NVv4, NDv2 (wersja zapoznawcza)        |
|Obliczenia o wysokiej wydajności     | H        | HB, HC, HBv2        |
|Poprzednie generacje     | F, A, D, L, G        | DS, GS, FS, NVv2        |

Uaktualnienie rozmiaru maszyny wirtualnej spowoduje sprawdzenie poprawności w celu sprawdzenia, czy nowy rozmiar maszyny wirtualnej obsługuje funkcję EncryptionAtHost.
