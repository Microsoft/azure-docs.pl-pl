---
title: Usługa ExpressRoute dla dostawców rozwiązań w chmurze — Azure | Microsoft Docs
description: Ten artykuł zawiera informacje dla dostawców rozwiązań w chmurze, którzy chcą włączyć do oferty usługi Azure i ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 17b8fc3824fb1c7e6cfcfc3d4333dc226b51724d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653642"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>Usługa ExpressRoute dla dostawców rozwiązań w chmurze (CSP)
Firma Microsoft oferuje usługi w hiperskali dla tradycyjnych odsprzedawców i dystrybutorów (CSP), dzięki czemu użytkownicy mogą szybko aprowizować nowe usługi i rozwiązania dla klientów bez konieczności inwestowania w ich opracowywanie. Aby umożliwić dostawcy rozwiązań w chmurze (CSP) bezpośrednie zarządzanie tymi nowymi usługami, firma Microsoft udostępnia programy oraz interfejsy API, które pozwalają dostawcy CSP na zarządzanie zasobami Microsoft Azure w imieniu klientów. Jednym z tych zasobów jest usługa ExpressRoute. Usługa ExpressRoute umożliwia dostawcy CSP łączenie istniejących zasobów klienta z usługami Azure. ExpressRoute to link do usług na platformie Azure o dużej szybkości. 

ExpressRoute składa się z pary obwodów o wysokiej dostępności, które są dołączone do subskrypcji jednego klienta i nie mogą być współużytkowane przez wielu klientów. Aby zachować wysoką dostępność, każdy obwód powinien kończyć się na innym routerze.

> [!NOTE]
> Usługa ExpressRoute obejmuje przepustowość i zakończenia połączenia, co oznacza, że duże/złożone wdrożenia będą wymagały wielu obwodów usługi ExpressRoute dla jednego klienta.
> 
> 

Firma Microsoft Azure oferuje coraz więcej usług, które można zaoferować klientom. ExpressRoute pomaga użytkownikom i klientom korzystać z tych usług, zapewniając szybki dostęp do środowiska Microsoft Azure o wysokiej szybkości.

