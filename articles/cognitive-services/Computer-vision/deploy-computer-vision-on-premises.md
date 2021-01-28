---
title: Używanie kontenera przetwarzanie obrazów z Kubernetes i Helm
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć kontener przetwarzanie obrazów przy użyciu Kubernetes i Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aahi
ms.openlocfilehash: 8f795a7c0d8a2bac09c588924b0fd87e62258da3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946197"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Używanie kontenera przetwarzanie obrazów z Kubernetes i Helm

Jedną z opcji zarządzania kontenerami przetwarzanie obrazów w środowisku lokalnym jest użycie Kubernetes i Helm. Korzystając z Kubernetes i Helm do definiowania obrazu kontenera przetwarzanie obrazów, utworzymy pakiet Kubernetes. Ten pakiet zostanie wdrożony w klastrze Kubernetes lokalnie. Na koniec zapoznaj się z tematem testowanie wdrożonych usług. Aby uzyskać więcej informacji na temat uruchamiania kontenerów platformy Docker bez aranżacji Kubernetes, zobacz [Install and run przetwarzanie obrazów Containers](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne przed użyciem kontenerów przetwarzanie obrazów lokalnie:

| Wymagane | Przeznaczenie |
|----------|---------|
| Konto platformy Azure | Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][free-azure-account]. |
| Interfejs wiersza polecenia Kubernetes | [Interfejs wiersza polecenia Kubernetes][kubernetes-cli] jest wymagany do zarządzania poświadczeniami udostępnionymi z rejestru kontenerów. Kubernetes jest również wymagany przed Helm, który jest menedżerem pakietów Kubernetes. |
| Interfejs wiersza polecenia Helm | Zainstaluj [interfejs wiersza polecenia Helm][helm-install], który służy do instalowania wykresu Helm (definicja pakietu kontenerów). |
| Zasób przetwarzanie obrazów |Aby można było używać kontenera, musisz mieć:<br><br>Zasób usługi Azure **Przetwarzanie obrazów** i skojarzony klucz interfejsu API dla identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach przeglądów i kluczy dla zasobu i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}**: punkt końcowy określony na stronie **Przegląd**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Nawiązywanie połączenia z klastrem Kubernetes

