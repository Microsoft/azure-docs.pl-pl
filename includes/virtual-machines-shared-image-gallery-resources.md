---
title: plik dołączania
description: plik dołączania
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82789005"
---
| Zasób | Opis|
|----------|------------|
| **Źródło obrazu** | Jest to zasób, którego można użyć do utworzenia **wersji obrazu** w galerii obrazów. Źródłem obrazu może być istniejąca maszyna wirtualna platformy Azure, która jest [uogólniona lub wyspecjalizowana](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images), zarządzanym obrazem, migawką lub wersją obrazu w innej galerii obrazów. |
| **Galeria obrazów** | Podobnie jak w przypadku portalu Azure Marketplace, **Galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale ty kontrolujesz, kto ma dostęp. |
| **Definicja obrazu** | Definicje obrazów są tworzone w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go wewnętrznie. Dotyczy to zarówno obrazu systemu Windows, jak i Linux, informacji o wersji oraz minimalnych i maksymalnych wymagań dotyczących pamięci. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii. Dla danego środowiska można mieć wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, w przypadku tworzenia maszyny wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać. |