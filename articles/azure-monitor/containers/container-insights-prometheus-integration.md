---
title: Konfigurowanie Azure Monitor do integracji kontenerów Prometheus | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Azure Monitor dla agenta kontenerów w celu odpadków metryk od Prometheus z klastrem Kubernetes.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: f5a9b364bc3e51307bd44d8338485f482bda6e1e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618770"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Konfigurowanie zbierania metryk systemu Prometheus za pomocą usługi Azure Monitor dla kontenerów

[Prometheus](https://prometheus.io/) to popularne rozwiązanie do monitorowania metryk typu "open source" i jest częścią [natywnej platformy obliczeniowej w chmurze](https://www.cncf.io/). Azure Monitor dla kontenerów to bezproblemowe środowisko dołączania do zbierania metryk Prometheus. Zazwyczaj w celu korzystania z Prometheus należy skonfigurować serwer Prometheus i zarządzać nim za pomocą magazynu. Przez integrację z Azure Monitor serwer Prometheus nie jest wymagany. Wystarczy uwidocznić punkt końcowy metryk Prometheus przez eksporterów lub punkty (aplikację), a agent kontenerowy dla Azure Monitor kontenerów może wyrównać te metryki. 

![Architektura monitorowania kontenerów dla Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Minimalna wersja agenta obsługiwana w przypadku wycinków Prometheus ma wartość ciprod07092019 lub nowszą, a wersja agenta obsługiwana w przypadku zapisywania błędów konfiguracji i agentów w `KubeMonAgentEvents` tabeli to ciprod10112019. W przypadku usług Azure Red Hat OpenShift i Red Hat OpenShift v4, Agent w wersji ciprod04162020 lub nowszej. 
>
>Aby uzyskać więcej informacji na temat wersji agenta i elementów uwzględnionych w każdej wersji, zobacz [Informacje o wersji agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). 
>Aby sprawdzić wersję agenta, z karty **węzeł** wybierz węzeł, a następnie w okienku właściwości wartość komentarza właściwości **tag obrazu agenta** .

Braki metryk Prometheus są obsługiwane w przypadku klastrów Kubernetes hostowanych w:

- Azure Kubernetes Service (AKS)
- Azure Stack lub lokalnie
- Azure Red Hat OpenShift w wersji 3. x
- Azure Red Hat OpenShift i Red Hat OpenShift w wersji 4. x

### <a name="prometheus-scraping-settings"></a>Ustawienia odpadków Prometheus

Aktywne odróżnienie metryk z Prometheus jest wykonywane z jednej z dwóch perspektyw:

* Adres URL protokołu HTTP w całym klastrze i odnajdywanie obiektów docelowych z listy punktów końcowych usługi. Na przykład usługi k8s Services, takie jak polecenia-DNS i polecenia-State-Metrics oraz adnotacje specyficzne dla aplikacji. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus data_collection_settings. cluster]*.
* Adres URL protokołu HTTP w całym węźle i odnajdywanie obiektów docelowych z listy punktów końcowych usługi. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus_data_collection_settings. Node]*.

| Punkt końcowy | Zakres | Przykład |
|----------|-------|---------|
| Pod adnotacją | Cały klaster | adnotacj <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Usługa Kubernetes | Cały klaster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| adres URL/punkt końcowy | Dla każdego węzła i/lub całego klastra | `http://myurl:9101/metrics` |

W przypadku określenia adresu URL Azure Monitor dla kontenerów odnosi się tylko do punktu końcowego. Gdy jest określona usługa Kubernetes, nazwa usługi jest rozpoznawana z serwerem DNS klastra w celu uzyskania adresu IP, a następnie rozwiązanej usługi.

