---
title: Instalowanie Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i używać Istio do tworzenia siatki usługi w klastrze usługi Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683832"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalowanie i używanie Istio w usłudze Azure Kubernetes Service (AKS)

[Istio][istio-github] to siatka usługi typu "open source", która udostępnia kluczowy zestaw funkcji dla mikrousług w klastrze Kubernetes. Te funkcje obejmują zarządzanie ruchem, tożsamość usługi i zabezpieczenia, wymuszanie zasad oraz ich przestrzeganie. Aby uzyskać więcej informacji na temat Istio, zobacz oficjalny dokument dotyczący [Istio?][istio-docs-concepts] .

W tym artykule opisano sposób instalowania programu Istio. `istioctl`Plik binarny klienta Istio jest instalowany na komputerze klienckim, a składniki Istio są instalowane w klastrze Kubernetes na AKS.

> [!NOTE]
> Następujące instrukcje odwołują się do wersji Istio `1.7.3` .
>
> Wersje Istio zostały `1.7.x` przetestowane przez zespół Istio w odniesieniu do wersji Kubernetes `1.16+` . Dodatkowe wersje Istio można znaleźć w artykułach usługi [GitHub-Istio][istio-github-releases], informacje o każdej z tych wersji w usłudze [Istio News][istio-release-notes] i obsługiwane wersje Kubernetes na [ogół często zadawane pytania][istio-faq]na temat Istio.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Pobierz i zainstaluj dane binarne klienta Istio istioctl
> * Zainstaluj Istio na AKS
> * Weryfikowanie instalacji Istio
> * Dostęp do dodatków
> * Odinstaluj Istio z AKS

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach przedstawionych w tym artykule przyjęto założenie, że utworzono klaster AKS (Kubernetes `1.16` lub nowszy z włączoną obsługą RBAC Kubernetes) i nawiązano `kubectl` połączenie z klastrem. Jeśli potrzebujesz pomocy z dowolnym z tych elementów, zobacz [Przewodnik Szybki Start AKS][aks-quickstart].

