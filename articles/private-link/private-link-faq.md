---
title: Azure Private Link — często zadawane pytania
description: Dowiedz się więcej o usłudze Azure Private link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496477"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Private Link — często zadawane pytania

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Co to jest prywatny punkt końcowy platformy Azure i usługa łącza prywatnego platformy Azure?

- **[Prywatny punkt końcowy platformy Azure](private-endpoint-overview.md)**: prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Możesz użyć prywatnych punktów końcowych, aby nawiązać połączenie z usługą Azure PaaS, która obsługuje link prywatny lub do własnej usługi linku prywatnego.
- **[Usługa linków prywatnych platformy Azure](private-link-service-overview.md)**: usługa link prywatny platformy Azure to usługa utworzona przez dostawcę usług. Obecnie usługa linku prywatnego może być dołączona do konfiguracji adresu IP frontonu usługa Load Balancer w warstwie Standardowa. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Jak jest wysyłany ruch w przypadku korzystania z prywatnego linku?
Ruch jest wysyłany prywatnie przy użyciu sieci szkieletowej firmy Microsoft. Nie przechodzi ona przez Internet. Łącze prywatne platformy Azure nie przechowuje danych klienta.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Jaka jest różnica między punktami końcowymi usługi i prywatnymi punktami końcowymi?
- Prywatne punkty końcowe zapewniają dostęp sieciowy do określonych zasobów za daną usługę zapewniającą szczegółowe segmentacji. Ruch może nawiązać połączenie z zasobem usługi lokalnie bez używania publicznych punktów końcowych.
- Punkt końcowy usługi pozostaje publicznie w adresie IP.  Prywatny punkt końcowy to prywatny adres IP w przestrzeni adresowej sieci wirtualnej, w której jest skonfigurowany prywatny punkt końcowy.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Jaka jest relacja między usługą link prywatny i prywatnym punktem końcowym?
Wiele typów zasobów połączenia prywatnego obsługuje dostęp za pośrednictwem prywatnego punktu końcowego. Zasoby obejmują usługi Azure PaaS Services i własną usługę łącza prywatnego. Jest to relacja jeden do wielu. 

Usługa linków prywatnych odbiera połączenia z wielu prywatnych punktów końcowych. Prywatny punkt końcowy nawiązuje połączenie z jedną usługą linku prywatnego.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>Czy jest konieczne wyłączenie zasad sieciowych dla prywatnego linku
Tak. Aby zapewnić prawidłowe działanie zasad sieciowych, zarówno prywatny punkt końcowy, jak i usługa łącza prywatnego muszą wyłączyć zasady sieciowe. Oba mają właściwości niezależne od siebie.

## <a name="private-endpoint"></a>Prywatny punkt końcowy 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Czy mogę utworzyć wiele prywatnych punktów końcowych w tej samej sieci wirtualnej? Czy mogą łączyć się z różnymi usługami? 
Tak. W tej samej sieci wirtualnej lub podsieci można mieć wiele prywatnych punktów końcowych. Mogą łączyć się z różnymi usługami.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Czy dla prywatnych punktów końcowych jest wymagana dedykowana podsieć? 
Nie. Nie jest wymagana dedykowana podsieć dla prywatnych punktów końcowych. Możesz wybrać prywatny adres IP punktu końcowego z dowolnej podsieci z sieci wirtualnej, w której została wdrożona usługa.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Czy prywatny punkt końcowy nawiązuje połączenie z usługami linków prywatnych w ramach dzierżaw Azure Active Directory? 
Tak. Prywatne punkty końcowe mogą łączyć się z usługami linków prywatnych lub z usługą Azure PaaS w dzierżawach Azure Active Directory. Prywatne punkty końcowe łączące się między dzierżawcami wymagają ręcznego zatwierdzenia żądania. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Czy prywatny punkt końcowy nawiązuje połączenie z zasobami usługi Azure PaaS w różnych regionach platformy Azure?
Tak. Prywatne punkty końcowe mogą łączyć się z zasobami usługi Azure PaaS w różnych regionach platformy Azure.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Czy mogę zmodyfikować prywatny interfejs sieciowy punktu końcowego (NIC)?
Po utworzeniu prywatnego punktu końcowego jest przypisana karta sieciowa tylko do odczytu. Nie można jej modyfikować i pozostanie w cyklu życia prywatnego punktu końcowego.

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>Jak mogę zapewnić dostępność podczas korzystania z prywatnych punktów końcowych w przypadku awarii regionalnych?

