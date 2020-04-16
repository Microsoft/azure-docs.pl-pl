---
title: Najważniejsze wskazówki dotyczące zasobów sieciowych
titleSuffix: Azure Kubernetes Service
description: Poznaj najważniejsze wskazówki dotyczące obsługi zasobów i łączności sieci wirtualnej w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 1eed6f1f82a8a91b2335760e99ea6b895d15547e
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392712"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące łączności sieciowej i zabezpieczeń w usłudze Azure Kubernetes Service

Podczas tworzenia klastrów i zarządzania nimi w usłudze Azure Kubernetes Service (AKS) zapewniasz łączność sieciową dla węzłów i aplikacji. Te zasoby sieciowe obejmują zakresy adresów IP, moduły równoważenia obciążenia i kontrolery transferu danych przychodzących. Aby utrzymać wysoką jakość usług dla aplikacji, należy zaplanować, a następnie skonfigurować te zasoby.

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na łączności sieciowej i zabezpieczeniach operatorów klastrów. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Porównanie trybów sieciowych kubenet i Azure CNI w usłudze AKS
> * Planowanie wymaganego adresowania IP i łączności
> * Rozdzielanie ruchu przy użyciu modułów równoważenia obciążenia, kontrolerów transferu danych przychodzących lub zapory aplikacji sieci web (WAF)
> * Bezpieczne łączenie się z węzłami klastra

## <a name="choose-the-appropriate-network-model"></a>Wybierz odpowiedni model sieci

**Wskazówki dotyczące najlepszych rozwiązań** — aby uzyskać integrację z istniejącymi sieciami wirtualnymi lub sieciami lokalnymi, użyj sieci CNI platformy Azure w usłudze AKS. Ten model sieci umożliwia również większe oddzielenie zasobów i formantów w środowisku przedsiębiorstwa.

Sieci wirtualne zapewniają podstawową łączność dla węzłów AKS i klientów, aby uzyskać dostęp do aplikacji. Klastry AKS można wdrożyć w sieciach wirtualnych na dwa różne sposoby:

* **Sieć Kubenet** — platforma Azure zarządza zasobami sieci wirtualnej podczas wdrażania klastra i używa wtyczki [kubenet][kubenet] Kubernetes.
* **Sieci CNI platformy Azure** — wdraża w istniejącej sieci wirtualnej i używa wtyczki Kubernetes [interfejsu sieci kontenera azure (CNI).][cni-networking] Zasobniki otrzymują indywidualne wiadomości IP, które mogą być kierowane do innych usług sieciowych lub zasobów lokalnych.

Interfejs sieci kontenera (CNI) to protokół neutralny dla dostawców, który umożliwia środowisko wykonawczemu kontenera żądania do dostawcy sieci. Usługa Azure CNI przypisuje adresy IP do zasobników i węzłów oraz udostępnia funkcje zarządzania adresami IP (IPAM) podczas łączenia się z istniejącymi sieciami wirtualnymi platformy Azure. Każdy zasób węzła i zasobu zasobu odbiera adres IP w sieci wirtualnej platformy Azure i nie jest potrzebne żadne dodatkowe routingu do komunikowania się z innymi zasobami lub usługami.

