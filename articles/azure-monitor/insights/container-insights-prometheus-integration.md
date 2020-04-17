---
title: Konfigurowanie usługi Azure Monitor dla kontenerów Prometheus Integration | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak skonfigurować agenta usługi Azure Monitor dla kontenerów, aby zeskrobać metryki z programu Prometeusz za pomocą klastra usługi Kubernetes.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 7fcf52cceb69834f68f8e4ce7a2674972a6430fd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537376"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Konfigurowanie skrobania metryk Prometeusza za pomocą usługi Azure Monitor dla kontenerów

[Prometheus](https://prometheus.io/) jest popularnym rozwiązaniem do monitorowania metryk open source i jest częścią [Cloud Native Compute Foundation.](https://www.cncf.io/) Usługa Azure Monitor dla kontenerów zapewnia bezproblemowe dołączanie do zbierania metryk Prometeusza. Zazwyczaj, aby korzystać z Prometheus, musisz skonfigurować serwer Prometheus i zarządzać nim w sklepie. Po zintegrowaniu z usługą Azure Monitor serwer Prometheus nie jest wymagany. Wystarczy udostępnić punkt końcowy metryk Prometheus za pośrednictwem eksporterów lub zasobników (aplikacji), a konteneryzowany agent dla usługi Azure Monitor dla kontenerów może zeskrobać metryki dla Ciebie. 

![Architektura monitorowania kontenerów dla Prometeusza](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Minimalna wersja agenta obsługiwana do skrobania metryk Prometheus jest ciprod07092019 lub nowsza, `KubeMonAgentEvents` a wersja agenta obsługiwana do zapisywania błędów konfiguracji i agenta w tabeli to ciprod10112019. Aby uzyskać więcej informacji na temat wersji agenta i tego, co jest zawarte w każdej wersji, zobacz [informacje o wersji agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Aby zweryfikować wersję agenta, na karcie **Węzeł** wybierz węzeł, a w okienku właściwości wartość notatki właściwości **Agent Image Tag.**

Skrobanie metryk Prometeusza jest obsługiwane w klastrach kubernetes hostowanych na:

- Azure Kubernetes Service (AKS)
- Usługa Azure Stack lub lokalnie
- Azure Red Hat OpenShift

>[!NOTE]
>W przypadku usługi Azure Red Hat OpenShift plik configmap szablonu jest tworzony w obszarze nazw *openshift-azure-logging.* Nie jest skonfigurowany do aktywnego zeskrobać metryki lub zbieranie danych z agenta.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Wymagania wstępne dotyczące openshift red hat azure

Przed rozpoczęciem upewnij się, że jesteś członkiem roli administratora klastra klienta klastra Azure Red Hat OpenShift, aby skonfigurować konteneryzowany agent i ustawienia skrobania Prometheus. Aby sprawdzić, czy jesteś członkiem grupy *osa-customer-admins,* uruchom następujące polecenie:

``` bash
  oc get groups
```

Dane wyjściowe będą podobne do następujących:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Jeśli jesteś członkiem grupy *osa-customer-admins,* powinieneś `container-azm-ms-agentconfig` mieć możliwość wyświetlenia configmapa za pomocą następującego polecenia:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Dane wyjściowe będą podobne do następujących:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Ustawienia skrobania Prometheus

Aktywne skrobanie metryk z Prometeusza odbywa się z jednej z dwóch perspektyw:

* Całego klastra — ADRES URL HTTP i odnajdywanie obiektów docelowych z wymienionych punktów końcowych usługi. Na przykład usługi k8s, takie jak kube-dns i kube-state-metrics i adnotacje zasobu specyficzne dla aplikacji. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus data_collection_settings.cluster]*.
* Cały węzeł — adres URL HTTP i odnajdywanie obiektów docelowych z wymienionych punktów końcowych usługi. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus_data_collection_settings.node]*.

| Endpoint | Zakres | Przykład |
|----------|-------|---------|
| Adnotacja pod | W całym klastrze | Adnotacje: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Usługa Kubernetes | W całym klastrze | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| adres URL/punkt końcowy | Dla węzła i/lub całego klastra | `http://myurl:9101/metrics` |

Po określeniu adresu URL usługa Azure Monitor dla kontenerów tylko zaciera punkt końcowy. Po określeniu usługi Kubernetes nazwa usługi jest rozpoznawana za pomocą serwera DNS klastra w celu uzyskania adresu IP, a następnie usługa rozwiązana jest zeskrobana.

|Zakres | Klucz | Typ danych | Wartość | Opis |
|------|-----|-----------|-------|-------------|
| W całym klastrze | | | | Określ jedną z następujących trzech metod, aby zeskrobać punkty końcowe dla metryk. |
| | `urls` | Ciąg | Tablica oddzielona przecinkami | Punkt końcowy HTTP (określono adres IP lub prawidłową ścieżkę adresu URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE_IP jest określonym parametrem usługi Azure Monitor dla kontenerów i może być używany zamiast adresu IP węzła. Muszą być wszystkie wielkie litery.) |
| | `kubernetes_services` | Ciąg | Tablica oddzielona przecinkami | Tablica usług Kubernetes do zeskrobać metryki z kube-state-metrics. Na przykład`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Wartość logiczna | true lub false | Po ustawieniu `true` w ustawieniach całego klastra usługa Azure Monitor dla kontenerów agent będzie zeskrobać zasobników Kubernetes w całym klastrze dla następujących adnotacji Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Wartość logiczna | true lub false | Umożliwia skrobanie zasobnika. `monitor_kubernetes_pods`musi być `true`ustawiona na . |
| | `prometheus.io/scheme` | Ciąg | http lub https | Domyślnie jest to złomowanie za pośrednictwem protokołu HTTP. W razie potrzeby `https`ustaw na . | 
| | `prometheus.io/path` | Ciąg | Tablica oddzielona przecinkami | Ścieżka zasobu HTTP, na której mają być pobierane metryki. Jeśli ścieżka metryki `/metrics`nie jest , zdefiniuj ją za pomocą tej adnotacji. |
| | `prometheus.io/port` | Ciąg | 9102 | Określ port, z który ma być skrobakowy. Jeśli port nie jest ustawiony, domyślnie będzie to 9102. |
| | `monitor_kubernetes_pods_namespaces` | Ciąg | Tablica oddzielona przecinkami | Lista dozwolonych obszarów nazw do scrape metryki z zasobników Kubernetes.<br> Na przykład: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Cały węzeł | `urls` | Ciąg | Tablica oddzielona przecinkami | Punkt końcowy HTTP (określono adres IP lub prawidłową ścieżkę adresu URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE_IP jest określonym parametrem usługi Azure Monitor dla kontenerów i może być używany zamiast adresu IP węzła. Muszą być wszystkie wielkie litery.) |
| Cały węzeł lub cały klaster | `interval` | Ciąg | 60s | Domyślny interwał zbierania wynosi jedną minutę (60 sekund). Kolekcję można zmodyfikować dla *[prometheus_data_collection_settings.node]* i/lub *[prometheus_data_collection_settings.cluster]* do jednostek czasowych, takich jak s, m, h. |
| Cały węzeł lub cały klaster | `fieldpass`<br> `fielddrop`| Ciąg | Tablica oddzielona przecinkami | Można określić pewne metryki, które mają być zbierane lub`fieldpass`nie z punktu`fielddrop`końcowego, ustawiając listę allow ( ) i nie zezwalaj ( ) na wyświetlanie. Najpierw należy ustawić listę dozwolonych. |

ConfigMaps jest globalną listą i do agenta może być zastosowany tylko jeden ConfigMap. Nie można mieć innego ConfigMaps unieważniania kolekcji.

## <a name="configure-and-deploy-configmaps"></a>Konfigurowanie i wdrażanie map konfiguratywnych

Wykonaj następujące kroki, aby skonfigurować plik konfiguracyjny configmap dla klastrów kubernetes.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) szablon ConfigMap yaml i zapisz go jako container-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Ten krok nie jest wymagany podczas pracy z usługą Azure Red Hat OpenShift, ponieważ szablon ConfigMap już istnieje w klastrze.

2. Edytuj plik ConfigMap yaml z dostosowaniami, aby zeskrobać metryki Prometheus. Jeśli edytujesz plik yaml ConfigMap dla usługi Azure Red `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Hat OpenShift, najpierw uruchom polecenie, aby otworzyć plik w edytorze tekstu.

    >[!NOTE]
    >Następująca adnotacja `openshift.io/reconcile-protect: "true"` musi zostać dodana w obszarze metadanych *container-azm-ms-agentconfig* ConfigMap, aby zapobiec uzgodnieniu. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Aby zebrać usługi Kubernetes usługi całego klastra, należy skonfigurować plik ConfigMap przy użyciu następującego przykładu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Aby skonfigurować skrobanie metryk Prometheus z określonego adresu URL w klastrze, skonfiguruj plik ConfigMap przy użyciu następującego przykładu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Aby skonfigurować skrobanie metryk Prometheus z zestawu demonów agenta dla każdego węzła w klastrze, należy skonfigurować następujące elementy w configmapie:
    
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
        >$NODE_IP jest określonym parametrem usługi Azure Monitor dla kontenerów i może być używany zamiast adresu IP węzła. To musi być wszystkie wielkie litery. 

    - Aby skonfigurować skrobanie metryk Prometheus, określając adnotację zasobnika, wykonaj następujące kroki:

       1. W configmap, określ następujące elementy:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Określ następującą konfigurację adnotacji zasobników:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Jeśli chcesz ograniczyć monitorowanie do określonych obszarów nazw dla zasobników, które mają adnotacje, na `monitor_kubernetes_pod` `true` przykład uwzględnić tylko zasobników przeznaczonych dla obciążeń produkcyjnych, ustaw na w ConfigMap i dodaj filtr `monitor_kubernetes_pods_namespaces` obszaru nazw określający obszary nazw do zeskrobania. Na przykład: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. W przypadku klastrów innych niż Azure Red Hat OpenShift `kubectl apply -f <configmap_yaml_file.yaml>`uruchom następujące polecenie kubectl: .
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    W przypadku usługi Azure Red Hat OpenShift zapisz zmiany w edytorze.

Zmiana konfiguracji może potrwać kilka minut, zanim zacznie obowiązywać, a wszystkie zasobniki omsagent w klastrze zostanie ponownie uruchomiony. Ponowne uruchomienie jest stopniowym restartem dla wszystkich zasobników omsagent, nie wszystkie restartują się w tym samym czasie. Po zakończeniu ponownego uruchamiania wyświetlany jest komunikat podobny do następującego i `configmap "container-azm-ms-agentconfig" created`zawierający wynik: .

Zaktualizowaną mapę konfiguracową dla usługi Azure Red `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`Hat OpenShift można wyświetlić, uruchamiając polecenie . 

## <a name="applying-updated-configmap"></a>Stosowanie zaktualizowanej mapy konfiguracyjnej

Jeśli configMap został już wdrożony w klastrze i chcesz go zaktualizować za pomocą nowszej konfiguracji, możesz edytować wcześniej używany plik ConfigMap, a następnie zastosować je za pomocą tych samych poleceń, co poprzednio.

W przypadku klastrów kubernetes innych niż Azure `kubectl apply -f <configmap_yaml_file.yaml`Red Hat OpenShift uruchom polecenie . 

W przypadku klastra OpenShift usługi `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Azure Red Hat uruchom polecenie, aby otworzyć plik w domyślnym edytorze, aby zmodyfikować, a następnie zapisać go.

Zmiana konfiguracji może potrwać kilka minut, zanim zacznie obowiązywać, a wszystkie zasobniki omsagent w klastrze zostanie ponownie uruchomiony. Ponowne uruchomienie jest stopniowym restartem dla wszystkich zasobników omsagent, nie wszystkie restartują się w tym samym czasie. Po zakończeniu ponownego uruchamiania wyświetlany jest komunikat podobny do następującego i `configmap "container-azm-ms-agentconfig" updated`zawierający wynik: .

## <a name="verify-configuration"></a>Weryfikowanie konfiguracji

Aby sprawdzić, czy konfiguracja została pomyślnie zastosowana do klastra, użyj `kubectl logs omsagent-fdf58 -n=kube-system`następującego polecenia, aby przejrzeć dzienniki z zasobnika agenta: . 

>[!NOTE]
>To polecenie nie ma zastosowania do klastra OpenShift usługi Azure Red Hat.
> 

Jeśli występują błędy konfiguracji z zasobników omsagent, dane wyjściowe będą wyświetlane błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy związane z zastosowaniem zmian konfiguracji są również dostępne do przeglądu. Dostępne są następujące opcje do wykonywania dodatkowych rozwiązywania problemów ze zmianami konfiguracji i skrobania metryk Prometheus:

- Z zasobnika agenta loguje się przy użyciu tego samego `kubectl logs` polecenia 
    >[!NOTE]
    >To polecenie nie ma zastosowania do klastra OpenShift usługi Azure Red Hat.
    > 

- Z danych na żywo (podgląd). Dzienniki live data (wersja zapoznawcza) pokazują błędy podobne do następujących:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Z tabeli **KubeMonAgentEvents** w obszarze roboczym usługi Log Analytics. Dane są wysyłane co godzinę z *ważnością ostrzeżenia* dla błędów zeskrobać i *ważność błędu* dla błędów konfiguracji. Jeśli nie ma żadnych błędów, wpis w tabeli będzie miał dane z *ważnością Info*, który zgłasza żadnych błędów. **Właściwość Tags** zawiera więcej informacji na temat identyfikatora zasobnika i kontenera, na którym wystąpił błąd, a także pierwsze wystąpienie, ostatnie wystąpienie i liczba w ostatniej godzinie.

- W przypadku usługi Azure Red Hat OpenShift sprawdź dzienniki omsagent, przeszukując tabelę **ContainerLog,** aby sprawdzić, czy kolekcja dziennika rejestrowania openshift-azure jest włączona.

Błędy uniemożliwiają omsagentowi analizowanie pliku, powodując jego ponowne uruchomienie i użycie domyślnej konfiguracji. Po skorygowaniu błędów w configmap w klastrach innych niż Azure Red Hat OpenShift zapisz plik yaml i `kubectl apply -f <configmap_yaml_file.yaml`zastosuj zaktualizowane ConfigMaps, uruchamiając polecenie: . 

W przypadku narzędzia Azure Red Hat OpenShift edytuj i zapisz `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`zaktualizowane mapy configmaps, uruchamiając polecenie: .

## <a name="query-prometheus-metrics-data"></a>Zapytanie Prometheus dane metryki

Aby wyświetlić metryki prometheus zeskrobane przez usługę Azure Monitor i wszelkie błędy konfiguracji/skrobania zgłoszone przez agenta, przejrzyj [dane metryk Query Prometheus](container-insights-log-search.md#query-prometheus-metrics-data) i [błędy konfiguracji kwerendy lub skrobania](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Zobacz metryki Prometeusza w Grafana

Usługa Azure Monitor dla kontenerów obsługuje wyświetlanie metryk przechowywanych w obszarze roboczym usługi Log Analytics na pulpitach nawigacyjnych usługi Grafana. Udostępniliśmy szablon, który można pobrać z [repozytorium pulpitu nawigacyjnego grafany,](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) aby rozpocząć pracę, i odwołać się do informacji, jak wysyłać zapytania o dodatkowe dane z monitorowanych klastrów w celu wizualizacji w niestandardowych pulpitach nawigacyjnych Grafana. 

## <a name="review-prometheus-data-usage"></a>Przejrzyj użycie danych Prometheus

Aby zidentyfikować wielkość pozyskiwania każdego rozmiaru metryki w GB dziennie, aby zrozumieć, czy jest wysoki, podana jest następująca kwerenda.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
Dane wyjściowe pokażą wyniki podobne do następujących:

![Rejestrowanie wyników kwerendy woluminu pozyskiwania danych](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Aby oszacować, jaki rozmiar poszczególnych metryk w GB jest dla miesiąca, aby zrozumieć, jeśli ilość danych pozyskanych odebranych w obszarze roboczym jest wysoka, podana jest następująca kwerenda.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Dane wyjściowe pokażą wyniki podobne do następujących:

![Rejestrowanie wyników kwerendy woluminu pozyskiwania danych](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Więcej informacji na temat monitorowania wykorzystania danych i analizowania kosztów jest dostępna w [obszarze Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o konfigurowaniu ustawień kolekcji agentów dla zmiennych stdout, stderr i środowiskowych z obciążeń kontenerów [tutaj](container-insights-agent-config.md). 
