---
title: Instalowanie konsolidatora w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i użyć konsolidatora, aby utworzyć siatkę usługi w klastrze usługi Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593739"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Instalowanie konsolidatora w usłudze Azure Kubernetes Service (AKS)

[Konsolidator][linkerd-github] jest siatką usługi Open Source i [CNCFą do inkubacji projektu][linkerd-cncf]. Konsolidator jest siatką usługi Ultralight, która udostępnia funkcje, które obejmują zarządzanie ruchem, tożsamość usługi i bezpieczeństwo, niezawodność i możliwości przestrzegania. Aby uzyskać więcej informacji na temat konsolidatora, zapoznaj się [z oficjalną][linkerd-faq] i dołączoną dokumentacją [architektury][linkerd-architecture] .

W tym artykule opisano sposób instalowania konsolidatora. Konsolidator `linkerd` binarny klienta jest instalowany na komputerze klienckim, a wbudowane składniki są instalowane w klastrze Kubernetes na AKS.

> [!NOTE]
> Te instrukcje odwołują się do konsolidatora `stable-2.6.0` .
>
> Konsolidator `stable-2.6.x` można uruchomić w odniesieniu do wersji Kubernetes `1.13+` . Dodatkowe wersje systemów stabilnych i brzegowych można znaleźć w wersjach [z konsolidatorem GitHub][linkerd-github-releases].

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobieranie i Instalowanie konsolidatora dwuskładnikowego klienta konsolidatora
> * Instalacja konsolidatora na AKS
> * Weryfikowanie instalacji konsolidatora
> * Dostęp do pulpitu nawigacyjnego
> * Odinstalowano z AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach przedstawionych w tym artykule przyjęto założenie, że utworzono klaster AKS (Kubernetes `1.13` lub nowszy z włączoną funkcją RBAC) i nawiązano `kubectl` połączenie z klastrem. Jeśli potrzebujesz pomocy z dowolnym z tych elementów, zobacz [Przewodnik Szybki Start AKS][aks-quickstart].

Aby można było uruchomić wszystkie Konsolidatory, muszą być zaplanowane do uruchomienia w węzłach systemu Linux — ta konfiguracja jest domyślnie opisana poniżej i nie wymaga dodatkowej konfiguracji.

Ten artykuł oddziela wskazówki dotyczące instalacji konsolidatora do kilku dyskretnych kroków. Wynik jest taki sam w strukturze co oficjalne [wskazówki dotyczące][linkerd-getting-started]rozpoczynania konsolidatora.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Instalacja konsolidatora na AKS

Przed zainstalowaniem konsolidatora zostaną uruchomione testy przed instalacją, aby określić, czy na tym klastrze AKS można zainstalować płaszczyznę kontroli:

```console
linkerd check --pre
```

Aby wskazać, że klaster AKS jest prawidłowym miejscem docelowym instalacji dla konsolidatora:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Teraz czas na zainstalowanie elementów konsolidatora. Użyj `linkerd` `kubectl` plików binarnych i, aby zainstalować elementy konsolidatora w klastrze AKS. `linkerd`Przestrzeń nazw zostanie utworzona automatycznie, a składniki zostaną zainstalowane w tej przestrzeni nazw.

```console
linkerd install | kubectl apply -f -
```

Konsolidator rozmieszcza wiele obiektów. Zostanie wyświetlona lista z danych wyjściowych `linkerd install` powyższego polecenia. Wdrożenie konsolidatora składników powinno trwać około 1 minuty, w zależności od środowiska klastra.

W tym momencie wdrożono konsolidator do klastra AKS. Aby upewnić się, że istnieje pomyślne wdrożenie konsolidatora, przejdź do następnej sekcji, aby [sprawdzić poprawność instalacji konsolidatora](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Weryfikowanie instalacji konsolidatora

Upewnij się, że zasoby zostały pomyślnie utworzone. Użyj poleceń [polecenia kubectl Get SVC][kubectl-get] i [polecenia kubectl Get pod][kubectl-get] , aby wykonać zapytanie dotyczące `linkerd` przestrzeni nazw, w przypadku których elementy konsolidatora zostały zainstalowane przez `linkerd install` polecenie:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

Następujące przykładowe dane wyjściowe pokazują usługi i zasobniki (zaplanowane na węzłach systemu Linux), które powinny być teraz uruchomione:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Konsolidator udostępnia polecenie za pośrednictwem `linkerd` pliku binarnego klienta, aby sprawdzić, czy Przykonsolidatora płaszczyzna kontroli została pomyślnie zainstalowana i skonfigurowana.

```console
linkerd check
```

Aby wskazać, że instalacja zakończyła się pomyślnie, powinien zostać wyświetlony następujący element:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Dostęp do pulpitu nawigacyjnego

Łączący się z pulpitem nawigacyjnym, który zapewnia wgląd w siatkę i obciążenia usługi. Aby uzyskać dostęp do pulpitu nawigacyjnego, użyj `linkerd dashboard` polecenia. To polecenie wykorzystuje [port polecenia kubectl do przodu][kubectl-port-forward] w celu utworzenia bezpiecznego połączenia między komputerem klienckim i odpowiednimi modułami w klastrze AKS. Następnie zostanie automatycznie otwarty pulpit nawigacyjny w domyślnej przeglądarce.

```console
linkerd dashboard
```

Polecenie spowoduje również utworzenie portu do przodu i zwrócenie linku do pulpitów nawigacyjnych Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Odinstalowano z AKS

> [!WARNING]
> Usunięcie konsolidatora z działającego systemu może skutkować problemami związanymi z ruchem między usługami. Upewnij się, że zostały wprowadzone przepisy dotyczące systemu, aby nadal działały prawidłowo bez konsolidatora przed kontynuowaniem.

Najpierw musisz usunąć serwery proxy płaszczyzny danych. Usuń wszystkie automatyczne [Adnotacje][linkerd-automatic-proxy-injection] iniekcji serwera proxy z przestrzeni nazw obciążeń i wdrażaj wdrożenia obciążeń. Obciążenia nie powinny już mieć żadnych powiązanych składników płaszczyzny danych.

Na koniec Usuń płaszczyznę kontroli w następujący sposób:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z dodatkowymi opcjami instalacji i konfiguracji dla konsolidatora, zobacz następujące oficjalne wskazówki dotyczące konsolidatora:

- [Instalacja z konsolidatorem-Helm][linkerd-install-with-helm]
- [Z konsolidatorem — instalacja wieloetapowa w celu zapewnienia uprawnień roli][linkerd-multi-stage-installation]

Możesz również postępować zgodnie z dodatkowymi scenariuszami przy użyciu:

- [Demonstracja emojivoto z konsolidatorem][linkerd-demo-emojivoto]
- [Demonstracja książek z konsolidatorem][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
