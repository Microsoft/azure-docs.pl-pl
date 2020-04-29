---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334670"
---
**Publiczne strefy DNS**

| Zasób | Limit |
| --- | --- |
| Strefy DNS publiczny na subskrypcję |250 <sup>1</sup> |
| Zestawy rekordów na publiczną strefę DNS |10 000 <sup>1</sup> |
| Rekordy na zestaw rekordów w publicznej strefie DNS |20 |
| Liczba rekordów aliasów dla pojedynczego zasobu platformy Azure |20|
| Strefy Prywatna strefa DNS na subskrypcję |1000|
| Zestawy rekordów na prywatną strefę DNS |25 000|
| Rekordy na zestaw rekordów dla prywatnych stref DNS |20|
| Linki Virtual Network na prywatną strefę DNS |1000|
| Linki sieci wirtualnych na prywatne strefy DNS z włączoną rejestracją autorejestrowania |100|
| Liczba prywatnych stref DNS, z którymi można połączyć sieć wirtualną z włączoną funkcją autorejestracji |1|
| Liczba prywatnych stref DNS, do których można połączyć sieć wirtualną |1000|
| Liczba zapytań DNS wysyłanych przez maszynę wirtualną do Azure DNS rozpoznawania nazw na sekundę |500 <sup>2</sup> |
| Maksymalna liczba zapytań DNS umieszczonych w kolejce (oczekiwanie na odpowiedź) na maszynę wirtualną |200 <sup>2</sup> |

<sup>1</sup> Jeśli musisz zwiększyć te limity, skontaktuj się z pomocą techniczną platformy Azure.

<sup>2</sup> Limity te są stosowane do każdej pojedynczej maszyny wirtualnej, a nie na poziomie sieci wirtualnej. Zapytania DNS przekraczające te limity są usuwane.