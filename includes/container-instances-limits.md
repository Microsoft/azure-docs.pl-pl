---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87384835"
---
| Zasób | Limit |
| --- | :--- |
| Standardowe grupy kontenerów jednostki SKU na region na subskrypcję | 100<sup>1</sup> |
| Dedykowane grupy kontenerów jednostek SKU na region na subskrypcję | 0<sup>1</sup> |
| Liczba kontenerów na grupę kontenerów | 60 |
| Liczba woluminów na grupę kontenerów | 20 |
| Standardowe rdzenie jednostki SKU (CPU) na region na subskrypcję | 10<sup>1, 2</sup> | 
| Standardowe rdzenie jednostki SKU (CPU) dla K80 procesora GPU na region na subskrypcję | 18<sup>1, 2</sup> |
| Standardowe rdzenie jednostki SKU (CPU) dla P100 lub V100 procesora GPU na region na subskrypcję | 0<sup>1, 2</sup> |
| Porty na adres IP | 5 |
| Rozmiar dziennika wystąpienia kontenera — uruchomione wystąpienie | 4 MB |
| Rozmiar dziennika wystąpienia kontenera — Zatrzymano wystąpienie | 16 KB lub 1 000 wierszy |
| Utworzenia kontenera na godzinę |300<sup>1</sup> |
| Utworzenia kontenera na 5 minut | 100<sup>1</sup> |
| Usunięcia kontenera na godzinę | 300<sup>1</sup> |
| Usunięcia kontenera na 5 minut | 100<sup>1</sup> |


<sup>1</sup> Aby zażądać zwiększenia limitu, Utwórz [support Request platformy Azure][azure-support]. Bezpłatne subskrypcje, w tym [bezpłatne konto platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) i oferty [platformy Azure dla studentów](https://azure.microsoft.com/offers/ms-azr-0170p/) , nie są przystosowane do zwiększania limitu przydziału. Jeśli masz bezpłatną subskrypcję, możesz [przeprowadzić uaktualnienie](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) do subskrypcji płatnej zgodnie z rzeczywistym użyciem.<br />
<sup>2</sup> Domyślny limit subskrypcji [z opcją płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem. Limit może różnić się w przypadku innych typów kategorii.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
