---
title: Przegląd jednostki obliczeniowej platformy Azure | Microsoft Docs
description: Przegląd koncepcji jednostek obliczeniowych platformy Azure. ACU umożliwia porównanie wydajności procesora CPU w jednostkach SKU platformy Azure.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 8e406aea10c15a97a7e66bbdc3c1d889c5bd2c52
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493867"
---
# <a name="azure-compute-unit-acu"></a>Jednostka obliczeniowa platformy Azure (ACU)

Koncepcja jednostki obliczeniowej platformy Azure (ACU) zapewnia sposób porównywania wydajności obliczeniowej (CPU) w ramach jednostek SKU platformy Azure. Ten parametr pomoże łatwo zidentyfikować jednostkę SKU, która najprawdopodobniej spełni określone potrzeby związane z wydajnością. Jednostka ACU jest obecnie standaryzowana na małej maszynie wirtualnej (Standardowa_A1) jako równa 100, a wszystkie pozostałe jednostki SKU reprezentują w przybliżeniu, o ile szybciej dana jednostka SKU może uruchomić standardowy test porównawczy.

> [!IMPORTANT]
> Wartość ACU jest tylko wskazówką. Wyniki dla konkretnego obciążenia mogą się różnić.
<br>

| Rodzina SKU | ACU/procesor wirtualny vCPU | vCPU: rdzeń |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 — A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 — WYDNIAJ](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 — A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 — A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 — A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 – D14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [D1_v2 — D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 — DS14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [DS1_v2 — DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2 — F72s_v2](fsv2-series.md) |195 – 210 * | 2:1\*\*\* |
| [F1 — F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s — F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1-G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 — GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 – 216 * * | 1:1 |
| [HC](hc-series.md) |297 – 315 * | 1:1 |
| [L4s — L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 — L80s_v2](lsv2-series.md) |150 – 175 * * | 2:1 |
| [M](m-series.md) | 160 – 180 | 2:1\*\*\* |

\* ACUs z technologią Intel® Turbo, aby zwiększyć częstotliwość procesora CPU i zwiększyć wydajność.  Stopień wzrostu wydajności może się różnić w zależności od rozmiaru maszyny wirtualnej, obciążenia i innych obciążeń uruchomionych na tym samym hoście.
\* * ACUs korzystaj z technologii AMD® zwiększać częstotliwość procesora CPU i zwiększyć wydajność.  Stopień wzrostu wydajności może się różnić w zależności od rozmiaru maszyny wirtualnej, obciążenia i innych obciążeń uruchomionych na tym samym hoście.
Funkcja Hyper-Threading i możliwość uruchamiania wirtualizacji zagnieżdżonej

Poniżej znajdują się linki do dodatkowych informacji o różnych rozmiarach:

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
