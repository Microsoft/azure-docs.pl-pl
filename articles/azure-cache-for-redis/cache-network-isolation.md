---
title: Pamięć podręczna platformy Azure dla opcji izolacji sieci Redis
description: W tym artykule dowiesz się, jak określić najlepsze rozwiązanie do izolacji sieci dla Twoich potrzeb. Zapoznajemy się z podstawowymi informacjami na temat powiązań prywatnych platformy Azure, iniekcji platformy Azure Virtual Network (VNet) i reguł zapory platformy Azure wraz z ich zaletami i ograniczeniami.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621522"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Pamięć podręczna platformy Azure dla opcji izolacji sieci Redis 
W tym artykule dowiesz się, jak określić najlepsze rozwiązanie do izolacji sieci dla Twoich potrzeb. Zapoznajemy się z podstawowymi informacjami na temat powiązań prywatnych platformy Azure, iniekcji platformy Azure Virtual Network (VNet) i reguł zapory platformy Azure wraz z ich zaletami i ograniczeniami.  

## <a name="azure-private-link-public-preview"></a>Prywatny link do platformy Azure (publiczna wersja zapoznawcza) 
Łącze prywatne platformy Azure zapewnia prywatne połączenie z sieci wirtualnej z usługami PaaS platformy Azure. Upraszcza to architekturę sieci i zabezpiecza połączenie między punktami końcowymi na platformie Azure, eliminując narażenie danych na publiczny Internet. 

### <a name="advantages"></a>Zalety
* Obsługiwane w usłudze Azure cache w warstwach Podstawowa, standardowa i Premium dla wystąpień Redis. 
* Za pomocą [linku prywatnego platformy Azure](../private-link/private-link-overview.md)można nawiązać połączenie z wystąpieniem usługi Azure cache z sieci wirtualnej za pośrednictwem prywatnego punktu końcowego, który ma przypisany prywatny adres IP w podsieci w sieci wirtualnej. Dzięki temu wystąpienia pamięci podręcznej są dostępne zarówno w sieci wirtualnej, jak i publicznie.  
* Po utworzeniu prywatnego punktu końcowego dostęp do sieci publicznej może być ograniczony przez `publicNetworkAccess` flagę. Ta flaga jest domyślnie ustawiona na wartość `Disabled` , która zezwala na dostęp do łącza prywatnego. Możesz ustawić wartość na `Enabled` lub `Disabled` przy użyciu żądania patch. Aby uzyskać więcej informacji, zobacz [Azure cache for Redis za pomocą prywatnego linku platformy Azure (wersja zapoznawcza)](cache-private-link.md). 
* Wszystkie zależności zewnętrznej pamięci podręcznej nie będą miały wpływu na reguły sieciowej grupy zabezpieczeń sieci wirtualnej.

### <a name="limitations"></a>Ograniczenia 
* Sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń) są wyłączone dla prywatnych punktów końcowych. Jeśli jednak w podsieci istnieją inne zasoby, wymuszanie sieciowej grupy zabezpieczeń będzie miało zastosowanie do tych zasobów.
* Replikacja geograficzna, reguły zapory, obsługa konsoli portalu, wiele punktów końcowych dla klastrowanej pamięci podręcznej, trwałość do zapory i wstrzyknięte pamięci podręczne nie są jeszcze obsługiwane. 
* Aby nawiązać połączenie z klastrowaną pamięcią podręczną, należy `publicNetworkAccess` ustawić wartość `Disabled` i mieć tylko jedno połączenie prywatnego punktu końcowego.

> [!NOTE]
> Podczas dodawania prywatnego punktu końcowego do wystąpienia pamięci podręcznej cały ruch Redis zostanie przeniesiony do prywatnego punktu końcowego ze względu na system DNS.
> Upewnij się, że poprzednie reguły zapory są dostosowane przed.  
>
>

## <a name="azure-virtual-network-injection"></a>Iniekcja Virtual Network platformy Azure 
Sieć wirtualna jest podstawowym blokiem konstrukcyjnym sieci prywatnej na platformie Azure. Sieć wirtualna umożliwia bezpieczne komunikowanie się ze sobą za pomocą wielu zasobów platformy Azure, Internetu i sieci lokalnych. Sieć wirtualna jest taka sama jak w przypadku tradycyjnej sieci, która będzie działać w Twoim centrum danych, ale z zaletami infrastruktury, skalowalności, dostępności i izolacji platformy Azure. 

### <a name="advantages"></a>Zalety
* Gdy usługa Azure cache for Redis jest skonfigurowana przy użyciu sieci wirtualnej, nie jest ona publicznie adresowana i jest dostępna tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej.  
* Gdy sieć wirtualna jest łączona z ograniczonymi zasadami sieciowej grupy zabezpieczeń, pomaga zmniejszyć ryzyko związane z eksfiltracji danych. 
* Wdrożenie sieci wirtualnej zapewnia ulepszone zabezpieczenia i izolację pamięci podręcznej platformy Azure dla usługi Redis, a także podsieci, zasady kontroli dostępu i inne funkcje w celu dodatkowego ograniczenia dostępu. 
* Replikacja geograficzna jest obsługiwana. 

### <a name="limitations"></a>Ograniczenia
* Wstrzyknięte pamięci podręczne są dostępne tylko dla pamięci podręcznej Premium platformy Azure dla Redis. 
* W przypadku korzystania z pamięci podręcznej z wstrzykiwaną siecią wirtualną należy otworzyć sieć wirtualną w celu buforowania zależności, takich jak listy CRL/PKI, AKV, Azure Storage, Azure Monitor i wiele innych.  


## <a name="azure-firewall-rules"></a>Reguły zapory platformy Azure
[Zapora systemu Azure](../firewall/overview.md) to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby sieci wirtualnej platformy Azure. Jest to w pełni stanowa Zapora jako usługa z wbudowaną wysoką dostępnością i nieograniczoną skalowalnością chmury. Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych.  

### <a name="advantages"></a>Zalety
* Po skonfigurowaniu reguł zapory tylko połączenia klienckie z określonych zakresów adresów IP mogą łączyć się z pamięcią podręczną. Połączenia z usługi Azure cache dla systemów monitorowania Redis są zawsze dozwolone, nawet jeśli są skonfigurowane reguły zapory. Zdefiniowane reguły sieciowej grupy zabezpieczeń są również dozwolone.  

### <a name="limitations"></a>Ograniczenia
* Reguły zapory mogą być używane w połączeniu z dowolnymi pamięciami podręcznymi, ale nie z obecnie prywatnymi punktami końcowymi. 


## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak skonfigurować dyskową pamięć podręczną z dodaną pamięcią podręczną dla [wystąpienia usługi Redis w warstwie Premium](cache-how-to-premium-vnet.md).  
* Dowiedz się, jak skonfigurować [reguły zapory dla wszystkich warstw usługi Azure cache for Redis](cache-configure.md#firewall). 
* Dowiedz się, jak [skonfigurować prywatne punkty końcowe dla wszystkich warstw usługi Azure cache for Redis](cache-private-link.md).