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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86231025"
---
- Nie obsługuje Ultra dysków.
- Nie można włączyć w przypadku, gdy na maszynach wirtualnych/w zestawach skalowania maszyn wirtualnych jest włączone Azure Disk Encryption (szyfrowanie za pomocą funkcji BitLocker/maszyny wirtualnej).
- Nie można włączyć Azure Disk Encryption na dyskach, na których włączono szyfrowanie na hoście.
- Szyfrowanie można włączyć w istniejącym zestawie skalowania maszyn wirtualnych. Jednak tylko nowe maszyny wirtualne utworzone po włączeniu szyfrowania są szyfrowane automatycznie.
- Istniejące maszyny wirtualne muszą zostać cofnięte i ponownie przydzieloną, aby można było je zaszyfrować.