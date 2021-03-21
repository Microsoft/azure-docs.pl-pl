---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750227"
---
- Ta funkcja jest obecnie obsługiwana tylko w przypadku wersji Premium dysków SSD.
- Aby można było zmienić warstwę dysku, należy cofnąć przydział maszyny wirtualnej lub odłączyć dysk od uruchomionej maszyny wirtualnej.
- Warstwy wydajności P60, P70 i P80 mogą być używane tylko przez dyski o rozmiarze większym niż 4 096 GiB.
- Warstwę wydajności dysku można zmienić na starszą wersję co 12 godzin.

## <a name="change-performance-tier-without-downtime-preview"></a>Zmień warstwę wydajności bez przestoju (wersja zapoznawcza)

Zwykle konieczna jest ponowna alokacja maszyny wirtualnej lub odłączenie dysku w celu zmiany warstwy wydajności. Jeśli jednak włączysz tę funkcję w wersji zapoznawczej, nie musisz cofnąć alokacji maszyny wirtualnej lub odłącz dysku, aby zmienić warstwę. Możesz utworzyć konto w wersji zapoznawczej [tutaj](https://aka.ms/liveperftiersignup).

Wersja zapoznawcza ma następujące ograniczenia:
- Dostępne tylko w regionie EastUS2EUAP.
- Aktualnie niedostępne dla dysków udostępnionych
- `2020-12-01`Aby zmienić warstwy wydajności bez przestoju, należy użyć szablonów Azure Resource Manager z interfejsem API.