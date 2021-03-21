---
title: Przegląd jednostki obliczeniowej platformy Azure
description: Przegląd koncepcji jednostek obliczeniowych platformy Azure. ACU umożliwia porównanie wydajności procesora CPU w jednostkach SKU platformy Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: azure-compute-unit
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: ae37771cd9e48f450ae538174b9bed9cfa866f9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565533"
---
# <a name="azure-compute-unit-acu"></a>Jednostka obliczeniowa platformy Azure (ACU)

Koncepcja jednostki obliczeniowej platformy Azure (ACU) zapewnia sposób porównywania wydajności obliczeniowej (CPU) w ramach jednostek SKU platformy Azure. Ten parametr pomoże łatwo zidentyfikować jednostkę SKU, która najprawdopodobniej spełni określone potrzeby związane z wydajnością. ACU jest obecnie ustandaryzowana na niewielkich maszynach wirtualnych (Standard_A1) o 100, a wszystkie inne jednostki SKU reprezentują znacznie większą szybkość działania jednostki SKU w ramach standardowego testu porównawczego

* ACUs z technologią Intel® Turbo, aby zwiększyć częstotliwość procesora CPU i zwiększyć wydajność.  Stopień wzrostu wydajności może się różnić w zależności od rozmiaru maszyny wirtualnej, obciążenia i innych obciążeń uruchomionych na tym samym hoście.

* * ACUs korzystaj z technologii AMD® zwiększać częstotliwość procesora CPU i zwiększyć wydajność.  Stopień wzrostu wydajności może się różnić w zależności od rozmiaru maszyny wirtualnej, obciążenia i innych obciążeń uruchomionych na tym samym hoście.

Funkcja Hyper-Threading i możliwość uruchamiania wirtualizacji zagnieżdżonej

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
| [B](sizes-b-series-burstable.md) |Różnie | Różnie |
| [D1 – D14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [D1_v2 — D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 — DS14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [DS1_v2 — DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230 – 260 * * | 2:1 |
| [Dasv4](dav4-dasv4-series.md) |230 – 260 * * | 2:1 |
| [Dv4](dv4-dsv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 – 190 * | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230 – 260 * * | 2:1 |
| [Easv4](eav4-easv4-series.md) | 230 – 260 * * | 2:1 |
| [Ev4](ev4-esv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [F2s_v2 — F72s_v2](fsv2-series.md) |195 – 210 * | 2:1\*\*\* |
| [F1 — F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s — F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1-G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 — GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [C](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 – 216 * * | 1:1 |
| [WĘGLOWODOR](hc-series.md) |297 – 315 * | 1:1 |
| [L4s — L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 — L80s_v2](lsv2-series.md) |150 – 175 * * | 2:1 |
| [Mol](m-series.md) | 160 – 180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230 – 260 * * | 2:1 |

Poniżej znajdują się linki do dodatkowych informacji o różnych rozmiarach:

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
