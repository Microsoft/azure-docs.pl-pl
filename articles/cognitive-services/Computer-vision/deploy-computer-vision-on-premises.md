---
title: Używanie kontenera przetwarzanie obrazów z Kubernetes i Helm
titleSuffix: Azure Cognitive Services
description: Wdróż kontener przetwarzanie obrazów w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9aac374de5af748eafbe4c22e5fc89f64e483c2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877981"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Używanie kontenera przetwarzanie obrazów z Kubernetes i Helm

Jedną z opcji zarządzania kontenerami przetwarzanie obrazów w środowisku lokalnym jest użycie Kubernetes i Helm. Korzystając z Kubernetes i Helm do definiowania obrazu kontenera przetwarzanie obrazów, utworzymy pakiet Kubernetes. Ten pakiet zostanie wdrożony w klastrze Kubernetes lokalnie. Na koniec zapoznaj się z tematem testowanie wdrożonych usług. Aby uzyskać więcej informacji na temat uruchamiania kontenerów platformy Docker bez aranżacji Kubernetes, zobacz [Install and run przetwarzanie obrazów Containers](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne przed użyciem kontenerów przetwarzanie obrazów lokalnie:

| Wymagany | Przeznaczenie |
|----------|---------|
| Konto platformy Azure | Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto][free-azure-account] . |
| Interfejs wiersza polecenia Kubernetes | [Interfejs wiersza polecenia Kubernetes][kubernetes-cli] jest wymagany do zarządzania poświadczeniami udostępnionymi z rejestru kontenerów. Kubernetes jest również wymagany przed Helm, który jest menedżerem pakietów Kubernetes. |
| Interfejs wiersza polecenia Helm | Zainstaluj [interfejs wiersza polecenia Helm][helm-install], który służy do instalowania wykresu Helm (definicja pakietu kontenerów). |
| Zasób przetwarzanie obrazów |Aby można było używać kontenera, musisz mieć:<br><br>Zasób usługi Azure **Przetwarzanie obrazów** i skojarzony klucz interfejsu API dla identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach przeglądów i kluczy dla zasobu i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}**: punkt końcowy określony na stronie **Przegląd**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Nawiązywanie połączenia z klastrem Kubernetes

Oczekuje się, że komputer hosta ma dostępny klaster Kubernetes. Zapoznaj się z tym samouczkiem dotyczącym [wdrażania klastra Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) , aby poznać koncepcję wdrażania klastra Kubernetes na komputerze hosta.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Udostępnianie poświadczeń platformy Docker w klastrze Kubernetes

Aby umożliwić klastrowi Kubernetes na `docker pull` skonfigurowane obrazy z rejestru `containerpreview.azurecr.io` kontenerów, należy przenieść poświadczenia platformy Docker do klastra. Wykonaj poniższe [`kubectl create`][kubectl-create] polecenie, aby utworzyć *wpis tajny platformy Docker* na podstawie poświadczeń podanych w ramach wymagania wstępnego dostępu do rejestru kontenerów.

Z wybranego interfejsu wiersza polecenia Uruchom następujące polecenie. Pamiętaj `<username>`, aby zastąpić poświadczenia rejestru `<password>`kontenerów `<email-address>` , i.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Jeśli masz już dostęp do rejestru `containerpreview.azurecr.io` kontenerów, możesz utworzyć wpis tajny Kubernetes przy użyciu flagi generycznej. Rozważ następujące polecenie, które jest wykonywane względem pliku JSON konfiguracji platformy Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Następujące dane wyjściowe są drukowane w konsoli po pomyślnym utworzeniu wpisu tajnego.

```console
secret "containerpreview" created
```

Aby sprawdzić, czy wpis tajny został utworzony, wykonaj [`kubectl get`][kubectl-get] polecenie z `secrets` flagą.

```console
kubectl get secrets
```

Wykonanie operacji `kubectl get secrets` drukuje wszystkie skonfigurowane wpisy tajne.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurowanie wartości wykresu Helm na potrzeby wdrożenia

Zacznij od utworzenia folderu o nazwie *Read*, a następnie wklej następującą zawartość YAML do nowego pliku o nazwie *Chart. yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Aby skonfigurować wartości domyślne wykresu Helm, skopiuj i wklej następujący YAML do pliku o nazwie `values.yaml`. Zastąp `# {ENDPOINT_URI}` Komentarze `# {API_KEY}` i komentarz własnymi wartościami.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Jeśli wartości `billing` i `apikey` nie zostaną podane, usługi wygasną po 15 minutach. Podobnie weryfikacja nie powiedzie się, ponieważ usługi nie będą dostępne.

Utwórz folder *szablonów* w katalogu *Read* . Skopiuj i wklej następujący YAML do pliku o nazwie `deployment.yaml`. `deployment.yaml` Plik będzie używany jako szablon Helm.

> Szablony generują pliki manifestu, które są YAMLmi opisami zasobów, które Kubernetes mogą zrozumieć. [— Przewodnik po szablonach wykresu Helm][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Szablon określa usługę modułu równoważenia obciążenia oraz wdrożenie kontenera/obrazu do odczytu.

### <a name="the-kubernetes-package-helm-chart"></a>Pakiet Kubernetes (wykres Helm)

*Wykres Helm* zawiera konfigurację, którą obrazy platformy Docker pobrać z rejestru `containerpreview.azurecr.io` kontenerów.

> [Wykres Helm][helm-charts] to zbiór plików, które opisują powiązany zestaw zasobów Kubernetes. Pojedynczy wykres może służyć do wdrażania bardzo prostego, takiego jak Memcached pod lub złożonego, takiego jak pełny stos aplikacji sieci Web z serwerami HTTP, bazami danych, pamięciami podręcznymi i tak dalej.

Dostarczone *wykresy Helm* pobierają obrazy platformy Docker usługi przetwarzanie obrazów i odpowiadające im usługi z rejestru `containerpreview.azurecr.io` kontenerów.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalowanie wykresu Helm w klastrze Kubernetes

Aby zainstalować *Wykres Helm*, należy wykonać [`helm install`][helm-install-cmd] polecenie. Upewnij się, że polecenie Install jest wykonane z katalogu znajdującego się powyżej `read` folderu.

```console
helm install read ./read
```

Oto przykładowe dane wyjściowe, które mogą być widoczne w przypadku pomyślnego wykonania instalacji:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Wdrożenie Kubernetes może potrwać kilka minut. Aby upewnić się, że oba typy i usługi są poprawnie wdrożone i dostępne, wykonaj następujące polecenie:

```console
kubectl get all
```

Powinieneś oczekiwać, że zobaczysz coś podobnego do następującego:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat instalowania aplikacji z programem Helm w usłudze Azure Kubernetes Service (AKS), [odwiedź to tutaj][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontenery Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
