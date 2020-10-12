---
title: Konfigurowanie sieci dla Azure Dev Spaces w różnych topologiach sieci
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Opisuje wymagania sieciowe dotyczące uruchamiania Azure Dev Spaces w usłudze Azure Kubernetes Services
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, CNI, korzystającą wtyczki kubenet, SDN, Network
ms.openlocfilehash: 0d9ebbec3e3c07a466acb58e88b67e6a32a20edb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88214163"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Konfigurowanie sieci dla Azure Dev Spaces w różnych topologiach sieci

Azure Dev Spaces działa w klastrach usługi Azure Kubernetes Service (AKS) z domyślną konfiguracją sieci. Jeśli chcesz zmienić konfigurację sieciową klastra AKS, na przykład przełączenie klastra za zaporą, użycie sieciowych grup zabezpieczeń lub zastosowanie zasad sieciowych, musisz uwzględnić dodatkowe zagadnienia dotyczące uruchamiania Azure Dev Spaces.

![Konfiguracja sieci wirtualnej](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Konfiguracje sieci wirtualnej lub podsieci

Klaster AKS może mieć inną konfigurację sieci wirtualnej lub podsieci w celu ograniczenia ruchu przychodzącego lub wychodzącego dla klastra AKS. Na przykład klaster może znajdować się za zaporą, taką jak Zapora platformy Azure, lub można użyć sieciowych grup zabezpieczeń lub ról niestandardowych do ograniczania ruchu sieciowego. Przykładową konfigurację sieci można znaleźć w przykładowym [repozytorium Azure dev Spaces w witrynie GitHub][sample-repo].

Azure Dev Spaces ma pewne wymagania dotyczące ruchu przychodzącego *i* wychodzącego w sieci, jak *również ruchu* przychodzącego. Jeśli używasz Azure Dev Spaces w klastrze AKS z konfiguracją sieci wirtualnej lub podsieci, która ogranicza ruch dla klastra AKS, należy wykonać następujące czynności związane z ruchem przychodzącym i transferem danych przychodzących i wychodzących, aby funkcja Azure Dev Spaces działała prawidłowo.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Wymagania dotyczące ruchu przychodzącego i wychodzącego w sieci

Azure Dev Spaces wymaga ruchu przychodzącego i wychodzącego dla następujących nazw FQDN:

| Nazwa FQDN                       | Port       | Zastosowanie      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Aby ściągnąć obrazy platformy Docker dla Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Aby ściągnąć obrazy Helm dla Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Aby ściągnąć obrazy Helm dla Azure Dev Spaces |

Zaktualizuj zaporę lub konfigurację zabezpieczeń, aby zezwalać na ruch sieciowy do i z wszystkich powyższych nazw FQDN i [usług Azure dev Spaces infrastruktury][service-tags]. Na przykład jeśli używasz zapory do zabezpieczenia sieci, powyższe nazwy FQDN należy dodać do reguły aplikacji zapory, a do zapory należy również [dodać][firewall-service-tags]tag usługi Azure dev Spaces. Obie te aktualizacje zapory muszą zezwalać na ruch do i z tych domen.

### <a name="ingress-only-network-traffic-requirements"></a>Tylko wymagania dotyczące ruchu sieciowego

Azure Dev Spaces zapewnia Routing na poziomie przestrzeni nazw Kubernetes oraz publiczny dostęp do usług przy użyciu własnej nazwy FQDN. Aby obie te funkcje działały, zaktualizuj konfigurację zapory lub sieci tak, aby zezwalała na publiczny ruch przychodzący do zewnętrznego adresu IP Azure Dev Spaces kontrolera transferu danych przychodzących w klastrze. Alternatywnie można utworzyć [wewnętrzny moduł równoważenia obciążenia][aks-internal-lb] i dodać regułę NAT w zaporze, aby przetłumaczyć publiczny adres IP zapory na adres IP wewnętrznego modułu równoważenia obciążenia. Można również użyć [traefik][traefik-ingress] lub [Nginx][nginx-ingress] , aby utworzyć niestandardowy kontroler transferu danych przychodzących.

## <a name="aks-cluster-network-requirements"></a>Wymagania dotyczące sieci klastrów AKS

AKS umożliwia korzystanie z [zasad sieciowych][aks-network-policies] w celu kontrolowania ruchu przychodzącego i wychodzącego między jednostkami w klastrze, a także ruchem wychodzącym z pod. Azure Dev Spaces ma pewne wymagania dotyczące ruchu przychodzącego *i* wychodzącego w sieci, jak *również ruchu* przychodzącego. Jeśli używasz Azure Dev Spaces w klastrze AKS z zasadami sieci AKS, musisz postępować zgodnie z wymaganiami dotyczącymi ruchu przychodzącego i ruchem przychodzącym, aby funkcja Azure Dev Spaces działała prawidłowo.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Wymagania dotyczące ruchu przychodzącego i wychodzącego w sieci

Azure Dev Spaces pozwala komunikować się bezpośrednio z obszarem w środowisku deweloperskim w klastrze w celu debugowania. Aby ta funkcja działała, należy dodać zasady sieciowe zezwalające na ruch przychodzący i wychodzący do adresów IP infrastruktury Azure Dev Spaces, które [różnią się w zależności od regionu][service-tags].

### <a name="ingress-only-network-traffic-requirements"></a>Tylko wymagania dotyczące ruchu sieciowego

Azure Dev Spaces udostępnia Routing między przestrzeniami nazw między obszarami. Na przykład przestrzenie nazw z włączonym Azure Dev Spaces mogą mieć relację nadrzędny/podrzędny, co umożliwia kierowanie ruchu sieciowego między jednostkami w przestrzeni nazw nadrzędnych i podrzędnych. Azure Dev Spaces również ujawnia punkty końcowe usługi przy użyciu własnej nazwy FQDN. Aby skonfigurować różne sposoby udostępniania usług i wpływ na Routing na poziomie przestrzeni nazw, zobacz [Używanie różnych opcji punktu końcowego][endpoint-options].

## <a name="using-azure-cni"></a>Korzystanie z usługi Azure CNI

Domyślnie klastry AKS są skonfigurowane do używania [korzystającą wtyczki kubenet][aks-kubenet] dla sieci, które współdziałają z Azure dev Spaces. Możesz również skonfigurować klaster AKS, aby korzystał z [interfejsu Azure Container Network Interface (CNI)][aks-cni]. Aby użyć Azure Dev Spaces z platformą Azure CNI w klastrze AKS, Zezwól na przestrzeń adresową sieci wirtualnej i podsieci do 10 prywatnych adresów IP dla wdrożonych przez Azure Dev Spaces. Więcej szczegółowych informacji na temat zezwalania na prywatne adresy IP można znaleźć w [dokumentacji usługi AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Korzystanie z autoryzowanych zakresów adresów IP serwera interfejsu API

Klastry AKS umożliwiają skonfigurowanie dodatkowych zabezpieczeń, które ograniczają, który adres IP może współdziałać z klastrami, na przykład przy użyciu niestandardowych sieci wirtualnych lub [zabezpieczania dostępu do serwera interfejsu API przy użyciu autoryzowanych zakresów adresów IP][aks-ip-auth-ranges]. Aby użyć Azure Dev Spaces podczas korzystania z tego dodatkowego zabezpieczenia podczas [tworzenia][aks-ip-auth-range-create] klastra, należy [zezwolić na dodatkowe zakresy w zależności od regionu][service-tags]. Można również [zaktualizować][aks-ip-auth-range-update] istniejący klaster, aby zezwalał na te dodatkowe zakresy. Należy również zezwolić na adres IP wszystkich maszyn deweloperskich, które łączą się z klastrem AKS w celu debugowania w celu nawiązania połączenia z serwerem interfejsu API.

## <a name="using-aks-private-clusters"></a>Używanie prywatnych klastrów AKS

W tej chwili Azure Dev Spaces nie jest obsługiwana w przypadku [prywatnych klastrów AKS][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Korzystanie z różnych opcji punktu końcowego

Azure Dev Spaces może uwidaczniać punkty końcowe dla usług uruchomionych w usłudze AKS. Podczas włączania Azure Dev Spaces w klastrze dostępne są następujące opcje konfigurowania typu punktu końcowego dla klastra:

* *Publiczny* punkt końcowy, który jest domyślnym, wdraża kontroler transferu danych przychodzących z publicznym adresem IP. Publiczny adres IP jest zarejestrowany w systemie DNS klastra, co pozwala na publiczny dostęp do usług przy użyciu adresu URL. Ten adres URL można wyświetlić za pomocą polecenia `azds list-uris` .
* *Prywatny* punkt końcowy wdraża kontroler transferu danych przychodzących z prywatnym adresem IP. Przy użyciu prywatnego adresu IP moduł równoważenia obciążenia dla klastra jest dostępny tylko w sieci wirtualnej klastra. Prywatny adres IP modułu równoważenia obciążenia jest zarejestrowany w systemie DNS klastra, aby można było uzyskać dostęp do usług w sieci wirtualnej klastra przy użyciu adresu URL. Ten adres URL można wyświetlić za pomocą polecenia `azds list-uris` .
* Ustawienie *Brak* dla opcji punktu końcowego powoduje, że nie zostanie wdrożony kontroler transferu danych przychodzących. Gdy nie wdrożono kontrolera transferu danych przychodzących, [możliwości routingu Azure dev Spaces][dev-spaces-routing] nie będą działały. Opcjonalnie można zaimplementować własne rozwiązanie kontrolera transferu danych przychodzących za pomocą [traefik][traefik-ingress] lub [Nginx][nginx-ingress], co umożliwi ponowne działanie funkcji routingu.

Aby skonfigurować opcję punktu końcowego, użyj *-e* lub *--Endpoint* przy włączaniu Azure dev Spaces w klastrze. Na przykład:

> [!NOTE]
> Opcja punktu końcowego wymaga, aby uruchomiono interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Wymagania dotyczące klientów

Azure Dev Spaces używa narzędzi po stronie klienta, takich jak rozszerzenie interfejsu wiersza polecenia Azure Dev Spaces, rozszerzenia Visual Studio Code i rozszerzenia programu Visual Studio, aby komunikować się z klastrem AKS na potrzeby debugowania. Aby użyć Azure Dev Spaces narzędzia po stronie klienta, Zezwól na ruch z maszyn deweloperskich do [infrastruktury Azure dev Spaces][dev-spaces-allow-infrastructure]. W przypadku korzystania z [dozwolonych zakresów adresów IP serwera interfejsu API][auth-range-section]należy również zezwolić na adres IP wszystkich maszyn deweloperskich, które łączą się z klastrem AKS w celu debugowania w celu nawiązania połączenia z serwerem interfejsu API.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags