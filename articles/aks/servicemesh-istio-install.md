---
title: Instalowanie Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i używać Istio do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 5d47bb7f1f9c1ce39239d6f6ee5826a60ce1741a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244173"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie Istio w usłudze Azure Kubernetes Service (AKS)

[Istio][istio-github] to siatka usługi typu "open source", która udostępnia kluczowy zestaw funkcji dla mikrousług w klastrze Kubernetes. Te funkcje obejmują zarządzanie ruchem, tożsamość usługi i zabezpieczenia, wymuszanie zasad oraz ich przestrzeganie. Aby uzyskać więcej informacji na temat Istio, zobacz oficjalny dokument dotyczący [Istio?][istio-docs-concepts] .

W tym artykule opisano sposób instalowania programu Istio. `istioctl`Plik binarny klienta Istio jest instalowany na komputerze klienckim, a składniki Istio są instalowane w klastrze Kubernetes na AKS.

> [!NOTE]
> Następujące instrukcje odwołują się do wersji Istio `1.4.0` .
>
> Wersje Istio zostały `1.4.x` przetestowane przez zespół Istio w odniesieniu do wersji Kubernetes `1.13` , `1.14` , `1.15` . Dodatkowe wersje Istio można znaleźć w artykułach usługi [GitHub-Istio][istio-github-releases], informacje o każdej z tych wersji w usłudze [Istio News][istio-release-notes] i obsługiwane wersje Kubernetes na [ogół często zadawane pytania][istio-faq]na temat Istio.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobierz i zainstaluj dane binarne klienta Istio istioctl
> * Zainstaluj Istio na AKS
> * Weryfikowanie instalacji Istio
> * Dostęp do dodatków
> * Odinstaluj Istio z AKS

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach przedstawionych w tym artykule przyjęto założenie, że utworzono klaster AKS (Kubernetes `1.13` lub nowszy z włączoną funkcją RBAC) i nawiązano `kubectl` połączenie z klastrem. Jeśli potrzebujesz pomocy z dowolnym z tych elementów, zobacz [Przewodnik Szybki Start AKS][aks-quickstart].