Prywatne punkty końcowe to zasoby o wysokiej dostępności z umową SLA 99,99% [[SLA dla prywatnego linku platformy Azure]](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/). Jednak ponieważ są to zasoby regionalne, dowolna awaria regionu platformy Azure może mieć wpływ na dostępność. Aby zapewnić dostępność w przypadku awarii regionalnych, można wdrożyć wiele elementów PEs podłączonych do tego samego zasobu docelowego w różnych regionach. W ten sposób, jeśli jeden region ulegnie awarii, można nadal kierować ruchem do scenariuszy odzyskiwania za pomocą środowiska PE w innym regionie, aby uzyskać dostęp do zasobu docelowego. Aby uzyskać informacje na temat sposobu obsługi błędów regionalnych na stronie usługi docelowej, zapoznaj się z dokumentacją usługi dotyczącą trybu failover i odzyskiwania. Ruch związany z linkiem prywatnym jest zgodny z Azure DNS rozpoznawanie docelowego punktu końcowego. 


## <a name="private-link-service"></a>Usługa łącza prywatnego
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Jakie są wymagania wstępne dotyczące tworzenia usługi linku prywatnego? 
Frontony usługi powinny znajdować się w sieci wirtualnej i za usługa Load Balancer w warstwie Standardowa.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Jak mogę skalować moją usługę linku prywatnego? 
Usługę linku prywatnego można skalować na kilka różnych sposobów: 
- Dodaj maszyny wirtualne zaplecza do puli za usługa Load Balancer w warstwie Standardowa 
- Dodaj adres IP do usługi łącza prywatnego. Zezwalamy na maksymalnie 8 adresów IP na usługę łącza prywatnego.  
- Dodaj nową usługę linku prywatnego do usługa Load Balancer w warstwie Standardowa. Zezwalamy na maksymalnie osiem usług linków prywatnych na moduł równoważenia obciążenia.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>Co to jest konfiguracja protokołu IP NAT (translator adresów sieciowych) używana w usłudze link prywatny? Jak można skalować w zakresie dostępnych portów i połączeń? 

Konfiguracja protokołu IP translatora adresów sieciowych gwarantuje, że nie występuje konflikt adresów IP między źródłem (po stronie klienta) a przestrzenią docelową (dostawcy usługi), dostarczając źródłowy translator adresów sieciowych na stronie docelowej (po stronie usługodawcy). Adres IP translatora adresów sieciowych będzie wyświetlany jako źródłowy adres IP dla wszystkich pakietów odebranych przez usługę i docelowy adres IP dla wszystkich pakietów wysłanych przez usługę.  Adres IP NAT można wybrać z dowolnej podsieci w sieci wirtualnej dostawcy usług. 

Każdy adres IP translatora adresów sieciowych zapewnia 64 KB połączeń TCP (64 KB) na maszynę wirtualną za usługa Load Balancer w warstwie Standardowa. Aby skalować i dodawać więcej połączeń, można dodać nowe adresy IP translatora adresów sieciowych lub dodać więcej maszyn wirtualnych za usługa Load Balancer w warstwie Standardowa. Wykonanie tej operacji spowoduje przeskalowanie dostępności portu i umożliwi obsługę większej liczby połączeń. Połączenia będą dystrybuowane między adresami IP i maszynami wirtualnymi NAT za usługa Load Balancer w warstwie Standardowa.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Czy mogę połączyć usługę z wieloma prywatnymi punktami końcowymi?
Tak. Jedna usługa linku prywatnego może odbierać połączenia z wielu prywatnych punktów końcowych. Jednak jeden prywatny punkt końcowy może nawiązać połączenie tylko z jedną usługą linku prywatnego.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Jak należy kontrolować ekspozycję mojej usługi linku prywatnego?
Można kontrolować narażenie przy użyciu konfiguracji widoczności w usłudze link prywatny. Widoczność obsługuje trzy ustawienia:

- **Brak** — subskrypcje z dostępem za pomocą usługi Azure RBAC mogą zlokalizować usługę. 
- Tylko **restrykcyjne** subskrypcje, które są zatwierdzone oraz dostęp z użyciem usługi Azure RBAC, mogą zlokalizować usługę. 
- **Wszyscy — wszyscy** mogą zlokalizować usługę. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Czy można utworzyć usługę linku prywatnego z podstawowym modułem równoważenia obciążenia? 
Nie. Usługa linku prywatnego za pośrednictwem podstawowego modułu równoważenia obciążenia nie jest obsługiwana.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Czy dedykowana podsieć jest wymagana dla usługi link prywatny? 
Nie. Usługa link prywatny nie wymaga dedykowanej podsieci. Możesz wybrać dowolną podsieć w sieci wirtualnej, w której została wdrożona usługa.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jestem dostawcą usług przy użyciu prywatnego linku platformy Azure. Czy muszę mieć pewność, że wszyscy klienci mają unikatową przestrzeń adresów IP i nie nakładają się na przestrzeń adresową IP? 
Nie. Usługa Azure Private Link udostępnia te funkcje. Nie trzeba mieć nienakładających się przestrzeni adresowych z przestrzenią adresową klienta. 

##  <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Private link](private-link-overview.md)
