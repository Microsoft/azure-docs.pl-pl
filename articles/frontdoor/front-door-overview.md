---
title: Moje drzwi platformy Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Front Door. Sprawdź, czy jest to właściwy wybór dla ruchu użytkowników z równoważeniem obciążenia dla aplikacji.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: sharadag
ms.openlocfilehash: 081305170020aa35511ad574bd8c5575c192d577
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322288"
---
# <a name="what-is-azure-front-door"></a>Co to jest usługa Azure Front Door?
Drzwi frontonu platformy Azure umożliwiają zdefiniowanie i monitorowanie globalnego routingu dla ruchu w sieci Web oraz zarządzanie nim przez optymalizację pod kątem najlepszej wydajności i szybkiej globalnej pracy awaryjnej w celu zapewnienia wysokiej dostępności. Za pomocą przednich drzwi można przekształcić globalne (wieloregionowe) aplikacje klienckie i korporacyjne na niezawodne, wysoce wydajne, nowoczesne aplikacje, interfejsy API i zawartość, które docierają do użytkowników globalnych na platformie Azure.

Usługa Front Door działa w warstwie 7 lub warstwie protokołu HTTP/HTTPS i używa protokołu emisji dowolnej z podzielonym protokołem TCP i globalną siecią firmy Microsoft w celu poprawienia łączności globalnej. Dlatego przez wybór metody routingu w konfiguracji można zagwarantować, że usługa Front Door będzie kierować żądania klienta do najszybszego i najbardziej dostępnego zaplecza aplikacji. Zaplecze aplikacji to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią. Usługa Front Door udostępnia szereg [metod routingu ruchu](front-door-routing-methods.md) oraz [opcji monitorowania kondycji zaplecza](front-door-health-probes.md), które zaspokoją potrzeby różnych aplikacji i modeli automatycznej pracy w trybie failover. Podobnie jak usługa [Traffic Manager](../traffic-manager/traffic-manager-overview.md), usługa Front Door jest odporna na awarie, w tym awarię całego regionu platformy Azure.

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli potrzebujesz opartego na systemie DNS routingu globalnego i **nie** masz wymagań dotyczących zakończenia protokołu zabezpieczeń TLS (Transport Layer Security) („odciążanie protokołu SSL”) ani przetwarzania poszczególnych żądań dotyczących protokołu HTTP/HTTPS na poziomie warstwy aplikacji, zapoznaj się z tematem dotyczącym usługi [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Jeśli chcesz równoważyć obciążenie między serwerami w regionie na potrzeby warstwy aplikacji, zapoznaj się z omówieniem usługi [Application Gateway](../application-gateway/application-gateway-introduction.md). W przypadku równoważenia obciążenia w warstwie sieci zapoznaj się z omówieniem usługi [Load Balancer](../load-balancer/load-balancer-overview.md). Scenariusze kompleksowe mogą w razie potrzeby korzystać z zalet łączenia tych rozwiązań.
>
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

W usłudze Front Door są dostępne następujące funkcje:

## <a name="accelerate-application-performance"></a>Zwiększanie wydajności aplikacji
Używając podzielonego protokołu emisji dowolnej opartego na protokole TCP, usługa Front Door gwarantuje, że użytkownicy końcowi mogą szybko łączyć się z najbliższym punktem POP (point of presence, punkt obecności) usługi Front Door. Używając globalnej sieci firmy Microsoft do łączenia się z zapleczami aplikacji z poziomu punktów POP, można zapewnić większą dostępność i niezawodność przy zachowaniu wydajności. Ta łączność z zapleczem opiera się również na najniższym opóźnieniu sieci. Dowiedz się więcej o technikach routingu usługi Front Door, takich jak [podzielony protokół TCP](front-door-routing-architecture.md#splittcp) i [protokół emisji dowolnej](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Zwiększanie dostępności aplikacji za pomocą inteligentnych sond kondycji

Drzwi z przodu zapewniają wysoką dostępność dla aplikacji o znaczeniu krytycznym przy użyciu inteligentnych sond kondycji, monitorując zaplecze zarówno w przypadku opóźnień, jak i dostępności, zapewniając szybką automatyczne przełączanie do trybu failover w przypadku awarii zaplecza. Dlatego można uruchamiać operacje planowanej konserwacji aplikacji bez przestojów. W czasie konserwacji usługa Front Door kieruje ruch do alternatywnych zapleczy.

## <a name="url-based-routing"></a>Routing oparty na adresach URL
Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul zaplecza na podstawie ścieżek URL żądania. Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pól zaplecza.

Na przykład żądania dotyczące adresu `http://www.contoso.com/users/*` są kierowane do puli UserProfilePool, a żądania dotyczące adresu `http://www.contoso.com/products/*` — do puli ProductInventoryPool.  Usługa Front Door umożliwia obsługę nawet bardziej złożonych scenariuszy dopasowywania tras za pomocą najlepszego algorytmu dopasowania. Jeśli żaden z wzorców ścieżki nie pasuje, jest wybierana domyślna reguła rozsyłania dla adresu `http://www.contoso.com/*`, a ruch jest kierowany do domyślnej reguły routingu przechwytującej wszystkie elementy. Dalsze informacje można znaleźć w temacie [Route Matching](front-door-route-matching.md) (Dopasowywanie tras).

## <a name="multiple-site-hosting"></a>Hostowanie wielu witryn
Hostowanie wielu witryn pozwala na skonfigurowanie więcej niż jednej witryny internetowej w tej samej konfiguracji usługi Front Door. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie różnych witryn internetowych do pojedynczej konfiguracji usługi Front Door. W oparciu o architekturę aplikacji można skonfigurować drzwi frontonu platformy Azure, aby skierować każdą witrynę sieci Web do własnej puli zaplecza lub mieć różne witryny sieci Web kierowane do tej samej puli zaplecza. Na przykład usługa Front Door może obsługiwać ruch na potrzeby witryn `images.contoso.com` i `videos.contoso.com` z dwóch pul zaplecza o nazwie ImagePool i VideoPool. Alternatywnie można skonfigurować hosty frontonu tak, aby kierowały ruch do pojedynczej puli zaplecza o nazwie MediaPool.

Podobnie można mieć dwie różne domeny `www.contoso.com` i `www.fabrikam.com` skonfigurowane w tej samej usłudze Front Door.

## <a name="session-affinity"></a>Koligacja sesji
Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz zachować sesję użytkownika w tym samym zapleczu aplikacji. Dzięki plikom cookie zarządzanym przez usługę Front Door dalszy ruch z sesji użytkownika jest kierowany do tego samego zaplecza aplikacji na potrzeby przetwarzania. Ta funkcja jest ważna w przypadkach, w których stan sesji jest zapisywany lokalnie w zapleczu dla sesji użytkownika.

## <a name="tls-termination"></a>Zakończenie protokołu TLS
Drzwiczki z przodu obsługują zakończenie protokołu TLS na granicy, która polega na tym, że indywidualni użytkownicy mogą skonfigurować połączenie TLS z środowiskami przednimi, zamiast ustanawiać je w połączeniach o długim zasięgu z zapleczem aplikacji. Ponadto usługa Front Door obsługuje łączność za pośrednictwem protokołów HTTP i HTTPS między środowiskami usługi Front Door i zapleczami. Dzięki temu można również skonfigurować kompleksowe szyfrowanie protokołu TLS. Jeśli na przykład usługa Front Door dla obciążenia aplikacji otrzymuje ponad 5000 żądań w ciągu minuty, ze względu na ponowne użycie połączenia bez wyłączania zasilania, w przypadku aktywnych usług zostanie ustanowionych tylko około 500 połączeń z zapleczem aplikacji, co znacznie zmniejszy obciążenie z zapleczy.

## <a name="custom-domains-and-certificate-management"></a>Zarządzanie domenami niestandardowymi i certyfikatami
W przypadku dostarczania zawartości przy użyciu usługi Front Door domena niestandardowa jest niezbędna, jeśli chcesz, aby nazwa Twojej domeny była widoczna w adresie URL usługi Front Door. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem.
Usługa Front Door obsługuje również protokół HTTPS w przypadku nazw domen niestandardowych. Użyj tej funkcji, wybierając opcję certyfikaty zarządzane przed drzwiami dla ruchu lub przekazując własny niestandardowy certyfikat TLS/SSL.

## <a name="application-layer-security"></a>Zabezpieczenia warstwy aplikacji
Drzwi frontonu platformy Azure umożliwiają tworzenie niestandardowych reguł zapory aplikacji sieci Web (WAF) na potrzeby kontroli dostępu w celu ochrony obciążeń protokołu HTTP/HTTPS przed wykorzystywaniem na podstawie adresów IP klientów, kodu kraju i parametrów protokołu HTTP. Ponadto usługa Front Door umożliwia również tworzenie reguł ograniczania szybkości, które pomagają zapobiegać ruchowi złośliwych botów. Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web, zobacz [co to jest Zapora aplikacji sieci Web platformy Azure?](../web-application-firewall/overview.md)

Sama platforma Front Door jest chroniona przez usługę [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) w warstwie Podstawowa. W celu zastosowania dalszej ochrony przez atakami w warstwie sieci (protokół TCP/UDP) za pośrednictwem funkcji automatycznego dostrajania i ograniczania ryzyka można włączyć usługę Azure DDoS Protection w warstwie Standardowa w sieciach wirtualnych i zasobach zabezpieczeń. Usługa Front Door to zwrotny serwer proxy warstwy 7, który umożliwia tylko przechodzenie ruchu internetowego do zapleczy i domyślnie blokuje inne typy ruchu.

## <a name="url-redirection"></a>Przekierowywanie adresu URL
Dzięki mocnej branży wypychania na potrzeby obsługi tylko bezpiecznej komunikacji aplikacje sieci Web powinny automatycznie przekierowywać dowolny ruch HTTP do protokołu HTTPS. Dzięki temu cała komunikacja między użytkownikami a aplikacją odbywa się za pośrednictwem zaszyfrowanej ścieżki. 

Tradycyjnie właściciele aplikacji mają do czynienia z tym wymaganiem, tworząc dedykowaną usługę, której jedynym celem było Przekierowanie żądań odbieranych w protokole HTTP do protokołu HTTPS. Drzwi frontonu platformy Azure obsługują możliwość przekierowywania ruchu z protokołu HTTP do protokołu HTTPS. Upraszcza to konfigurację aplikacji, optymalizuje wykorzystanie zasobów i umożliwia obsługę nowych scenariuszy przekierowania, w tym przekierowania globalnego i opartego na ścieżce. Przekierowywanie adresów URL z zewnętrznych drzwi platformy Azure nie jest ograniczone do samego przekierowania protokołu HTTP do protokołu HTTPS, ale również do przekierowywania do innej nazwy hosta, przekierowywania do innej ścieżki lub nawet przekierowania do nowego ciągu zapytania w adresie URL.

Aby uzyskać więcej informacji, zobacz [przekierowywanie ruchu](front-door-url-redirect.md) za pomocą usług Azure front-drzwi.

## <a name="url-rewrite"></a>Regenerowanie adresów URL
Usługa Front Door obsługuje [ponowne zapisywanie adresów URL](front-door-url-rewrite.md), pozwalając na konfigurowanie opcjonalnej niestandardowej ścieżki przekazywania dalej podczas konstruowania żądania do przekazania do zaplecza. Dodatkowo usługa Front Door umożliwia konfigurowanie nagłówka hosta do wysłania w przypadku przekazywania żądania dalej do zaplecza.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Obsługa protokołu — ruch IPv6 i HTTP/2
Usługa Azure Front Door natywnie obsługuje kompleksową łączność w protokole IPv6, a także protokół HTTP/2. 

Protokół HTTP/2 umożliwia pełnodupleksową komunikację między zapleczami aplikacji i klientem przez długotrwałe połączenie TCP. Protokół HTTP/2 pozwala na bardziej interaktywną komunikację między zapleczem a klientem, która może być dwukierunkowa bez konieczności sondowania, co jest wymagane w implementacjach opartych na protokole HTTP. Protokół HTTP/2 ma niskie obciążenie, w odróżnieniu od protokołu HTTP, i może ponownie używać tego samego połączenia TCP dla wielu żądań lub odpowiedzi, co zapewnia bardziej efektywne wykorzystanie zasobów. Dowiedz się więcej o [obsłudze protokołu HTTP/2 na platformie Azure front-drzwi](front-door-http2.md).

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, zobacz [cennik usługi Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
