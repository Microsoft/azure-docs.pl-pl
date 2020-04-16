---
title: Często zadawane pytania dotyczące usługi Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Znajdź odpowiedzi na niektóre z często zadawanych pytań dotyczących usługi Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s '
ms.openlocfilehash: b5a380f20640b9bc328aa30289ff7f915cc0b73c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414310"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Często zadawane pytania dotyczące usługi Azure Dev Spaces

Dotyczy to często zadawanych pytań dotyczących usługi Azure Dev Spaces.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Jakie wersje usługi Kubernetes są obsługiwane dla usługi Azure Dev Spaces?

Usługa Azure Dev Spaces obsługuje wszystkie [obecnie obsługiwane wersje usługi Kubernetes w wersji ogólnej dostępności (GA) w usłudze AKS.][aks-supported-k8s]

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Które regiony platformy Azure obecnie zapewniają usługi Azure Dev Spaces?

Pełna lista dostępnych regionów można znaleźć w [obsługiwanych regionach.][supported-regions]

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Czy mogę przeprowadzić migrację klastra AKS za pomocą usługi Azure Dev Spaces do innego regionu?

Tak, jeśli chcesz przenieść klaster AKS z usługą Azure Dev Spaces do innego [obsługiwanego regionu,][supported-regions]zaleca się utworzenie nowego klastra w innym regionie, a następnie zainstalowanie i skonfigurowanie usługi Azure Dev Spaces oraz wdrożenie zasobów i aplikacji w nowym klastrze. Aby uzyskać więcej informacji na temat migrowania usługi AKS, zobacz [Migrowanie do usługi Azure Kubernetes Service (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Czy mogę używać usługi Azure Dev Spaces z istniejącymi plikami dockerfile lub wykresami Helm?

Tak, jeśli projekt ma już dockerfile lub wykres Helm, można użyć tych plików z usługi Azure Dev Spaces. Po uruchomieniu `azds prep`użyj `--chart` parametru i określ lokalizację wykresu. Usługa Azure Dev Spaces nadal będzie generować plik *azds.yaml* i *Dockerfile.develop,* ale nie zastąpi ani nie zmodyfikuje istniejącego pliku dockerfile ani wykresu Helm. Może być konieczne zmodyfikowanie plików *azds.yaml* i *Dockerfile.develop,* aby wszystko działało poprawnie z istniejącą aplikacją podczas uruchamiania `azds up`.

Podczas korzystania z własnego wykresu Dockerfile lub Helm istnieją następujące ograniczenia:
* Jeśli używasz tylko jednego pliku Dockerfile, musi zawierać wszystko, czego potrzebujesz, aby włączyć scenariusze rozwoju, takie jak język SDK nie tylko środowiska wykonawczego. Jeśli używasz oddzielnego pliku Dockerfile dla usługi Azure Dev Spaces, takiego jak Dockerfile.develop, wszystko, czego potrzebujesz, aby włączyć scenariusze rozwoju, musi zostać uwzględnione w tym pliku Dockerfile.
* Wykres Helm musi obsługiwać przekazywanie części lub całego znacznika obrazu jako wartość z *pliku values.yaml*.
* Jeśli modyfikujesz cokolwiek za pomocą transferu danych przychodzących, możesz również zaktualizować wykres Helm, aby użyć rozwiązania przychodzącego dostarczonego przez usługi Azure Dev Spaces.
* Jeśli chcesz korzystać z [funkcji routingu oferowanych przez usługę Azure Dev Spaces,][dev-spaces-routing]wszystkie usługi dla poszczególnych projektów muszą zmieścić się w jednym obszarze nazw Kubernetes i muszą być wdrożone z prostymnazysłem, na przykład *service-a*. W standardowych wykresów Helm tej aktualizacji nazewnictwa można wykonać, określając wartość *właściwości fullnameOverride.*

Aby porównać własny wykres Dockerfile lub Helm z istniejącą wersją, która współpracuje z usługą Azure Dev Spaces, przejrzyj pliki wygenerowane w [przewodniku Szybki start][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Czy mogę zmodyfikować pliki generowane przez usługi Azure Dev Spaces?

Tak, podczas przygotowywania projektu można zmodyfikować wykres *pliku azds.yaml,* Dockerfile i Helm [wygenerowany przez usługę Azure Dev Spaces.][dev-spaces-prep] Modyfikacja tych plików zmienia sposób tworzenia i uruchamiania projektu.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Czy mogę używać usługi Azure Dev Spaces bez publicznego adresu IP?

Nie, nie można aprowizować usługi Azure Dev Spaces w klastrze usługi AKS bez publicznego adresu IP. Publiczny adres IP jest [potrzebny do routingu usługi Azure Dev Spaces.][dev-spaces-routing]

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Czy mogę używać własnego transferu przychodzącego z usługą Azure Dev Spaces?

Tak, można skonfigurować własne przychodzące dane przychodzące wraz z przychodzącym usługi Azure Dev Spaces tworzy. Można na przykład użyć [traefik][ingress-traefik] lub [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Czy mogę używać protokołu HTTPS z usługą Azure Dev Spaces?

Tak, można skonfigurować własne dane przychodzące za pomocą protokołu HTTPS za pomocą [traefik][ingress-https-traefik] lub [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Czy mogę używać usługi Azure Dev Spaces w klastrze, który używa CNI zamiast kubenet? 

Tak, można użyć usługi Azure Dev Spaces w klastrze AKS, który używa CNI do sieci. Na przykład można użyć usługi Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows,][windows-containers]który używa CNI do sieci. Więcej informacji na temat korzystania z CNI do tworzenia sieci za pomocą usługi Azure Dev Spaces można znaleźć [tutaj](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Czy mogę używać usługi Azure Dev Spaces z kontenerami systemu Windows?

Obecnie usługa Azure Dev Spaces jest przeznaczona tylko do uruchamiania zasobników i węzłów systemu Linux, ale można uruchamiać usługi Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows.][windows-containers]

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Czy mogę używać usługi Azure Dev Spaces w klastrach AKS z włączonymi zakresami adresów IP autoryzowanych serwera interfejsu API?

Tak, można używać usługi Azure Dev Spaces w klastrach AKS z [włączonymi zakresami adresów IP autoryzowanych serwera interfejsu API.][aks-auth-range] Więcej informacji na temat korzystania z klastrów AKS z zakresami autoryzowanych adresów IP serwera INTERFEJSU API włączonych w usłudze Azure Dev Spaces można znaleźć [tutaj](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Czy mogę używać usługi Azure Dev Spaces w klastrach AKS z ograniczonym ruchem wychodzącym dla węzłów klastra?

Tak, można używać usługi Azure Dev Spaces w klastrach AKS z [ograniczonym ruchem wychodzącym dla węzłów klastra włączonym][aks-restrict-egress-traffic] po dopuszczeniu prawidłowych sieci FQDN. Więcej informacji na temat korzystania z klastrów AKS z ograniczonym ruchem wychodzącym dla węzłów klastra włączonych za pomocą usługi Azure Dev Spaces jest dostępna [tutaj](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Czy mogę używać usługi Azure Dev Spaces w klastrach AKS z włączoną funkcją RBAC?

Tak, można używać usługi Azure Dev Spaces w klastrach AKS z włączoną funkcją RBAC lub bez.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Co się stanie po włączeniu transferu ruchu przychodzącego dla projektu w programie Visual Studio?

Podczas korzystania z programu Visual Studio do przygotowania projektu, masz możliwość włączenia transferu przychodzącego dla usługi. Włączenie transferu przychodzącego powoduje utworzenie publicznego punktu końcowego w celu uzyskania dostępu do usługi podczas uruchamiania w klastrze AKS, co jest opcjonalne. Jeśli nie włączysz transferu przychodzącego, usługa będzie dostępna tylko z poziomu klastra usługi AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Czy można używać tożsamości zarządzanych zasobników za pomocą usługi Azure Dev Spaces?

Tak, można używać [tożsamości zarządzanych zasobników][aks-pod-managed-id] w klastrach AKS z włączoną włączoną usługą Azure Dev Spaces, ale istnieją [dodatkowe kroki konfiguracji][dev-spaces-pod-managed-id-steps] po włączeniu usługi Azure Dev Spaces w klastrze z tożsamościami zarządzanymi zasobnika. Jeśli masz zainstalowane tożsamości zarządzane i chcesz je odinstalować, możesz znaleźć więcej szczegółów w [notatkach dezinstalacji][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Czy mogę używać usługi Azure Dev Spaces z wieloma mikrousługami w aplikacji?

Tak, można użyć usługi Azure Dev Spaces w aplikacji z wieloma mikrousługami, ale należy przygotować i uruchomić poszczególne mikrousługi w ich katalogu głównym. Interfejs wiersza polecenia cli usługi Azure Dev Spaces, rozszerzenie usługi Azure Dev Spaces VS Code i obciążenie programu Visual Studio Azure Development oczekują, że plik *azds.yaml* będzie w katalogu głównym mikrousługi w celu uruchomienia i debugowania. Zobacz [przykładową aplikację udostępniania roweru,][bike-sharing] na przykład wielu mikrousług w jednej aplikacji.

W programie Visual Studio Code można [otworzyć oddzielne projekty w jednym obszarze roboczym][vs-code-multi-root-workspaces] i debugować je oddzielnie za pośrednictwem usługi Azure Dev Spaces. Każdy z projektów musi być samodzielny i przygotowany dla usługi Azure Dev Spaces.

W programie Visual Studio można skonfigurować rozwiązania .NET Core do debugowania za pośrednictwem usługi Azure Dev Spaces.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Czy mogę używać usługi Azure Dev Spaces z siatką usług?

Obecnie nie można używać usługi Azure Dev Spaces z siatkami usług, takimi jak [Istio][istio] lub [Linkerd.][linkerd] Usługi Azure Dev Spaces i siatki usług można uruchomić w tym samym klastrze usługi AKS, ale nie można włączyć zarówno usługi Azure Dev Spaces, jak i siatki usług w tym samym obszarze nazw.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#list-currently-supported-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md