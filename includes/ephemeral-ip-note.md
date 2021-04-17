---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529562"
---
> [!NOTE]
> Platforma Azure udostępnia efemeralny adres IP dla usługi Azure Virtual Machines, które nie mają przypisanego publicznego adresu IP lub znajdują się w puli zaplecza wewnętrznego serwera Azure Load Balancer. Efemeralny mechanizm adresów IP zapewnia wychodzący adres IP, który nie jest konfigurowalny. 
>
>Efemeralny adres IP jest wyłączony, gdy publiczny adres IP jest przypisany do maszyny wirtualnej lub maszyna wirtualna jest umieszczana w puli zaplecza serwera usługa Load Balancer w warstwie Standardowa z regułami ruchu wychodzącego lub bez nich. Jeśli [zasób Translator adresów sieciowych usługi Azure Virtual Network](../articles/virtual-network/nat-overview.md) zostanie przypisany do podsieci maszyny wirtualnej, efemeralny adres IP zostanie wyłączony.
>
> Aby uzyskać więcej informacji na temat połączeń wychodzących na platformie Azure, zobacz Using Source Network Address Translation (SNAT) for outbound connections (Używanie translatora źródłowych adresów [sieciowych (SNAT) dla połączeń wychodzących).](../articles/load-balancer/load-balancer-outbound-connections.md)