---
title: Często zadawane pytania dotyczące usługi Azure Application Gateway
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/01/2020
ms.author: victorh
ms.openlocfilehash: 0e5a683c95e213a0fc37ec4f559e89abfecf034a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758910"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Często zadawane pytania dotyczące bramy aplikacji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniżej przedstawiono typowe pytania dotyczące usługi Azure Application Gateway.

## <a name="general"></a>Ogólne

### <a name="what-is-application-gateway"></a>Co to jest usługa Application Gateway?

Usługa Azure Application Gateway udostępnia kontroler dostarczania aplikacji (ADC) jako usługę. Oferuje różne możliwości równoważenia obciążenia warstwy 7 dla twoich aplikacji. Ta usługa jest wysoce dostępna, skalowalna i w pełni zarządzana przez platformę Azure.

### <a name="what-features-does-application-gateway-support"></a>Jakie funkcje obsługuje brama aplikacji?

Brama aplikacji obsługuje skalowanie automatyczne, odciążanie TLS i end-to-end TLS, zaporę aplikacji sieci web (WAF), koligacji sesji opartej na plikach cookie, routing oparty na ścieżce adresów URL, hosting wielolokajny i inne funkcje. Aby uzyskać pełną listę obsługiwanych funkcji, zobacz [Wprowadzenie do bramy aplikacji](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Czym różnią się bramy aplikacji i modułu równoważenia obciążenia platformy Azure?

Brama aplikacji jest modułem równoważenia obciążenia warstwy 7, co oznacza, że działa tylko z ruchem internetowym (HTTP, HTTPS, WebSocket i HTTP/2). Obsługuje możliwości, takie jak zakończenie TLS, koligacja sesji oparta na plikach cookie i działanie okrężne dla ruchu równoważenia obciążenia. Moduł równoważe obciążenia równoważe ruch w warstwie 4 (TCP lub UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jakie protokoły obsługuje brama aplikacji?

Brama aplikacji obsługuje protokoły HTTP, HTTPS, HTTP/2 i WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>W jaki sposób brama aplikacji obsługuje protokół HTTP/2?

Zobacz [http/2 wsparcie](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Jakie zasoby są obsługiwane jako część puli zaplecza?

Zobacz [obsługiwane zasoby zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>W jakich regionach jest dostępna brama aplikacji?

Brama aplikacji jest dostępna we wszystkich regionach globalnej platformy Azure. Jest również dostępny w [usłudze Azure China 21Vianet](https://www.azure.cn/) i [azure government.](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Czy to wdrożenie jest dedykowane dla mojej subskrypcji, czy jest współużytkowane przez klientów?

Brama aplikacji to dedykowane wdrożenie w sieci wirtualnej.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Czy brama aplikacji obsługuje przekierowanie http-to-HTTPS?

Przekierowanie jest obsługiwane. Zobacz [omówienie przekierowania bramy aplikacji](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>W jakiej kolejności są przetwarzane słuchacze?

Zobacz [kolejność przetwarzania odbiornika](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Gdzie można znaleźć adres IP bramy aplikacji i system DNS?

Jeśli używasz publicznego adresu IP jako punktu końcowego, informacje o adresie IP i DNS znajdują się w zasobie publicznego adresu IP. Lub znaleźć go w portalu, na stronie przeglądu bramy aplikacji. Jeśli używasz wewnętrznych adresów IP, znajdź informacje na stronie przeglądu.

Dla jednostki SKU w wersji 2 otwórz publiczny zasób IP i wybierz pozycję **Konfiguracja**. Pole **etykiety nazwy DNS (opcjonalnie)** jest dostępne do skonfigurowania nazwy DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Jakie są ustawienia limitu czasu keep-alive i limitu czasu bezczynnego TCP?

*Limit czasu Keep-Alive* określa, jak długo brama aplikacji będzie czekać na klienta, aby wysłać inne żądanie HTTP na trwałe połączenie przed ponownym użyciem lub zamknięciem go. *Limit czasu bezczynności TCP* określa, jak długo połączenie TCP jest otwarte w przypadku braku działania. 

*Limit czasu keep-alive* w jednostce SKU bramy aplikacji w wersji 1 wynosi 120 sekund, a w jednostce SKU w wersji 2 jest to 75 sekund. Limit *czasu bezczynności protokołu TCP* jest domyślnie 4-minutowym wirtualnym adresem IP (VIP) identyfikatora IP (FRONTON) zarówno jednostki SKU aplikacji w wersji 1, jak i w wersji 2. Nie można zmienić tych wartości.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Czy nazwa IP lub DNS zmienia się w okresie istnienia bramy aplikacji?

W jednostce SKU bramy aplikacji w wersji 1 program VIP może ulec zmianie po zatrzymaniu i uruchomieniu bramy aplikacji. Ale nazwa DNS skojarzona z bramą aplikacji nie zmienia się w ciągu okresu istnienia bramy. Ponieważ nazwa DNS nie zmienia się, należy użyć aliasu CNAME i wskazać go na adres DNS bramy aplikacji. W jednostce SKU bramy aplikacji w wersji 2 można ustawić adres IP jako statyczny, aby nazwa adresu IP i DNS nie zmieniała się przez cały okres istnienia bramy aplikacji. 

### <a name="does-application-gateway-support-static-ip"></a>Czy brama aplikacji obsługuje statyczny adres IP?

Tak, jednostka SKU bramy aplikacji w wersji 2 obsługuje statyczne publiczne adresy IP. Jednostka SKU w wersji 1 obsługuje statyczne wewnętrzne ip.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Czy brama aplikacji obsługuje wiele publicznych adresów IP w bramie?

Brama aplikacji obsługuje tylko jeden publiczny adres IP.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak duża powinna być podsieć bramy aplikacji?

Zobacz [Zagadnienia dotyczące rozmiaru podsieci bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Czy można wdrożyć więcej niż jeden zasób bramy aplikacji w jednej podsieci?

Tak. Oprócz wielu wystąpień danego wdrożenia bramy aplikacji można aprowizować inny unikatowy zasób bramy aplikacji do istniejącej podsieci, która zawiera inny zasób bramy aplikacji.

Pojedyncza podsieć nie może obsługiwać razem zarówno Standard_v2, jak i standardowej bramy aplikacji.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Czy brama aplikacji w wersji 2 obsługuje trasy zdefiniowane przez użytkownika (UDR)?

Tak, ale tylko konkretne scenariusze. Aby uzyskać więcej informacji, zobacz [Omówienie konfiguracji bramy aplikacji](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Czy brama aplikacji obsługuje nagłówki x-forwarded-for?

Tak. Zobacz [Modyfikacje żądania](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Jak długo trwa wdrażanie bramy aplikacji? Czy brama aplikacji będzie działać podczas aktualizacji?

Nowe wdrożenia jednostki SKU bramy aplikacji w wersji 1 może potrwać do 20 minut. Zmiany rozmiaru lub liczby wystąpień nie są destrukcyjne, a brama pozostaje aktywna w tym czasie.

Większość wdrożeń, które używają jednostki SKU w wersji 2 trwa około 6 minut, aby aprowizować. Jednak może to potrwać dłużej w zależności od typu wdrożenia. Na przykład wdrożenia w wielu strefach dostępności z wieloma wystąpieniami może potrwać dłużej niż 6 minut. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Czy mogę używać programu Exchange Server jako wewnętrznej bazy danych z bramą aplikacji?

Nie. Brama aplikacji nie obsługuje protokołów poczty e-mail, takich jak SMTP, IMAP i POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Czy dostępne są wskazówki dotyczące migracji z jednostki SKU w wersji 1 do jednostki SKU w wersji 2?

Tak. Aby uzyskać szczegółowe informacje, zobacz [Migrowanie bramy aplikacji platformy Azure i Zapory aplikacji sieci Web z wersji 1 do wersji 2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Czy jednostka SKU bramy aplikacji w wersji 1 będzie nadal obsługiwana?

Tak. Jednostka SKU bramy aplikacji w wersji 1 będzie nadal obsługiwana. Jednak zdecydowanie zaleca się przejście do wersji 2, aby skorzystać z aktualizacji funkcji w tej jednostce SKU. Aby uzyskać więcej informacji, zobacz [Skalowanie automatyczne i Brama aplikacji nadmiarowa strefowa w wersji 2](application-gateway-autoscaling-zone-redundant.md).

## <a name="performance"></a>Wydajność

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>W jaki sposób brama aplikacji obsługuje wysoką dostępność i skalowalność?

Jednostka SKU bramy aplikacji w wersji 1 obsługuje scenariusze wysokiej dostępności po wdrożeniu dwóch lub więcej wystąpień. Platforma Azure dystrybuuje te wystąpienia w domenach aktualizacji i błędów, aby upewnić się, że wystąpienia nie wszystkie nie wszystkie nie powiodą się w tym samym czasie. Jednostka SKU w wersji 1 obsługuje skalowalność, dodając wiele wystąpień tej samej bramy w celu współużytkowanie obciążenia.

Jednostka SKU w wersji 2 automatycznie zapewnia, że nowe wystąpienia są rozłożone na domeny błędów i domeny aktualizacji. Jeśli wybierzesz nadmiarowość stref, najnowsze wystąpienia są również rozłożone na strefy dostępności, aby zaoferować odporność na awarie strefowe.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Jak osiągnąć scenariusz odzyskiwania po awarii w centrach danych przy użyciu bramy aplikacji?

Usługa Traffic Manager służy do dystrybucji ruchu między wieloma bramami aplikacji w różnych centrach danych.

### <a name="does-application-gateway-support-autoscaling"></a>Czy brama aplikacji obsługuje skalowanie automatyczne?

Tak, jednostka SKU bramy aplikacji w wersji 2 obsługuje skalowanie automatyczne. Aby uzyskać więcej informacji, zobacz [Skalowanie automatyczne i Brama aplikacji nadmiarowa strefowa](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Czy ręczne lub automatyczne skalowanie w górę lub skalowanie w dół powoduje przestoje?

Nie. Wystąpienia są rozproszone między domenami uaktualnienia i domenami błędów.

### <a name="does-application-gateway-support-connection-draining"></a>Czy brama aplikacji obsługuje opróżnianie połączeń?

Tak. Można skonfigurować opróżnianie połączenia, aby zmienić elementy członkowskie w puli wewnętrznej bazy danych bez zakłóceń. Aby uzyskać więcej informacji, zobacz [sekcję opróżniania połączeń bramy aplikacji](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Czy mogę zmienić rozmiar instancji ze średniego na duży bez zakłóceń?

Tak.

## <a name="configuration"></a>Konfigurowanie

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Czy brama aplikacji jest zawsze wdrażana w sieci wirtualnej?

Tak. Brama aplikacji jest zawsze wdrażana w podsieci sieci wirtualnej. Ta podsieć może zawierać tylko bramy aplikacji. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące sieci wirtualnej i podsieci](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Czy brama aplikacji może komunikować się z wystąpieniami poza siecią wirtualną lub poza subskrypcją?

Tak długo, jak masz łączność IP, brama aplikacji może komunikować się z wystąpień poza siecią wirtualną, w której się znajduje. Brama aplikacji może również komunikować się z wystąpieniami poza subskrypcją, w jakiej się znajduje. Jeśli planujesz używać wewnętrznych adresów IP jako członków puli wewnętrznej bazy danych, użyj [komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) lub [bramy sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Czy można wdrożyć cokolwiek innego w podsieci bramy aplikacji?

Nie. Można jednak wdrożyć inne bramy aplikacji w podsieci.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Czy sieciowe grupy zabezpieczeń są obsługiwane w podsieci bramy aplikacji?

Zobacz [Sieciowe grupy zabezpieczeń w podsieci Bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Czy podsieć bramy aplikacji obsługuje trasy zdefiniowane przez użytkownika?

Zobacz [Trasy zdefiniowane przez użytkownika obsługiwane w podsieci Bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jakie są limity bramy aplikacji? Czy mogę zwiększyć te limity?

Zobacz [Limity bramy aplikacji](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Czy mogę jednocześnie używać bramy aplikacji zarówno dla ruchu zewnętrznego, jak i wewnętrznego?

Tak. Brama aplikacji obsługuje jeden wewnętrzny adres IP i jeden zewnętrzny adres IP na bramę aplikacji.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Czy brama aplikacji obsługuje komunikację równorzędnie sieci wirtualnej?

Tak. Komunikacja równorzędna sieci wirtualnej pomaga równoważyć obciążenie ruchu w innych sieciach wirtualnych.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Czy mogę rozmawiać z serwerami lokalnymi, gdy są połączone tunelami usługi ExpressRoute lub VPN?

Tak, o ile ruch jest dozwolony.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Czy jedna pula zaplecza może obsługiwać wiele aplikacji na różnych portach?

Architektura mikrousług jest obsługiwana. Aby sondować na różnych portach, należy skonfigurować wiele ustawień HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Czy niestandardowe sondy obsługują symbole wieloznaczne lub wyrażenia regularnego na danych odpowiedzi?

Nie. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Jak reguły routingu są przetwarzane w bramie aplikacji?

Zobacz [Kolejność reguł przetwarzania](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Co oznacza pole Host w przypadku sond niestandardowych?

Pole Host określa nazwę, do której ma być wysyłana sonda po skonfigurowaniu wielu ścieleń w bramie aplikacji. W przeciwnym razie należy użyć '127.0.0.1'. Ta wartość różni się od nazwy hosta maszyny wirtualnej. Jego format \<\>to\<\>protocol\<\>\<://\>host : port path .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Czy mogę zezwolić bramie aplikacji na dostęp tylko do kilku źródłowych adresów IP?

Tak. Zobacz [ograniczanie dostępu do określonych źródłowych usług IP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Czy mogę używać tego samego portu zarówno dla słuchaczy publicznych, jak i prywatnych?

Nie.

### <a name="does-application-gateway-support-ipv6"></a>Czy brama aplikacji obsługuje IPv6?

Brama aplikacji w wersji 2 nie obsługuje obecnie IPv6. Może działać w sieci wirtualnej z dwoma stosami przy użyciu tylko IPv4, ale podsieć bramy musi być tylko IPv4. Brama aplikacji w wersji 1 nie obsługuje sieci wirtualnych z dwoma stosami. 

## <a name="configuration---tls"></a>Konfiguracja - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Jakie certyfikaty obsługuje brama aplikacji?

Brama aplikacji obsługuje certyfikaty z podpisem własnym, certyfikaty urzędu certyfikacji, certyfikaty rozszerzonej weryfikacji (EV) i certyfikaty wieloznaczne.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Jakie mechanizmy szyfrowania obsługuje brama aplikacji?

Brama aplikacji obsługuje następujące mechanizmy szyfrowania. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Aby uzyskać informacje na temat dostosowywania opcji TLS, zobacz [Konfigurowanie wersji zasad TLS i pakietów szyfrowania w bramie aplikacji](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Czy brama aplikacji obsługuje odsyłanie ruchu do wewnętrznej bazy danych?

Tak. Brama aplikacji obsługuje odciążanie TLS i end-to-end TLS, które reencrypt ruchu do wewnętrznej bazy danych.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Czy mogę skonfigurować zasady TLS do kontrolowania wersji protokołu TLS?

Tak. Bramę aplikacji można skonfigurować tak, aby odmawiała protokołu TLS1.0, TLS1.1 i TLS1.2. Domyślnie ssl 2.0 i 3.0 są już wyłączone i nie można ich konfigurować.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Czy mogę skonfigurować mechanizmy szyfrowania i kolejność zasad?

Tak. W bramie aplikacji można [skonfigurować mechanizmy szyfrowania](application-gateway-ssl-policy-overview.md). Aby zdefiniować zasady niestandardowe, włącz co najmniej jeden z następujących mechanizmów szyfrowania. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Brama aplikacji używa SHA256 do zarządzania zapleczem.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Ile certyfikatów TLS/SSL obsługuje brama aplikacji?

Brama aplikacji obsługuje do 100 certyfikatów TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Ile certyfikatów uwierzytelniania do ponownej szyfrowania wewnętrznej obsługuje brama aplikacji?

Brama aplikacji obsługuje maksymalnie 100 certyfikatów uwierzytelniania.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Czy brama aplikacji natywnie integruje się z usługą Azure Key Vault?

Tak, jednostka SKU bramy aplikacji w wersji 2 obsługuje usługę Key Vault. Aby uzyskać więcej informacji, zobacz [Zakończenie protokołu TLS z certyfikatami Usługi Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Jak skonfigurować odbiorniki HTTPS dla witryn .com i .net? 

W przypadku routingu opartego na wielu domenach (opartych na hostach) można tworzyć odbiorniki wielolokalowe, konfigurowanie odbiorników, które używają protokołu HTTPS jako protokołu, oraz kojarzyć odbiorniki z regułami routingu. Aby uzyskać więcej informacji, zobacz [Hostowanie wielu witryn przy użyciu bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Czy mogę używać znaków specjalnych w haśle pliku .pfx?

Nie, w haśle pliku .pfx należy używać tylko znaków alfanumerycznej.

## <a name="configuration---web-application-firewall-waf"></a>Konfiguracja — zapora aplikacji sieci Web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Czy jednostka SKU WAF oferuje wszystkie funkcje dostępne w standardowej jednostce SKU?

Tak. WAF obsługuje wszystkie funkcje w standardowej jednostce SKU.

### <a name="how-do-i-monitor-waf"></a>Jak monitorować WAF?

Monitoruj WAF za pomocą rejestrowania diagnostycznego. Aby uzyskać więcej informacji, zobacz [Rejestrowanie diagnostyczne i metryki bramy aplikacji](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Czy tryb wykrywania blokuje ruch?

Nie. Tryb wykrywania rejestruje tylko ruch, który wyzwala regułę WAF.

### <a name="can-i-customize-waf-rules"></a>Czy mogę dostosować reguły WAF?

Tak. Aby uzyskać więcej informacji, zobacz [Dostosowywanie grup reguł waf i reguł](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Jakie zasady są obecnie dostępne dla WAF?

WAF obsługuje obecnie CRS [2.2.9,](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229) [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)i [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Te reguły zapewniają bezpieczeństwo według planu bazowego w przypadku większości 10 największych luk w zabezpieczeniach, które identyfikuje program OWASP (Open Web Application Security Project): 

* Ochrona przed atakami polegającymi na iniekcji SQL
* Ochrona skryptów między witrynami
* Ochrona przed typowymi atakami internetowymi, takimi jak iniekcja poleceń, przemyt żądań HTTP, podział odpowiedzi HTTP i zdalny atak na dołączanie plików
* Ochrona przed naruszeniami protokołu HTTP
* Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków
* Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów
* Wykrywanie typowych błędnych konfiguracji aplikacji (czyli Apache, IIS itd.)

Aby uzyskać więcej informacji, zobacz [Luki w zabezpieczeniach OWASP top-10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Czy WAF obsługuje ochronę DDoS?

Tak. Ochronę przed atakami DDoS można włączyć w sieci wirtualnej, w której wdrożono bramę aplikacji. To ustawienie gwarantuje, że usługa Ochrony przed atakami DDoS platformy Azure chroni również wirtualny adres IP (VIP) bramy aplikacji.

## <a name="configuration---ingress-controller-for-aks"></a>Konfiguracja — kontroler transferu danych przychodzących dla usługi AKS

### <a name="what-is-an-ingress-controller"></a>Co to jest kontroler transferu danych przychodzących?

Kubernetes umożliwia `deployment` tworzenie `service` i zasobów, aby udostępnić grupę zasobników wewnętrznie w klastrze. Aby udostępnić tę samą [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) usługę zewnętrznie, zdefiniowany jest zasób, który zapewnia równoważenie obciążenia, zakończenie TLS i hosting wirtualny oparty na nazwach.
Aby zaspokoić ten `Ingress` zasób, kontroler transferu danych przychodzących `Ingress` jest wymagane, który nasłuchuje wszelkich zmian w zasobach i konfiguruje zasady modułu równoważenia obciążenia.

Kontroler transferu danych przychodzących bramy aplikacji umożliwia [bramie aplikacji platformy Azure, która](https://azure.microsoft.com/services/application-gateway/) ma być używana jako ruch przychodzący dla [usługi Azure Kubernetes,](https://azure.microsoft.com/services/kubernetes-service/) znanej również jako klaster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Czy wystąpienie pojedynczego kontrolera transferu danych przychodzących może zarządzać wieloma bramami aplikacji?

Obecnie jedno wystąpienie kontrolera transferu danych przychodzących można skojarzyć tylko z jedną bramą aplikacji.

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Jakie typy dzienników zapewnia brama aplikacji?

Brama aplikacji udostępnia trzy dzienniki: 

* **ApplicationGatewayAccessLog**: Dziennik dostępu zawiera każde żądanie przesłane do frontonu bramy aplikacji. Dane obejmują adres IP wywołującego, żądany adres URL, opóźnienie odpowiedzi, kod zwrotny oraz bajty w i na zewnątrz. Zawiera jeden rekord na bramę aplikacji.
* **ApplicationGatewayPerformanceLog**: Dziennik wydajności przechwytuje informacje o wydajności dla każdej bramy aplikacji. Informacje obejmują przepływność w bajtach, całkowita liczba obsługiwanych żądań, liczba żądań nie powiodło się oraz liczba wystąpień wewnętrznej bazy danych w dobrej kondycji i złej kondycji.
* **ApplicationGatewayFirewallLog**: W przypadku bram aplikacji skonfigurowanych za pomocą narzędzia WAF dziennik zapory zawiera żądania rejestrowane w trybie wykrywania lub w trybie zapobiegania.

Wszystkie dzienniki są zbierane co 60 sekund. Aby uzyskać więcej informacji, zobacz [kondycja wewnętrznej bazy danych, dzienniki diagnostyczne i metryki bramy aplikacji](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Skąd mam wiedzieć, czy członkowie puli wewnętrznej bazy danych są zdrowi?

Sprawdź kondycję przy użyciu polecenia `Get-AzApplicationGatewayBackendHealth` cmdlet programu PowerShell lub portalu. Aby uzyskać więcej informacji, zobacz [Diagnostyka bramy aplikacji](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Jakie są zasady przechowywania dzienników diagnostycznych?

Dzienniki diagnostyczne przepływają do konta magazynu klienta. Klienci mogą ustawić zasady przechowywania na podstawie swoich preferencji. Dzienniki diagnostyczne mogą być również wysyłane do centrum zdarzeń lub dzienników usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [Diagnostyka bramy aplikacji](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Jak uzyskać dzienniki inspekcji bramy aplikacji?

W portalu w bloku menu bramy aplikacji wybierz dziennik **aktywności,** aby uzyskać dostęp do dziennika inspekcji. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Czy można ustawić alerty za pomocą bramy aplikacji?

Tak. W bramie aplikacji alerty są konfigurowane na metrykach. Aby uzyskać więcej informacji, zobacz [metryki bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) i [Odbieranie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Jak analizować statystyki ruchu dla bramy aplikacji?

Dzienniki dostępu można wyświetlać i analizować na kilka sposobów. Użyj dzienników usługi Azure Monitor, programu Excel, Power BI itd.

Można również użyć szablonu Menedżera zasobów, który instaluje i uruchamia popularny analizator dziennika [GoAccess](https://goaccess.io/) dla dzienników dostępu bramy aplikacji. GoAccess zapewnia cenne statystyki ruchu HTTP, takie jak unikatowe osoby odwiedzające, żądane pliki, hosty, systemy operacyjne, przeglądarki i kody stanu HTTP. Aby uzyskać więcej informacji, w usłudze GitHub zobacz [plik Readme w folderze szablonów Menedżera zasobów](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Co może spowodować, że kondycja zaplecza zwróci nieznany stan?

Zazwyczaj w podsieci bramy bramy aplikacji jest widoczny nieznany stan, gdy dostęp do wewnętrznej bazy danych jest blokowany przez sieciową grupę zabezpieczeń(NSG), niestandardowy system DNS lub routing zdefiniowany przez użytkownika (UDR). Aby uzyskać więcej informacji, zobacz [kondycja wewnętrznej bazy danych, rejestrowanie diagnostyki i metryki bramy aplikacji](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Czy jest jakiś przypadek, w którym dzienniki przepływu nsg nie będą wyświetlane dozwolony ruch?

Tak. Jeśli konfiguracja jest zgodna z następującym scenariuszem, nie zobaczysz dozwolonego ruchu w dziennikach przepływu sieciowej grupy zabezpieczeń:
- Wdrożono bramę aplikacji w wersji 2
- Masz sieciowej sieciowej sieciowej sieciowej w podsieci bramy aplikacji
- Włączono dzienniki przepływu nsg na tej nsg

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Jak używać bramy aplikacji w wersji 2 z tylko prywatnym adresem IP frontendu?

Brama aplikacji V2 obecnie nie obsługuje tylko tryb prywatnych adresów IP. Obsługuje następujące kombinacje
* Prywatny adres IP i publiczny adres IP
* Tylko publiczny adres IP

Ale jeśli chcesz używać bramy aplikacji V2 tylko z prywatnym adresem IP, możesz wykonać poniższy proces:
1. Tworzenie bramy aplikacji z publicznym i prywatnym adresem IP frontendu
2. Nie należy tworzyć żadnych odbiorników dla adresu IP publicznej wewnętrznej bazy. Brama aplikacji nie będzie nasłuchiwać żadnego ruchu na publiczny adres IP, jeśli nie są tworzone dla niego odbiorniki.
3. Tworzenie i dołączanie [sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview) dla podsieci Bramy aplikacji z następującą konfiguracją w kolejności priorytetu:
    
    a. Zezwalaj na ruch ze źródła jako tag usługi **GatewayManager** i port **docelowy** jako dowolny i docelowy jako **65200-65535**. Ten zakres portów jest wymagany do komunikacji z infrastrukturą platformy Azure. Te porty są chronione (zablokowane) przez uwierzytelnianie certyfikatów. Jednostki zewnętrzne, w tym administratorzy użytkowników bramy, nie mogą inicjować zmian w tych punktach końcowych bez odpowiednich certyfikatów
    
    b. Zezwalaj na ruch ze źródła jako tag usługi **AzureLoadBalancer** i port docelowy jako **dowolny**
    
    d. Odmów wszelkiego ruchu przychodzącego ze źródła jako tagu usługi **internetowej** i portu docelowego jako **Dowolny**. Nadaj tej regule *najmniejszy priorytet* w regułach przychodzących
    
    d. Zachowaj domyślne reguły, takie jak zezwalanie na przychodzące sieci wirtualne, aby dostęp do prywatnego adresu IP nie był zablokowany
    
    e. Nie można zablokować wychodzącej łączności internetowej. W przeciwnym razie napotkasz problemy z rejestrowaniem, metrykami i tak dalej.

Przykładowa konfiguracja sieciowej grupy ![zabezpieczeń dla dostępu tylko do prywatnego adresu IP: Konfiguracja sieciowej bramy aplikacji V2 dla prywatnego dostępu do ip tylko](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Czy plik cookie koligacji bramy aplikacji obsługuje atrybut SameSite?
Tak, aktualizacja [chromium browser](https://www.chromium.org/Home) [v80](https://chromiumdash.appspot.com/schedule) wprowadziła mandat na pliki cookie HTTP bez atrybutu SameSite, który ma być traktowany jako SameSite=Lax. Oznacza to, że plik cookie koligacji bramy aplikacji nie będzie wysyłany przez przeglądarkę w kontekście innej firmy. Aby obsługiwać ten scenariusz, Brama aplikacji wstrzykuje inny plik cookie o nazwie *ApplicationGatewayAffinityCORS* oprócz istniejącego pliku cookie *ApplicationGatewayAffinity.*  Te pliki cookie są podobne, ale plik cookie *ApplicationGatewayAffinityCORS* ma dwa dodatkowe atrybuty dodane do niego: *SameSite=None; Bezpieczne*. Te atrybuty utrzymują lepkie sesje nawet dla żądań między źródłami. Aby uzyskać więcej informacji, zobacz [sekcję koligacji opartą na plikach cookie.](configuration-overview.md#cookie-based-affinity)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o bramie aplikacji, zobacz [Co to jest brama aplikacji platformy Azure?](overview.md).
