---
title: Co to jest adres IP 168.63.129.16? | Microsoft Docs
description: Dowiedz się więcej o 168.63.129.16 adresów IP i sposobach współdziałania z zasobami.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77114362"
---
# <a name="what-is-ip-address-1686312916"></a>Co to jest adres IP 168.63.129.16?

Adres IP 168.63.129.16 to wirtualny publiczny adres IP, który służy do ułatwienia kanału komunikacyjnego z zasobami platformy Azure. Klienci mogą definiować dowolną przestrzeń adresową prywatnej sieci wirtualnej na platformie Azure. W związku z tym zasoby platformy Azure muszą być prezentowane jako unikatowy publiczny adres IP. Ten wirtualny publiczny adres IP umożliwia wykonywanie następujących czynności:

- Umożliwia agentowi maszyny wirtualnej komunikowanie się z platformą Azure w celu zasygnalizowania, że jest w stanie "gotowe".
- Umożliwia komunikację z serwerem wirtualnym DNS w celu zapewnienia przefiltrowanego rozpoznawania nazw do zasobów (takich jak maszyna wirtualna), które nie mają niestandardowego serwera DNS. To filtrowanie gwarantuje, że klienci mogą rozpoznać tylko nazwy hostów swoich zasobów.
- Włącza [sondy kondycji z modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-custom-probe-overview.md) w celu określenia stanu kondycji maszyn wirtualnych.
- Umożliwia maszynie wirtualnej uzyskanie dynamicznego adresu IP z usługi DHCP na platformie Azure.
- Włącza komunikaty pulsu agenta gościa dla roli PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Zakres adresów IP 168.63.129.16

Publiczny adres IP 168.63.129.16 jest używany we wszystkich regionach i wszystkich chmurach narodowych. Ten specjalny publiczny adres IP należy do firmy Microsoft i nie zmieni się. Zalecamy Zezwalanie na ten adres IP w ramach zasad zapory lokalnych (w maszynie wirtualnej) (kierunek wychodzący). Komunikacja między tym specjalnym adresem IP a zasobami jest bezpieczna, ponieważ tylko wewnętrzna platforma platformy Azure może uzyskać komunikat z tego adresu IP. Jeśli ten adres jest zablokowany, może wystąpić nieoczekiwane zachowanie w różnych scenariuszach. 168.63.129.16 jest [wirtualnym adresem IP węzła hosta](../virtual-network/security-overview.md#azure-platform-considerations) i nie podlega trasom zdefiniowanym przez użytkownika.

- Agent maszyny wirtualnej wymaga komunikacji wychodzącej przez porty 80, 443, 32526 z WireServer (168.63.129.16). Powinny być one otwarte w lokalnej zaporze na maszynie wirtualnej. Komunikacja na tych portach z 168.63.129.16 nie podlega skonfigurowanym grupom zabezpieczeń sieci.
- 168.63.129.16 mogą udostępniać usługi DNS dla maszyny wirtualnej. Jeśli nie jest to potrzebne, ten ruch może być blokowany w lokalnej zaporze na maszynie wirtualnej. Domyślnie komunikacja DNS nie podlega skonfigurowanym grupom zabezpieczeń sieci, chyba że jest to celowe wykorzystanie tagu usługi [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) .
- Jeśli maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia, komunikacja z [sondą kondycji](../load-balancer/load-balancer-custom-probe-overview.md) powinna być dozwolona z 168.63.129.16. Domyślna konfiguracja grupy zabezpieczeń sieci ma regułę, która zezwala na tę komunikację. Ta reguła wykorzystuje tag usługi [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) . W razie potrzeby można zablokować ten ruch przez skonfigurowanie sieciowej grupy zabezpieczeń, co spowoduje niepowodzenie sond.

W scenariuszu sieci niewirtualnej (klasyczny) sonda kondycji pochodzi z prywatnego adresu IP, a 168.63.129.16 nie jest używana.

## <a name="next-steps"></a>Następne kroki

- [Grupy zabezpieczeń](security-overview.md)
- [Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń](manage-network-security-group.md)
