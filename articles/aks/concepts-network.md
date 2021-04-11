---
title: Pojęcia — obsługa sieci w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej na temat sieci w usłudze Azure Kubernetes Service (AKS), w tym korzystającą wtyczki kubenet i Azure CNI Networking, kontrolerów przychodzących, modułów równoważenia obciążenia i statycznych adresów IP.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105344"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Pojęcia dotyczące sieci dla aplikacji w usłudze Azure Kubernetes Service (AKS)

W przypadku mikrousług opartych na kontenerach podejście do tworzenia aplikacji, składniki aplikacji współpracują ze sobą, aby przetwarzać zadania. Kubernetes oferuje różne zasoby umożliwiające współpracę:
* Możesz łączyć się z aplikacjami i udostępniać je wewnętrznie lub zewnętrznie. 
* Możesz tworzyć aplikacje o wysokiej dostępności, równoważyć obciążenie aplikacji. 
* W przypadku bardziej złożonych aplikacji można skonfigurować ruch przychodzący dla zakończenia protokołu SSL/TLS lub Routing wielu składników. 
* Ze względów bezpieczeństwa można ograniczyć przepływ ruchu sieciowego do lub między nimi i węzłami.

W tym artykule przedstawiono podstawowe koncepcje zapewniające obsługę sieci dla aplikacji w programie AKS:

