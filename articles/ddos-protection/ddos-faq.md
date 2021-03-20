---
title: Często zadawane pytania Azure DDoS Protection standardowe
description: Często zadawane pytania dotyczące Azure DDoS Protection Standard, które pomagają zapewnić ochronę przed atakami DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 89685d1b8c3a57fa142bbbfd1114f9aa0ff0c400
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98611121"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Często zadawane pytania Azure DDoS Protection standardowe

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure DDoS Protection Standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Co to jest atak typu "odmowa usługi" (DDoS)?
Rozproszone odmowa usługi lub DDoS jest typem ataku, w którym osoba atakująca wysyła więcej żądań do aplikacji, niż może obsługiwać aplikacja. Wynikowy efekt polega na tym, że zasoby są wyczerpywane, co wpływa na dostępność i możliwość obsługi klientów przez aplikację. W ciągu ostatnich kilku lat branża zaobserwowano gwałtowny wzrost ataków, a ataki stają się bardziej zaawansowane i większe. Ataki DDoS mogą być przeznaczone dla każdego punktu końcowego, który jest publicznie dostępny przez Internet.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Co to jest usługa Azure DDoS Protection Standard?
Azure DDoS Protection Standard, w połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji, zapewnia ulepszone funkcje ograniczenia DDoS w celu obrony przed atakami DDoS. Jest on automatycznie dostosowany do ochrony określonych zasobów platformy Azure w sieci wirtualnej. Ochrona jest prosta do włączenia w przypadku każdej nowej lub istniejącej sieci wirtualnej i nie wymaga żadnych zmian aplikacji ani zasobów. Ma kilka korzyści w porównaniu z usługą podstawową, w tym rejestrowanie, alerty i dane telemetryczne. Aby uzyskać więcej informacji, zobacz [omówienie Azure DDoS Protection Standard](ddos-protection-overview.md) . 

## <a name="how-does-pricing-work"></a>Jak działa cennik?
Plany ochrony DDoS mają stałą miesięczną opłatą równą $2 944 miesięcznie, która obejmuje do 100 publicznych adresów IP. W przypadku ochrony dodatkowych zasobów będzie kosztować dodatkowe $30 za każdy zasób miesięcznie. 

W ramach dzierżawy może być używany jeden plan ochrony DDoS w wielu subskrypcjach, więc nie ma potrzeby tworzenia więcej niż jednego planu ochrony DDoS.