Oczekuje się, że komputer hosta ma dostępny klaster Kubernetes. Zapoznaj się z tym samouczkiem dotyczącym [wdrażania klastra Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) , aby poznać koncepcję wdrażania klastra Kubernetes na komputerze hosta. Więcej informacji na temat wdrożeń można znaleźć w [dokumentacji Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurowanie wartości wykresu Helm na potrzeby wdrożenia

Zacznij od utworzenia folderu o nazwie *Read*. Następnie wklej następującą zawartość YAML w nowym pliku o nazwie `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Aby skonfigurować wartości domyślne wykresu Helm, skopiuj i wklej następujący YAML do pliku o nazwie `values.yaml` . Zastąp `# {ENDPOINT_URI}` `# {API_KEY}` Komentarze i komentarz własnymi wartościami. W razie konieczności Skonfiguruj resultExpirationPeriod, Redis i RabbitMQ.

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Jeśli `billing` wartości i `apikey` nie zostaną podane, usługi wygasną po 15 minutach. Podobnie weryfikacja nie powiedzie się, ponieważ usługi nie są dostępne.
> 
> - W przypadku wdrożenia wielu kontenerów odczytu za modułem równoważenia obciążenia, na przykład w obszarze Docker Compose lub Kubernetes, wymagana jest zewnętrzna pamięć podręczna. Ponieważ kontener przetwarzania i kontener żądania GET nie mogą być takie same, zewnętrzna pamięć podręczna przechowuje wyniki i udostępnia je między kontenerami. Aby uzyskać szczegółowe informacje na temat ustawień pamięci podręcznej, zobacz [Configure przetwarzanie obrazów Docker Containers](./computer-vision-resource-container-config.md).
>

Utwórz folder *szablonów* w katalogu *Read* . Skopiuj i wklej następujący YAML do pliku o nazwie `deployment.yaml` . `deployment.yaml`Plik będzie używany jako szablon Helm.

> Szablony generują pliki manifestu, które są YAMLmi opisami zasobów, które Kubernetes mogą zrozumieć. [— Przewodnik po szablonach wykresu Helm][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
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
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

W tym samym folderze *templates* skopiuj i wklej następujące funkcje pomocnika do programu `helpers.tpl` . `helpers.tpl` definiuje przydatne funkcje, które ułatwiają generowanie szablonu Helm.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku podrzędnego, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMain := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostReplica := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMain $hostReplica $passWord $connTail -}}
{{- end -}}
```
Szablon określa usługę modułu równoważenia obciążenia oraz wdrożenie kontenera/obrazu do odczytu.

### <a name="the-kubernetes-package-helm-chart"></a>Pakiet Kubernetes (wykres Helm)

*Wykres Helm* zawiera konfigurację, którą obrazy platformy Docker pobrać z `mcr.microsoft.com` rejestru kontenerów.

> [Wykres Helm][helm-charts] to zbiór plików, które opisują powiązany zestaw zasobów Kubernetes. Pojedynczy wykres może służyć do wdrażania bardzo prostego, takiego jak Memcached pod lub złożonego, takiego jak pełny stos aplikacji sieci Web z serwerami HTTP, bazami danych, pamięciami podręcznymi i tak dalej.

Dostarczone *wykresy Helm* pobierają obrazy platformy Docker usługi przetwarzanie obrazów i odpowiadające im usługi z `mcr.microsoft.com` rejestru kontenerów.

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

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>Wdrażanie wielu kontenerów V3 w klastrze Kubernetes

Począwszy od wersji v3 kontenera, można używać kontenerów równolegle na poziomie zadania i strony.

Zgodnie z projektem każdy kontener v3 ma dyspozytora i proces roboczy rozpoznawania. Dyspozytor jest odpowiedzialny za dzielenie zadania wielostronicowego na wiele podzadań pojedynczej strony. Proces roboczy rozpoznawania jest zoptymalizowany pod kątem rozpoznawania jednostronicowego dokumentu. Aby osiągnąć równoległość na poziomie strony, wdróż wiele kontenerów v3 za modułem równoważenia obciążenia i pozwól kontenerom na udostępnianie uniwersalnego magazynu i kolejki. 

> [!NOTE] 
> Obecnie obsługiwane są tylko usługi Azure Storage i Azure Queue. 

Kontener otrzymujący żądanie może podzielić zadanie na podrzędne podzadania pojedynczej strony i dodać je do kolejki uniwersalnej. Każdy proces roboczy rozpoznawania z kontenera mniej obciążony może korzystać z podzadań jednostronicowych z kolejki, przeprowadzać rozpoznawanie i przekazywać wynik do magazynu. Przepływność można ulepszyć do `n` czasu, w zależności od liczby wdrożonych kontenerów.

Kontener v3 udostępnia interfejs API sondowania na żywo pod `/ContainerLiveness` ścieżką. Skorzystaj z następującego przykładu wdrażania, aby skonfigurować sondę na żywo dla Kubernetes. 

Skopiuj i wklej następujący YAML do pliku o nazwie `deployment.yaml` . Zastąp `# {ENDPOINT_URI}` `# {API_KEY}` Komentarze i komentarz własnymi wartościami. Zastąp `# {AZURE_STORAGE_CONNECTION_STRING}` komentarz własnymi parametrami połączenia usługi Azure Storage. Skonfiguruj do pożądanej `replicas` liczby, która jest ustawiona na `3` w poniższym przykładzie.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        livenessProbe:
          httpGet:
            path: /ContainerLiveness
            port: 5000
          initialDelaySeconds: 60
          periodSeconds: 60
          timeoutSeconds: 20
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

Uruchom następujące polecenie. 

```console
kubectl apply -f deployment.yaml
```

Poniżej znajduje się przykładowe dane wyjściowe, które mogą zostać wyświetlone po pomyślnym wykonaniu wdrożenia:

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

Wdrożenie Kubernetes może potrwać kilka minut. Aby upewnić się, że oba typy i usługi są poprawnie wdrożone i dostępne, wykonaj następujące polecenie:

```console
kubectl get all
```

Powinny pojawić się dane wyjściowe konsoli podobne do następujących:

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat instalowania aplikacji z programem Helm w usłudze Azure Kubernetes Service (AKS), [odwiedź to tutaj][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontenery usług Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
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
