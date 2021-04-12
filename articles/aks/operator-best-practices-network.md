---
title: Najlepsze rozwiązania dotyczące zasobów sieciowych
titleSuffix: Azure Kubernetes Service
description: Informacje o najlepszych rozwiązaniach dotyczących operatorów klastrów dotyczących zasobów i łączności sieci wirtualnej w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 1e0212766e7d5443664d57a97cfa9ea9d0035da3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104953"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące łączności sieciowej i zabezpieczeń w usłudze Azure Kubernetes Service

Podczas tworzenia klastrów i zarządzania nimi w usłudze Azure Kubernetes Service (AKS) można zapewnić łączność sieciową dla węzłów i aplikacji. Te zasoby sieciowe obejmują zakresy adresów IP, moduły równoważenia obciążenia i kontrolery transferu danych przychodzących. Aby zachować wysoką jakość usług dla aplikacji, należy ułożeniu i skonfigurować te zasoby.

Te najlepsze rozwiązania koncentrują się na łączności sieciowej i zabezpieczeniach dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Porównaj tryby sieciowe korzystającą wtyczki kubenet i Azure Container Networking Interface (CNI) w AKS.
> * Zaplanuj wymagane adresowanie IP i łączność.
> * Dystrybuuj ruch przy użyciu modułów równoważenia obciążenia, kontrolerów transferu danych przychodzących lub zapory aplikacji sieci Web (WAF).
> * Bezpieczne łączenie z węzłami klastra.

## <a name="choose-the-appropriate-network-model"></a>Wybierz odpowiedni model sieci

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Użyj usługi Azure CNI Networking w AKS do integracji z istniejącymi sieciami wirtualnymi lub sieciami lokalnymi. Ten model sieci umożliwia większe rozdzielenie zasobów i kontrolek w środowisku przedsiębiorstwa.

Sieci wirtualne zapewniają podstawową łączność z węzłami AKS i klientami w celu uzyskiwania dostępu do aplikacji. Istnieją dwa różne sposoby wdrażania klastrów AKS w sieciach wirtualnych:

* **Sieć CNI Azure**

    Wdraża w sieci wirtualnej i używa wtyczki [usługi Azure CNI][cni-networking] Kubernetes. Usługi zasobnikowe odbierają pojedyncze adresy IP, które mogą być kierowane do innych usług sieciowych lub zasobów lokalnych.
* **Korzystającą wtyczki kubenet sieci**

    Platforma Azure zarządza zasobami sieci wirtualnej w miarę wdrażania klastra i korzysta z wtyczki [korzystającą wtyczki kubenet][kubenet] Kubernetes.


W przypadku wdrożeń produkcyjnych zarówno korzystającą wtyczki kubenet, jak i Azure CNI są prawidłowymi opcjami.

### <a name="cni-networking"></a>CNI sieci

Azure CNI to protokół neutralny od dostawcy, który umożliwia wykonywanie żądań do dostawcy sieci przez środowisko uruchomieniowe kontenera. Przypisuje adresy IP do wielofirmowych i węzłów oraz udostępnia funkcje zarządzania adresami IP (IPAM) podczas nawiązywania połączenia z istniejącymi sieciami wirtualnymi platformy Azure. Każdy węzeł i zasób pod są odbierane przy użyciu adresu IP w sieci wirtualnej platformy Azure — nie ma potrzeby dodatkowego routingu do komunikacji z innymi zasobami lub usługami.

