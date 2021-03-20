---
title: Przypisywanie publicznych adresów IP po przejściu w tryb failover za pomocą Azure Site Recovery
description: Opisuje sposób konfigurowania publicznych adresów IP przy użyciu Azure Site Recovery i platformy Azure Traffic Manager na potrzeby odzyskiwania po awarii i migracji
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 01c2f61dcf024e8c9dbbd5b2ee11a479b3c16305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86130280"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Skonfiguruj publiczne adresy IP po przejściu w tryb failover

Publiczne adresy IP umożliwiają zasobom internetowym komunikowanie się w ramach ruchu przychodzącego z zasobami platformy Azure. Publiczne adresy IP umożliwiają również zasobom platformy Azure komunikowanie się w ramach ruchu wychodzącego z Internetem i publicznymi usługami platformy Azure za pomocą adresu IP przypisanego do zasobu.
- Komunikacja przychodząca z Internetu do zasobu, taka jak Azure Virtual Machines (maszyna wirtualna), Azure Application Gateways, Azure Load Gateways, Azure sieci VPN i inne. Można nadal komunikować się z niektórymi zasobami, takimi jak maszyny wirtualne, z Internetu, jeśli maszyna wirtualna nie ma przypisanego publicznego adresu IP, pod warunkiem, że maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia, a moduł równoważenia obciążenia ma przypisany publiczny adres IP.
- Łączność wychodząca z Internetem przy użyciu przewidywalnego adresu IP. Na przykład maszyna wirtualna może komunikować się z Internetem bez przypisanego do niego publicznego adresu IP, ale jego adres jest domyślnie przetłumaczony przez platformę Azure do nieprzewidywalnego adresu publicznego. Przypisanie publicznego adresu IP do zasobu pozwala dowiedzieć się, który adres IP jest używany do połączenia wychodzącego. Chociaż przewidywalna, adres może się zmienić, w zależności od wybranej metody przypisania. Aby uzyskać więcej informacji, zobacz [Tworzenie publicznego adresu IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Aby dowiedzieć się więcej o połączeniach wychodzących z zasobów platformy Azure, zobacz [Omówienie połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

W Azure Resource Manager publiczny adres IP jest zasobem, który ma własne właściwości. Niektóre zasoby, z którymi możesz powiązać zasób publicznego adresu IP, to:

* Interfejsy sieciowe maszyny wirtualnej
* Moduły równoważenia obciążenia dostępne z Internetu
* Bramy sieci VPN
* Bramy aplikacji

W tym artykule opisano, jak można używać publicznych adresów IP z Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Przypisanie publicznego adresu IP przy użyciu planu odzyskiwania

Nie można zachować publicznego adresu IP aplikacji produkcyjnej **w trybie failover**. Do obciążeń przetworzonych w ramach procesu przełączania do trybu failover należy przypisać zasób publicznego adresu IP platformy Azure dostępny w regionie docelowym. Ten krok można wykonać ręcznie lub zautomatyzowany przy użyciu planów odzyskiwania. Plan odzyskiwania umożliwia zbieranie maszyn w grupach odzyskiwania. Ułatwia on zdefiniowanie procesu odzyskiwania systematyczne. Można użyć planu odzyskiwania, aby wprowadzić zamówienie i zautomatyzować akcje potrzebne w każdym kroku, korzystając z Azure Automation elementów Runbook w celu przełączenia w tryb failover do platformy Azure lub skryptów.

Konfiguracja jest następująca:
- Utwórz [plan odzyskiwania](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) i Grupuj swoje obciążenia w miarę potrzeb w planie.
- Dostosuj plan, dodając krok w celu dołączenia publicznego adresu IP za pomocą skryptów [elementów runbook Azure Automation](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) do maszyny wirtualnej przełączonej w tryb failover.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Przełączanie publicznego punktu końcowego przy użyciu routingu na poziomie systemu DNS

Usługa Azure Traffic Manager umożliwia routing na poziomie systemu DNS między punktami końcowymi i może pomóc w podniesieniu RTO do scenariusza odzyskiwania po [awarii](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) . 

Więcej informacji na temat scenariuszy trybu failover można znaleźć w Traffic Manager:
1. [Lokalne do trybu failover platformy Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) za pomocą Traffic Manager 
2. [Przejście do trybu failover platformy Azure do platformy Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) za pomocą Traffic Manager 

Konfiguracja jest następująca:
- Utwórz [profil Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Korzystając z metody routingu **priorytetowego** , Utwórz dwa punkty końcowe — **podstawowe** dla źródła i **trybu failover** dla platformy Azure. **Podstawowym** przypisano priorytet 1, a **tryb failover** ma przypisany priorytet 2.
- **Podstawowym** punktem końcowym może być [platforma Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) lub [zewnętrzna](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) w zależności od tego, czy środowisko źródłowe znajduje się na platformie Azure, czy poza nią.
- Punkt końcowy **trybu failover** jest tworzony jako punkt końcowy **platformy Azure** . Użyj **statycznego publicznego adresu IP** , ponieważ będzie to zewnętrzny punkt końcowy dla Traffic Manager w zdarzeniu awarii.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [Traffic Manager z Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Dowiedz się więcej na temat Traffic Manager [metod routingu](../traffic-manager/traffic-manager-routing-methods.md).
- Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.
