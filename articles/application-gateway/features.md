---
title: Funkcje Application Gateway platformy Azure
description: Dowiedz się więcej o funkcjach Application Gateway platformy Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: ba9f42bc932a37e1052f17db2ae00413e0769d59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91355741"
---
# <a name="azure-application-gateway-features"></a>Funkcje Application Gateway platformy Azure

[Azure Application Gateway](overview.md) to moduł równoważenia obciążenia sieci Web, który umożliwia zarządzanie ruchem do aplikacji sieci Web.

![Koncepcja modułu Application Gateway](media/overview/figure1-720.png)

Application Gateway obejmuje następujące funkcje:

- [Zakończenie Secure Sockets Layer (SSL/TLS)](#secure-sockets-layer-ssltls-termination)
- [Skalowanie automatyczne](#autoscaling)
- [Nadmiarowość stref](#zone-redundancy)
- [Statyczny adres VIP](#static-vip)
- [Web Application Firewall](#web-application-firewall)
- [Kontroler ruchu przychodzącego dla usługi AKS](#ingress-controller-for-aks)
- [Routing oparty na adresach URL](#url-based-routing)
- [Hostowanie wielu witryn](#multiple-site-hosting)
- [Przekierowania](#redirection)
- [Koligacja sesji](#session-affinity)
- [Ruch protokołów WebSocket i HTTP/2](#websocket-and-http2-traffic)
- [Opróżnianie połączeń](#connection-draining)
- [Niestandardowe strony błędów](#custom-error-pages)
- [Ponowne zapisywanie nagłówków HTTP i adresów URL](#rewrite-http-headers-and-url)
- [Ustalanie rozmiaru](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Zakończenie Secure Sockets Layer (SSL/TLS)

Brama Application Gateway obsługuje zakończenie protokołu SSL/TLS w bramie, po czym ruch przeważnie jest przenoszony do serwerów zaplecza. Ta funkcja umożliwia odciążenie serwerów sieci Web z nadmiaru kosztownych operacji szyfrowania i odszyfrowywania. Jednak czasami zaszyfrowana komunikacja z serwerami nie jest akceptowalną opcją. Może to być spowodowane wymaganiami dotyczącymi zabezpieczeń, wymaganiami dotyczącymi zgodności lub zastosowaniem tylko bezpiecznego połączenia. W przypadku tych aplikacji Brama Application Gateway obsługuje kompleksowe szyfrowanie SSL/TLS.

Aby uzyskać więcej informacji, zobacz [Omówienie zakończenia protokołu SSL i kompleksowego protokołu SSL z usługą Application Gateway](ssl-overview.md)

## <a name="autoscaling"></a>Skalowanie automatyczne

Application Gateway Standard_v2 obsługuje skalowanie automatyczne i można skalować w górę lub w dół w zależności od zmiany wzorców obciążenia ruchu sieciowego. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. 

Aby uzyskać więcej informacji na temat funkcji Standard_v2 Application Gateway, zobacz Automatyczne [skalowanie jednostki SKU w wersji 2](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Nadmiarowość stref

Application Gateway Standard_v2 może obejmować wiele Strefy dostępnościów, co zapewnia lepszą odporność na uszkodzenia i eliminuje konieczność udostępniania oddzielnych bram aplikacji w każdej strefie.

## <a name="static-vip"></a>Statyczny adres VIP

Jednostka SKU Standard_v2 bramy aplikacji obsługuje wyłącznie statyczny typ adresu VIP. Dzięki temu wirtualne adresy IP skojarzone z bramą aplikacji nie zmieniają się nawet w okresie istnienia Application Gateway.

## <a name="web-application-firewall"></a>Web Application Firewall

Zapora aplikacji sieci Web (WAF) to usługa, która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach. WAF opiera się na regułach z [OWASP (Open Web Application Security Project) podstawowych zestawów reguł](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1 (tylko WAF_v2), 3,0 i 2.2.9. 

Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy aplikacji można łatwo przekonwertować na bramę aplikacji sieci Web.

Aby uzyskać więcej informacji, zobacz [co to jest Zapora aplikacji sieci Web platformy Azure?](../web-application-firewall/overview.md)

## <a name="ingress-controller-for-aks"></a>Kontroler ruchu przychodzącego dla usługi AKS
Application Gateway kontroler transferu danych przychodzących (AGIC) umożliwia korzystanie z Application Gateway jako ruchu przychodzącego dla klastra [usługi Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . 

Kontroler transferu danych przychodzących działa w ramach klastra AKS i zużywa zasoby związane z ruchem przychodzącym [Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) i konwertuje je na konfigurację Application Gateway, dzięki czemu Brama może równoważyć obciążenie ruchu do zasobników Kubernetes. Kontroler transferu danych przychodzących obsługuje tylko Application Gateway jednostki SKU Standard_v2 i WAF_v2. 

Aby uzyskać więcej informacji, zobacz [Application Gatewayal Transferer Controller (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Routing oparty na adresach URL

Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul serwerów zaplecza na podstawie ścieżek URL żądania. Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pul.

Na przykład żądania dotyczące adresu `http://contoso.com/video/*` są kierowane do puli VideoServerPool, a żądania dotyczące adresu `http://contoso.com/images/*` — do puli ImageServerPool. Pula DefaultServerPool jest wybierana, jeśli żaden z wzorców ścieżki nie pasuje.

Aby uzyskać więcej informacji, zobacz [Omówienie routingu opartego na ścieżkach URL](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Hostowanie wielu witryn

Za pomocą Application Gateway można skonfigurować Routing na podstawie nazwy hosta lub nazwy domeny dla więcej niż jednej aplikacji sieci Web w tej samej bramie aplikacji. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie nawet ponad 100 witryn internetowych do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. Na przykład trzy domeny — contoso.com, fabrikam.com i adatum.com — wskazują adres IP bramy aplikacji. Utworzysz trzy odbiorniki obejmujące wiele witryn i skonfigurujesz każdy odbiornik dla odpowiedniego portu i ustawienia protokołu. 

Żądania dla `http://contoso.com` są kierowane do puli contososerverpool, `http://fabrikam.com` są kierowane do puli fabrikamserverpool i tak dalej.

Podobnie dwie domeny podrzędne tej samej domeny nadrzędnej mogą być hostowane w ramach tego samego wdrożenia usługi Application Gateway. Przykłady użycia domen podrzędnych mogą obejmować domeny `http://blog.contoso.com` i `http://app.contoso.com` hostowane w jednym wdrożeniu bramy Application Gateway. Aby uzyskać więcej informacji, zobacz [Application Gateway obsługa wielu witryn](multiple-site-overview.md).

Możesz również określić nazwy hosta z symbolami wieloznacznymi w odbiorniku obejmującym wiele witryn, z maksymalnie pięcioma nazwami hostów na odbiornik. Aby dowiedzieć się więcej, zobacz [symbole wieloznaczne nazw hostów w odbiorniku (wersja zapoznawcza)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

## <a name="redirection"></a>Przekierowania

Typowy scenariusz dla wielu aplikacji internetowych obejmuje obsługę automatycznego przekierowania protokołu HTTP do HTTPS, aby zagwarantować, że cała komunikacja między aplikacją a jej użytkownikami odbywa się za pośrednictwem ścieżki szyfrowanej.

W przeszłości można było użyć technik takich jak tworzenie dedykowanej puli, których jedynym celem jest przekierowanie żądań odbieranych przez protokół HTTP do protokołu HTTPS. Usługa Application Gateway obsługuje możliwość przekierowywania ruchu sieciowego w tej usłudze. Upraszcza to konfigurację aplikacji, optymalizuje wykorzystanie zasobów i umożliwia obsługę nowych scenariuszy przekierowania, w tym przekierowania globalnego i opartego na ścieżce. Obsługa przekierowań Application Gateway nie jest ograniczona do samego przekierowania protokołu HTTP do protokołu HTTPS. Jest to ogólny mechanizm przekierowania, dzięki czemu możliwe jest przekierowanie z i do dowolnego portu zdefiniowanego przy użyciu reguł. Obsługiwane jest również przekierowanie do zewnętrznej witryny.

Obsługa przekierowania dla usługi Application Gateway oferuje następujące możliwości:

- Globalne przekierowanie z jednego portu do innego portu w bramie. Umożliwia to przekierowanie protokołu HTTP do HTTPS w witrynie.
- Przekierowanie na podstawie ścieżki. Ten typ przekierowania umożliwia przekierowanie protokołu HTTP do HTTPS tylko w określonym obszarze witryny, na przykład obszarze koszyka określonym przez element `/cart/*`.
- Przekierowanie do zewnętrznej witryny.

Aby uzyskać więcej informacji, zobacz [Omówienie przekierowania Application Gateway](redirect-overview.md).

## <a name="session-affinity"></a>Koligacja sesji

Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Używając plików cookie zarządzanych przez bramę, usługa Application Gateway może kierować dalszy ruch z sesji użytkownika na ten sam serwer w celu przetwarzania. Jest to ważne w przypadkach, w których stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika.

Aby uzyskać więcej informacji, zobacz [jak działa Brama aplikacji](how-application-gateway-works.md#modifications-to-the-request).

## <a name="websocket-and-http2-traffic"></a>Ruch protokołów WebSocket i HTTP/2

Usługa Application Gateway zapewnia natywną obsługę protokołów WebSocket i HTTP/2. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket.

Protokoły WebSocket i HTTP/2 umożliwiają pełnodupleksową komunikację między serwerem i klientem przez długotrwałe połączenie TCP. Pozwala to na bardziej interaktywną komunikację między serwerem internetowym a klientem, która może być dwukierunkowa bez konieczności sondowania, co jest wymagane w implementacjach opartych na protokole HTTP. Te protokoły mają niewielkie obciążenie, w przeciwieństwie do protokołu HTTP i mogą ponownie używać tego samego połączenia TCP dla wielu żądań/odpowiedzi, co zwiększa efektywność użycia zasobów. Te protokoły są przeznaczone do pracy z użyciem tradycyjnych portów HTTP, tj. 80 i 443.

Aby uzyskać więcej informacji, zobacz [Obsługa protokołu WebSocket](application-gateway-websocket.md) i [Obsługa protokołu HTTP/2](configuration-listeners.md#http2-support).

## <a name="connection-draining"></a>Opróżnianie połączeń

Opróżnianie połączeń umożliwia bezproblemowe usunięcie członków puli zaplecza podczas planowanych aktualizacji usługi. To ustawienie jest włączane za pośrednictwem ustawienia http zaplecza i można je zastosować do wszystkich członków puli zaplecza podczas tworzenia reguły. Po włączeniu Application Gateway gwarantuje, że wszystkie wystąpienia puli zaplecza nie otrzymają żadnego nowego żądania, jednocześnie zezwalając na ukończenie istniejących żądań w skonfigurowanym limicie czasu. Dotyczy to zarówno wystąpień zaplecza, które zostały jawnie usunięte z puli zaplecza przez zmianę konfiguracji użytkownika, jak i wystąpienia zaplecza, które są zgłaszane jako w złej kondycji określone przez sondy kondycji. Jedynym wyjątkiem są żądania związane z wyrejestrowywaniem wystąpień, które zostały wyrejestrowane jawnie, z powodu koligacji sesji zarządzanej przez bramę i w dalszym ciągu są przekazywane przez serwer proxy do wyrejestrowania wystąpień.

Aby uzyskać więcej informacji, zobacz [Omówienie konfiguracji Application Gateway](configuration-http-settings.md#connection-draining).

## <a name="custom-error-pages"></a>Niestandardowe strony błędów

Usługa Application Gateway umożliwia tworzenie niestandardowych stron błędów wyświetlanych zamiast domyślnych strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu.

Aby uzyskać więcej informacji, zobacz [Błędy niestandardowe](custom-error.md).

## <a name="rewrite-http-headers-and-url"></a>Ponowne zapisywanie nagłówków HTTP i adresów URL

Nagłówki HTTP umożliwiają klientowi i serwerowi przekazywanie dodatkowych informacji z żądaniem lub odpowiedzią. Ponowne Zapisywanie tych nagłówków HTTP pomaga wykonać kilka ważnych scenariuszy, takich jak:

- Dodawanie pól nagłówków związanych z zabezpieczeniami, takich jak HSTS/X-XSS-Protection.
- Usuwanie pól nagłówka odpowiedzi, które mogą ujawniać poufne informacje.
- Usuwanie informacji o porcie z X-Forwarded-For Headers.

Usługa Application Gateway i jednostka SKU zapory aplikacji internetowej w wersji 2 umożliwiają dodawanie, usuwanie lub aktualizowanie nagłówków żądań i odpowiedzi HTTP podczas przenoszenia pakietów żądań i odpowiedzi między pulami klientów i zaplecza. Można też ponownie zapisywać adresy URL, parametry ciągu zapytania i nazwę hosta. W przypadku ponownego zapisywania adresów URL i routingu opartego na ścieżkach URL można wybrać kierowanie żądań do jednej z pul zaplecza na podstawie oryginalnej ścieżki lub ponownie zapisanej ścieżki, stosując opcję ponownej oceny mapowania ścieżek. 

Ta funkcja umożliwia też dodawanie warunków w celu zapewnienia, że określone nagłówki lub adres URL zostaną zapisane ponownie tylko po spełnieniu określonych warunków. Te warunki są oparte na informacjach dotyczących żądania i odpowiedzi.

Aby uzyskać więcej informacji, zobacz [Zapisywanie nagłówków HTTP i adresów URL](rewrite-http-headers-url.md).

## <a name="sizing"></a>Ustalanie rozmiaru

Standard_v2 Application Gateway można skonfigurować na potrzeby wdrożeń skalowania automatycznego lub stałego rozmiaru. Jednostka SKU v2 nie oferuje różnych rozmiarów wystąpień. Aby uzyskać więcej informacji na temat wydajności i cen w wersji 2, zobacz [Skalowanie automatyczne w wersji 2](application-gateway-autoscaling-zone-redundant.md) i [Informacje o cenach](understanding-pricing.md).

Standard Application Gateway (v1) jest oferowany w trzech rozmiarach: **małych**, **średnich** i **dużych**. Rozmiary małych wystąpień są przeznaczone na potrzeby programowania i scenariuszy testowania.

Pełna lista limitów usługi Application Gateway znajduje się na stronie [ograniczeń usługi Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

W poniższej tabeli przedstawiono średnią przepływność wydajności dla każdego wystąpienia usługi Application Gateway V1 z włączonym odciążeniem SSL:

| Średni rozmiar odpowiedzi strony zaplecza | Mały | Śred. | Duży |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Są to przybliżone wartości przepływności bramy aplikacji. Rzeczywista przepływność zależy od różnorodnych szczegółów środowiska, takich jak średni rozmiar strony, lokalizacja wystąpień zaplecza i czas przetwarzania potrzebny do obsługi strony. Aby uzyskać dokładne wartości wydajności, należy przeprowadzić własne testy. Te wartości są podane tylko jako wskazówki na potrzeby planowania pojemności.

## <a name="version-feature-comparison"></a>Porównanie funkcji wersji

Aby uzyskać Application Gateway porównanie funkcji v1-v2, zobacz [Skalowanie automatyczne i strefowo nadmiarowe Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak działa Application Gateway — [jak działa Brama aplikacji](how-application-gateway-works.md)