|Zakres | Klucz | Typ danych | Wartość | Opis |
|------|-----|-----------|-------|-------------|
| Cały klaster | | | | Określ jedną z następujących trzech metod, aby wypróbować punkty końcowe dla metryk. |
| | `urls` | Ciąg | Tablica rozdzielona przecinkami | Punkt końcowy HTTP (podano adres IP lub prawidłową ścieżkę URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery). |
| | `kubernetes_services` | Ciąg | Tablica rozdzielona przecinkami | Tablica usług Kubernetes Services do odporności metryk z metryk polecenia-State-Metrics. Na przykład `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Wartość logiczna | true lub false | Po ustawieniu na wartość `true` w ustawieniach całego klastra, Azure monitor dla agenta kontenerów będą odporne na Kubernetesy w całym klastrze dla następujących adnotacji Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Wartość logiczna | true lub false | Włącza odpadków pod. `monitor_kubernetes_pods` musi być ustawiony na `true` . |
| | `prometheus.io/scheme` | Ciąg | http lub https | Wartość domyślna to złomowanie za pośrednictwem protokołu HTTP. W razie potrzeby ustaw wartość `https` . | 
| | `prometheus.io/path` | Ciąg | Tablica rozdzielona przecinkami | Ścieżka zasobu HTTP, z której mają zostać pobrane metryki. Jeśli ścieżka metryk nie jest `/metrics` zdefiniowana, zdefiniuj ją z tą adnotacją. |
| | `prometheus.io/port` | Ciąg | 9102 | Określ port, z którego mają zostać wycinki. Jeśli port nie jest ustawiony, wartość domyślna to 9102. |
| | `monitor_kubernetes_pods_namespaces` | Ciąg | Tablica rozdzielona przecinkami | Lista dozwolonych przestrzeni nazw do wypadków metryk z Kubernetesowych.<br> Na przykład `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Cały węzeł | `urls` | Ciąg | Tablica rozdzielona przecinkami | Punkt końcowy HTTP (podano adres IP lub prawidłową ścieżkę URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery). |
| Cały węzeł lub cały klaster | `interval` | Ciąg | 60 s | Interwał kolekcji jest wartością domyślną 1 minuty (60 sekund). Kolekcję można modyfikować w przypadku wartości *[prometheus_data_collection_settings. Node]* i/lub *[prometheus_data_collection_settings. cluster]* do jednostek czasu, takich jak s, m, h. |
| Cały węzeł lub cały klaster | `fieldpass`<br> `fielddrop`| Ciąg | Tablica rozdzielona przecinkami | Można określić, które metryki mają być zbierane lub nie z punktu końcowego przez ustawienie listy Zezwalaj ( `fieldpass` ) i nie zezwalaj ( `fielddrop` ). Należy najpierw ustawić listę dozwolonych. |

ConfigMaps jest globalną listą, a do agenta może być zastosowany tylko jeden ConfigMap. Nie można ConfigMaps kolekcji.

## <a name="configure-and-deploy-configmaps"></a>Konfigurowanie i wdrażanie ConfigMaps

Wykonaj następujące kroki, aby skonfigurować plik konfiguracji ConfigMap dla następujących klastrów:

* Azure Kubernetes Service (AKS)
* Azure Stack lub lokalnie
* Azure Red Hat OpenShift w wersji 4. x i Red Hat OpenShift w wersji 4. x

