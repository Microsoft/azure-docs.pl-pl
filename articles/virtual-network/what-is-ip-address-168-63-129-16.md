---
title: Co to jest adres IP 168.63.129.16? | Microsoft Docs
description: Dowiedz się więcej o adresie IP 168.63.129.16, w którym jest używany do ułatwienia kanału komunikacyjnego z zasobami platformy Azure.
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
ms.openlocfilehash: 1e304bc30a48c92fdff576723dae6af1e26ef3da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222585"
---
# <a name="what-is-ip-address-1686312916"></a>Co to jest adres IP 168.63.129.16?

Adres IP 168.63.129.16 to wirtualny publiczny adres IP, który służy do ułatwienia kanału komunikacyjnego z zasobami platformy Azure. Klienci mogą definiować dowolną przestrzeń adresową prywatnej sieci wirtualnej na platformie Azure. W związku z tym zasoby platformy Azure muszą być prezentowane jako unikatowy publiczny adres IP. Ten wirtualny publiczny adres IP umożliwia wykonywanie następujących czynności:

- Umożliwia agentowi maszyny wirtualnej komunikowanie się z platformą Azure w celu zasygnalizowania, że jest w stanie "gotowe".
- Umożliwia komunikację z serwerem wirtualnym DNS w celu zapewnienia przefiltrowanego rozpoznawania nazw do zasobów (takich jak maszyna wirtualna), które nie mają niestandardowego serwera DNS. To filtrowanie gwarantuje, że klienci mogą rozpoznać tylko nazwy hostów swoich zasobów.
- Włącza [sondy kondycji z modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-custom-probe-overview.md) w celu określenia stanu kondycji maszyn wirtualnych.
- Umożliwia maszynie wirtualnej uzyskanie dynamicznego adresu IP z usługi DHCP na platformie Azure.
- Włącza komunikaty pulsu agenta gościa dla roli PaaS.

> [!NOTE]
> W scenariuszu sieci niewirtualnej (klasyczny) zamiast 168.63.129.16 jest używany prywatny adres IP. Ten prywatny adres IP jest dynamicznie odnajdowany przy użyciu protokołu DHCP. Reguły zapory specyficzne dla 168.63.129.16 muszą być dostosowane odpowiednio do potrzeb.

## <a name="scope-of-ip-address-1686312916"></a>Zakres adresów IP 168.63.129.16

Publiczny adres IP 168.63.129.16 jest używany we wszystkich regionach i wszystkich chmurach narodowych. Ten specjalny publiczny adres IP należy do firmy Microsoft i nie zmieni się. Zalecamy Zezwalanie na ten adres IP w ramach zasad zapory lokalnych (w maszynie wirtualnej) (kierunek wychodzący). Komunikacja między tym specjalnym adresem IP a zasobami jest bezpieczna, ponieważ tylko wewnętrzna platforma platformy Azure może uzyskać komunikat z tego adresu IP. Jeśli ten adres jest zablokowany, może wystąpić nieoczekiwane zachowanie w różnych scenariuszach. 168.63.129.16 jest [wirtualnym adresem IP węzła hosta](./network-security-groups-overview.md#azure-platform-considerations) i nie podlega trasom zdefiniowanym przez użytkownika.

- Agent maszyny wirtualnej wymaga komunikacji wychodzącej za pośrednictwem portów 80/TCP i 32526/TCP z WireServer (168.63.129.16). Powinny być one otwarte w lokalnej zaporze na maszynie wirtualnej. Komunikacja na tych portach z 168.63.129.16 nie podlega skonfigurowanym grupom zabezpieczeń sieci.

- 168.63.129.16 mogą udostępniać usługi DNS dla maszyny wirtualnej. Jeśli nie jest to potrzebne, ruch wychodzący do 168.63.129.16 portów 53/UDP i 53/TCP można zablokować w lokalnej zaporze na maszynie wirtualnej.

  Domyślnie komunikacja DNS nie podlega skonfigurowanym grupom zabezpieczeń sieci, chyba że jest to celowe wykorzystanie tagu usługi [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) . Aby zablokować ruch DNS do Azure DNS za sieciowej grupy zabezpieczeń, Utwórz regułę ruchu wychodzącego w celu odblokowania ruchu [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)i określ "*" jako "docelowe zakresy portów" i "any" jako protokół.

- Jeśli maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia, komunikacja z [sondą kondycji](../load-balancer/load-balancer-custom-probe-overview.md) powinna być dozwolona z 168.63.129.16. Domyślna konfiguracja grupy zabezpieczeń sieci ma regułę, która zezwala na tę komunikację. Ta reguła wykorzystuje tag usługi [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) . W razie potrzeby można zablokować ten ruch przez skonfigurowanie sieciowej grupy zabezpieczeń, co spowoduje niepowodzenie sond.

## <a name="next-steps"></a>Następne kroki

- [Grupy zabezpieczeń](./network-security-groups-overview.md)
- [Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń](manage-network-security-group.md)