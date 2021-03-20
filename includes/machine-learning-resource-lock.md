---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204470"
---
System Azure pozwala na umieszczenie _blokad_ w zasobach, dzięki czemu nie można ich usunąć lub tylko do odczytu. __Zablokowanie zasobu może prowadzić do nieoczekiwanych wyników.__ Niektóre operacje, które nie wydają się do modyfikacji zasobu, rzeczywiście wymagają akcji blokowanych przez blokadę. 

Na przykład zastosowanie blokady usuwania do grupy zasobów dla obszaru roboczego uniemożliwi operacje skalowania klastrów obliczeniowych platformy Azure ML.

Aby uzyskać więcej informacji na temat blokowania zasobów, zobacz [blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](../articles/azure-resource-manager/management/lock-resources.md).