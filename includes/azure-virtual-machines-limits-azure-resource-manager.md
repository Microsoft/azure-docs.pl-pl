---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 651027ffd63a376ff0b8595636ece4c8f39c86e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82615998"
---
| Zasób | Limit |
| --- | --- |
| Maszyny wirtualne na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> na region. |
| Całkowita liczba rdzeni maszyn wirtualnych na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Skontaktuj się z pomocą techniczną, aby zwiększyć limit. |
| Łączna liczba rdzeni maszyn wirtualnych platformy Azure na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Skontaktuj się z pomocą techniczną, aby zwiększyć limit. |
| Maszyny wirtualne na serię, takie jak Dv2 i F, rdzenie na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Skontaktuj się z pomocą techniczną, aby zwiększyć limit. |
| [Zestawy dostępności](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na subskrypcję |2 500 na region. |
| Maszyny wirtualne na zestaw dostępności | 200 |
| Certyfikaty na subskrypcję |Bez ograniczeń<sup>2</sup> |

<sup>1</sup> Domyślne limity różnią się w zależności od typu kategorii oferty, na przykład bezpłatnej wersji próbnej i płatnej zgodnie z rzeczywistym użyciem oraz według serii, takich jak Dv2, F i G. Na przykład domyślna wartość dla subskrypcji Umowa Enterprise to 350.

<sup>2</sup> W przypadku Azure Resource Manager certyfikaty są przechowywane w Azure Key Vault. Liczba certyfikatów jest nieograniczona dla subskrypcji. Istnieje limit 1 MB certyfikatów na wdrożenie, które składa się z jednej maszyny wirtualnej lub zestawu dostępności.

> [!NOTE]
> Rdzenie maszyn wirtualnych mają limit regionalny dla regionu. Mają również limit dla regionalnych serii dla poszczególnych rozmiarów, takich jak Dv2 i F. Limity te są wykonywane osobno. Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. Ta subskrypcja może wdrażać 30 maszyn wirtualnych a1 lub 30 D1 maszyn wirtualnych albo kombinację tych dwóch, aby nie przekroczyć całkowitej liczby 30 rdzeni. Przykładem kombinacji są 10 maszyn wirtualnych a1 i 20 maszyn wirtualnych.  
> <!-- -->
> 
