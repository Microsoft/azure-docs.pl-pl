---
title: Często zadawane pytania dotyczące Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548838"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Często zadawane pytania dotyczące Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Dotyczy to często zadawanych pytań dotyczących Azure Dev Spaces.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Jakie wersje Kubernetes są obsługiwane dla Azure Dev Spaces?

Azure Dev Spaces obsługuje [obecnie obsługiwane ogólnie dostępne wersje usług Kubernetes w AKS, do 1,18][aks-supported-k8s]. Kubernetes 1,19 i nowsze na AKS używa kontenerów jako środowiska uruchomieniowego kontenera, które nie działają z Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Które regiony platformy Azure są obecnie dostępne Azure Dev Spaces?

Aby uzyskać pełną listę dostępnych regionów, zobacz sekcję [Obsługiwane regiony][supported-regions] .

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Czy mogę migrować klaster AKS z Azure Dev Spaces do innego regionu?

Tak. Jeśli chcesz przenieść klaster AKS z Azure Dev Spaces do innego [obsługiwanego regionu][supported-regions], zalecamy utworzenie nowego klastra w innym regionie, a następnie zainstalowanie i skonfigurowanie Azure dev Spaces i wdrożenie zasobów i aplikacji w nowym klastrze. Aby uzyskać więcej informacji na temat migrowania AKS, zobacz [Migrowanie do usługi Azure Kubernetes Service (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Czy mogę używać Azure Dev Spaces z istniejącymi wykresami wieloetapowe dockerfile lub Helm?

Tak, jeśli projekt zawiera już wykres pliku dockerfile lub Helm, można użyć tych plików z Azure Dev Spaces. Po uruchomieniu `azds prep` , użyj `--chart` parametru i określ lokalizację wykresu. Azure Dev Spaces nadal generuje plik *azds. YAML* i *pliku dockerfile. opracowywać* , ale nie zastąpi ani nie zmodyfikuje istniejącego wykresu pliku dockerfile lub Helm. Może być konieczne zmodyfikowanie plików *azds. YAML* i *pliku dockerfile. opracowywać* , aby wszystkie elementy działały prawidłowo z istniejącą aplikacją podczas uruchamiania `azds up` .

Korzystając z własnego wykresu pliku dockerfile lub Helm, istnieją następujące ograniczenia:
* Jeśli używany jest tylko jeden pliku dockerfile, musi on zawierać wszystko, co jest potrzebne do włączenia scenariuszy programistycznych, takich jak zestaw SDK języka nie tylko środowisko uruchomieniowe. W przypadku używania oddzielnego pliku dockerfile dla Azure Dev Spaces, takich jak pliku dockerfile. Development, wszystko, czego potrzebujesz do włączenia scenariuszy deweloperskich, musi być zawarte w tym pliku dockerfile.
* Wykres Helm musi obsługiwać przekazywanie części lub całego znacznika obrazu jako wartość z *wartości. YAML*.
* Jeśli modyfikujesz wszystko za pomocą transferu danych przychodzących, możesz również zaktualizować wykres Helm, aby korzystał z rozwiązania transferu danych przychodzących dostarczonego przez Azure Dev Spaces.
* Jeśli chcesz użyć [możliwości routingu zapewnianych przez Azure dev Spaces][dev-spaces-routing], wszystkie usługi dla poszczególnych projektów muszą pasować do jednej przestrzeni nazw Kubernetes i muszą zostać wdrożone przy użyciu prostego nazewnictwa, na przykład *Service-a*. W standardowych wykresach Helm tę aktualizację nazewnictwa można wykonać, określając wartość właściwości *fullnameOverride* .

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Czy mogę modyfikować pliki wygenerowane przez Azure Dev Spaces?

Tak, można zmodyfikować *azds. YAML* plik, pliku dockerfile i Helm [wygenerowany przez Azure dev Spaces podczas przygotowywania projektu][dev-spaces-prep]. Modyfikowanie tych plików zmienia sposób kompilowania i uruchamiania projektu.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Czy mogę używać Azure Dev Spaces bez publicznego adresu IP?

Nie, nie można zainicjować obsługi administracyjnej Azure Dev Spaces w klastrze AKS bez publicznego adresu IP. Publiczny adres IP jest [wymagany przez Azure dev Spaces do routingu][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Czy mogę użyć własnych danych przychodzących z Azure Dev Spaces?

Tak, możesz skonfigurować własne przychodzące po stronie przychodzące Azure Dev Spaces tworzenia. Na przykład można użyć [traefik][ingress-traefik] lub [Nginx][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Czy mogę używać protokołu HTTPS z Azure Dev Spaces?

Tak, możesz skonfigurować własny ruch przychodzący przy użyciu protokołu HTTPS za pomocą [traefik][ingress-https-traefik] lub [Nginx][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Czy mogę używać Azure Dev Spaces w klastrze korzystającym z CNI zamiast korzystającą wtyczki kubenet? 

Tak, można użyć Azure Dev Spaces w klastrze AKS, który używa CNI do obsługi sieci. Na przykład można użyć Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows][windows-containers], które używają CNI do obsługi sieci. Więcej informacji na temat korzystania z CNI dla sieci z Azure Dev Spaces jest dostępna [tutaj](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Czy mogę używać Azure Dev Spaces z kontenerami systemu Windows?

Obecnie Azure Dev Spaces ma być uruchamiany tylko w przypadku systemów Linux i w węzłach, ale można uruchomić Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Czy można używać Azure Dev Spaces w klastrach AKS z włączonymi zakresami adresów IP autoryzowanych serwerów interfejsu API?

Tak, można użyć Azure Dev Spaces w klastrach AKS z włączonymi [zakresami adresów IP autoryzowanych przez serwer interfejsu API][aks-auth-range] . Więcej informacji na temat korzystania z klastrów AKS z dozwolonymi zakresami adresów IP serwera interfejsu API w Azure Dev Spaces jest dostępny [tutaj](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Czy można używać Azure Dev Spaces w klastrach AKS z ograniczonym ruchem wychodzącym dla węzłów klastra?

Tak, można użyć Azure Dev Spaces w klastrach AKS z [ograniczonym ruchem wychodzącym dla węzłów klastra][aks-restrict-egress-traffic] włączonych po dodaniu prawidłowych nazw FQDN. Więcej informacji na temat używania klastrów AKS z ograniczonym ruchem wychodzącym dla węzłów klastra z włączonym Azure Dev Spaces jest dostępny [tutaj](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>Czy można używać Azure Dev Spaces na Kubernetesch klastrów AKS z włączoną funkcją RBAC?

Tak, można użyć Azure Dev Spaces w klastrach AKS z włączoną funkcją kontroli dostępu opartą na rolach (Kubernetes RBAC) lub bez niej.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Co się stanie po włączeniu ruchu przychodzącego dla projektu w programie Visual Studio?

W przypadku korzystania z programu Visual Studio w celu przygotowania projektu można włączyć obsługę ruchu przychodzącego dla usługi. Włączenie transferu danych przychodzących powoduje utworzenie publicznego punktu końcowego w celu uzyskania dostępu do usługi w ramach klastra AKS, który jest opcjonalny. Jeśli nie włączysz transferu danych przychodzących, usługa będzie dostępna tylko z poziomu klastra AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Czy mogę używać tożsamości zarządzanych z Azure Dev Spaces?

Tak, można użyć [tożsamości zarządzanych][aks-pod-managed-id] w klastrach AKS z włączonym Azure dev Spaces, ale po włączeniu Azure dev Spaces w klastrze za pomocą tożsamości zarządzanych należy wykonać [dodatkowe czynności konfiguracyjne][dev-spaces-pod-managed-id-steps] . Jeśli masz zainstalowane tożsamości zarządzane i chcesz je odinstalować, możesz znaleźć więcej szczegółów w [informacjach o odinstalowaniu][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Czy można użyć Azure Dev Spaces z wieloma mikrousługami w aplikacji?

Tak, można użyć Azure Dev Spaces w aplikacji z wieloma mikrousługami, ale należy przygotować i uruchomić poszczególne mikrousługi w ich katalogu głównym. Azure Dev Spaces interfejs wiersza polecenia, Azure Dev Spaces VS Code rozszerzenie i obciążenie Programowanie na platformie Azure dla programu Visual Studio oczekują, że plik *azds. YAML* powinien znajdować się w katalogu głównym mikrousługi do uruchamiania i debugowania. Zapoznaj się z przykładową [aplikacją do udostępniania roweru][bike-sharing] , aby zapoznać się z wieloma mikrousługami w pojedynczej aplikacji.

W Visual Studio Code można [otworzyć oddzielne projekty w jednym obszarze roboczym][vs-code-multi-root-workspaces] i debugować je oddzielnie za pomocą Azure dev Spaces. Każdy z projektów musi być samodzielny i przygotowany do Azure Dev Spaces.

W programie Visual Studio możliwe jest skonfigurowanie rozwiązań .NET Core na potrzeby debugowania przez Azure Dev Spaces.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Czy można użyć Azure Dev Spaces z siatką usługi?

W tej chwili nie można używać Azure Dev Spaces z oczkami usługi, takimi jak [Istio][istio] lub [konsolidatored][linkerd]. Można uruchomić Azure Dev Spaces i siatkę usługi w tym samym klastrze AKS, ale nie można mieć jednocześnie Azure Dev Spaces i sieci usługi w tej samej przestrzeni nazw.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
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
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md