- [Usługi](#services)
- [Sieci wirtualne platformy Azure](#azure-virtual-networks)
- [Kontrolery ruchu przychodzącego](#ingress-controllers)
- [Zasady sieciowe](#network-policies)

## <a name="kubernetes-basics"></a>Podstawy platformy Kubernetes

Aby zezwolić na dostęp do aplikacji lub między składnikami aplikacji, Kubernetes zapewnia warstwę abstrakcji dla sieci wirtualnych. Węzły Kubernetes łączą się z siecią wirtualną, zapewniając łączność ruchu przychodzącego i wychodzącego dla zasobników. Składnik *polecenia-proxy* działa w każdym węźle, aby udostępnić te funkcje sieciowe.

W Kubernetes:
* *Usługi* logicznie grupują elementy w celu umożliwienia bezpośredniego dostępu do określonego portu za pośrednictwem adresu IP lub nazwy DNS. 
* Ruch można dystrybuować przy użyciu *modułu równoważenia obciążenia*. 
* Bardziej skomplikowany Routing ruchu aplikacji można również uzyskać za pomocą *kontrolerów* transferu danych przychodzących. 
* Zabezpieczenia i filtrowanie ruchu sieciowego dla każdego z nich jest możliwe przy użyciu *zasad sieciowych* Kubernetes.

Platforma Azure upraszcza także sieci wirtualne klastrów AKS. Podczas tworzenia modułu równoważenia obciążenia Kubernetes należy również utworzyć i skonfigurować źródłowy zasób modułu równoważenia obciążenia platformy Azure. Podczas otwierania portów sieciowych do zasobników, są konfigurowane odpowiednie reguły grupy zabezpieczeń sieci platformy Azure. W przypadku routingu aplikacji HTTP platforma Azure może również skonfigurować *zewnętrzny system DNS* jako nowe trasy transferu danych przychodzących.

## <a name="services"></a>Usługi

Aby uprościć konfigurację sieci dla obciążeń aplikacji, Kubernetes używa *usług* do logicznego grupowania zestawu i zapewnienia łączności sieciowej. Dostępne są następujące typy usług:

- **Adres IP klastra** 
  
  Tworzy wewnętrzny adres IP do użycia w klastrze AKS. Dobre dla aplikacji tylko wewnętrznych, które obsługują inne obciążenia w klastrze.

    ![Diagram przedstawiający przepływ ruchu IP klastra w klastrze AKS][aks-clusterip]

- **NodePort** 

  Tworzy mapowanie portów w podstawowym węźle, który umożliwia dostęp do aplikacji bezpośrednio z adresem IP i portem węzła.

    ![Diagram przedstawiający przepływ ruchu NodePort w klastrze AKS][aks-nodeport]

- **LoadBalancer** 

  Tworzy zasób modułu równoważenia obciążenia platformy Azure, konfiguruje zewnętrzny adres IP i łączy żądaną pulę zasobów z pulą zaplecza modułu równoważenia obciążenia. Aby umożliwić klientom dostęp do aplikacji, reguły równoważenia obciążenia są tworzone na odpowiednich portach. 

    ![Diagram przedstawiający przepływ ruchu Load Balancer w klastrze AKS][aks-loadbalancer]

    W celu zapewnienia dodatkowej kontroli i routingu ruchu przychodzącego można użyć [kontrolera](#ingress-controllers)transferu danych przychodzących.

- **Zewnętrznaname** 

  Tworzy określony wpis DNS w celu ułatwienia dostępu do aplikacji.

Można dynamicznie przypisywać usługi równoważenia obciążenia i adresy IP usług lub określić istniejący statyczny adres IP. Można przypisać wewnętrzne i zewnętrzne statyczne adresy IP. Istniejące statyczne adresy IP są często powiązane z wpisem DNS.

Można utworzyć *wewnętrzne* i *zewnętrzne* usługi równoważenia obciążenia. Do wewnętrznych modułów równoważenia obciążenia przypisany jest tylko prywatny adres IP, dlatego nie można uzyskać do nich dostępu z Internetu.

## <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

W usłudze AKS można wdrożyć klaster, który jest oparty na jednym z następujących modeli sieciowych:

- *Korzystającą wtyczki kubenet* sieci

  Zasoby sieciowe są zwykle tworzone i konfigurowane jako wdrożony klaster AKS.

- Sieć *Azure Container Network Interface (CNI)*
 
  klaster usługi AKS jest połączony z istniejącymi zasobami i konfiguracjami sieci wirtualnej.

### <a name="kubenet-basic-networking"></a>Sieć korzystającą wtyczki kubenet (podstawowa)

Opcja sieci *korzystającą wtyczki kubenet* jest konfiguracją domyślną dla tworzenia klastra AKS. Z *korzystającą wtyczki kubenet*:
1. Węzły otrzymują adres IP z podsieci sieci wirtualnej platformy Azure. 
1. W przypadku odbioru adresów IP od logicznej przestrzeni adresowej w jednostkach. 
1. Dzięki skonfigurowaniu translatora adresów sieciowych (NAT) zasobniki mogą uzyskać dostęp do zasobów w sieci wirtualnej platformy Azure. 
1. Źródłowy adres IP ruchu jest tłumaczony na podstawowy adres IP węzła.

Węzły korzystają z wtyczki [korzystającą wtyczki kubenet][kubenet] Kubernetes. Oto co możesz zrobić:
* Zezwól platformie Azure na tworzenie i Konfigurowanie sieci wirtualnych. 
* Wybierz wdrożenie klastra AKS w istniejącej podsieci sieci wirtualnej. 

Pamiętaj, że tylko węzły otrzymują adres IP z obsługą routingu. Przy użyciu translatora adresów sieciowych można komunikować się z innymi zasobami poza klastrem AKS. Takie podejście zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej, aby można było korzystać z nich.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sieci korzystającą wtyczki kubenet dla klastra AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (Advanced) — sieć

W sieci Azure CNI każdy zasobnik uzyskuje adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być planowane z góry i unikatowe w całej przestrzeni sieciowej. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby obsługiwanych przez nią identyfikatorów. Równoważna liczba adresów IP na węzeł jest następnie zastrzeżona. Bez planowania takie podejście może prowadzić do wyczerpania adresów IP lub konieczność odbudowania klastrów w większej podsieci, w miarę wzrostu wymagań aplikacji.

W przeciwieństwie do korzystającą wtyczki kubenet, ruch do punktów końcowych w tej samej sieci wirtualnej nie jest NAT do podstawowego adresu IP węzła. Adres źródłowy ruchu w sieci wirtualnej jest adresem IP pod. Ruch zewnętrzny do sieci wirtualnej nadal jest NAT na podstawowym adresie IP węzła.

W węzłach jest używana wtyczka [Azure CNI][cni-networking] Kubernetes.

![Diagram przedstawiający dwa węzły z mostkami łączącymi każdy z jedną siecią wirtualną platformy Azure][advanced-networking-diagram]

Aby uzyskać więcej informacji, zobacz [Konfigurowanie platformy Azure CNI dla klastra AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Porównanie modeli sieci

Zarówno korzystającą wtyczki kubenet, jak i Azure CNI zapewniają łączność sieciową dla klastrów AKS. Istnieją jednak zalety i wady każdej z nich. Na wysokim poziomie są stosowane następujące zagadnienia:

* **korzystającą wtyczki kubenet**
    * Zachowuje przestrzeń adresów IP.
    * Używa wewnętrznego lub zewnętrznego modułu równoważenia obciążenia Kubernetes w celu uzyskania dostępu do elementów z zewnątrz klastra.
    * Można ręcznie zarządzać i obsługiwać trasy zdefiniowane przez użytkownika (UDR).
    * Maksymalnie 400 węzłów na klaster.
* **Azure CNI**
    * W celu uzyskania pełnej łączności między sieciami wirtualnymi i można je uzyskać bezpośrednio z połączonych sieci.
    * Wymaga więcej przestrzeni adresów IP.

Między korzystającą wtyczki kubenet i Azure CNI istnieją następujące różnice dotyczące zachowań:

| Możliwość                                                                                   | Korzystającą wtyczki kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Wdróż klaster w istniejącej lub nowej sieci wirtualnej                                            | Obsługiwane — UDR ręcznie | Obsługiwane |
| Łączność pod kątem                                                                         | Obsługiwane | Obsługiwane |
| Łączność z maszyną wirtualną; Maszyna wirtualna w tej samej sieci wirtualnej                                          | Działa po zainicjowaniu przez | Działa w obu kierunkach |
| Łączność z maszyną wirtualną; Maszyna wirtualna w równorzędnej sieci wirtualnej                                            | Działa po zainicjowaniu przez | Działa w obu kierunkach |
| Dostęp lokalny przy użyciu sieci VPN lub usługi Express Route                                                | Działa po zainicjowaniu przez | Działa w obu kierunkach |
| Dostęp do zasobów zabezpieczonych przez punkty końcowe usługi                                             | Obsługiwane | Obsługiwane |
| Uwidacznianie usług Kubernetes Services za pomocą usługi równoważenia obciążenia, bramy aplikacji lub kontrolera transferu danych przychodzących | Obsługiwane | Obsługiwane |
| Domyślne Azure DNS i strefy prywatne                                                          | Obsługiwane | Obsługiwane |

W przypadku systemu DNS, z dodatkiem korzystającą wtyczki kubenet i usługą Azure CNI w systemie DNS są oferowane przez CoreDNS, wdrożenie działające w AKS z własnym skalowaniem automatycznym. Aby uzyskać więcej informacji na temat CoreDNS na Kubernetes, zobacz [Dostosowywanie usługi DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). Domyślnie CoreDNS jest skonfigurowany do przekazywania nieznanych domen do funkcji DNS Virtual Network platformy Azure, w której wdrożono klaster AKS. W związku z tym strefy Azure DNS i prywatne będą działać w przypadku zasobników z systemem AKS.

### <a name="support-scope-between-network-models"></a>Zakres pomocy technicznej między modelami sieci

Niezależnie od używanego modelu sieci zarówno korzystającą wtyczki kubenet, jak i Azure CNI można wdrożyć w jeden z następujących sposobów:

* Platforma Azure może automatycznie tworzyć i konfigurować zasoby sieci wirtualnej podczas tworzenia klastra AKS.
* Możesz ręcznie utworzyć i skonfigurować zasoby sieci wirtualnej i dołączyć je do tych zasobów podczas tworzenia klastra AKS.

Mimo że funkcje, takie jak punkty końcowe usługi lub UDR, są obsługiwane zarówno w korzystającą wtyczki kubenet, jak i na platformie Azure CNI, [zasady pomocy technicznej dla AKS][support-policies] określają, jakie zmiany można wprowadzić. Na przykład:

* Jeśli ręcznie utworzysz zasoby sieci wirtualnej dla klastra AKS, jest ono obsługiwane podczas konfigurowania własnych UDR lub punktów końcowych usługi.
* Jeśli platforma Azure automatycznie tworzy zasoby sieci wirtualnej dla klastra AKS, nie można ręcznie zmienić tych zasobów zarządzanych przez AKS, aby skonfigurować własne UDR lub punkty końcowe usługi.

## <a name="ingress-controllers"></a>Kontrolery ruchu przychodzącego

Podczas tworzenia usługi typu modułu równoważenia obciążenia można również utworzyć źródłowy zasób usługi Azure load module. Moduł równoważenia obciążenia jest skonfigurowany do dystrybucji ruchu do zasobników w usłudze na dany port. 

Moduł równoważenia obciążenia działa tylko w warstwie 4. W warstwie 4 usługa nie jest w posiadaniu informacji o rzeczywistych aplikacjach i nie może być bardziej zagadnienia dotyczące routingu.

*Kontrolery* transferu danych przychodzących działają w warstwie 7 i mogą używać bardziej inteligentnych reguł do dystrybucji ruchu aplikacji. Kontrolery transferu danych przychodzących zazwyczaj kierują ruch HTTP do różnych aplikacji na podstawie przychodzącego adresu URL.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS][aks-ingress]

### <a name="create-an-ingress-resource"></a>Tworzenie zasobu transferu danych przychodzących
W AKS można utworzyć zasób transferu danych przychodzących za pomocą NGINX, podobnego narzędzia lub funkcji routingu aplikacji HTTP AKS. Po włączeniu routingu aplikacji protokołu HTTP dla klastra AKS platforma Azure tworzy kontroler transferu danych przychodzących i *zewnętrzny kontroler DNS* . Ponieważ nowe zasoby związane z ruchem przychodzącym są tworzone w Kubernetes, wymagane są rekordy A DNS w strefie DNS. 

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji http Routing][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Application Gateway kontroler transferu danych przychodzących (AGIC)

Za pomocą dodatku Application Gateway AGIC (inAKS Controller) klienci korzystają z usług równoważenia obciążenia macierzystego Application Gateway poziomu 7 platformy Azure w celu udostępnienia oprogramowania chmury do Internetu. AGIC monitoruje klaster Kubernetes hosta i ciągle aktualizuje Application Gateway, uwidaczniając wybrane usługi w Internecie. 

Aby dowiedzieć się więcej na temat dodatku AGIC dla AKS, zobacz [co to jest Application Gateway kontroler][agic-overview]transferu danych przychodzących?

### <a name="ssltls-termination"></a>Zakończenie protokołu SSL/TLS

Zakończenie protokołu SSL/TLS jest kolejną wspólną funkcją transferu danych przychodzących. W przypadku dużych aplikacji sieci Web, do których uzyskuje się dostęp za pośrednictwem protokołu HTTPS, zasób transferu danych przychodzących obsługuje zakończenie protokołu TLS zamiast samej aplikacji. Aby zapewnić automatyczną generację i konfigurację certyfikacji TLS, można skonfigurować zasób transferu danych przychodzących tak, aby korzystał z dostawców, takich jak "Szyfrujmy". 

Aby uzyskać więcej informacji na temat konfigurowania kontrolera NGINX ingresing z szyfrowaniem, zobacz artykuł dotyczący [protokołów przychodzących i TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>Zachowywanie adresu IP źródła klienta

Skonfiguruj kontroler transferu danych przychodzących, aby zachować źródłowy adres IP klienta w przypadku żądań do kontenerów w klastrze AKS. Gdy kontroler transferu danych przychodzących kieruje żądanie klienta do kontenera w klastrze AKS, oryginalny źródłowy adres IP tego żądania jest niedostępny dla kontenera docelowego. Po włączeniu *zachowywania źródłowego adresu IP klienta* jest dostępny źródłowy adres IP klienta w nagłówku żądania w obszarze *X-forwardd-for*. 

W przypadku korzystania z funkcji zachowywania źródłowych adresów IP klienta na kontrolerze transferu danych przychodzących nie można używać przekazywania protokołu TLS. Przechowywanie źródłowych adresów IP klienta i przekazywanie protokołu TLS mogą być używane z innymi usługami, takimi jak typ *modułu równoważenia obciążenia* .

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowa Grupa zabezpieczeń filtruje ruch dla maszyn wirtualnych, takich jak węzły AKS. Podczas tworzenia usług, takich jak moduł równoważenia obciążenia, platforma Azure automatycznie konfiguruje niezbędne reguły sieciowej grupy zabezpieczeń. 

Nie musisz ręcznie konfigurować zasad sieciowych grup zabezpieczeń, aby filtrować ruch dla zasobników w klastrze AKS. Po prostu Zdefiniuj wszystkie wymagane porty i przekazanie w ramach manifestów usługi Kubernetes. Zezwól na tworzenie lub aktualizowanie odpowiednich reguł na platformie Azure. 

Zasad sieciowych można także użyć do automatycznego zastosowania reguł filtru ruchu do zasobników.

## <a name="network-policies"></a>Zasady sieciowe

Domyślnie wszystkie zasobniki w klastrze AKS mogą wysyłać i odbierać ruch bez ograniczeń. W celu zwiększenia bezpieczeństwa należy zdefiniować reguły kontrolujące przepływ ruchu, takie jak:
* Aplikacje zaplecza są dostępne tylko dla wymaganych usług frontonu. 
* Składniki bazy danych są dostępne tylko dla warstw aplikacji, które łączą się z nimi.

Zasady sieciowe to funkcja Kubernetes dostępna w AKS, która umożliwia sterowanie przepływem ruchu między zasobnikami. Zezwalasz na ruch lub odmawiasz go na podstawie ustawień, takich jak przypisane etykiety, przestrzeń nazw lub port ruchu. Grupy zabezpieczeń sieci są lepsze dla węzłów AKS, jednak zasady sieciowe są bardziej dopasowane, natywne w chmurze w celu kontrolowania przepływu ruchu dla każdego z nich. Ponieważ w klastrze AKS są tworzone w sposób dynamiczny, wymagane zasady sieciowe mogą być automatycznie stosowane.

Aby uzyskać więcej informacji, zobacz [bezpieczny ruch między różnymi elementami sieciowymi przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą AKS Networking, Utwórz i skonfiguruj klaster AKS z własnymi zakresami adresów IP przy użyciu [korzystającą wtyczki kubenet][aks-configure-kubenet-networking] lub [Azure CNI][aks-configure-advanced-networking].

W przypadku skojarzonych najlepszych rozwiązań należy zapoznać się [z najlepszymi rozwiązaniami dotyczącymi łączności sieciowej i zabezpieczeń w AKS][operator-best-practices-network].

Aby uzyskać więcej informacji na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

- [Kubernetes/AKS klastrów i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes/AKS, dostęp i tożsamość][aks-concepts-identity]
- [Zabezpieczenia Kubernetes/AKS][aks-concepts-security]
- [Magazyn Kubernetes/AKS][aks-concepts-storage]
- [Skala Kubernetes/AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
