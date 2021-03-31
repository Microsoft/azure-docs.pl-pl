---
title: Najlepsze rozwiązania dotyczące zasobów sieciowych
titleSuffix: Azure Kubernetes Service
description: Informacje o najlepszych rozwiązaniach dotyczących operatorów klastrów dotyczących zasobów i łączności sieci wirtualnej w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 2bd332dbf9412f5c42e77b14ada3aab67ec8b66a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508592"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące łączności sieciowej i zabezpieczeń w usłudze Azure Kubernetes Service

Podczas tworzenia klastrów i zarządzania nimi w usłudze Azure Kubernetes Service (AKS) można zapewnić łączność sieciową dla węzłów i aplikacji. Te zasoby sieciowe obejmują zakresy adresów IP, moduły równoważenia obciążenia i kontrolery transferu danych przychodzących. Aby zachować wysoką jakość usługi dla aplikacji, należy zaplanować, a następnie skonfigurować te zasoby.

Te najlepsze rozwiązania koncentrują się na łączności sieciowej i zabezpieczeniach dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Porównanie trybów sieciowych korzystającą wtyczki kubenet i Azure Container Networking Interface (CNI) w AKS
> * Planowanie wymaganych adresów IP i połączeń
> * Dystrybuuj ruch przy użyciu modułów równoważenia obciążenia, kontrolerów transferu danych przychodzących lub zapory aplikacji sieci Web (WAF)
> * Bezpieczne łączenie z węzłami klastra

## <a name="choose-the-appropriate-network-model"></a>Wybierz odpowiedni model sieci

**Wskazówki dotyczące najlepszych** rozwiązań — w przypadku integracji z istniejącymi sieciami wirtualnymi lub sieciami lokalnymi Użyj usługi Azure CNI Networking w AKS. Ten model sieci umożliwia również większe rozdzielenie zasobów i kontrolek w środowisku przedsiębiorstwa.

Sieci wirtualne zapewniają podstawową łączność z węzłami AKS i klientami w celu uzyskiwania dostępu do aplikacji. Istnieją dwa różne sposoby wdrażania klastrów AKS w sieciach wirtualnych:

* **Korzystającą wtyczki kubenet Networking** — platforma Azure zarządza zasobami sieci wirtualnej w miarę wdrażania klastra i korzysta z wtyczki Kubernetes [korzystającą wtyczki kubenet][kubenet] .
* **Azure CNI Networking** — wdraża w sieci wirtualnej i używa wtyczki Kubernetes [interfejsu sieciowego kontenera platformy Azure (CNI)][cni-networking] . Usługi zasobnikowe odbierają pojedyncze adresy IP, które mogą być kierowane do innych usług sieciowych lub zasobów lokalnych.

W przypadku wdrożeń produkcyjnych zarówno korzystającą wtyczki kubenet, jak i Azure CNI są prawidłowymi opcjami.

### <a name="cni-networking"></a>CNI sieci

Interfejs sieciowy kontenera (CNI) to protokół neutralny od dostawcy, który umożliwia wykonywanie żądań do dostawcy sieci przez środowisko uruchomieniowe kontenera. Usługa Azure CNI przypisuje adresy IP do wielofirmowych i węzłów oraz udostępnia funkcje zarządzania adresami IP (IPAM) podczas nawiązywania połączenia z istniejącymi sieciami wirtualnymi platformy Azure. Każdy węzeł i zasób pod są odbierane w sieci wirtualnej platformy Azure, a żadne dodatkowe Routing nie są potrzebne do komunikowania się z innymi zasobami lub usługami.