![Diagram przedstawiający dwa węzły z mostkami łączącymi każdy z jedną siecią wirtualną platformy Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

W przypadku większości wdrożeń produkcyjnych należy użyć sieci Azure CNI. Ten model sieci umożliwia oddzielenie kontroli i zarządzania zasobami. Z punktu widzenia zabezpieczeń często chcesz, aby różne zespoły zarządzał i zabezpieczał te zasoby. Sieci CNI platformy Azure umożliwia łączenie się z istniejącymi zasobami platformy Azure, zasobów lokalnych lub innych usług bezpośrednio za pośrednictwem adresów IP przypisanych do każdego zasobnika.

Korzystając z sieci CNI platformy Azure, zasób sieci wirtualnej znajduje się w osobnej grupie zasobów do klastra AKS. Delegować uprawnienia jednostki usługi AKS do dostępu i zarządzania tymi zasobami. Podmiot zabezpieczeń usługi używany przez klaster AKS musi mieć co najmniej uprawnienia [współautora sieci](../role-based-access-control/built-in-roles.md#network-contributor) w podsieci w sieci wirtualnej. Jeśli chcesz zdefiniować [rolę niestandardową](../role-based-access-control/custom-roles.md) zamiast wbudowanej roli współautora sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Aby uzyskać więcej informacji na temat delegowania jednostki usługi AKS, zobacz [Delegowanie dostępu do innych zasobów platformy Azure][sp-delegation]. Zamiast jednostki usługi można również użyć systemu przypisanego tożsamości zarządzanej dla uprawnień. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

Ponieważ każdy węzeł i zasobnik otrzymują własny adres IP, zaplanuj zakresy adresów dla podsieci AKS. Podsieć musi być wystarczająco duża, aby zapewnić adresy IP dla każdego węzła, zasobników i zasobów sieciowych, które wdrażasz. Każdy klaster AKS musi być umieszczony we własnej podsieci. Aby zezwolić na łączność z sieciami lokalnymi lub równorzędnych na platformie Azure, nie należy używać zakresów adresów IP, które nakładają się na istniejące zasoby sieciowe. Istnieją domyślne limity liczby zasobników, które każdy węzeł jest uruchamiany za pomocą sieci kubenet i azure CNI. Aby obsłużyć zdarzenia skalowania w poziomie lub uaktualnienia klastra, potrzebne są również dodatkowe adresy IP dostępne do użycia w przypisanej podsieci. Ta dodatkowa przestrzeń adresowa jest szczególnie ważna w przypadku korzystania z kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS), ponieważ te pule węzłów wymagają uaktualnienia w celu zastosowania najnowszych poprawek zabezpieczeń. Aby uzyskać więcej informacji o węzłach systemu Windows Server, zobacz [Uaktualnianie puli węzłów w systemie AKS][nodepool-upgrade].

Aby obliczyć wymagany adres IP, zobacz [Konfigurowanie sieci CNI platformy Azure w usłudze AKS][advanced-networking].

### <a name="kubenet-networking"></a>Sieć Kubenet

Mimo że kubenet nie wymaga konfigurowania sieci wirtualnych przed wdrożeniem klastra, istnieją wady:

* Węzły i zasobniki są umieszczane w różnych podsieciach IP. Routing zdefiniowany przez użytkownika (UDR) i przekierowanie IP jest używany do kierowania ruchu między zasobnikami i węzłami. Ta dodatkowa routing może zmniejszyć wydajność sieci.
* Połączenia z istniejącymi sieciami lokalnymi lub komunikacja równorzędna z innymi sieciami wirtualnymi platformy Azure może być złożona.

Kubenet jest odpowiedni dla małych obciążeń deweloperskich lub testowych, ponieważ nie trzeba tworzyć sieci wirtualnej i podsieci oddzielnie od klastra AKS. Proste witryny sieci Web o małym natężeniu ruchu lub w celu podniesienia i przeniesienia obciążeń do kontenerów mogą również korzystać z prostoty klastrów AKS wdrożonych w sieciach kubenet. W przypadku większości wdrożeń produkcyjnych należy zaplanować i używać sieci Azure CNI. Można również [skonfigurować własne zakresy adresów IP i sieci wirtualne za pomocą kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Dystrybucja ruchu przychodzącego

**Wskazówki dotyczące najlepszych rozwiązań** — aby rozpowszechniać ruch HTTP lub HTTPS do aplikacji, użyj zasobów transferu danych przychodzących i kontrolerów. Kontrolery transferu danych przychodzących zapewniają dodatkowe funkcje za pomocą zwykłego modułu równoważenia obciążenia platformy Azure i mogą być zarządzane jako natywne zasoby kubernetes.

Moduł równoważenia obciążenia platformy Azure może dystrybuować ruch klientów do aplikacji w klastrze AKS, ale jest ograniczony w tym, co rozumie o tym ruchu. Zasób modułu równoważenia obciążenia działa w warstwie 4 i dystrybuuje ruch na podstawie protokołu lub portów. Większość aplikacji sieci web korzystających z protokołu HTTP lub HTTPS powinna używać zasobów i kontrolerów transferu danych przychodzących kuberenetes, które działają w warstwie 7. Ruch przychodzący może rozpowszechniać ruch na podstawie adresu URL aplikacji i obsługiwać zakończenie protokołu TLS/SSL. Ta możliwość zmniejsza również liczbę adresów IP, które są wystawiane i mapowane. Za pomocą modułu równoważenia obciążenia każda aplikacja zazwyczaj potrzebuje publicznego adresu IP przypisanego i mapowanego do usługi w klastrze AKS. Za pomocą zasobu przychodzących pojedynczy adres IP może dystrybuować ruch do wielu aplikacji.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS](media/operator-best-practices-network/aks-ingress.png)

 Istnieją dwa składniki dla ruchu przychodzącego:

 * *Zasób*transferu przychodzącego i
 * Kontroler transferu *danych* przychodzących

Zasób transferu danych przychodzących `kind: Ingress` jest manifestem YAML, który definiuje hosta, certyfikaty i reguły do kierowania ruchu do usług uruchamianych w klastrze AKS. Poniższy przykład manifestu YAML będzie dystrybuować ruch dla *myapp.com* do jednej z dwóch usług, *usługi bloga* lub *usługi storeservice.* Klient jest kierowany do jednej usługi lub do drugiej na podstawie adresu URL, do który uzyskuje dostęp.

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

Kontroler transferu danych przychodzących to demon, który działa w węźle AKS i obserwuje żądania przychodzące. Ruch jest następnie dystrybuowany na podstawie reguł zdefiniowanych w zasobie transferu danych przychodzących. Najczęściej występujący kontroler ruchu przychodzącego jest oparty na [NGINX]. AKS nie ogranicza cię do określonego kontrolera, więc można użyć innych kontrolerów, takich jak [Contour][contour], [HAProxy][haproxy]lub [Traefik][traefik].

Kontrolery transferu danych przychodzących muszą być zaplanowane w węźle systemu Linux. Węzły systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS) nie powinny uruchamiać kontrolera transferu danych przychodzących. Użyj selektora węzłów w manifeście YAML lub we wdrożeniu wykresu Helm, aby wskazać, że zasób powinien działać w węźle opartym na systemie Linux. Aby uzyskać więcej informacji, zobacz [Używanie selektorów węzłów do kontrolowania, gdzie zasobniki są zaplanowane w aks][concepts-node-selectors].

