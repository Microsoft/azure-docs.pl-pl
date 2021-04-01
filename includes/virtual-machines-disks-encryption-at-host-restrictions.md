---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231025"
---
- Nie obsługuje Ultra dysków.
- Nie można włączyć w przypadku, gdy na maszynach wirtualnych/w zestawach skalowania maszyn wirtualnych jest włączone Azure Disk Encryption (szyfrowanie za pomocą funkcji BitLocker/maszyny wirtualnej).
- Nie można włączyć Azure Disk Encryption na dyskach, na których włączono szyfrowanie na hoście.
- Szyfrowanie można włączyć w istniejącym zestawie skalowania maszyn wirtualnych. Jednak tylko nowe maszyny wirtualne utworzone po włączeniu szyfrowania są szyfrowane automatycznie.
- Istniejące maszyny wirtualne muszą zostać cofnięte i ponownie przydzieloną, aby można było je zaszyfrować.