## <a name="microsoft-azure-management"></a>Zarządzanie na platformie Microsoft Azure
Firma Microsoft udostępnia dostawcom usług kryptograficznych interfejsy API do zarządzania subskrypcjami klientów platformy Azure, umożliwiając programową integrację z własnymi systemami zarządzania usługami. Obsługiwane funkcje zarządzania można znaleźć [tutaj](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Zarządzanie zasobami Microsoft Azure
Umowa z klientem określa, jak będzie zarządzana subskrypcja. Dostawca CSP może bezpośrednio zarządzać tworzeniem i obsługą zasobów lub klient może zachować kontrolę nad subskrypcją Microsoft Azure i tworzyć zasoby Azure zgodnie z zapotrzebowaniem. Jeśli klient zarządza tworzeniem zasobów w ramach subskrypcji Microsoft Azure, użyje jednego z dwóch modeli: modelu "*connect-through*" lub modelu "*Direct-to*". Te modele zostały szczegółowo opisane w poniższych sekcjach.  

### <a name="connect-through-model"></a>Model typu „połącz przez”
![Diagram przedstawiający model "connect-through".](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

W modelu typu „połącz przez” dostawca CSP tworzy bezpośrednie połączenie między centrum danych a subskrypcją Azure klienta. Połączenie bezpośrednie jest nawiązywane za pomocą usługi ExpressRoute i łączy sieć z platformą Azure. Klient łączy się z siecią. Ten scenariusz wymaga, aby klient w celu uzyskania dostępu do usług Azure korzystał z sieci dostawcy CSP. 

Jeśli klient ma inne subskrypcje platformy Azure, które nie są zarządzane przez użytkownika, mogą korzystać z publicznej sieci Internet lub własnego połączenia prywatnego do łączenia się z tymi usługami, które są obsługiwane w ramach subskrypcji spoza dostawcy CSP. 

W przypadku dostawcy CSP zarządzającego usługami platformy Azure zakłada się, że dostawca CSP ma wcześniej ustanowiony magazyn tożsamości klienta, który następnie zostanie zreplikowany do Azure Active Directory w celu zarządzania subskrypcją dostawcy usług kryptograficznych za pomocą administratora (AOBO). Kluczowe sterowniki w tym scenariuszu obejmują miejsce, w którym dany partner lub dostawca usług ma ustanowioną relację z klientem, klient korzysta obecnie z usług dostawcy lub partner chce zapewnić kombinację rozwiązań hostowanych przez dostawcę i hostowanych na platformie Azure, aby zapewnić elastyczność i sprostać wyzwaniom związanym z klientami, których nie można spełnić wyłącznie przez dostawcę usług kryptograficznych. Ten model jest przedstawiony na poniższej **ilustracji** .

![Diagram przedstawiający szczegółowy scenariusz dla modelu "connect-through".](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Model typu „połącz z”
![Diagram przedstawiający model "łączenie do".](./media/expressroute-for-cloud-solution-providers/connect-to.png)

W modelu typu „połącz z” dostawca usług tworzy bezpośrednie połączenie między centrum danych swojego klienta a subskrypcją Azure inicjowaną przez dostawcę CSP za pomocą usługi ExpressRoute za pośrednictwem sieci klienta.

> [!NOTE]
> Aby użyć usługi ExpressRoute, klient musi utworzyć i obsługiwać obwód usługi ExpressRoute.  
> 
> 

Ten scenariusz łączności wymaga, aby klient łączył się bezpośrednio za pośrednictwem sieci klienta, aby uzyskać dostęp do subskrypcji platformy Azure zarządzanej przez dostawcę CSP przy użyciu bezpośredniego połączenia sieciowego, które jest tworzone, własnością i zarządzane w całości lub w części przez klienta. W przypadku tych klientów zakłada się, że dostawca nie ma obecnie ustanowionego magazynu tożsamości klienta, a dostawca pomaga klientowi w replikacji bieżącego magazynu identyfikacji do Azure Active Directory zarządzania swoją subskrypcją za pomocą usługi AOBO. Scenariusz ten jest używany przede wszystkim wtedy, gdy dany partner lub dostawca usług ma ustanowioną relację z klientem, klient obecnie używa usług dostawcy lub partner chce dostarczać usługi oparte wyłącznie na rozwiązaniach obsługiwanych przez Azure, bez potrzeby istnienia centrum danych czy infrastruktury dostawcy.

![Diagram przedstawiający szczegółowy scenariusz dla modelu "Connect-to".](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Opcje między tymi dwiema opcjami są zależne od potrzeb klientów i Twoich bieżących potrzeb związanych z udostępnianiem usług platformy Azure. Szczegółowe informacje dotyczące tych modeli oraz powiązanych z nimi opartych na rolach wzorów projektu kontroli dostępu, sieci i tożsamości znajdują się w artykułach, do których odwołują się poniższe linki:

* **Kontrola dostępu oparta na rolach (Azure RBAC)** — RBAC jest oparta na Azure Active Directory.  Aby uzyskać więcej informacji na temat usługi Azure RBAC, zobacz [tutaj](../role-based-access-control/role-assignments-portal.md).
* **Sieć** — obejmuje różne tematy dotyczące sieci na platformie Microsoft Azure.
* **Azure Active Directory (Azure AD)** — usługa Azure AD zapewnia zarządzanie tożsamościami dla aplikacji SaaS Microsoft Azure i innych firm. Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [tutaj](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Szybkość sieci
ExpressRoute obsługuje szybkość sieci z 50 MB/s do 10 GB/s. Dzięki temu klienci mogą wykupić przepustowość odpowiadającą konkretnym potrzebom ich środowiska.

> [!NOTE]
> Przepustowość sieci można w razie potrzeby zwiększać bez przerywania komunikacji, ale zmniejszenie szybkości sieci wymaga zniszczenia obwodu i odtworzenia go z mniejszą szybkością.  
> 
> 

Usługa ExpressRoute obsługuje połączenia wielu sieci wirtualnych z jednym obwodem usługi ExpressRoute w celu lepszego wykorzystania połączeń o większej szybkości. Pojedynczy obwód usługi ExpressRoute może być współdzielony przez wiele subskrypcji Azure należących do tego samego klienta.

## <a name="configuring-expressroute"></a>Konfigurowanie usługi ExpressRoute
Usługę ExpressRoute można skonfigurować do obsługi trzech rodzajów ruchu ([domen routingu](#expressroute-routing-domains)) za pośrednictwem jednego obwodu usługi ExpressRoute. Ten ruch można podzielić na komunikację równorzędną Microsoft, publiczną i prywatną komunikację równorzędną Azure. Można wybrać jeden rodzaj lub wszystkie rodzaje ruchu do wysłania przez jeden obwód usługi ExpressRoute lub użyć wielu obwodów usługi ExpressRoute w zależności od rozmiaru obwodu usługi ExpressRoute i izolacji wymaganej przez klienta. Poziom bezpieczeństwa klienta może nie pozwalać na to, by ruch publiczny i prywatny przechodził przez ten sam obwód.

### <a name="connect-through-model"></a>Model typu „połącz przez”
W konfiguracji typu "Nawiązywanie połączenia" ponosisz odpowiedzialność za wszystkie unieruchomienia sieci, aby połączyć zasoby centrum danych z subskrypcjami hostowanymi na platformie Azure. Każdy klient, który chce użyć funkcji platformy Azure będzie potrzebować własnego połączenia za pomocą usługi ExpressRoute, które będzie zarządzane przez Ciebie. Będziesz stosować te same metody, których używałby klient do zaopatrzenia obwodu usługi ExpressRoute. Wykonasz instrukcje dotyczące inicjowania obsługi obwodów i stanów obwodów opisane w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute). Następnie skonfigurujesz trasy protokołu Border Gateway Protocol (BGP) do sterowania ruchem odbywającym się między siecią lokalną a siecią wirtualną Azure.

### <a name="connect-to-model"></a>Model typu „połącz z”
W konfiguracji typu „połącz z” klient ma już istniejące połączenie z platformą Azure lub zainicjuje połączenie z dostawcą usług internetowych łączące usługę ExpressRoute z centrum danych klienta bezpośrednio z platformą Azure, a nie z Twojego centrum danych. Aby rozpocząć proces inicjowania obsługi, klient wykona instrukcje opisane w modelu typu „połącz z” powyżej. Po ustanowieniu obwodu klient będzie musiał skonfigurować routery lokalne w taki sposób, aby mogły uzyskać dostęp do Twojej sieci i sieci wirtualnych Azure.

Możesz pomóc podczas konfigurowania połączenia i tras do zezwalania zasobom w centrum danych na komunikację z zasobami klienta w centrum danych lub zasobami obsługiwanymi na platformie Azure.

## <a name="expressroute-routing-domains"></a>Domeny routingu usługi ExpressRoute
Usługa ExpressRoute oferuje trzy domeny routingu: publiczną, prywatną i komunikacji równorzędnej firmy Microsoft. Każda z domen routingu jest konfigurowana z identycznymi routerami w konfiguracji Active-Active w celu zapewnienia wysokiej dostępności. Więcej szczegółowych informacji dotyczących domen routingu usługi ExpressRoute można znaleźć [tutaj](expressroute-circuit-peerings.md).

Możesz zdefiniować filtry tras niestandardowych w taki sposób, aby zezwalać tylko na trasy, na które chcesz zezwolić lub których potrzebujesz. Opis wprowadzania tych zmian i więcej szczegółów dotyczących filtrów routingu znajduje się w artykule [Create and modify routing for an ExpressRoute circuit using PowerShell](expressroute-howto-routing-classic.md) (Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute za pomocą programu PowerShell).

> [!NOTE]
> W przypadku firmy Microsoft i publicznej komunikacji równorzędnej połączenie musi odbywać się przez publiczny adres IP należący do klienta lub dostawcy CSP i musi być zgodne ze wszystkimi zdefiniowanymi regułami. Więcej informacji znajduje się na stronie [ExpressRoute Prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).  
> 
> 

## <a name="routing"></a>Routing
Usługa ExpressRoute łączy się z sieciami Azure za pośrednictwem usługi Azure Virtual Network. Bramy sieci zapewniają routing dla sieci wirtualnych Azure.

Tworzenie sieci wirtualnych Azure powoduje również utworzenie tabeli routingu domyślnego dla sieci wirtualnej w celu kierowania ruchu do/z jej podsieci. Jeśli tabela routingu domyślnego jest niewystarczająca dla rozwiązania, mogą zostać utworzone trasy niestandardowe do kierowania ruchu wychodzącego do urządzeń niestandardowych lub do blokowania tras do określonych podsieci lub sieci zewnętrznych.

### <a name="default-routing"></a>Routing domyślny
Tabela routingu domyślnego obejmuje następujące trasy:

* Routing w podsieci
* Podsieć do podsieci w sieci wirtualnej
* Do Internetu
* Sieć wirtualna do sieci wirtualnej przy użyciu bramy sieci VPN
* Sieć wirtualna do sieci lokalnej przy użyciu bramy sieci VPN lub usługi ExpressRoute

![Diagram przedstawiający domyślne opcje routingu.](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Routing zdefiniowany przez użytkownika (UDR)
Trasy zdefiniowane przez użytkownika umożliwiają sterowanie ruchem wychodzącym z przypisanej podsieci do innych podsieci w sieci wirtualnej lub przez jedną z innych wstępnie zdefiniowanych bram (usługę ExpressRoute, Internet lub sieć VPN). Tabelę routingu domyślnego systemu można zastąpić tabelą routingu zdefiniowanego przez użytkownika, która zastępuje tabelę routingu domyślnego trasami niestandardowymi. W przypadku routingu zdefiniowanego przez użytkownika klienci mogą tworzyć określone trasy do takich urządzeń jak zapory lub urządzenia do wykrywania włamań bądź blokować dostęp do określonych podsieci z podsieci obsługującej trasę zdefiniowaną przez użytkownika. Omówienie User-Defined tras znajdziesz [tutaj](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Zabezpieczenia
W zależności od tego, czy używany jest model „połącz z” czy „połącz przez”, klient określa zasady zabezpieczeń w sieci wirtualnej lub przekazuje wymagania zasad bezpieczeństwa dostawcy CSP do zdefiniowania dla sieci wirtualnych. Można zdefiniować następujące kryteria zabezpieczeń:

1. **Izolacja klienta** — platforma Azure zapewnia izolację klienta przez przechowywanie identyfikatora klienta i informacji o sieci wirtualnej w bezpiecznej bazie danych, używanej do hermetyzacji ruchu poszczególnych klientów w tunelu GRE.
2. Reguły **grupy zabezpieczeń sieci (NSG)** służą do definiowania dopuszczonego ruchu do i z podsieci w ramach sieci wirtualnych na platformie Azure. Domyślnie sieciowej grupy zabezpieczeń zawiera reguły blokowania, aby blokować ruch z Internetu do sieci wirtualnej i zezwalać na reguły ruchu w sieci wirtualnej. Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [tutaj](https://azure.microsoft.com/blog/network-security-groups/).
3. **Tunelowanie wymuszone** — jest to opcja przekierowywania ruchu powiązanego z Internetem pochodzącego z platformy Azure za pośrednictwem połączenia usługi ExpressRoute do lokalnego centrum danych. Więcej informacji o na temat tunelowania wymuszonego można znaleźć [tutaj](expressroute-routing.md#advertising-default-routes).  
4. **Szyfrowanie** — mimo że obwody usługi ExpressRoute są przeznaczone dla określonego klienta, istnieje możliwość naruszenia bezpieczeństwa dostawcy sieci, które pozwoliłoby intruzowi na sprawdzenie ruchu pakietu. Aby temu zapobiec, klient lub dostawca CSP może zaszyfrować ruch w ramach połączenia przez zdefiniowanie zasad trybu tunelu ochrony IPSec dla całego ruchu odbywającego się między zasobami lokalnymi i zasobami Azure (informacje na temat opcjonalnej ochrony IPSec trybu tunelu dla klienta 1 znajdują się powyżej na rysunku 5 — Zabezpieczenia usługi ExpressRoute). Druga opcja polega na użyciu urządzenia zapory na każdym punkcie końcowym obwodu usługi ExpressRoute. Wymaga to zainstalowania dodatkowych maszyn wirtualnych/urządzeń zapory innej firmy w celu zaszyfrowania ruchu przez obwód usługi ExpressRoute.

![tekst alternatywny](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Następne kroki
Usługa dostawcy rozwiązań w chmurze zapewnia sposób na zwiększenie wartości dla klientów bez konieczności nabywania kosztownej infrastruktury i zakupów funkcji, z zachowaniem pozycji podstawowego dostawcy usług firmy zewnętrznej. Bezproblemową integrację z platformą Microsoft Azure można uzyskać przez interfejs API dostawcy CSP. Umożliwia on zintegrowanie zarządzania platformą Microsoft Azure w ramach istniejących struktur zarządzania.  

Dodatkowe informacje można znaleźć, używając następujących linków:

[Azure Cloud Solution Provider program](https://docs.microsoft.com/azure/cloud-solution-provider) (Program dostawcy rozwiązań w chmurze dla platformy Azure).  
[Get ready to transact as a Cloud Solution Provider](https://partner.microsoft.com/solutions/cloud-reseller-pre-launch) (Przygotowanie do transakcji w roli dostawcy rozwiązań w chmurze).  
[Microsoft Cloud Solution Provider resources](https://partner.microsoft.com/solutions/cloud-reseller-resources) (Zasoby dostawcy rozwiązań w chmurze Microsoft).
