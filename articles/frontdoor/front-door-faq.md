---
title: Moje drzwi platformy Azure — często zadawane pytania
description: Na tej stronie znajdują się odpowiedzi na często zadawane pytania dotyczące zewnętrznych drzwi platformy Azure
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2020
ms.author: duau
ms.openlocfilehash: de6a94b36dab9dd5662062be99f4515d78558b5e
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889658"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Często zadawane pytania dotyczące drzwi platformy Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące funkcji i funkcjonalnych drzwi platformy Azure. Jeśli nie widzisz odpowiedzi na pytanie, możesz skontaktować się z nami za pomocą następujących kanałów (w kolejności eskalacji):

1. Sekcja komentarzy w tym artykule.
2. [Azure front drzwi UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Pomoc techniczna firmy Microsoft:** Aby utworzyć nowe żądanie obsługi, w Azure Portal na karcie **Pomoc** wybierz przycisk **Pomoc i obsługa techniczna** , a następnie wybierz pozycję **nowe żądanie obsługi**.

## <a name="general"></a>Ogólne

### <a name="what-is-azure-front-door"></a>Co to jest usługa Azure Front Door?

Azure Front Drzwiczks to Application Delivery Network (wdrożenie) jako usługa, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Zapewnia przyspieszanie witryn dynamicznych (DSA) wraz z globalnym równoważeniem obciążenia, umożliwiając przejście w tryb failover prawie w czasie rzeczywistym. Jest to wysoce dostępna i skalowalna usługa, która jest w pełni zarządzana przez platformę Azure.

### <a name="what-features-does-azure-front-door-support"></a>Jakie funkcje obsługuje usługa Azure front-drzwi?

Platformy Azure Front-drzwiczki obsługują funkcję dynamicznego przyspieszania witryn (DSA), odciążania protokołów TLS/SSL i kompleksowego protokołu TLS, zapory aplikacji sieci Web, koligacji sesji opartej na plikach cookie, routingu opartego na ścieżkach URL, bezpłatnych certyfikatów i zarządzania wieloma domenami. Aby uzyskać pełną listę obsługiwanych funkcji, zobacz [Omówienie drzwi platformy Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Jaka jest różnica między usługami frontonu platformy Azure i Application Gateway platformy Azure?

Chociaż zarówno przód, jak i Application Gateway są modułami równoważenia obciążenia warstwy 7 (HTTP/HTTPS), podstawowa różnica polega na tym, że przód drzwi jest usługą globalną, a Application Gateway to usługa regionalna. Podczas gdy drzwi tylne mogą równoważyć obciążenie między różnymi jednostkami skalowania/klastrów/jednostkami sygnatur w różnych regionach, Application Gateway umożliwia Równoważenie obciążenia między maszynami wirtualnymi i kontenerami itp., które jest w jednostce skalowania.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Kiedy należy wdrożyć Application Gateway za drzwiami przednimi?

Najważniejsze scenariusze, dla których należy używać Application Gateway za drzwiami przednimi:

- Z przodu drzwi można korzystać z równoważenia obciążenia opartego na ścieżkach tylko na poziomie globalnym, ale jeśli jedna z nich chce, aby równoważyć obciążenie ruchem jeszcze bardziej w ramach ich sieci wirtualnych (VNET), powinny używać Application Gateway.
- Ponieważ tylne drzwi nie działają na poziomie maszyny wirtualnej/kontenera, więc nie można ich opróżniać. Jednak Application Gateway umożliwia opróżnianie połączeń. 
- Za pomocą Application Gateway za drzwiami przednimi, jeden może osiągnąć 100% protokołu TLS/SSL i kierować tylko żądania HTTP w ramach sieci wirtualnej (VNET).
- Drzwiczki z przodu i Application Gateway obsługują koligacje sesji. Podczas gdy drzwi tylne mogą kierować ruch z sesji użytkownika do tego samego klastra lub zaplecza w danym regionie, Application Gateway może kierować ruch do tego samego serwera w klastrze.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Czy można wdrażać Azure Load Balancer za drzwiami przednimi?

Drzwi frontonu platformy Azure muszą mieć publiczny adres VIP lub publicznie dostępną nazwę DNS, aby skierować ruch do programu. Wdrożenie Azure Load Balancer za drzwiami przednimi to typowy przypadek użycia.

### <a name="what-protocols-does-azure-front-door-support"></a>Jakie protokoły są obsługiwane przez platformę Azure front-drzwi?

Platforma Azure front-drzwi obsługuje protokoły HTTP, HTTPS i HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Jak usługa Azure front-drzwi obsługuje protokół HTTP/2?

Obsługa protokołu HTTP/2 jest dostępna dla klientów nawiązujących połączenie z usługami frontonu platformy Azure. Komunikacja do zaplecza w puli zaplecza jest za pośrednictwem protokołu HTTP/1.1. Obsługa protokołu HTTP/2 jest włączona domyślnie.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Jakie zasoby są obecnie obsługiwane w ramach puli zaplecza?

Pule zaplecza mogą składać się z magazynu, aplikacji sieci Web, wystąpień Kubernetes lub dowolnej innej niestandardowej nazwy hosta z łącznością publiczną. Drzwi frontonu platformy Azure wymagają, aby te punkty końcowe były zdefiniowane za pośrednictwem publicznego adresu IP lub publicznie rozpoznawalnej nazwy hosta DNS. Elementy członkowskie pul zaplecza mogą znajdować się między strefami, regionami lub nawet poza platformą Azure, o ile mają łączność publiczną.

### <a name="what-regions-is-the-service-available-in"></a>Jakie regiony są dostępne w ramach usługi?

Drzwi frontonu platformy Azure to usługa globalna i nie jest ona powiązana z żadnym określonym regionem świadczenia usługi Azure. Jedyną lokalizacją, którą należy określić podczas tworzenia drzwi czołowych, jest lokalizacja grupy zasobów, która zasadniczo określa, gdzie będą przechowywane metadane dla grupy zasobów. Sam zasób z drzwiami jest tworzony jako zasób globalny, a konfiguracja jest wdrażana globalnie we wszystkich punktach obecności. 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Jakie są lokalizacje POP dla drzwi frontonu platformy Azure?

Drzwi frontonu platformy Azure mają tę samą listę lokalizacji POP (punkt obecności) jako Azure CDN od firmy Microsoft. Aby uzyskać pełną listę naszych punktów obecności, należy odnieść się [Azure CDN lokalizacji pop od firmy Microsoft](../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Czy na platformie Azure z przodu jest dedykowane wdrożenie dla mojej aplikacji lub czy jest ono współużytkowane przez klientów?

Drzwi frontonu platformy Azure to globalnie dystrybuowana usługa wielodostępna. Dlatego infrastruktura dla drzwi przednich jest współdzielona przez wszystkich klientów. Jednak przez utworzenie profilu frontu drzwi należy zdefiniować konkretną konfigurację wymaganą dla aplikacji, a zmiany wprowadzone do przodu nie mają wpływu na inne konfiguracje drzwi z przodu.

### <a name="is-http-https-redirection-supported"></a>Czy obsługiwane jest przekierowanie HTTPS >HTTP?

Tak. Z tego względu drzwi frontonu platformy Azure obsługują przekierowania hosta, ścieżki i ciągu zapytania, jak również część przekierowania adresów URL. Dowiedz się więcej na temat [przekierowywania adresów URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>W jakiej kolejności są przetwarzane reguły routingu?

Trasy dla drzwi przednich nie są uporządkowane, a określona trasa jest wybierana na podstawie najlepszego dopasowania. Dowiedz się więcej o tym [, w jaki sposób przód jest zgodny z żądaniami do reguły routingu](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Jak mogę zablokować dostęp do mojego zaplecza tylko do drzwi platformy Azure z przodu?

> [!NOTE]
> Nowa wersja Premium dla drzwi z przodu zapewnia bardziej zalecany sposób blokowania aplikacji za pośrednictwem prywatnego punktu końcowego. [Dowiedz się więcej o prywatnym punkcie końcowym](./standard-premium/concept-private-link.md)

Aby zablokować aplikację w celu akceptowania tylko ruchu pochodzącego z określonych czołowych drzwi, należy skonfigurować listy ACL adresów IP dla zaplecza, a następnie ograniczyć ruch w zapleczu do określonej wartości nagłówka "X-Azure-FDID" wysyłanego przez zewnętrzne drzwi. Poniższe kroki są szczegółowo opisane poniżej:

- Skonfiguruj ACLing IP dla zaplecza, aby akceptować ruch z przestrzeni adresów IP zaplecza platformy Azure i usług infrastruktury platformy Azure. Zapoznaj się z poniższymi informacjami dotyczącymi adresu IP ACLing zaplecza:
 
    - Zapoznaj *się z* sekcją *AzureFrontDoor. zaplecza* w obszarze zakresy adresów IP [platformy Azure i Tagi usług](https://www.microsoft.com/download/details.aspx?id=56519) dla zakresu adresu IP zaplecza [IPv4.](../virtual-network/network-security-groups-overview.md#security-rules)
    - [Podstawowe usługi infrastruktury](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) platformy Azure za pomocą zwirtualizowanych adresów IP hosta: `168.63.129.16` i`169.254.169.254`

    > [!WARNING]
    > Przestrzeń adresów IP zaplecza z drzwiami może ulec zmianie później, jednak zapewnimy, że przed takim wdrożeniem będziemy zintegrowane z [zakresem adresów IP i tagami usług platformy Azure](https://www.microsoft.com/download/details.aspx?id=56519). Zalecamy, aby zasubskrybować [zakresy adresów IP platformy Azure i Tagi usług](https://www.microsoft.com/download/details.aspx?id=56519) dla wszelkich zmian lub aktualizacji.

- Poszukaj `Front Door ID` wartości w sekcji Przegląd na stronie portalu front-drzwi. Następnie można filtrować w przychodzącym nagłówku "**X-Azure-FDID**", który jest wysyłany przez tylne drzwi do zaplecza z tą wartością, aby zapewnić, że tylko Twoje własne wystąpienie typu front-drzwi jest dozwolone (ponieważ powyższe zakresy adresów IP są współdzielone z innymi wystąpieniami zewnętrznych drzwi innych klientów).

- Zastosuj filtrowanie reguł na serwerze sieci Web zaplecza, aby ograniczyć ruch na podstawie otrzymanej wartości nagłówka "X-Azure-FDID". Należy pamiętać, że niektóre usługi, takie jak Azure App Service zapewniają możliwość [filtrowania na podstawie tego nagłówka](../app-service/app-service-ip-restrictions.md#restrict-access-to-a-specific-azure-front-door-instance) , bez konieczności zmiany aplikacji lub hosta.

  Oto przykład dla [programu Microsoft Internet Information Services (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Czy adres IP emisji jest zmieniany w okresie istnienia moich zewnętrznych drzwi?

Adres IP usługi frontonu dla drzwi przednich nie powinien się zwykle zmieniać i może pozostawać statyczny w okresie istnienia drzwi z przodu. Nie ma jednak **żadnych gwarancji** dla tego samego. W adresie IP nie są wykonywane żadne bezpośrednie zależności.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Czy usługa Azure front-drzwi obsługuje statyczne lub dedykowane adresy IP?

Nie, przed drzwiami platformy Azure nie są obecnie obsługiwane statyczne lub dedykowane adresy IP emisji. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Czy usługa Azure Front-drzwiczk obsługuje x-Forwards-for Headers?

Tak, usługa Azure Front-drzwiczk obsługuje nagłówki X-Forward-for, X-Forwarded-Host i X-Forwarded-proto. Dla elementu X-forwardd-for, jeśli nagłówek już istnieje, drzwi do przodu dołączają do niego adres IP gniazda klienta. W przeciwnym razie dodaje nagłówek z adresem IP gniazda klienta jako wartość. Dla X-forwardd-Host i X-Forwarded-proto wartość zostanie zastąpiona.

Dowiedz się więcej o [nagłówkach HTTP z obsługą przednią drzwi](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Jak długo trwa wdrażanie drzwi platformy Azure z góry? Czy moje tylne drzwi nadal działają po aktualizacji?

Nowe tworzenie do przodu lub wszelkie aktualizacje istniejących przednich drzwi zajmują od 3 do 5 minut na wdrożenie globalne. Oznacza to, że od 3 do 5 minut konfiguracja drzwi do przodu zostanie wdrożona we wszystkich ogólnych punktach obecności.

Uwaga — niestandardowe aktualizacje certyfikatu TLS/SSL trwają około 30 minut, aby można je było wdrożyć globalnie.

Wszystkie aktualizacje tras lub pul zaplecza itp. są bezproblemowe i spowodują zero przestojów (Jeśli nowa konfiguracja jest poprawna). Aktualizacje certyfikatów są również niepodzielne i nie spowodują awarii, chyba że zostanie przełączone z "AFD Managed" na "Użyj własnego certyfikatu" lub na odwrót.


## <a name="configuration"></a>Konfigurowanie

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Czy można zrównoważyć ruch z przodu platformy Azure lub kierowaniu ruchu w sieci wirtualnej?

Drzwi frontonu platformy Azure (AFD) wymagają publicznego adresu IP lub publicznie rozpoznawalnej nazwy DNS, aby kierować ruchem. Dlatego odpowiedź nie jest AFD bezpośrednio w ramach sieci wirtualnej, ale użycie Application Gateway lub Azure Load Balancer między nimi spowoduje rozwiązanie tego scenariusza.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Jakie są różne limity czasu i limity dotyczące zewnętrznych drzwi platformy Azure?

Dowiedz się więcej na temat wszystkich udokumentowanych [limitów czasu i limitów dla drzwi platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Jak długo trwa wykonywanie reguły po dodaniu do aparatu reguł dla drzwi przednich?

Konfiguracja aparatu reguł trwa od 10 do 15 minut, aby ukończyć aktualizację. Można oczekiwać, że reguła zacznie obowiązywać zaraz po zakończeniu aktualizacji. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-vice-versa"></a>Czy mogę skonfigurować Azure CDN za moim profilem drzwi przednich lub odwrotnie?

Nie można jednocześnie skonfigurować drzwi i Azure CDN platformy Azure, ponieważ obie usługi wykorzystują te same Lokacje usługi Azure Edge podczas odpowiadania na żądania. 

## <a name="performance"></a>Wydajność

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Jak platforma Azure front-drzwi obsługuje wysoką dostępność i skalowalność?

Drzwi frontonu platformy Azure to globalnie dystrybuowana platforma z wieloma dzierżawcami z ogromnymi ilościami pojemności, które spełniają potrzeby skalowalności aplikacji. Od brzegu sieci globalnej firmy Microsoft drzwi do przodu udostępniają globalną funkcję równoważenia obciążenia, która umożliwia przechodzenie w tryb failover całej aplikacji lub nawet poszczególnych mikrousług między regionami lub różnymi chmurami.

## <a name="tls-configuration"></a>Konfiguracja protokołu TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Jakie wersje protokołu TLS są obsługiwane przez moje drzwi platformy Azure?

Wszystkie profile frontonu utworzone po wrzesień 2019 używają protokołu TLS 1,2 jako domyślnego minimum.

Drzwi z przodu obsługują protokół TLS w wersji 1,0, 1,1 i 1,2. Protokół TLS 1,3 nie jest jeszcze obsługiwany.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Jakie certyfikaty są obsługiwane w ramach zewnętrznych drzwi platformy Azure?

Aby włączyć protokół HTTPS w celu bezpiecznego dostarczania zawartości w domenie niestandardowej z drzwiami niestandardowymi, możesz wybrać użycie certyfikatu zarządzanego przez usługę Azure front-drzwi lub użyć własnego certyfikatu.
Opcja zarządzana przed drzwiami udostępnia standardowy certyfikat TLS/SSL za pośrednictwem DigiCert i jest przechowywany w Key Vaultch przednich drzwi. Jeśli zdecydujesz się na korzystanie z własnego certyfikatu, możesz dołączyć certyfikat z obsługiwanego urzędu certyfikacji i może być to standardowy protokół TLS, rozszerzony certyfikat weryfikacji, a nawet certyfikat z symbolami wieloznacznymi. Certyfikaty z podpisem własnym nie są obsługiwane. Dowiedz się [, jak włączyć protokół HTTPS dla domeny niestandardowej](./front-door-custom-domain-https.md).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Czy przód drzwi obsługuje autorotację certyfikatów?

W przypadku opcji certyfikat zarządzany przed drzwiami certyfikaty są obracane na podstawie drzwi. Jeśli używasz certyfikatu zarządzanego z drzwiami i zobaczysz, że data wygaśnięcia certyfikatu jest mniejsza niż 60 dni, zapoznaj się z biletem pomocy technicznej.
</br>W przypadku własnego niestandardowego certyfikatu TLS/SSL, autorotacja nie jest obsługiwane. Podobnie jak w przypadku, gdy został on skonfigurowany po raz pierwszy dla danej domeny niestandardowej, należy wskazać przód drzwi do odpowiedniej wersji certyfikatu w Key Vault i upewnić się, że jednostka usługi dla drzwi zewnętrznych nadal ma dostęp do Key Vault. Ta zaktualizowana operacja wdrażania certyfikatu przez drzwi tylne jest niepodzielna i nie powoduje żadnego wpływu na produkcję, gdy nazwa podmiotu lub sieć SAN dla certyfikatu nie ulegają zmianie.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Jakie są bieżące mechanizmy szyfrowania obsługiwane przez frontony platformy Azure?

W przypadku protokołu TLS 1.2 obsługiwane są następujące mechanizmy szyfrowania: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

W przypadku używania domen niestandardowych z włączonym protokołem TLS 1.0/1.1 obsługiwane są następujące mechanizmy szyfrowania:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Czy można skonfigurować zasady protokołu TLS w celu kontrolowania wersji protokołu TLS?

Można skonfigurować minimalną wersję protokołu TLS na platformie Azure Front drzwiczki w ustawieniach protokołu HTTPS domeny niestandardowej za pośrednictwem Azure Portal lub [interfejsu API REST platformy Azure](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Obecnie można wybrać między 1,0 a 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Czy można skonfigurować drzwiczki frontonu w celu obsługi tylko określonych mechanizmów szyfrowania?

Nie, Konfigurowanie drzwi zewnętrznych dla określonych mechanizmów szyfrowania nie jest obsługiwane. Można jednak uzyskać własny niestandardowy certyfikat TLS/SSL z urzędu certyfikacji (Powiedz VeriSign, powierzyć lub DigiCert) i mieć określone mechanizmy szyfrowania oznaczone w certyfikacie podczas jego generowania. 

### <a name="does-front-door-support-ocsp-stapling"></a>Czy drzwi z przodu obsługują zszywanie protokołu OCSP?

Tak, zszywanie protokołu OCSP jest domyślnie obsługiwane przez frontony i nie jest wymagana żadna konfiguracja.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Czy usługa Azure front-drzwi obsługuje również ponowne szyfrowanie ruchu do zaplecza?

Tak, platforma Azure Front-drzwiczk obsługuje odciążanie protokołu TLS/SSL i kompleksową transpozycję TLS, która powoduje ponowne zaszyfrowanie ruchu do zaplecza. W rzeczywistości, ponieważ połączenia z zapleczem odbywają się za pośrednictwem jego publicznego adresu IP, zaleca się skonfigurowanie drzwi frontonu do używania protokołu HTTPS jako protokół przekazywania dalej.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Czy drzwi z podpisem własnym obsługują certyfikaty z podpisem własnym w ramach zaplecza dla połączenia HTTPS?

Nie, certyfikaty z podpisem własnym nie są obsługiwane w przypadku drzwi zewnętrznych, a ograniczenie dotyczy obydwu:

1. **Zastąp: nie** można użyć certyfikatów z podpisem własnym podczas przekazywania ruchu jako sond kondycji protokołu HTTPS lub HTTPS lub wypełniania pamięci podręcznej z poziomu źródła dla reguł routingu z włączonym buforowaniem.
2. **Fronton**: nie można używać certyfikatów z podpisem własnym podczas korzystania z własnego NIESTANDARDOWEGO certyfikatu TLS/SSL do włączania protokołu HTTPS w domenie niestandardowej.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Dlaczego ruch HTTPS do mojej wewnętrznej bazy danych kończy się niepowodzeniem?

W przypadku pomyślnego nawiązania połączenia HTTPS z zaplecem niezależnie od tego, czy są używane sondy kondycji, czy przekazywanie żądań, może istnieć dwa przyczyny niepowodzenia ruchu HTTPS:

1. **Niezgodność nazwy podmiotu certyfikatu**: w przypadku połączeń HTTPS, drzwiczki z przodu oczekują, że zaplecze przedstawia certyfikat z prawidłowego urzędu certyfikacji z nazwami podmiotu pasującymi do nazwy hosta zaplecza. Jeśli na przykład nazwa hosta zaplecza jest ustawiona na, `myapp-centralus.contosonews.net` a certyfikat, którego zaplecze prezentuje się podczas uzgadniania protokołu TLS `myapp-centralus.contosonews.net` , nie ma ani `*myapp-centralus*.contosonews.net` w nazwie podmiotu, drzwi tylne odmówią połączenia i spowodują wystąpienie błędu. 
    1. **Rozwiązanie**: Chociaż nie jest to zalecane w punktu widzenia zgodności, można obejść ten błąd, wyłączając Sprawdzanie nazwy podmiotu certyfikatu dla drzwi z przodu. Ta opcja jest dostępna w obszarze Ustawienia w Azure Portal i w obszarze BackendPoolsSettings w interfejsie API.
2. **Certyfikat hostingu zaplecza z nieprawidłowego urzędu certyfikacji**: można używać tylko certyfikatów z [prawidłowych urzędów certyfikacji](./front-door-troubleshoot-allowed-ca.md) w zapleczu z przednimi drzwiami. Certyfikaty z wewnętrznych urzędów certyfikacji lub certyfikatów z podpisem własnym są niedozwolone.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Czy mogę używać uwierzytelniania klienta/wzajemnego na platformie Azure

Nie. Mimo że platforma Azure front-drzwi obsługuje protokół TLS 1,2, który wprowadził uwierzytelnianie klienta/wzajemnego uwierzytelniania w [dokumencie RFC 5246](https://tools.ietf.org/html/rfc5246), obecnie nie jest obsługiwane uwierzytelnianie klienta/uwierzytelniania wzajemnego.

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Jakie typy metryk i dzienników są dostępne z usługami Azure front-drzwi?

Aby uzyskać informacje na temat dzienników i innych funkcji diagnostycznych, zobacz [monitorowanie metryk i dzienników dla drzwi zewnętrznych](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Co to są zasady przechowywania w dziennikach diagnostycznych?

Dzienniki diagnostyczne przepływają do konta magazynu klientów i klienci mogą ustawić zasady przechowywania na podstawie ich preferencji. Dzienniki diagnostyczne można także wysyłać do centrum zdarzeń lub dzienników Azure Monitor. Aby uzyskać więcej informacji, zobacz [Diagnostyka z przodu platformy Azure](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Jak mogę pobrać dzienników inspekcji dla drzwi frontonu platformy Azure?

Dzienniki inspekcji są dostępne dla drzwi frontonu platformy Azure. W portalu kliknij pozycję **Dziennik aktywności** w bloku menu swojego drzwi czołowych, aby uzyskać dostęp do dziennika inspekcji. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Czy mogę ustawić alerty za pomocą usług Azure front-drzwi?

Tak, frontony platformy Azure obsługują alerty. Alerty są konfigurowane na metrykach. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