![Diagram przedstawiający dwa węzły z mostkami łączącymi każdy z jedną siecią wirtualną platformy Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

Istotną zaletą usługi Azure CNI Networking dla środowiska produkcyjnego jest model sieci umożliwiający rozdzielenie kontroli i zarządzanie zasobami. Z punktu widzenia zabezpieczeń często chcesz, aby inne zespoły zarządzali i zabezpieczali te zasoby. Usługa Azure CNI Networking umożliwia łączenie się z istniejącymi zasobami platformy Azure, zasobami lokalnymi lub innymi usługami bezpośrednio za pośrednictwem adresów IP przypisanych do każdego z nich.

W przypadku korzystania z sieci Azure CNI, zasób sieci wirtualnej znajduje się w osobnej grupie zasobów do klastra AKS. Delegowanie uprawnień dla tożsamości klastra AKS w celu uzyskiwania dostępu do tych zasobów i zarządzania nimi. Tożsamość klastra używana przez klaster AKS musi mieć co najmniej uprawnienia [współautora sieci](../role-based-access-control/built-in-roles.md#network-contributor) w podsieci w sieci wirtualnej. Jeśli chcesz zdefiniować [rolę niestandardową](../role-based-access-control/custom-roles.md) , zamiast korzystać z wbudowanej roli współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Domyślnie AKS używa tożsamości zarządzanej dla swojej tożsamości klastra, ale istnieje możliwość użycia jednostki usługi zamiast tego. Aby uzyskać więcej informacji na temat delegowania nazwy głównej usługi AKS, zobacz [delegowanie dostępu do innych zasobów platformy Azure][sp-delegation]. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [Korzystanie z tożsamości zarządzanych](use-managed-identity.md).

Ponieważ każdy węzeł i pod otrzymają własny adres IP, Zaplanuj zakresy adresów dla podsieci AKS. Podsieć musi być wystarczająco duża, aby zapewnić adresy IP dla wszystkich wdrażanych zasobów w węźle, w poszczególnych zasobach i w sieci. Każdy klaster AKS musi być umieszczony w własnej podsieci. Aby umożliwić łączność z lokalnymi lub sieciami równorzędnymi na platformie Azure, nie używaj zakresów adresów IP, które pokrywają się z istniejącymi zasobami sieciowymi. Istnieją domyślne limity liczby kart sieciowych, które są uruchamiane w każdym węźle, zarówno w korzystającą wtyczki kubenet, jak i na platformie Azure CNI. Aby obsłużyć zdarzenia skalowania w poziomie lub uaktualnienia klastra, potrzebne są również dodatkowe adresy IP do użycia w przypisanej podsieci. Ta dodatkowa przestrzeń adresowa jest szczególnie ważna w przypadku korzystania z kontenerów systemu Windows Server, ponieważ te pule węzłów wymagają uaktualnienia w celu zastosowania najnowszych poprawek zabezpieczeń. Aby uzyskać więcej informacji na temat węzłów systemu Windows Server, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

Aby obliczyć wymagany adres IP, zobacz [Konfigurowanie usługi Azure CNI Networking w AKS][advanced-networking].

Podczas tworzenia klastra za pomocą usługi Azure CNI Networking należy określić inne zakresy adresów, które mają być używane przez klaster, takie jak adres mostka platformy Docker, IP usługi DNS i zakres adresów usługi. Ogólnie rzecz biorąc, te zakresy adresów nie powinny nakładać się na siebie i nie powinny nakładać się na żadne sieci skojarzone z klastrem, w tym między innymi sieci wirtualne, podsieci, lokalne i równorzędne sieci. Aby uzyskać szczegółowe informacje dotyczące limitów i rozmiarów tych zakresów adresów, zobacz [Konfigurowanie usługi Azure CNI Networking w AKS][advanced-networking].

### <a name="kubenet-networking"></a>Korzystającą wtyczki kubenet sieci

Chociaż korzystającą wtyczki kubenet nie wymaga konfigurowania sieci wirtualnych przed wdrożeniem klastra, istnieją wady:

* Węzły i zasobniki są umieszczane w różnych podsieciach IP. Routing zdefiniowany przez użytkownika (UDR) i przekazywanie adresów IP są używane do kierowania ruchu między nimi i węzłami. Ten dodatkowy Routing może zmniejszyć wydajność sieci.
* Połączenia z istniejącymi sieciami lokalnymi lub komunikacji równorzędnej z innymi sieciami wirtualnymi platformy Azure mogą być złożone.

Korzystającą wtyczki kubenet jest odpowiednie dla małych obciążeń programistycznych lub testowych, ponieważ nie jest konieczne tworzenie sieci wirtualnej i podsieci niezależnie od klastra AKS. Proste witryny sieci Web z niskim ruchem lub do przenoszenia i przenoszenia obciążeń do kontenerów mogą również korzystać z prostoty klastrów AKS wdrożonych przy użyciu sieci korzystającą wtyczki kubenet. W przypadku większości wdrożeń produkcyjnych należy zaplanować usługę Azure CNI Networking i korzystać z niej.

Możesz również [skonfigurować własne zakresy adresów IP i sieci wirtualne przy użyciu korzystającą wtyczki kubenet][aks-configure-kubenet-networking]. Podobnie jak w przypadku sieci Azure CNI, te zakresy adresów nie powinny nakładać się na siebie i nie powinny nakładać się na żadne sieci skojarzone z klastrem, w tym sieci wirtualne, podsieci, lokalne i sieci równorzędne. Aby uzyskać szczegółowe informacje dotyczące limitów i rozmiarów tych zakresów adresów, zobacz [Korzystanie z sieci korzystającą wtyczki kubenet z własnymi zakresami adresów IP w AKS][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Dystrybuuj ruch przychodzący

**Wskazówki dotyczące najlepszych** rozwiązań — aby dystrybuować ruch HTTP lub HTTPS do aplikacji, użyj zasobów i kontrolerów przychodzących. Kontrolery transferu danych przychodzących zapewniają dodatkowe funkcje za pośrednictwem zwykłego modułu równoważenia obciążenia platformy Azure i mogą być zarządzane jako natywne zasoby Kubernetes.

Moduł równoważenia obciążenia platformy Azure może dystrybuować ruch klientów do aplikacji w klastrze AKS, ale jest ograniczony do tego, co rozumie ten ruch. Zasób modułu równoważenia obciążenia działa w warstwie 4 i dystrybuuje ruch oparty na protokole lub portach. Większość aplikacji sieci Web korzystających z protokołu HTTP lub HTTPS powinna używać Kubernetes zasobów przychodzących i kontrolerów, które działają w warstwie 7. Ruch przychodzący można dystrybuować na podstawie adresu URL aplikacji i obsłużyć zakończenie protokołu TLS/SSL. Ta możliwość zmniejsza również liczbę ujawnianych i mapowanych adresów IP. W przypadku usługi równoważenia obciążenia każda aplikacja zwykle wymaga publicznego adresu IP przypisanego i zamapowanego na usługę w klastrze AKS. W przypadku zasobu związanego z transferem danych przychodzących pojedynczy adres IP może dystrybuować ruch do wielu aplikacji.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS](media/operator-best-practices-network/aks-ingress.png)

 Istnieją dwa składniki związane z ruchem przychodzącym:

 * *Zasób* transferu danych przychodzących i
 * *Kontroler* transferu danych przychodzących

Zasób transferu danych przychodzących to YAML manifest, `kind: Ingress` który definiuje host, certyfikaty i reguły do kierowania ruchu do usług uruchomionych w KLASTRZE AKS. Poniższy przykład manifestu YAML dystrybuuje ruch dla *MyApp.com* do jednej z dwóch usług, *blogservice* lub *storeservice*. Klient jest kierowany do jednej usługi lub innej w zależności od adresu URL, do którego uzyskuje dostęp.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Kontroler transferu danych przychodzących to demon, który działa w węźle AKS i Obserwujący żądania przychodzące. Ruch jest dystrybuowany na podstawie reguł zdefiniowanych w zasobie transferu danych przychodzących. Najbardziej typowym kontrolerem transferu danych przychodzących jest oparty na [Nginx]. AKS nie ogranicza do określonego kontrolera, dlatego można użyć innych kontrolerów, takich jak [rozkład][contour], [HAProxy][haproxy]lub [Traefik][traefik].

Kontrolery transferu danych przychodzących muszą być zaplanowane w węźle systemu Linux. Nie należy go uruchamiać w węzłach z systemem Windows Server. Użyj selektora węzłów w manifeście YAML lub we wdrożeniu wykresu Helm, aby wskazać, że zasób ma być uruchamiany w węźle opartym na systemie Linux. Aby uzyskać więcej informacji, zobacz [Używanie selektorów węzłów w celu kontrolowania, gdzie są planowane w AKS][concepts-node-selectors].

Istnieje wiele scenariuszy związanych z ruchem przychodzącym, w tym następujące przewodniki:

* [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
* [Utwórz kontroler transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
* [Tworzenie kontrolera transferu danych przychodzących korzystającego z własnych certyfikatów TLS][aks-ingress-own-tls]
* Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Zabezpieczanie ruchu za pomocą zapory aplikacji sieci Web (WAF)

**Wskazówki dotyczące najlepszych** rozwiązań — aby skanować ruch przychodzący pod kątem potencjalnych ataków, użyj zapory aplikacji sieci Web (WAF), takiej jak [Barracuda WAF dla platformy Azure][barracuda-waf] lub platformy Azure Application Gateway. Te bardziej zaawansowane zasoby sieciowe mogą również kierować ruchem poza tylko połączenia HTTP i HTTPS lub zakończenie podstawowego protokołu TLS.

Kontroler transferu danych przychodzących, który dystrybuuje ruch do usług i aplikacji, jest zazwyczaj zasobem Kubernetes w klastrze AKS. Kontroler działa jako demon w węźle AKS i zużywa niektóre zasoby węzła, takie jak procesor CPU, pamięć i przepustowość sieci. W dużych środowiskach często chcesz odciążać część tego routingu ruchu lub przerwania TLS do zasobu sieciowego poza klastrem AKS. Należy również skanować ruch przychodzący pod kątem potencjalnych ataków.

![Zapora aplikacji sieci Web (WAF), taka jak Azure App Gateway, umożliwia ochronę i dystrybucję ruchu dla klastra AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Zapora aplikacji sieci Web (WAF) zapewnia dodatkową warstwę zabezpieczeń przez filtrowanie ruchu przychodzącego. Projekt Open Web Application Security Project (OWASP) zawiera zestaw reguł służących do oglądania ataków, takich jak skrypty między lokacjami lub zatrucie plików cookie. [Usługa Azure Application Gateway][app-gateway] (obecnie dostępna w wersji zapoznawczej w AKS) to WAF, którą można zintegrować z klastrami AKS w celu zapewnienia tych funkcji zabezpieczeń, zanim ruch osiągnie klaster AKS i aplikacje. Inne rozwiązania innych firm również wykonują te funkcje, więc można nadal korzystać z istniejących inwestycji lub wiedzy w danym produkcie.

Usługa równoważenia obciążenia lub zasoby związane z ruchem przychodzącym nadal działają w klastrze AKS, aby dodatkowo udoskonalić dystrybucję ruchu. Brama aplikacji może być centralnie zarządzana jako kontroler transferu danych przychodzących z definicją zasobu. Aby rozpocząć, [utwórz Application Gateway kontroler][app-gateway-ingress]transferu danych przychodzących.

## <a name="control-traffic-flow-with-network-policies"></a>Sterowanie przepływem ruchu przy użyciu zasad sieciowych

**Wskazówki dotyczące najlepszych** rozwiązań — Użyj zasad sieciowych, aby zezwolić na ruch do zasobników lub go zabronić. Domyślnie cały ruch jest dozwolony między zasobnikami w klastrze. W celu zwiększenia bezpieczeństwa należy zdefiniować reguły ograniczające komunikację pod kątem komunikacji.

Zasady sieciowe to funkcja Kubernetes, która umożliwia sterowanie przepływem ruchu między zasobnikami. Możesz zezwalać na ruch lub odmawiać go na podstawie ustawień, takich jak przypisane etykiety, przestrzeń nazw lub port ruchu. Korzystanie z zasad sieciowych umożliwia natywne w chmurze sterowanie przepływem ruchu. Ponieważ w klastrze AKS są tworzone w sposób dynamiczny, wymagane zasady sieciowe mogą być automatycznie stosowane. Nie używaj sieciowych grup zabezpieczeń platformy Azure do sterowania ruchem ze stosu, użyj zasad sieciowych.

Aby można było używać zasad sieciowych, funkcja musi być włączona podczas tworzenia klastra AKS. Nie można włączyć zasad sieciowych w istniejącym klastrze AKS. Zaplanuj z wyprzedzeniem, aby upewnić się, że zasady sieciowe są włączone w klastrach i mogą być używane w razie potrzeby. Zasady sieciowe powinny być używane tylko dla węzłów i zasobników opartych na systemie Linux w AKS.

Zasada sieciowa jest tworzona jako zasób Kubernetes przy użyciu manifestu YAML. Zasady są stosowane do określonych zasobników, a następnie reguły ruchu przychodzącego lub wychodzącego definiują sposób, w jaki ruch może przepływać. W poniższym przykładzie zastosowano zasady sieciowe do programu z *aplikacją: etykieta zaplecza* zastosowana do nich. Reguła transferu danych przychodzących zezwala tylko na ruch z *aplikacji: etykieta frontonu* :

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Aby rozpocząć pracę z zasadami, zobacz [bezpieczny ruch sieciowy między identyfikatorami aplikacji przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Bezpieczne łączenie z węzłami za pomocą hosta bastionu

**Wskazówki dotyczące najlepszych** rozwiązań — nie ujawniaj łączności zdalnej z węzłami AKS. Utwórz hosta bastionu lub pole skoku w sieci wirtualnej zarządzania. Użyj hosta bastionu, aby bezpiecznie kierować ruch do klastra AKS do zadań zdalnego zarządzania.

Większość operacji w AKS można wykonać przy użyciu narzędzi do zarządzania platformy Azure lub serwera interfejsu API Kubernetes. Węzły AKS nie są połączone z publicznym Internetem i są dostępne tylko w sieci prywatnej. Aby nawiązać połączenie z węzłami i przeprowadzić konserwację lub rozwiązywać problemy, Roześlij połączenia za pomocą hosta bastionu lub pola skoku. Ten host powinien znajdować się w oddzielnej sieci wirtualnej zarządzania, która jest bezpiecznie komunikacji równorzędnej z siecią wirtualną klastra AKS.

![Nawiązywanie połączenia z węzłami AKS przy użyciu hosta bastionu lub pola skoku](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Sieć zarządzania dla hosta bastionu powinna być również zabezpieczona. Użyj [usługi Azure ExpressRoute][expressroute] lub [bramy sieci VPN][vpn-gateway] , aby nawiązać połączenie z siecią lokalną i kontrolować dostęp przy użyciu sieciowych grup zabezpieczeń.

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na łączności sieciowej i zabezpieczeniach. Aby uzyskać więcej informacji na temat podstawy sieci w Kubernetes, zobacz [pojęcia dotyczące sieci dla aplikacji w usłudze Azure Kubernetes Service (AKS)][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool