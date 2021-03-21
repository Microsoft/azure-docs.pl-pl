---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98859893"
---
- Obsługiwane są tylko [klucze RSA oprogramowania i modułu HSM](../articles/key-vault/keys/about-keys.md) o rozmiarze 2 048-bitowym, 3 072-bitowym i 4 096-bitowym, brak innych kluczy ani rozmiarów.
    - Klucze [HSM](../articles/key-vault/keys/hsm-protected-keys.md) wymagają warstwy **Premium** magazynów kluczy platformy Azure.
- Dyski utworzone na podstawie obrazów niestandardowych zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta i muszą znajdować się w tej samej subskrypcji.
- Migawki utworzone na podstawie dysków zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta.
- Wszystkie zasoby związane z kluczami zarządzanymi przez klienta (magazyny kluczy Azure, zestawy szyfrowania dysków, maszyny wirtualne, dyski i migawki) muszą znajdować się w tej samej subskrypcji i regionie.
- Dyski, migawki i obrazy zaszyfrowane przy użyciu kluczy zarządzanych przez klienta nie mogą zostać przeniesione do innej grupy zasobów i subskrypcji.
- Dyski zarządzane aktualnie lub wcześniej zaszyfrowane przy użyciu Azure Disk Encryption nie mogą być szyfrowane przy użyciu kluczy zarządzanych przez klienta.
- Można utworzyć maksymalnie 1000 zestawów szyfrowania dysków na region na subskrypcję.
- Aby uzyskać informacje o korzystaniu z kluczy zarządzanych przez klienta z udostępnionymi galeriami obrazów, zobacz [wersja zapoznawcza: Używanie kluczy zarządzanych przez klienta do szyfrowania obrazów](../articles/virtual-machines/image-version-encryption.md).
