---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8833639b6efacc664596ecb2aa6f9da41ad23b81
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814143"
---
- Obsługiwane są tylko [klucze RSA oprogramowania i modułu HSM](../articles/key-vault/keys/about-keys.md) o rozmiarze 2 048-bitowym, 3 072-bitowym i 4 096-bitowym, brak innych kluczy ani rozmiarów.
    - Klucze [HSM](../articles/key-vault/keys/hsm-protected-keys.md) wymagają warstwy **Premium** magazynów kluczy platformy Azure.
- Dyski utworzone na podstawie obrazów niestandardowych zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta i muszą znajdować się w tej samej subskrypcji.
- Migawki utworzone na podstawie dysków zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta.
- Wszystkie zasoby związane z kluczami zarządzanymi przez klienta (magazyny kluczy Azure, zestawy szyfrowania dysków, maszyny wirtualne, dyski i migawki) muszą znajdować się w tej samej subskrypcji i regionie.
- Dyski, migawki i obrazy zaszyfrowane przy użyciu kluczy zarządzanych przez klienta nie mogą zostać przeniesione do innej subskrypcji.
- Dyski zarządzane aktualnie lub wcześniej zaszyfrowane przy użyciu Azure Disk Encryption nie mogą być szyfrowane przy użyciu kluczy zarządzanych przez klienta.
- Można utworzyć maksymalnie 50 zestawów szyfrowania dysków na region na subskrypcję.
- Aby uzyskać informacje o korzystaniu z kluczy zarządzanych przez klienta z udostępnionymi galeriami obrazów, zobacz [wersja zapoznawcza: Używanie kluczy zarządzanych przez klienta do szyfrowania obrazów](../articles/virtual-machines/image-version-encryption.md).