Upewnij się, że zapoznaj się z dokumentacją dotyczącą [wydajności i skalowalności Istio](https://istio.io/docs/concepts/performance-and-scalability/) , aby poznać dodatkowe wymagania dotyczące zasobów związanych z uruchamianiem Istio w klastrze AKS. Wymagania podstawowe i dotyczące pamięci będą się różnić w zależności od konkretnego obciążenia. Wybierz odpowiednią liczbę węzłów i rozmiar maszyny wirtualnej, które mają być przeznaczone do instalacji.

Ten artykuł oddziela wskazówki dotyczące instalacji Istio do kilku dyskretnych kroków. Wynik końcowy ma taką samą strukturę jak oficjalne [wskazówki dotyczące][istio-install-istioctl]instalacji Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Zainstaluj składniki Istio na AKS

Będziemy instalować [Grafana][grafana] i [Kiali][kiali] w ramach naszej instalacji Istio. Usługa Grafana udostępnia pulpity nawigacyjne do analizy i monitorowania, a Kiali udostępnia pulpit nawigacyjny zauważalności siatki usług. W naszej instalacji każdy z tych składników wymaga poświadczeń, które muszą być podane jako [wpis tajny][kubernetes-secrets].

Przed zainstalowaniem składników Istio należy utworzyć klucze tajne zarówno dla Grafana, jak i Kiali. Te klucze tajne muszą być zainstalowane w `istio-system` przestrzeni nazw, która będzie używana przez Istio, więc konieczne będzie utworzenie przestrzeni nazw. Musimy użyć `--save-config` opcji podczas tworzenia przestrzeni nazw za pomocą, `kubectl create` aby Instalator Istio mógł działać `kubectl apply` na tym obiekcie w przyszłości.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Zainstaluj składniki Istio

Teraz, po pomyślnym utworzeniu wpisów tajnych Grafana i Kiali w klastrze AKS, czas na zainstalowanie składników Istio. 

Podejście instalacji [Helm][helm] dla Istio będzie przestarzałe w przyszłości. Nowe podejście instalacji dla usługi Istio wykorzystuje dane `istioctl` binarne klienta, [profile konfiguracji Istio][istio-configuration-profiles]oraz nową [specyfikację i interfejs API płaszczyzny kontroli Istio][istio-control-plane]. To nowe podejście polega na tym, czego będziemy używać do instalacji usługi Istio.

> [!NOTE]
> Istio obecnie muszą być zaplanowane do uruchomienia w węzłach systemu Linux. Jeśli w klastrze znajdują się węzły systemu Windows Server, musisz upewnić się, że Istio są zaplanowane do uruchomienia tylko w węzłach z systemem Linux. Użyjemy [selektorów węzłów][kubernetes-node-selectors] , aby upewnić się, że zasobniki są zaplanowane do poprawnego węzła.

> [!CAUTION]
> Funkcje [usługi biodiscovery (poufnego odnajdowania)][istio-feature-sds] i [Istio CNI][istio-feature-cni] Istio są obecnie w [technologii alfa][istio-feature-stages], dlatego należy je zapewnić przed ich włączeniem. 
>
> Należy pamiętać, że funkcja Kubernetes [projekcji woluminu tokenów konta usługi][kubernetes-feature-sa-projected-volume] jest teraz **włączona** dla wszystkich Kubernetes 1,13 i nowszych wersji na AKS.

Utwórz plik o nazwie `istio.aks.yaml` z następującą zawartością. Ten plik zawiera szczegółowe informacje dotyczące [Istio kontroli płaszczyzny][istio-control-plane] dla konfigurowania Istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Zainstaluj program istio przy użyciu `istioctl apply` polecenia i powyższej warstwy `istio.aks.yaml` kontrolnej płaszczyzny kontroli istio w następujący sposób:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Instalator wdroży wiele [CRDs][kubernetes-crd] , a następnie zarządza zależnościami w celu zainstalowania wszystkich odpowiednich obiektów zdefiniowanych dla tej konfiguracji programu Istio. Powinien wyglądać podobnie do poniższego fragmentu kodu.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

W tym momencie wdrożono Istio w klastrze AKS. Aby upewnić się, że mamy pomyślne wdrożenie Istio, przejdź do następnej sekcji, aby [sprawdzić poprawność instalacji Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Weryfikowanie instalacji Istio

Najpierw upewnij się, że zostały utworzone oczekiwane usługi. Aby wyświetlić uruchomione usługi, użyj polecenia [polecenia kubectl Get SVC][kubectl-get] . Zbadaj `istio-system` przestrzeń nazw, w której składniki Istio i dodatek zostały zainstalowane przez `istio` Wykres Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

Następujące przykładowe dane wyjściowe przedstawiają usługi, które powinny być teraz uruchomione:

- `istio-*`Services
- `jaeger-*``tracing` `zipkin` usługi śledzenia dodatków, i
- `prometheus`Usługa metryk dodatków
- `grafana`Usługa do analizy i monitorowania pulpitu nawigacyjnego
- `kiali`usługa pulpitu nawigacyjnego sieci usługi dodatku

Jeśli zostanie `istio-ingressgateway` wyświetlony zewnętrzny adres IP `<pending>` , odczekaj kilka minut, aż adres IP nie zostanie przypisany przez sieć platformy Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Następnie upewnij się, że zostały utworzone wymagane zasobniki. Użyj polecenia [polecenia kubectl Get][kubectl-get] -Binding i ponownie Zbadaj `istio-system` przestrzeń nazw:

```console
kubectl get pods --namespace istio-system
```

Następujące przykładowe dane wyjściowe pokazują, które z nich są uruchomione:

- na `istio-*` podst.
- `prometheus-*`metryki dodatków pod
- `grafana-*`pulpit nawigacyjny dodatku i analiz
- `kiali`pulpit nawigacyjny sieci dodatku usług w systemie

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Wszystkie z tych zasobników powinny zawierać stan `Running` . Jeśli Twoje zasobniki nie mają tych stanów, Zaczekaj chwilę lub dwa, aż do ich wykonania. Jeśli którykolwiek z raportów zawiera raport o problemie, użyj polecenia [polecenia kubectl opisz pod][kubectl-describe] , aby przejrzeć ich dane wyjściowe i stan.

## <a name="accessing-the-add-ons"></a>Uzyskiwanie dostępu do dodatków

Wiele dodatków zostało zainstalowanych przez Istio w naszych konfiguracjach, które zapewniają dodatkową funkcjonalność. Aplikacje sieci Web dla dodatków **nie** są udostępniane publicznie za pośrednictwem zewnętrznego adresu IP. 

Aby uzyskać dostęp do interfejsów użytkownika dodatku, użyj `istioctl dashboard` polecenia. To polecenie wykorzystuje port [polecenia kubectl do przodu][kubectl-port-forward] i losowo, aby utworzyć bezpieczne połączenie między komputerem klienckim i odpowiednim pod nim w klastrze AKS. Następnie aplikacja sieci Web dodatku zostanie automatycznie otwarta w domyślnej przeglądarce.

Dodaliśmy dodatkową warstwę zabezpieczeń dla Grafana i Kiali, określając poświadczenia dla nich wcześniej w tym artykule.

### <a name="grafana"></a>Grafana

Pulpity nawigacyjne analizy i monitorowania dla Istio są udostępniane przez [Grafana][grafana]. Pamiętaj, aby przed wyświetleniem monitu użyć poświadczeń utworzonych za pośrednictwem klucza tajnego Grafana. Otwórz pulpit nawigacyjny Grafana w następujący sposób:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Metryki dla Istio są dostarczane przez [Prometheus][prometheus]. Otwórz pulpit nawigacyjny Prometheus w następujący sposób:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Śledzenie w ramach Istio jest dostarczane przez [Jaeger][jaeger]. Otwórz pulpit nawigacyjny Jaeger w następujący sposób:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Pulpit nawigacyjny obserwowania sieci usługi jest udostępniany przez [Kiali][kiali]. Pamiętaj, aby przed wyświetleniem monitu użyć poświadczeń utworzonych za pośrednictwem klucza tajnego Kiali. Otwórz pulpit nawigacyjny Kiali w następujący sposób:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Dostępny jest prosty interfejs do serwerów proxy [wysłannika][envoy] . Zawiera informacje o konfiguracji i metryki dla serwera proxy wysłannika uruchomionego w określonym pod. Należy bezpiecznie otworzyć interfejs wysłannika w następujący sposób:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Odinstaluj Istio z AKS

> [!WARNING]
> Usunięcie Istio z działającego systemu może skutkować problemami związanymi z ruchem między usługami. Upewnij się, że zostały wprowadzone przepisy dotyczące systemu, aby nadal działały prawidłowo bez Istio przed kontynuowaniem.

### <a name="remove-istio-components-and-namespace"></a>Usuń składniki Istio i przestrzeń nazw

Aby usunąć Istio z klastra AKS, użyj `istioctl manifest generate` polecenia z `istio.aks.yaml` plikiem specyfikacji płaszczyzny kontroli Istio. Spowoduje to wygenerowanie wdrożonego manifestu, do którego zostanie potoku, `kubectl delete` Aby usunąć wszystkie zainstalowane składniki i `istio-system` przestrzeń nazw.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Usuń Istio CRDs i wpisy tajne

Powyższe polecenia powodują usunięcie wszystkich składników Istio i przestrzeni nazw, ale nadal pozostawiamy wygenerowane wpisy tajne Istio. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano, jak można użyć usługi Istio do udostępnienia inteligentnego routingu w celu przeprowadzenia wydania w wersji kanaryjskiej:

> [!div class="nextstepaction"]
> [Scenariusz inteligentnego routingu AKS Istio][istio-scenario-routing]

Aby zapoznać się z dodatkowymi opcjami instalacji i konfiguracji dla usługi Istio, zobacz następujące oficjalne wskazówki dotyczące Istio:

- [Istio — przewodniki instalacji][istio-installation-guides]

Możesz również postępować zgodnie z dodatkowymi scenariuszami przy użyciu:

- [Przykład aplikacji Istio Bookinfo][istio-bookinfo-example]

Aby dowiedzieć się, jak monitorować aplikację AKS przy użyciu Application Insights i Istio, zobacz następującą dokumentację Azure Monitor:

- [Zero monitorowanie aplikacji Instrumentacji dla aplikacji hostowanych Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: /windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: ../azure-monitor/app/kubernetes.md

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
