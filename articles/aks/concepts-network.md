---
title: Pojęcia — obsługa sieci w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej na temat sieci w usłudze Azure Kubernetes Service (AKS), w tym korzystającą wtyczki kubenet i Azure CNI Networking, kontrolerów przychodzących, modułów równoważenia obciążenia i statycznych adresów IP.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: edb195fae2e05a1f746c10482576f7e0b1bff7c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88243908"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Pojęcia dotyczące sieci dla aplikacji w usłudze Azure Kubernetes Service (AKS)

W przypadku mikrousług opartych na kontenerach do tworzenia aplikacji składniki aplikacji muszą współpracować ze sobą, aby przetwarzać swoje zadania. Kubernetes udostępnia różne zasoby, które umożliwiają komunikację z tą aplikacją. Możesz łączyć się z aplikacjami i udostępniać je wewnętrznie lub zewnętrznie. W celu utworzenia aplikacji o wysokiej dostępności można równoważyć obciążenie aplikacji. Bardziej złożone aplikacje mogą wymagać konfiguracji ruchu przychodzącego dla zakończenia protokołu SSL/TLS lub routingu wielu składników. Ze względów bezpieczeństwa może być również konieczne ograniczenie przepływu ruchu sieciowego do lub między nazwami i węzłami.

W tym artykule przedstawiono podstawowe koncepcje zapewniające obsługę sieci dla aplikacji w programie AKS:

- [Usługi](#services)
- [Sieci wirtualne platformy Azure](#azure-virtual-networks)
- [Kontrolery ruchu przychodzącego](#ingress-controllers)
- [Zasady sieciowe](#network-policies)

## <a name="kubernetes-basics"></a>Podstawy platformy Kubernetes

Aby zezwolić na dostęp do aplikacji lub do komunikacji między składnikami aplikacji, Kubernetes zapewnia warstwę abstrakcji dla sieci wirtualnych. Węzły Kubernetes są połączone z siecią wirtualną i mogą zapewniać łączność ruchu przychodzącego i wychodzącego w przypadku zasobników. Składnik *polecenia-proxy* działa w każdym węźle, aby udostępnić te funkcje sieciowe.

W programie Kubernetes *usługi* logicznie grupują elementy w celu umożliwienia bezpośredniego dostępu za pośrednictwem adresu IP lub nazwy DNS i określonego portu. Ruch można także dystrybuować przy użyciu *modułu równoważenia obciążenia*. Bardziej skomplikowany Routing ruchu aplikacji można również uzyskać za pomocą *kontrolerów* transferu danych przychodzących. Zabezpieczenia i filtrowanie ruchu sieciowego dla każdego z nich jest możliwe przy użyciu *zasad sieciowych* Kubernetes.

Platforma Azure pomaga również uprościć sieci wirtualne klastrów AKS. Podczas tworzenia modułu równoważenia obciążenia Kubernetes zostanie utworzony i skonfigurowany źródłowy zasób modułu równoważenia obciążenia platformy Azure. Podczas otwierania portów sieciowych do zasobników, są konfigurowane odpowiednie reguły grupy zabezpieczeń sieci platformy Azure. W przypadku routingu aplikacji HTTP platforma Azure może również skonfigurować *zewnętrzny system DNS* jako nowe trasy transferu danych przychodzących.

## <a name="services"></a>Usługi

Aby uprościć konfigurację sieci dla obciążeń aplikacji, Kubernetes używa *usług* do logicznego grupowania zestawu i zapewnienia łączności sieciowej. Dostępne są następujące typy usług:

- **IP klastra** — tworzy wewnętrzny adres IP do użycia w klastrze AKS. Dobre dla aplikacji tylko wewnętrznych, które obsługują inne obciążenia w klastrze.

    ![Diagram przedstawiający przepływ ruchu IP klastra w klastrze AKS][aks-clusterip]

- **NodePort** — tworzy mapowanie portów w podstawowym węźle, który umożliwia dostęp do aplikacji bezpośrednio z adresem IP i portem węzła.

    ![Diagram przedstawiający przepływ ruchu NodePort w klastrze AKS][aks-nodeport]

- Moduł **równoważenia** obciążenia — tworzy zasób usługi Azure Load, konfiguruje zewnętrzny adres IP i łączy żądaną pulę zasobów z pulą zaplecza modułu równoważenia obciążenia. Aby umożliwić klientom dostęp do aplikacji, reguły równoważenia obciążenia są tworzone na odpowiednich portach. 

    ![Diagram przedstawiający przepływ ruchu Load Balancer w klastrze AKS][aks-loadbalancer]

    W celu uzyskania dodatkowej kontroli i routingu ruchu przychodzącego można użyć [kontrolera](#ingress-controllers)transferu danych przychodzących.

- **Externalname** — tworzy określony wpis DNS w celu ułatwienia dostępu do aplikacji.

Adres IP dla modułów równoważenia obciążenia i usług może być dynamicznie przypisywany lub można określić istniejący statyczny adres IP do użycia. Można przypisać zarówno wewnętrzne, jak i zewnętrzne statyczne adresy IP. Ten istniejący statyczny adres IP jest często powiązany z wpisem DNS.

Można utworzyć *wewnętrzne* i *zewnętrzne* usługi równoważenia obciążenia. Do wewnętrznych modułów równoważenia obciążenia przypisany jest tylko prywatny adres IP, dlatego nie można uzyskać do nich dostępu z Internetu.

## <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

W usłudze AKS można wdrożyć klaster, który jest oparty na jednym z następujących modeli sieciowych:

- *Korzystającą wtyczki kubenet* Networking — zasoby sieciowe są zwykle tworzone i konfigurowane jako wdrożony klaster AKS.
- Sieć *Azure Container Networking Interface (CNI)* — klaster AKS jest połączony z istniejącymi zasobami i konfiguracjami sieci wirtualnej.

### <a name="kubenet-basic-networking"></a>Sieć korzystającą wtyczki kubenet (podstawowa)

Opcja sieci *korzystającą wtyczki kubenet* jest konfiguracją domyślną dla tworzenia klastra AKS. W przypadku *korzystającą wtyczki kubenet* węzły uzyskują adres IP z podsieci sieci wirtualnej platformy Azure. Zasobniki uzyskują adresy IP z przestrzeni adresowej, która jest logicznie różna od podsieci sieci wirtualnej platformy Azure, używanej przez węzły. Dzięki skonfigurowaniu translatora adresów sieciowych (NAT) zasobniki mogą uzyskać dostęp do zasobów w sieci wirtualnej platformy Azure. Źródłowy adres IP ruchu to NAT do podstawowego adresu IP węzła.

Węzły korzystają z wtyczki [korzystającą wtyczki kubenet][kubenet] Kubernetes. Możesz zezwolić na platformę Azure, aby utworzyć i skonfigurować sieci wirtualne, lub wybrać wdrożenie klastra AKS w istniejącej podsieci sieci wirtualnej. Ponownie tylko węzły odbierają adres IP z obsługą routingu, a w przypadku korzystania z translatora adresów sieciowych w celu komunikowania się z innymi zasobami poza klastrem AKS. Takie podejście znacznie zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej, aby można było użyć używanych przez nią zasobników.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sieci korzystającą wtyczki kubenet dla klastra AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (Advanced) — sieć

W sieci Azure CNI każdy zasobnik uzyskuje adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być unikatowe w przestrzeni sieci i muszą być planowane z wyprzedzeniem. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby obsługiwanych przez nią zasobników. Równoważna liczba adresów IP na węzeł jest następnie rezerwowana na początku dla tego węzła. Takie podejście wymaga większego planowania, ponieważ w przeciwnym razie może prowadzić do wyczerpania adresów IP lub trzeba ponownie skompilować klastry w większej podsieci, w miarę wzrostu wymagań aplikacji.

W przeciwieństwie do korzystającą wtyczki kubenet, ruch do punktów końcowych w tej samej sieci wirtualnej nie jest NAT do podstawowego adresu IP węzła. Adres źródłowy ruchu w sieci wirtualnej jest adresem IP pod. Ruch zewnętrzny do sieci wirtualnej nadal jest NAT na podstawowym adresie IP węzła.

W węzłach jest używany dodatek [Azure Container Network Interface (CNI)][cni-networking] Kubernetes.

![Diagram przedstawiający dwa węzły z mostkami łączącymi każdy z jedną siecią wirtualną platformy Azure][advanced-networking-diagram]

Aby uzyskać więcej informacji, zobacz [Konfigurowanie platformy Azure CNI dla klastra AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Porównanie modeli sieci

Zarówno korzystającą wtyczki kubenet, jak i Azure CNI zapewniają łączność sieciową dla klastrów AKS. Istnieją jednak zalety i wady każdej z nich. Na wysokim poziomie są stosowane następujące zagadnienia:

* **korzystającą wtyczki kubenet**
    * Zachowuje przestrzeń adresów IP.
    * Używa wewnętrznego lub zewnętrznego modułu równoważenia obciążenia Kubernetes w celu uzyskania dostępu do elementów z zewnątrz klastra.
    * Należy ręcznie zarządzać i obsługiwać trasy zdefiniowane przez użytkownika (UDR).
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

W przypadku systemu DNS, z dodatkiem korzystającą wtyczki kubenet i usługą Azure CNI w systemie DNS jest oferowana przez CoreDNS, wdrożenie działające w AKS ze swoim własnym skalowaniem automatycznym. Aby uzyskać więcej informacji na temat CoreDNS na Kubernetes, zobacz [Dostosowywanie usługi DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS jest domyślnie skonfigurowany do przesyłania nieznanych domen do serwerów DNS węzłów, innymi słowy, do funkcji DNS Virtual Network platformy Azure, w której wdrożono klaster AKS. W związku z tym strefy Azure DNS i prywatne będą działać w przypadku zasobników z systemem AKS.

### <a name="support-scope-between-network-models"></a>Zakres pomocy technicznej między modelami sieci

Niezależnie od modelu sieci, z którego korzystasz, zarówno korzystającą wtyczki kubenet, jak i Azure CNI można wdrożyć w jeden z następujących sposobów:

* Platforma Azure może automatycznie tworzyć i konfigurować zasoby sieci wirtualnej podczas tworzenia klastra AKS.
* Możesz ręcznie utworzyć i skonfigurować zasoby sieci wirtualnej i dołączyć je do tych zasobów podczas tworzenia klastra AKS.

Mimo że funkcje, takie jak punkty końcowe usługi lub UDR, są obsługiwane zarówno w korzystającą wtyczki kubenet, jak i na platformie Azure CNI, [zasady pomocy technicznej dla AKS][support-policies] określają, jakie zmiany można wprowadzić. Na przykład:

* Jeśli ręcznie utworzysz zasoby sieci wirtualnej dla klastra AKS, jest ono obsługiwane podczas konfigurowania własnych UDR lub punktów końcowych usługi.
* Jeśli platforma Azure automatycznie tworzy zasoby sieci wirtualnej dla klastra AKS, nie jest obsługiwane ręczne zmienianie tych zasobów zarządzanych przez AKS w celu skonfigurowania własnych UDR lub punktów końcowych usługi.

## <a name="ingress-controllers"></a>Kontrolery ruchu przychodzącego

Podczas tworzenia usługi typu modułu równoważenia obciążenia tworzony jest podstawowy zasób usługi Azure load module. Moduł równoważenia obciążenia jest skonfigurowany do dystrybucji ruchu do zasobników w usłudze na dany port. Moduł równoważenia obciążenia działa tylko w warstwie 4 — usługa nie rozpoznaje rzeczywistych aplikacji i nie może dokonywać żadnych dodatkowych zagadnień związanych z routingiem.

*Kontrolery* transferu danych przychodzących działają w warstwie 7 i mogą używać bardziej inteligentnych reguł do dystrybucji ruchu aplikacji. Typowym zastosowaniem kontrolera transferu danych przychodzących jest kierowanie ruchu HTTP do różnych aplikacji na podstawie przychodzącego adresu URL.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS][aks-ingress]

W programie AKS można utworzyć zasób transferu danych przychodzących przy użyciu elementu like NGINX lub użyć funkcji routingu aplikacji HTTP AKS. Po włączeniu routingu aplikacji protokołu HTTP dla klastra AKS platforma Azure tworzy kontroler transferu danych przychodzących i *zewnętrzny kontroler DNS* . Ponieważ nowe zasoby związane z ruchem przychodzącym są tworzone w Kubernetes, wymagane są rekordy A DNS w strefie DNS. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji http Routing][aks-http-routing].

Dodatek Application Gateway transferu danych przychodzących (AGIC) umożliwia klientom AKS korzystanie z usługi równoważenia obciążenia na poziomie macierzystym Application Gateway w systemie Azure w celu udostępnienia oprogramowania chmury do Internetu. AGIC monitoruje klaster Kubernetes, na którym jest on hostowany, i ciągle aktualizuje Application Gateway, dzięki czemu wybrane usługi są dostępne w Internecie. Aby dowiedzieć się więcej na temat dodatku AGIC dla AKS, zobacz [co to jest Application Gateway kontroler][agic-overview] transferu danych przychodzących?

Inną wspólną funkcją transferu danych przychodzących jest protokół SSL/TLS. W przypadku dużych aplikacji sieci Web, do których uzyskuje się dostęp za pośrednictwem protokołu HTTPS, zakończenie protokołu TLS może być obsługiwane przez zasób transferu danych przychodzących, a nie w samej aplikacji. Aby zapewnić automatyczną generację i konfigurację certyfikacji TLS, można skonfigurować zasób transferu danych przychodzących tak, aby korzystał z dostawców, takich jak szyfrowanie. Aby uzyskać więcej informacji na temat konfigurowania kontrolera NGINX ingresing z szyfrowaniem, zobacz artykuł dotyczący [protokołów przychodzących i TLS][aks-ingress-tls].

Możesz również skonfigurować kontroler transferu danych przychodzących, aby zachować źródłowy adres IP klienta w przypadku żądań do kontenerów w klastrze AKS. Gdy żądanie klienta jest kierowane do kontenera w klastrze AKS za pośrednictwem kontrolera transferu danych przychodzących, oryginalny źródłowy adres IP tego żądania nie będzie dostępny dla kontenera docelowego. Po włączeniu *zachowywania źródłowego adresu IP klienta* jest dostępny źródłowy adres IP klienta w nagłówku żądania w obszarze *X-forwardd-for*. W przypadku korzystania z funkcji zachowywania źródłowych adresów IP klienta na kontrolerze transferu danych przychodzących nie można używać przekazywania protokołu TLS. Przechowywanie źródłowych adresów IP klienta i przekazywanie protokołu TLS mogą być używane z innymi usługami, takimi jak typ *modułu równoważenia obciążenia* .

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowa Grupa zabezpieczeń filtruje ruch dla maszyn wirtualnych, takich jak węzły AKS. Podczas tworzenia usług, takich jak moduł równoważenia obciążenia, platforma Azure automatycznie konfiguruje wszystkie potrzebne reguły sieciowej grupy zabezpieczeń. Nie należy ręcznie konfigurować zasad sieciowych grup zabezpieczeń w celu filtrowania ruchu dla zasobników w klastrze AKS. Zdefiniuj wszystkie wymagane porty i przekazanie w ramach manifestów usługi Kubernetes, a platforma Azure umożliwia tworzenie i aktualizowanie odpowiednich reguł. Możesz również użyć zasad sieciowych, jak opisano w następnej sekcji, aby automatycznie zastosować reguły filtru ruchu do zasobników.

## <a name="network-policies"></a>Zasady sieciowe

Domyślnie wszystkie zasobniki w klastrze AKS mogą wysyłać i odbierać ruch bez ograniczeń. W celu zwiększenia bezpieczeństwa warto zdefiniować reguły sterujące przepływem ruchu. Aplikacje zaplecza często są udostępniane wymaganym usługom frontonu lub składniki bazy danych są dostępne tylko dla warstw aplikacji, które się z nimi łączą.

Zasady sieciowe to funkcja Kubernetes dostępna w AKS, która umożliwia sterowanie przepływem ruchu między zasobnikami. Możesz zezwalać na ruch lub odmawiać go na podstawie ustawień, takich jak przypisane etykiety, przestrzeń nazw lub port ruchu. Sieciowe grupy zabezpieczeń są bardziej przeznaczone dla węzłów AKS, a nie do zasobników. Korzystanie z zasad sieciowych jest bardziej odpowiednim, natywnym sposobem na sterowanie przepływem ruchu. Ponieważ w klastrze AKS są tworzone w sposób dynamiczny, wymagane zasady sieciowe mogą być automatycznie stosowane.

Aby uzyskać więcej informacji, zobacz [bezpieczny ruch między różnymi elementami sieciowymi przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą AKS Networking, Utwórz i skonfiguruj klaster AKS z własnymi zakresami adresów IP przy użyciu [korzystającą wtyczki kubenet][aks-configure-kubenet-networking] lub [Azure CNI][aks-configure-advanced-networking].

W przypadku skojarzonych najlepszych rozwiązań należy zapoznać się [z najlepszymi rozwiązaniami dotyczącymi łączności sieciowej i zabezpieczeń w AKS][operator-best-practices-network].

Aby uzyskać dodatkowe informacje na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

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