Aby uzyskać więcej informacji, zobacz [Cennik Standard Azure DDoS Protection](https://azure.microsoft.com/pricing/details/ddos-protection/) .

## <a name="is-the-service-zone-resilient"></a>Czy strefa usługi jest odporna?
Tak. Azure DDoS Protection jest domyślnie odporny na strefy.

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>Jak mogę skonfigurować usługę tak, aby była odporna na strefy?
W celu włączenia odporności stref nie trzeba wykonywać żadnych czynności konfiguracyjnych. Odporność stref na zasoby Azure DDoS Protection jest domyślnie dostępna i zarządzana przez samą usługę.

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>Co z ochroną w warstwie usług (warstwa 7)?
Klienci mogą używać usługi Azure DDoS Protection w połączeniu z zaporą aplikacji sieci Web (WAF) w celu zapewnienia ochrony zarówno w warstwie sieciowej (warstwy 3 i 4, oferowanej przez Azure DDoS Protection standard), jak i w warstwie aplikacji (warstwa 7, oferowana przez WAF). Oferty WAF obejmują usługi Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oraz oferty zapory aplikacji internetowych innych firm dostępne w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Czy usługi nie są niebezpieczne na platformie Azure bez usługi?
Usługi działające na platformie Azure są chronione przez Azure DDoS Protection podstawowe, które są stosowane do ochrony infrastruktury platformy Azure. Jednak ochrona, która chroni infrastrukturę, ma znacznie wyższy próg niż większość aplikacji ma pojemność do obsłużenia i nie zapewnia telemetrii ani alertów, dlatego w przypadku, gdy natężenie ruchu sieciowego może być postrzegane jako nieszkodliwe przez platformę, można materialne do aplikacji, która je otrzymuje. 

Dzięki dołączeniu do usługi standardowej Azure DDoS Protection aplikacja pobiera dedykowane monitorowanie w celu wykrywania ataków i progów specyficznych dla aplikacji. Usługa będzie chroniona przy użyciu profilu, który jest dostrojony do oczekiwanego natężenia ruchu sieciowego, zapewniając dużo ściślejszą ochronę przed atakami DDoS.

## <a name="what-are-the-supported-protected-resource-types"></a>Jakie są obsługiwane chronione typy zasobów?
Publiczne adresy IP w sieci wirtualnych opartych na usłudze ARM są obecnie jedynym typem chronionego zasobu. Usługi PaaS Services (Wielodostępność) nie są obsługiwane w przedstawionym obszarze. Aby uzyskać więcej informacji, zobacz [standardowe architektury referencyjne Azure DDoS Protection](ddos-protection-reference-architectures.md) .

## <a name="are-classicrdfe-protected-resources-supported"></a>Czy są obsługiwane zasoby klasyczne/frontonu reddog?
Tylko chronione zasoby ARM są obsługiwane w wersji zapoznawczej. Maszyny wirtualne w wdrożeniach klasycznych/frontonu reddog nie są obsługiwane. Pomoc techniczna nie jest obecnie planowana dla zasobów klasycznych/frontonu reddog. Aby uzyskać więcej informacji, zobacz [standardowe architektury referencyjne Azure DDoS Protection](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>Czy mogę chronić moje zasoby PaaS za pomocą DDoS Protection?
Publiczne adresy IP dołączone do wielodostępnej usługi PaaS z wieloma dzierżawcami nie są obecnie obsługiwane. Przykłady nieobsługiwanych zasobów obejmują adresy VIP magazynu, adresy VIP centrum zdarzeń i aplikacje aplikacji/Cloud Services. Aby uzyskać więcej informacji, zobacz [standardowe architektury referencyjne Azure DDoS Protection](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Czy mogę chronić zasoby lokalne przy użyciu DDoS Protection?
Należy dysponować publicznymi punktami końcowymi usługi skojarzoną z siecią wirtualną na platformie Azure, aby można było włączyć ochronę DDoS. Przykładowe projekty obejmują:
- Witryny sieci Web (IaaS) na platformie Azure i bazach danych zaplecza w Premium Datacenter. 
- Application Gateway na platformie Azure (ochrona DDoS włączona w aplikacjach App Gateway/WAF) i w witrynach sieci Web w Premium centrach danych.

Aby uzyskać więcej informacji, zobacz [standardowe architektury referencyjne Azure DDoS Protection](ddos-protection-reference-architectures.md) .

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Czy można zarejestrować domenę spoza platformy Azure i skojarzyć ją z chronionym zasobem, np. maszyną wirtualną lub ELB?
W przypadku scenariuszy publicznych adresów IP usługa DDoS Protection będzie obsługiwać dowolną aplikację, niezależnie od lokalizacji, w której skojarzona domena jest zarejestrowana lub hostowana, o ile skojarzony publiczny adres IP jest hostowany na platformie Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Czy można ręcznie skonfigurować zasady DDoS stosowane do sieci wirtualnych/publicznych adresów IP?
Nie, niestety dostosowanie zasad nie jest obecnie dostępne.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>Czy mogę dozwolonych/listy blokowania określone adresy IP?
Nie, niestety Konfiguracja ręczna nie jest teraz dostępna.

## <a name="how-can-i-test-ddos-protection"></a>Jak można testować DDoS Protection?
Zobacz [testowanie przez symulacje](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Jak długo trwa ładowanie metryk w portalu?
Metryki powinny być widoczne w portalu w ciągu 5 minut. Jeśli zasób jest objęty atakami, inne metryki będą wyświetlane w portalu w ciągu 5-7 minut. 

## <a name="does-the-service-store-customer-data"></a>Czy usługa przechowuje dane klienta?
Nie, usługa Azure DDoS Protection nie przechowuje danych klienta.
    
