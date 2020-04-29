---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335013"
---
| Zasób | Limit |
| --- | --- |
| [Maszyny wirtualne](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na usługę w chmurze<sup>1</sup> |50 |
| Wejściowe punkty końcowe na usługę w chmurze<sup>2</sup> |150 |

<sup>1</sup> Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania zamiast Azure Resource Manager są automatycznie przechowywane w usłudze w chmurze. Do usługi w chmurze można dodać więcej maszyn wirtualnych na potrzeby równoważenia obciążenia i dostępności. 

<sup>2</sup> Wejściowe punkty końcowe umożliwiają komunikację z maszyną wirtualną spoza usługi w chmurze maszyny wirtualnej. Maszyny wirtualne w tej samej usłudze w chmurze lub sieci wirtualnej mogą komunikować się automatycznie ze sobą. Aby uzyskać więcej informacji, zobacz [jak skonfigurować punkty końcowe na maszynie wirtualnej](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