![Diagram przedstawiający dwa węzły z mostkami łączącymi każdy z jedną siecią wirtualną platformy Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

W szczególności usługa Azure CNI Networking dla środowiska produkcyjnego umożliwia rozdzielenie kontroli i zarządzanie zasobami. Z punktu widzenia zabezpieczeń często chcesz, aby inne zespoły zarządzali i zabezpieczali te zasoby. Za pomocą usługi Azure CNI Networking można łączyć się z istniejącymi zasobami platformy Azure, zasobami lokalnymi lub innymi usługami bezpośrednio za pośrednictwem adresów IP przypisanych do każdego z nich.

W przypadku korzystania z sieci Azure CNI, zasób sieci wirtualnej znajduje się w osobnej grupie zasobów do klastra AKS. Delegowanie uprawnień dla tożsamości klastra AKS w celu uzyskiwania dostępu do tych zasobów i zarządzania nimi. Tożsamość klastra używana przez klaster AKS musi mieć co najmniej uprawnienia [współautora sieci](../role-based-access-control/built-in-roles.md#network-contributor) w podsieci w sieci wirtualnej. 

Jeśli chcesz zdefiniować [rolę niestandardową](../role-based-access-control/custom-roles.md) , zamiast korzystać z wbudowanej roli współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Domyślnie AKS używa tożsamości zarządzanej dla swojej tożsamości klastra. Można jednak zamiast tego użyć nazwy głównej usługi. Aby uzyskać więcej informacji dotyczących:
* Delegowanie nazwy głównej usługi AKS, zobacz [delegowanie dostępu do innych zasobów platformy Azure][sp-delegation]. 
* Zarządzane tożsamości, zobacz [Korzystanie z tożsamości zarządzanych](use-managed-identity.md).

Ponieważ każdy węzeł i pod otrzymuje swój własny adres IP, Zaplanuj zakresy adresów dla podsieci AKS. Należy pamiętać o następujących kwestiach:
* Podsieć musi być wystarczająco duża, aby zapewnić adresy IP dla każdego wdrożonego węzła, każdego z zasobów sieciowych i sieci. 
    * W przypadku sieci korzystającą wtyczki kubenet i Azure CNI każdy węzeł z systemem ma domyślne limity liczby.
* Każdy klaster AKS musi być umieszczony w własnej podsieci. 
* Unikaj używania zakresów adresów IP, które nakładają się na istniejące zasoby sieciowe. 
    * Jest to konieczne, aby umożliwić łączność z sieciami lokalnymi lub równorzędnymi na platformie Azure.
* Aby obsłużyć zdarzenia skalowania w poziomie lub uaktualnienia klastra, potrzebne są dodatkowe adresy IP dostępne w przypisanej podsieci. 
    * Ta dodatkowa przestrzeń adresowa jest szczególnie ważna w przypadku korzystania z kontenerów systemu Windows Server, ponieważ te pule węzłów wymagają uaktualnienia w celu zastosowania najnowszych poprawek zabezpieczeń. Aby uzyskać więcej informacji na temat węzłów systemu Windows Server, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

Aby obliczyć wymagany adres IP, zobacz [Konfigurowanie usługi Azure CNI Networking w AKS][advanced-networking].

Podczas tworzenia klastra za pomocą usługi Azure CNI Networking należy określić inne zakresy adresów dla klastra, takie jak adres mostka Docker, IP usługi DNS i zakres adresów usługi. Ogólnie rzecz biorąc upewnij się, że te zakresy adresów:
* Nie nakładaj się na siebie.
* Nie nakładają się na żadne sieci skojarzone z klastrem, w tym sieci wirtualne, podsieci, lokalne i równorzędne sieci. 

Aby uzyskać szczegółowe informacje dotyczące limitów i rozmiarów tych zakresów adresów, zobacz [Konfigurowanie usługi Azure CNI Networking w AKS][advanced-networking].

### <a name="kubenet-networking"></a>Korzystającą wtyczki kubenet sieci

Chociaż korzystającą wtyczki kubenet nie wymaga konfigurowania sieci wirtualnych przed wdrożeniem klastra, istnieją wady oczekiwania na:

* Ponieważ węzły i zasobniki są umieszczane w różnych podsieciach IP, routing zdefiniowany przez użytkownika (UDR) i przekazywanie IP kieruje ruchem między nazwami i węzłami. Ten dodatkowy Routing może zmniejszyć wydajność sieci.
* Połączenia z istniejącymi sieciami lokalnymi lub komunikacji równorzędnej z innymi sieciami wirtualnymi platformy Azure mogą być złożone.

Ponieważ nie utworzysz sieci wirtualnej i podsieci niezależnie od klastra AKS, korzystającą wtyczki kubenet jest idealnym rozwiązaniem dla:
* Małe obciążenia związane z programowaniem i testowaniem. 
* Proste witryny sieci Web z małym ruchem.
* Podnoszenie i przesuwanie obciążeń do kontenerów.

W przypadku większości wdrożeń produkcyjnych należy zaplanować usługę Azure CNI Networking i korzystać z niej.

Możesz również [skonfigurować własne zakresy adresów IP i sieci wirtualne przy użyciu korzystającą wtyczki kubenet][aks-configure-kubenet-networking]. Podobnie jak w przypadku sieci Azure CNI, te zakresy adresów nie powinny nakładać się na siebie i nie powinny nakładać się na żadne sieci skojarzone z klastrem (sieci wirtualne, podsieci, lokalne i równorzędne sieci). 

Aby uzyskać szczegółowe informacje dotyczące limitów i rozmiarów tych zakresów adresów, zobacz [Korzystanie z sieci korzystającą wtyczki kubenet z własnymi zakresami adresów IP w AKS][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Dystrybuuj ruch przychodzący

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Aby dystrybuować ruch HTTP lub HTTPS do aplikacji, użyj zasobów i kontrolerów przychodzących. W porównaniu do modułu równoważenia obciążenia platformy Azure, kontrolery transferu danych przychodzących zapewniają dodatkowe funkcje i mogą być zarządzane jako natywne zasoby Kubernetes.

Moduł równoważenia obciążenia platformy Azure może dystrybuować ruch klientów do aplikacji w klastrze AKS, ale jest to ograniczone w zrozumieniu tego ruchu. Zasób modułu równoważenia obciążenia działa w warstwie 4 i dystrybuuje ruch oparty na protokole lub portach. 

Większość aplikacji sieci Web korzystających z protokołu HTTP lub HTTPS powinna używać Kubernetes zasobów przychodzących i kontrolerów, które działają w warstwie 7. Ruch przychodzący można dystrybuować na podstawie adresu URL aplikacji i obsłużyć zakończenie protokołu TLS/SSL. Ruch przychodzący również zmniejsza liczbę ujawnianych i mapowanych adresów IP. 

W przypadku usługi równoważenia obciążenia każda aplikacja zwykle wymaga publicznego adresu IP przypisanego i zamapowanego na usługę w klastrze AKS. W przypadku zasobu związanego z transferem danych przychodzących pojedynczy adres IP może dystrybuować ruch do wielu aplikacji.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS](media/operator-best-practices-network/aks-ingress.png)

 Istnieją dwa składniki związane z ruchem przychodzącym:

 * *Zasób* transferu danych przychodzących
 * *Kontroler* transferu danych przychodzących

### <a name="ingress-resource"></a>Zasób transferu danych przychodzących

*Zasób* transferu danych przychodzących jest manifestem YAML `kind: Ingress` . Definiuje host, certyfikaty i reguły do kierowania ruchu do usług uruchomionych w klastrze AKS. 

Poniższy przykład manifestu YAML dystrybuuje ruch dla *MyApp.com* do jednej z dwóch usług, *blogservice* lub *storeservice*. Klient jest kierowany do jednej usługi lub innej w zależności od adresu URL, do którego uzyskuje dostęp.

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

### <a name="ingress-controller"></a>Kontroler transferu danych przychodzących

*Kontroler* transferu danych przychodzących to demon, który działa w węźle AKS i Obserwujący żądania przychodzące. Ruch jest dystrybuowany na podstawie reguł zdefiniowanych w zasobie transferu danych przychodzących. Chociaż najbardziej typowym kontrolerem danych wejściowych jest oparty na [Nginx], AKS nie ogranicza do określonego kontrolera. Można użyć [konturów][contour], [HAProxy][haproxy], [Traefik][traefik]itp.

Kontrolery transferu danych przychodzących muszą być zaplanowane w węźle systemu Linux. Wskaż, że zasób powinien być uruchamiany w węźle z systemem Linux przy użyciu selektora węzła w manifeście YAML lub we wdrożeniu wykresu Helm. Aby uzyskać więcej informacji, zobacz [Używanie selektorów węzłów w celu kontrolowania, gdzie są planowane w AKS][concepts-node-selectors].

> [!NOTE]
> Nie należy go uruchamiać w węzłach z systemem Windows Server.

Istnieje wiele scenariuszy związanych z ruchem przychodzącym, w tym następujące przewodniki:

* [Tworzenie podstawowego kontrolera danych wejściowych z łącznością sieci zewnętrznej][aks-ingress-basic]
* [Utwórz kontroler transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
* [Tworzenie kontrolera transferu danych przychodzących korzystającego z własnych certyfikatów TLS][aks-ingress-own-tls]
* Utwórz kontroler transferu danych przychodzących, który używa szyfrowania, aby automatycznie generować certyfikaty TLS [z dynamicznym publicznym adresem IP][aks-ingress-tls] lub [statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Zabezpieczanie ruchu za pomocą zapory aplikacji sieci Web (WAF)

> **Wskazówki dotyczące najlepszych rozwiązań**
> 
> Aby skanować ruch przychodzący pod kątem potencjalnych ataków, użyj zapory aplikacji sieci Web (WAF), takiej jak [Barracuda WAF dla platformy Azure][barracuda-waf] lub platformy Azure Application Gateway. Te bardziej zaawansowane zasoby sieciowe mogą również kierować ruchem poza tylko połączenia HTTP i HTTPS lub zakończenie podstawowego protokołu TLS.

Zazwyczaj kontroler transferu danych przychodzących jest zasobem Kubernetes w klastrze AKS, który dystrybuuje ruch do usług i aplikacji. Kontroler działa jako demon w węźle AKS i zużywa część zasobów węzła, takich jak procesor CPU, pamięć i przepustowość sieci. W większych środowiskach warto:
* Odciążanie niektórych przepływów routingu lub protokołu TLS do zasobu sieciowego poza klastrem AKS.
* Skanuj ruch przychodzący pod kątem potencjalnych ataków.

![Zapora aplikacji sieci Web (WAF), taka jak Azure App Gateway, umożliwia ochronę i dystrybucję ruchu dla klastra AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

W przypadku tej dodatkowej warstwy zabezpieczeń Zapora aplikacji sieci Web (WAF) filtruje ruch przychodzący. W przypadku zestawu reguł, otwarty projekt zabezpieczeń aplikacji sieci Web (OWASP) czuje się pod kątem ataków, takich jak skrypty między lokacjami lub zatrucie plików cookie. [Usługa Azure Application Gateway][app-gateway] (obecnie dostępna w wersji zapoznawczej w programie AKS) to WAF, która integruje się z klastrami AKS, blokując w tych funkcjach zabezpieczeń, zanim ruch osiągnie klaster AKS i aplikacje. 

Ponieważ inne rozwiązania innych firm również wykonują te funkcje, można nadal korzystać z istniejących inwestycji lub ekspertyz w twoim preferowanym produkcie.

Usługa równoważenia obciążenia lub zasoby związane z transferem danych przychodzących są ciągle uruchamiane w klastrze AKS i ograniczają rozkład ruchu. Brama aplikacji może być centralnie zarządzana jako kontroler transferu danych przychodzących z definicją zasobu. Aby rozpocząć, [utwórz Application Gateway kontroler][app-gateway-ingress]transferu danych przychodzących.

## <a name="control-traffic-flow-with-network-policies"></a>Sterowanie przepływem ruchu przy użyciu zasad sieciowych

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Użyj zasad sieciowych, aby zezwolić na ruch do zasobników lub go zabronić. Domyślnie cały ruch jest dozwolony między zasobnikami w klastrze. W celu zwiększenia bezpieczeństwa należy zdefiniować reguły ograniczające komunikację pod kątem komunikacji.

Zasady sieciowe to funkcja Kubernetes dostępna w AKS, która umożliwia sterowanie przepływem ruchu między zasobnikami. Zezwalasz na ruch lub odmawiasz go na podstawie ustawień, takich jak przypisane etykiety, przestrzeń nazw lub port ruchu. Zasady sieciowe to natywna w chmurze metoda kontrolowania przepływu ruchu dla zasobników. Ponieważ w klastrze AKS są tworzone w sposób dynamiczny, wymagane zasady sieciowe mogą być automatycznie stosowane.

Aby użyć zasad sieciowych, należy włączyć tę funkcję podczas tworzenia nowego klastra AKS. Nie można włączyć zasad sieciowych w istniejącym klastrze AKS. Planuj z wyprzedzeniem Włączanie zasad sieciowych w niezbędnych klastrach. 

>[!NOTE]
>Zasady sieciowe powinny być używane tylko dla węzłów i zasobników opartych na systemie Linux w AKS.

Zasady sieciowe można utworzyć jako zasób Kubernetes przy użyciu manifestu YAML. Zasady są stosowane do zdefiniowanych zasobników, z regułami ruchu przychodzącego lub wychodzącego definiującymi ruch. 

W poniższym przykładzie zastosowano zasady sieciowe do programu z *aplikacją: etykieta zaplecza* zastosowana do nich. Reguła komunikacji przychodzącej zezwala tylko na ruch z aplikacji w postaci *: etykieta frontonu* :

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

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Nie ujawniaj łączności zdalnej z węzłami AKS. Utwórz hosta bastionu lub pole skoku w sieci wirtualnej zarządzania. Użyj hosta bastionu, aby bezpiecznie kierować ruch do klastra AKS do zadań zdalnego zarządzania.

Większość operacji w AKS można wykonać przy użyciu narzędzi do zarządzania platformy Azure lub serwera interfejsu API Kubernetes. Węzły AKS są dostępne tylko w sieci prywatnej i nie są połączone z publicznym Internetem. Aby połączyć się z węzłami i zapewnić konserwację i obsługę, Roześlij połączenia za pomocą hosta bastionu lub pola skoku. Sprawdź, czy ten host znajduje się w oddzielnej, bezpiecznie połączonej sieci wirtualnej do sieci wirtualnej klastra AKS.

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