1. [Pobierz](https://aka.ms/container-azm-ms-agentconfig) plik Template ConfigMap YAML i Zapisz go jako Container-AZM-MS-agentconfig. YAML.

   >[!NOTE]
   >Ten krok nie jest wymagany podczas pracy z usługą Azure Red Hat OpenShift, ponieważ szablon ConfigMap już istnieje w klastrze.

2. Edytuj plik YAML ConfigMap przy użyciu dostosowań do metryk Prometheusy.

    >[!NOTE]
    >Jeśli edytujesz plik ConfigMap YAML dla usługi Azure Red Hat OpenShift, najpierw uruchom polecenie, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Aby otworzyć plik w edytorze tekstu.

    >[!NOTE]
    >Należy dodać następującą adnotację `openshift.io/reconcile-protect: "true"` w metadanych *kontenera-AZM-MS-agentconfig* ConfigMap, aby zapobiec uzgodnieniu. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Aby zebrać usługi Kubernetes Services w całym klastrze, Skonfiguruj plik ConfigMap przy użyciu poniższego przykładu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Aby skonfigurować oddzielanie metryk Prometheus z określonego adresu URL w klastrze, należy skonfigurować plik ConfigMap przy użyciu poniższego przykładu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Aby skonfigurować oddzielanie metryk Prometheus z elementu daemonset agenta dla każdego węzła w klastrze, skonfiguruj następujące elementy w ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery. 

    - Aby skonfigurować braki metryk Prometheus przez określenie adnotacji pod, wykonaj następujące czynności:

       1. W ConfigMap określ następujące elementy:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Określ następującą konfigurację dla adnotacji pod pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Jeśli chcesz ograniczyć monitorowanie do określonych obszarów nazw dla kart nazwanych, które mają adnotacje, na przykład Uwzględnij tylko zasobniki dedykowane dla obciążeń produkcyjnych, ustaw wartość na `monitor_kubernetes_pod` `true` w ConfigMap i Dodaj filtr przestrzeni nazw `monitor_kubernetes_pods_namespaces` określający przestrzenie nazw, z których mają być oddzielone. Na przykład `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Uruchom następujące polecenie polecenia kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` .
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" created` .

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>Konfigurowanie i wdrażanie ConfigMaps — Azure Red Hat OpenShift v3

Ta sekcja zawiera wymagania i kroki, aby pomyślnie skonfigurować plik konfiguracji ConfigMap dla klastra Red Hat OpenShift v3. x platformy Azure.

>[!NOTE]
>W przypadku usługi Azure Red Hat OpenShift v3. x plik szablonu ConfigMap jest tworzony w przestrzeni nazw *OpenShift-Azure-Logging* . Nie jest on skonfigurowany do aktywnej metryki lub zbierania danych od agenta.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że jesteś członkiem roli administratora klastra klienta w klastrze Red Hat OpenShift w celu skonfigurowania ustawień wycineker agenta i Prometheus. Aby sprawdzić, czy jesteś członkiem grupy *OSA-Customer-admins* , uruchom następujące polecenie:

``` bash
  oc get groups
```

Dane wyjściowe będą wyglądać następująco:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Jeśli jesteś członkiem grupy *OSA-Customer-admins* , musisz mieć możliwość wyświetlania listy `container-azm-ms-agentconfig` ConfigMap za pomocą następującego polecenia:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Dane wyjściowe będą wyglądać następująco:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>Włączanie monitorowania

Wykonaj następujące kroki, aby skonfigurować plik konfiguracyjny ConfigMap dla klastra Red Hat OpenShift v3. x platformy Azure.

1. Edytuj plik YAML ConfigMap przy użyciu dostosowań do metryk Prometheusy. Szablon ConfigMap już istnieje w klastrze Red Hat OpenShift v3. Uruchom polecenie, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Aby otworzyć plik w edytorze tekstu.

    >[!NOTE]
    >Należy dodać następującą adnotację `openshift.io/reconcile-protect: "true"` w metadanych *kontenera-AZM-MS-agentconfig* ConfigMap, aby zapobiec uzgodnieniu. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Aby zebrać usługi Kubernetes Services w całym klastrze, Skonfiguruj plik ConfigMap przy użyciu poniższego przykładu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Aby skonfigurować oddzielanie metryk Prometheus z określonego adresu URL w klastrze, należy skonfigurować plik ConfigMap przy użyciu poniższego przykładu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Aby skonfigurować oddzielanie metryk Prometheus z elementu daemonset agenta dla każdego węzła w klastrze, skonfiguruj następujące elementy w ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery. 

    - Aby skonfigurować braki metryk Prometheus przez określenie adnotacji pod, wykonaj następujące czynności:

       1. W ConfigMap określ następujące elementy:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Określ następującą konfigurację dla adnotacji pod pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Jeśli chcesz ograniczyć monitorowanie do określonych obszarów nazw dla kart nazwanych, które mają adnotacje, na przykład Uwzględnij tylko zasobniki dedykowane dla obciążeń produkcyjnych, ustaw wartość na `monitor_kubernetes_pod` `true` w ConfigMap i Dodaj filtr przestrzeni nazw `monitor_kubernetes_pods_namespaces` określający przestrzenie nazw, z których mają być oddzielone. Na przykład `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. Zapisz zmiany w edytorze.

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" created` .

Zaktualizowane ConfigMap można wyświetlić, uruchamiając polecenie, `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` . 

## <a name="applying-updated-configmap"></a>Stosowanie zaktualizowanych ConfigMap

Jeśli wdrożono już ConfigMap w klastrze i chcesz ją zaktualizować przy użyciu nowszej konfiguracji, możesz edytować poprzednio używany plik ConfigMap, a następnie zastosować te same polecenia jak wcześniej.

W przypadku następujących środowisk Kubernetes:

- Azure Kubernetes Service (AKS)
- Azure Stack lub lokalnie
- Azure Red Hat OpenShift i Red Hat OpenShift w wersji 4. x

Uruchom polecenie `kubectl apply -f <configmap_yaml_file.yaml` . 

W przypadku klastra usługi Azure Red Hat OpenShift v3. x Uruchom polecenie, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Aby otworzyć plik w domyślnym edytorze, aby go zmodyfikować, a następnie Zapisz.

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verify-configuration"></a>Weryfikuj konfigurację

Aby sprawdzić, czy konfiguracja została pomyślnie zastosowana do klastra, użyj następującego polecenia, aby przejrzeć dzienniki z agenta pod: `kubectl logs omsagent-fdf58 -n=kube-system` . 

>[!NOTE]
>To polecenie nie dotyczy klastra usługi Azure Red Hat OpenShift v3. x.
> 

Jeśli wystąpiły błędy konfiguracji z omsagentch, w danych wyjściowych zostaną wyświetlone błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy związane z zastosowaniem zmian konfiguracji są również dostępne do przeglądu. Następujące opcje są dostępne w celu przeprowadzenia dodatkowego rozwiązywania problemów z zmianami konfiguracji i odpadkami od Prometheus metryk:

- Z dzienników agenta pod przy użyciu tego samego `kubectl logs` polecenia 
    >[!NOTE]
    >To polecenie nie ma zastosowania do klastra Red Hat OpenShift platformy Azure.
    > 

- Z danych na żywo (wersja zapoznawcza). W dziennikach danych na żywo (wersja zapoznawcza) są wyświetlane błędy podobne do następujących:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Z tabeli **KubeMonAgentEvents** w obszarze roboczym log Analytics. Dane są wysyłane co godzinę z ważnością *ostrzegawczą* dotyczącą błędów *odpadków i ważności błędów w* przypadku błędów konfiguracji. Jeśli nie ma żadnych błędów, wpis w tabeli będzie zawierał dane z *informacjami* o ważności, które nie zgłaszają błędów. Właściwość **Tags** zawiera więcej informacji na temat identyfikatora kontenera, na których wystąpił błąd, a także pierwszego wystąpienia, ostatniego wystąpienia i liczby w ciągu ostatniej godziny.

- W przypadku usługi Azure Red Hat OpenShift v3. x i v4. x Sprawdź dzienniki omsagent, przeszukując tabelę **ContainerLog** , aby sprawdzić, czy jest włączone zbieranie dzienników na platformie Azure.

Błędy uniemożliwiają programowi omsagent analizowanie pliku, powodując ponowne uruchomienie i użycie konfiguracji domyślnej. Po naprawieniu błędów w ConfigMap w klastrach innych niż Azure Red Hat OpenShift v3. x Zapisz plik YAML i Zastosuj zaktualizowany ConfigMaps, uruchamiając polecenie: `kubectl apply -f <configmap_yaml_file.yaml` . 

W przypadku usługi Azure Red Hat OpenShift v3. x Edytuj i Zapisz zaktualizowany ConfigMaps, uruchamiając polecenie: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` .

## <a name="query-prometheus-metrics-data"></a>Zapytanie danych metryk Prometheus

Aby wyświetlić metryki prometheuse oddzielone przez Azure Monitor i wszelkie błędy dotyczące konfiguracji/odpadków, które są raportowane przez agenta, przejrzyj [zapytanie Prometheus dane metryk](container-insights-log-search.md#query-prometheus-metrics-data) i konfigurację [zapytania lub błędy związane z brakiem](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Wyświetlanie metryk Prometheus w Grafana

Azure Monitor dla kontenerów obsługuje wyświetlanie metryk przechowywanych w obszarze roboczym Log Analytics na pulpitach nawigacyjnych Grafana. Podano szablon, który można pobrać z [repozytorium pulpitu nawigacyjnego](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, aby rozpocząć pracę i zapoznać się z pomocą techniczną, aby dowiedzieć się, jak wykonywać zapytania dotyczące dodatkowych danych z monitorowanych klastrów w celu wizualizowania niestandardowych pulpitów nawigacyjnych Grafana. 

## <a name="review-prometheus-data-usage"></a>Przegląd użycia danych Prometheus

Aby określić objętość pozyskiwania każdego rozmiaru metryki w GB dziennie, aby zrozumieć, czy jest ono wysokie, podano następujące zapytanie.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

Wyniki będą wyglądać podobnie do następujących:

![Zrzut ekranu przedstawia wyniki zapytania dziennika woluminu pozyskiwania danych](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Aby oszacować każdy rozmiar metryk w GB w miesiącu, aby zrozumieć, czy ilość danych odebranych w obszarze roboczym jest wysoka, podano następujące zapytanie.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Wyniki będą wyglądać podobnie do następujących:

![Wyniki zapytania dziennika woluminu pozyskiwania danych](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Dodatkowe informacje na temat monitorowania użycia danych i analizowania kosztów są dostępne w temacie [Zarządzanie użyciem i kosztami przy użyciu dzienników Azure monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Następne kroki

Więcej informacji o konfigurowaniu ustawień kolekcji agentów dla zmiennych stdout, stderr i środowiskowych w ramach obciążeń kontenera można znaleźć [tutaj](container-insights-agent-config.md). 