Upewnij się, że zapoznaj się z dokumentacją dotyczącą [wydajności i skalowalności Istio](https://istio.io/docs/concepts/performance-and-scalability/) , aby poznać dodatkowe wymagania dotyczące zasobów związanych z uruchamianiem Istio w klastrze AKS. Wymagania podstawowe i dotyczące pamięci będą się różnić w zależności od konkretnego obciążenia. Wybierz odpowiednią liczbę węzłów i rozmiar maszyny wirtualnej, które mają być przeznaczone do instalacji.

Ten artykuł oddziela wskazówki dotyczące instalacji Istio do kilku dyskretnych kroków. Wynik końcowy ma taką samą strukturę jak oficjalne [wskazówki dotyczące][istio-install-istioctl]instalacji Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>Instalowanie operatora Istio na AKS

Istio zapewnia [operatorowi][istio-install-operator] zarządzanie instalacją i aktualizacjami składników Istio w klastrze AKS. Zainstalujemy operatora Istio przy użyciu `istioctl` pliku binarnego klienta.

```bash
istioctl operator init
```

Powinny być widoczne następujące dane wyjściowe, aby potwierdzić, że zainstalowano operatora Istio.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Operator Istio jest instalowany w `istio-operator` przestrzeni nazw. Zbadaj przestrzeń nazw.

```bash
kubectl get all -n istio-operator
```

Należy zobaczyć następujące składniki zostały wdrożone.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Możesz dowiedzieć się więcej na temat wzorca operatora i sposobu, w jaki może on ułatwić automatyzację złożonych zadań za pośrednictwem [Kubernetes.IO][kubernetes-operator].


### <a name="install-istio-components"></a>Zainstaluj składniki Istio

Teraz, po pomyślnym zainstalowaniu operatora Istio w klastrze AKS, należy zainstalować składniki Istio. 

`default`Do skompilowania [specyfikacji operatora Istio][istio-control-plane]zostanie wykorzystamy [profil konfiguracji Istio][istio-configuration-profiles] .

Można uruchomić następujące polecenie, `istioctl` Aby wyświetlić konfigurację `default` profilu konfiguracji Istio.

```bash
istioctl profile dump default
```

> [!NOTE]
> Istio obecnie muszą być zaplanowane do uruchomienia w węzłach systemu Linux. Jeśli w klastrze znajdują się węzły systemu Windows Server, musisz upewnić się, że Istio są zaplanowane do uruchomienia tylko w węzłach z systemem Linux. Użyjemy [selektorów węzłów][kubernetes-node-selectors] , aby upewnić się, że zasobniki są zaplanowane do poprawnego węzła.

> [!CAUTION]
> Funkcje [ISTIO CNI][istio-feature-cni] Istio są obecnie w [alfa][istio-feature-stages], dlatego należy je zapewnić przed ich włączeniem. 

Utwórz plik o nazwie `istio.aks.yaml` z następującą zawartością. Ten plik zawiera [specyfikację operatora Istio][istio-control-plane] na potrzeby konfigurowania Istio.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Utwórz `istio-system` przestrzeń nazw i Wdróż specyfikację operatora Istio w tej przestrzeni nazw. Operator Istio będzie oglądany dla specyfikacji operatora Istio i będzie używać go do instalowania i konfigurowania Istio w klastrze AKS.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

W tym momencie wdrożono Istio w klastrze AKS. Aby upewnić się, że mamy pomyślne wdrożenie Istio, przejdź do następnej sekcji, aby [sprawdzić poprawność instalacji Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Weryfikowanie instalacji Istio

Wykonaj zapytanie dotyczące `istio-system` przestrzeni nazw, gdzie Istio i składniki dodatku zostały zainstalowane przez operatora Istio:

```bash
kubectl get all -n istio-system
```

Powinny zostać wyświetlone następujące składniki:

- `istio*` -składniki Istio
- `jaeger-*`, `tracing` i `zipkin` — dodatek do śledzenia
- `prometheus` -dodatek metryk
- `grafana` -analizowanie i monitorowanie pulpitu nawigacyjnego
- `kiali` -dodatek pulpitu nawigacyjnego sieci usługi

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

Możesz również uzyskać dodatkowe informacje na temat instalacji, obserwując dzienniki dla operatora Istio.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Jeśli zostanie `istio-ingressgateway` wyświetlony zewnętrzny adres IP `<pending>` , odczekaj kilka minut, aż adres IP nie zostanie przypisany przez sieć platformy Azure.

Wszystkie z tych zasobników powinny zawierać stan `Running` . Jeśli Twoje zasobniki nie mają tych stanów, Zaczekaj chwilę lub dwa, aż do ich wykonania. Jeśli którykolwiek z raportów zawiera raport o problemie, użyj polecenia [polecenia kubectl opisz pod][kubectl-describe] , aby przejrzeć ich dane wyjściowe i stan.

## <a name="accessing-the-add-ons"></a>Uzyskiwanie dostępu do dodatków

Wiele dodatków zostało zainstalowanych przez operatora Istio, który zapewnia dodatkową funkcjonalność. Aplikacje sieci Web dla dodatków **nie** są udostępniane publicznie za pośrednictwem zewnętrznego adresu IP. 

Aby uzyskać dostęp do interfejsów użytkownika dodatku, użyj `istioctl dashboard` polecenia. To polecenie służy do tworzenia bezpiecznego połączenia między komputerem klienckim i odpowiednim elementem w klastrze AKS przy użyciu portu [polecenia kubectl do przodu][kubectl-port-forward] i losowego. Następnie aplikacja sieci Web dodatku zostanie automatycznie otwarta w domyślnej przeglądarce.

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

### <a name="remove-istio"></a>Usuń Istio

Aby usunąć Istio z klastra AKS, Usuń `IstioOperator` zasób o nazwie `istio-control-plane` , który został dodany wcześniej. Operator Istio rozpozna, że Specyfikacja operatora Istio została usunięta, a następnie usunie wszystkie skojarzone składniki Istio.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

Aby sprawdzić, kiedy wszystkie składniki Istio zostały usunięte, można uruchomić następujące czynności.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Usuń operator Istio

Po pomyślnym odinstalowaniu Istio można także usunąć operator Istio.

```bash
istioctl operator remove
```

A następnie usuń `istio-` przestrzenie nazw.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z dodatkowymi opcjami instalacji i konfiguracji dla usługi Istio, zobacz następujące oficjalne wskazówki dotyczące Istio:

- [Istio — przewodniki instalacji][istio-installation-guides]

Możesz również postępować zgodnie z dodatkowymi scenariuszami przy użyciu:

- [Przykład aplikacji Istio Bookinfo][istio-bookinfo-example]

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
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
