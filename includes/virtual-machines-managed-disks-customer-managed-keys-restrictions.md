---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4ae190b55df70753f8f4dd081e8077c00e4c688a
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231034"
---
- Obsługiwane są tylko [klucze RSA oprogramowania i modułu HSM](../articles/key-vault/keys/about-keys.md) o rozmiarze 2 048-bitowym, 3 072-bitowym i 4 096-bitowym, brak innych kluczy ani rozmiarów.
    - Klucze [HSM](../articles/key-vault/keys/hsm-protected-keys.md) wymagają warstwy **Premium** magazynów kluczy platformy Azure.
- Dyski utworzone na podstawie obrazów niestandardowych zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta i muszą znajdować się w tej samej subskrypcji.
- Migawki utworzone na podstawie dysków zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta.
- Wszystkie zasoby związane z kluczami zarządzanymi przez klienta (magazyny kluczy Azure, zestawy szyfrowania dysków, maszyny wirtualne, dyski i migawki) muszą znajdować się w tej samej subskrypcji i regionie.
- Dyski, migawki i obrazy zaszyfrowane przy użyciu kluczy zarządzanych przez klienta nie mogą zostać przeniesione do innej subskrypcji.
- Dyski zarządzane zaszyfrowane przy użyciu kluczy zarządzanych przez klienta nie mogą być również szyfrowane za pomocą Azure Disk Encryption.
- Aby uzyskać informacje o korzystaniu z kluczy zarządzanych przez klienta z udostępnionymi galeriami obrazów, zobacz [wersja zapoznawcza: Używanie kluczy zarządzanych przez klienta do szyfrowania obrazów](../articles/virtual-machines/image-version-encryption.md).
