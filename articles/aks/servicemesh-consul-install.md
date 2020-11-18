---
title: Instalowanie Consul w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i używać Consul do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 7c5ad53c0040009e9ed1f28072540b46ce7b0b9a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683923"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie Consul w usłudze Azure Kubernetes Service (AKS)

[Consul][consul-github] to siatka usługi typu "open source", która udostępnia kluczowy zestaw funkcji dla mikrousług w klastrze Kubernetes. Te funkcje obejmują odnajdowanie usług, sprawdzanie kondycji, segmentację usług i możliwości przestrzegania. Aby uzyskać więcej informacji na temat Consul, zobacz oficjalny dokument dotyczący [Consul?][consul-docs-concepts] .

W tym artykule opisano sposób instalowania programu Consul. Składniki Consul są instalowane w klastrze Kubernetes na AKS.

> [!NOTE]
> Te instrukcje odwołują `1.6.0` się do wersji Consul i używają co najmniej Helm wersji `2.14.2` .
>
> Wersje Consul `1.6.x` można uruchamiać z wersjami Kubernetes `1.13+` . Dodatkowe wersje Consul można znaleźć w [wersjach usługi GitHub-Consul][consul-github-releases] i informacje o każdej z nich w informacjach o [wersji Consul][consul-release-notes].

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zainstaluj składniki Consul na AKS
> * Weryfikowanie instalacji Consul
> * Odinstaluj Consul z AKS

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach przedstawionych w tym artykule przyjęto założenie, że utworzono klaster AKS (Kubernetes `1.13` lub nowszy z włączoną obsługą RBAC Kubernetes) i nawiązano `kubectl` połączenie z klastrem. Jeśli potrzebujesz pomocy z dowolnym z tych elementów, zobacz [Przewodnik Szybki Start AKS][aks-quickstart]. Upewnij się, że klaster zawiera co najmniej 3 węzły w puli węzłów systemu Linux.

Musisz [Helm][helm] , aby wykonać te instrukcje i zainstalować Consul. Zalecane jest, aby Najnowsza stabilna wersja była zainstalowana i skonfigurowana w klastrze. Jeśli potrzebujesz pomocy dotyczącej instalowania Helm, zobacz [wskazówki dotyczące instalacji programu AKS Helm][helm-install]. Wszystkie Consulowe zasobniki muszą być również zaplanowane do uruchomienia w węzłach systemu Linux.

Ten artykuł oddziela wskazówki dotyczące instalacji Consul do kilku dyskretnych kroków. Wynik końcowy ma taką samą strukturę jak oficjalne [wskazówki dotyczące][consul-install-k8]instalacji Consul.

### <a name="install-the-consul-components-on-aks"></a>Zainstaluj składniki Consul na AKS

Zaczniemy od pobrania wersji `v0.10.0` wykresu Consul Helm. Ta wersja wykresu zawiera wersję Consul `1.6.0` .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Użyj Helm i pobranego `consul-helm` wykresu, aby zainstalować składniki Consul w `consul` przestrzeni nazw w klastrze AKS. 

> [!NOTE]
> **Opcje instalacji**
> 
> W ramach naszej instalacji są używane następujące opcje:
> - `connectInject.enabled=true` -Włącz serwery proxy do dodania do zasobników
> - `client.enabled=true` -Włącz uruchamianie klientów Consul w każdym węźle
> - `client.grpc=true` -Włączanie odbiornika gRPC dla connectInject
> - `syncCatalog.enabled=true` -Sync Kubernetes i Consul Services
>
> **Selektory węzłów**
>
> Consul obecnie muszą być zaplanowane do uruchomienia w węzłach systemu Linux. Jeśli w klastrze znajdują się węzły systemu Windows Server, musisz upewnić się, że Consul są zaplanowane do uruchomienia tylko w węzłach z systemem Linux. Użyjemy [selektorów węzłów][kubernetes-node-selectors] , aby upewnić się, że zasobniki są zaplanowane do poprawnego węzła.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul`Wykres Helm służy do wdrażania wielu obiektów. Można wyświetlić listę z danych wyjściowych podanego `helm install` polecenia. Wdrożenie składników Consul może potrwać około 3 minut, w zależności od środowiska klastra.

W tym momencie wdrożono Consul w klastrze AKS. Aby upewnić się, że mamy pomyślne wdrożenie Consul, przejdź do następnej sekcji, aby sprawdzić poprawność instalacji Consul.

## <a name="validate-the-consul-installation"></a>Weryfikowanie instalacji Consul

Upewnij się, że zasoby zostały pomyślnie utworzone. Użyj poleceń [polecenia kubectl Get SVC][kubectl-get] i [polecenia kubectl Get pod][kubectl-get] , aby wykonać zapytanie dotyczące `consul` przestrzeni nazw, gdzie składniki Consul zostały zainstalowane przez `helm install` polecenie:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Następujące przykładowe dane wyjściowe pokazują usługi i zasobniki (zaplanowane na węzłach systemu Linux), które powinny być teraz uruchomione:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Wszystkie z tych zasobników powinny zawierać stan `Running` . Jeśli Twoje zasobniki nie mają tych stanów, Zaczekaj chwilę lub dwa, aż do ich wykonania. Jeśli którykolwiek z raportów zawiera raport o problemie, użyj polecenia [polecenia kubectl opisz pod][kubectl-describe] , aby przejrzeć ich dane wyjściowe i stan.

## <a name="accessing-the-consul-ui"></a>Uzyskiwanie dostępu do interfejsu użytkownika Consul

Interfejs użytkownika Consul został zainstalowany w instalatorze powyżej i zawiera konfigurację opartą na interfejsie użytkownika Consul. Interfejs użytkownika dla Consul nie jest ujawniany publicznie za pośrednictwem zewnętrznego adresu IP. Aby uzyskać dostęp do interfejsu użytkownika Consul, użyj polecenia [port-forward usługi polecenia kubectl][kubectl-port-forward] . To polecenie tworzy bezpieczne połączenie między komputerem klienckim i odpowiednim pod nim w klastrze AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Teraz możesz otworzyć przeglądarkę i wskazać ją `http://localhost:8080/ui` , aby otworzyć interfejs użytkownika Consul. Podczas otwierania interfejsu użytkownika powinny zostać wyświetlone następujące elementy:

![Interfejs użytkownika Consul](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Odinstaluj Consul z AKS

> [!WARNING]
> Usunięcie Consul z działającego systemu może skutkować problemami związanymi z ruchem między usługami. Upewnij się, że zostały wprowadzone przepisy dotyczące systemu, aby nadal działały prawidłowo bez Consul przed kontynuowaniem.

### <a name="remove-consul-components-and-namespace"></a>Usuń składniki Consul i przestrzeń nazw

Aby usunąć Consul z klastra AKS, użyj następujących poleceń. `helm delete`Polecenia spowodują usunięcie `consul` wykresu, a `kubectl delete namespace` polecenie spowoduje usunięcie `consul` przestrzeni nazw.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Następne kroki

Aby poznać więcej opcji instalacji i konfiguracji dla usługi Consul, zobacz następujące oficjalne artykuły Consul:

- [Podręcznik instalacji Consul-Helm][consul-install-k8]
- [Opcje instalacji Consul-Helm][consul-install-helm-options]

Możesz również postępować zgodnie z dodatkowymi scenariuszami przy użyciu:

- [Przykładowa aplikacja Consul][consul-app-example]
- [Architektura referencyjna Consul Kubernetes][consul-reference]
- [Bramy siatki Consul][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
