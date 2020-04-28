---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: f0e575af51f952a80fe42102b033727713c75cf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183656"
---
## <a name="configuration-scenario"></a>Scenariusz konfiguracji

W tym scenariuszu tworzymy wewnętrzny moduł równoważenia obciążenia w sieci wirtualnej, co zostało pokazane na poniższej ilustracji:

![Scenariusz wewnętrznego modułu równoważenia obciążenia](./media/load-balancer-get-started-ilb-scenario-include/figure1.png)

Konfiguracja w naszym scenariuszu jest następująca:

* Dwie maszyny wirtualne o nazwach **DB1** i **DB2**
* Punkty końcowe dla wewnętrznego modułu równoważenia obciążenia
* Wewnętrzny moduł równoważenia obciążenia
