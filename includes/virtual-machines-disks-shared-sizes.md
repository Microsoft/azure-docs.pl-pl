---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81008355"
---
Obecnie tylko usługi Ultra disks i Premium dysków SSD mogą włączyć dyski udostępnione. Różne rozmiary dysków mogą mieć inny `maxShares` limit, którego nie można przekroczyć podczas ustawiania `maxShares` wartości. W przypadku wersji Premium dysków SSD rozmiary dysków, które obsługują udostępnianie ich dysków, są P15 i większe.

Dla każdego dysku można zdefiniować `maxShares` wartość, która reprezentuje maksymalną liczbę węzłów, które mogą jednocześnie udostępniać dysk. Na przykład jeśli planujesz skonfigurować klaster trybu failover z 2 węzłami, należy ustawić `maxShares=2` . Wartość maksymalna jest górną granicą. Węzły mogą dołączyć lub opuścić klaster (zainstalować lub odinstalować dysk) tak długo, jak liczba węzłów jest mniejsza niż określona `maxShares` wartość.

> [!NOTE]
> `maxShares`Wartość można ustawić lub edytować tylko wtedy, gdy dysk jest odłączony od wszystkich węzłów.

### <a name="premium-ssd-ranges"></a>Zakresy SSD w warstwie Premium

W poniższej tabeli przedstawiono dozwolone maksymalne wartości dla `maxShares` rozmiaru dysków w warstwie Premium:

|Rozmiary dysków  |limit maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Wartość nie ma wpływ na limity IOPS i przepustowość dla dysku `maxShares` . Na przykład maksymalna liczba operacji we/wy dysku P15 to 1100, czy maxShares = 1 lub maxShares > 1.

### <a name="ultra-disk-ranges"></a>Zakresy Ultra Disk

Wartość minimalna `maxShares` to 1, a maksymalna `maxShares` wartość to 5. Nie ma ograniczeń dotyczących rozmiaru dla Ultra disks, każdy rozmiar Ultra Disk może korzystać z dowolnej wartości dla `maxShares` , włącznie z wartością maksymalną.