Istnieje wiele scenariuszy dla transferu przychodzącego, w tym następujące przewodniki in-to:

* [Tworzenie podstawowego kontrolera transferu danych przychodzących z zewnętrzną łącznością sieciową][aks-ingress-basic]
* [Tworzenie kontrolera transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
* [Tworzenie kontrolera transferu danych przychodzących, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
* Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub ze [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Zabezpieczanie ruchu za pomocą zapory aplikacji sieci Web (WAF)

**Wskazówki dotyczące najlepszych rozwiązań** — aby skanować ruch przychodzący w poszukiwaniu potencjalnych ataków, użyj zapory aplikacji sieci web (WAF), takiej jak [Barracuda WAF for Azure][barracuda-waf] lub Azure Application Gateway. Te bardziej zaawansowane zasoby sieciowe mogą również kierować ruch poza tylko połączenia HTTP i HTTPS lub podstawowe zakończenie protokołu SSL.

Kontroler transferu danych przychodzących, który dystrybuuje ruch do usług i aplikacji jest zazwyczaj zasobem Kubernetes w klastrze AKS. Kontroler działa jako demon w węźle AKS i zużywa niektóre zasoby węzła, takie jak procesor, pamięć i przepustowość sieci. W większych środowiskach często chcesz odciążyć część tego routingu ruchu lub zakończenia TLS do zasobu sieciowego poza klastrem AKS. Chcesz również skanować ruch przychodzący w poszukiwaniu potencjalnych ataków.

![Zapora aplikacji sieci Web (WAF), taka jak Usługa Azure App Gateway, może chronić i dystrybuować ruch w klastrze AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Zapora aplikacji sieci web (WAF) zapewnia dodatkową warstwę zabezpieczeń przez filtrowanie ruchu przychodzącego. Projekt bezpieczeństwa otwartej aplikacji sieci Web (OWASP) udostępnia zestaw reguł do obserwowaniu ataków, takich jak skrypty krzyżowe witryny lub zatruwanie plikami cookie. [Brama aplikacji platformy Azure][app-gateway] (obecnie w wersji zapoznawczej w usłudze AKS) to usługa WAF, która może integrować się z klastrami usługi AKS w celu zapewnienia tych funkcji zabezpieczeń, zanim ruch dotrze do klastra i aplikacji AKS. Inne rozwiązania innych firm również wykonują te funkcje, dzięki czemu można nadal korzystać z istniejących inwestycji lub wiedzy specjalistycznej w danym produkcie.

Moduł równoważenia obciążenia lub zasoby transferu danych przychodzących nadal działają w klastrze usługi AKS w celu dalszego uściślania dystrybucji ruchu. Brama aplikacji może być centralnie zarządzana jako kontroler transferu danych przychodzących z definicją zasobu. Aby rozpocząć, [należy utworzyć kontroler transferu danych przychodzących bramy aplikacji][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Sterowanie przepływem ruchu za pomocą zasad sieciowych

**Wskazówki dotyczące najlepszych rozwiązań** — użyj zasad sieciowych, aby zezwolić lub odmówić ruchu zasobnikom. Domyślnie cały ruch jest dozwolony między zasobnikami w klastrze. Aby zwiększyć bezpieczeństwo, należy zdefiniować reguły, które ograniczają komunikację zasobników.

Zasady sieciowe to funkcja Kubernetes, która umożliwia sterowanie przepływem ruchu między zasobnikami. Można zezwolić lub odmówić ruchu na podstawie ustawień, takich jak przypisane etykiety, obszar nazw lub port ruchu. Korzystanie z zasad sieciowych daje natywnego sposobu chmury do kontrolowania przepływu ruchu. Ponieważ zasobniki są tworzone dynamicznie w klastrze AKS, wymagane zasady sieciowe mogą być automatycznie stosowane. Nie używaj grup zabezpieczeń sieci platformy Azure do kontrolowania ruchu zasobników, używaj zasad sieciowych.

Aby korzystać z zasad sieciowych, funkcja musi być włączona podczas tworzenia klastra AKS. Nie można włączyć zasad sieciowych w istniejącym klastrze AKS. Planuj z wyprzedzeniem, aby upewnić się, że włączysz zasady sieciowe w klastrach i możesz ich używać w razie potrzeby. Zasady sieciowe powinny być używane tylko dla węzłów i zasobników opartych na systemie Linux w udręki AKS.

Zasady sieciowe są tworzone jako zasób Kubernetes przy użyciu manifestu YAML. Zasady są stosowane do zdefiniowanych zasobników, a następnie reguły ruchu przychodzącego lub wychodzącego definiują sposób przepływu ruchu. Poniższy przykład stosuje zasady sieci do zasobników z *etykietą aplikacji: wewnętrznej bazy danych* zastosowane do nich. Reguła transferu danych przychodzących zezwala na ruch tylko z zasobników za pomocą etykiety *aplikacji: fronton:*

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

Aby rozpocząć korzystanie z zasad, zobacz [Bezpieczny ruch między zasobnikami przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Bezpieczne łączenie się z węzłami za pośrednictwem hosta bastionu

**Wskazówki dotyczące najlepszych rozwiązań** — nie udostępniaj łączności zdalnej w węzłach usługi AKS. Utwórz hosta bastionu lub pole przeskoku w sieci wirtualnej zarządzania. Użyj hosta bastionu, aby bezpiecznie kierować ruch do klastra AKS do zadań zdalnego zarządzania.

Większość operacji w usłudze AKS można wykonać za pomocą narzędzi do zarządzania platformy Azure lub za pośrednictwem serwera interfejsu API usługi Kubernetes. Węzły usługi AKS nie są połączone z publicznym Internetem i są dostępne tylko w sieci prywatnej. Aby połączyć się z węzłami i wykonać konserwację lub rozwiązać problemy, należy kierować połączenia za pośrednictwem hosta bastionu lub pola szybkiego dostępu. Ten host powinien znajdować się w oddzielnej sieci wirtualnej zarządzania, która jest bezpiecznie równorzędna z siecią wirtualną klastra AKS.

![Łączenie się z węzłami AKS przy użyciu hosta bastionu lub pola szybkiego dostępu](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Sieć zarządzania dla hosta bastionu powinna być również zabezpieczona. Użyj [bramy usługi Azure ExpressRoute][expressroute] lub [bramy sieci VPN,][vpn-gateway] aby połączyć się z siecią lokalną i kontrolować dostęp przy użyciu sieciowych grup zabezpieczeń.

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na łączności sieciowej i bezpieczeństwie. Aby uzyskać więcej informacji na temat podstaw sieci w usłudze Kubernetes, zobacz [Pojęcia dotyczące sieci dla aplikacji w usłudze Azure Kubernetes (AKS)][aks-concepts-network]

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