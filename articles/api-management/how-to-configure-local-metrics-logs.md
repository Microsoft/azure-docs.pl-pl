---
title: Konfigurowanie lokalnych metryk i dzienników dla platformy Azure API Management Brama samodzielna hostowana | Microsoft Docs
description: Dowiedz się, jak skonfigurować lokalne metryki i dzienniki dla platformy Azure API Management samohostowanej bramy na Kubernetes Custer
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: e34c25b2e3bfa845e258dc5d9699497d7ffcb004
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526674"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Konfigurowanie lokalnych metryk i dzienników dla usługi Azure API Management Brama samoobsługowa

Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania metryk i dzienników lokalnych dla [bramy samohostowanej](./self-hosted-gateway-overview.md) wdrożonej w klastrze Kubernetes. Aby skonfigurować metryki i dzienniki w chmurze, zobacz [ten artykuł](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Metryki
Brama samoobsługowa obsługuje [statystyki](https://github.com/statsd/statsd), która staje się protokołem ujednolicania dla kolekcji metryk i agregacji. W tej sekcji omówiono procedurę wdrażania statystyki do Kubernetes, konfigurowania bramy w celu emitowania metryk przy użyciu statystyk i używania [Prometheus](https://prometheus.io/) do monitorowania metryk. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Wdróż statystyki i Prometheus w klastrze

Poniżej znajduje się Przykładowa konfiguracja YAML do wdrażania statystyk i Prometheus w klastrze Kubernetes, w którym wdrożono bramę samohostowaną. Tworzy również [usługę](https://kubernetes.io/docs/concepts/services-networking/service/) dla każdej z nich. Brama samoobsługowa będzie publikować metryki dla usługi statystycznej. Dostęp do pulpitu nawigacyjnego Prometheus zostanie nadany za pośrednictwem usługi.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Zapisz konfiguracje w pliku o nazwie `metrics.yaml` i użyj poniższego polecenia, aby wdrożyć wszystkie elementy w klastrze:

```console
kubectl apply -f metrics.yaml
```

Po zakończeniu wdrożenia uruchom poniższe polecenie, aby sprawdzić, czy są uruchomione. Należy pamiętać, że nazwa użytkownika jest inna. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Uruchom poniższe polecenie, aby sprawdzić, czy usługi są uruchomione. Zanotuj `CLUSTER-IP` usługę i usługi z `PORT` statystyką, która będzie potrzebna później. Pulpit nawigacyjny Prometheus można odwiedzić przy użyciu funkcji `EXTERNAL-IP` i `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Konfigurowanie bramy samoobsługowej do emisji metryk

Teraz, gdy wdrożono zarówno statystyki, jak i Prometheus, możemy zaktualizować konfiguracje bramy samohostowanej, aby rozpocząć emitowanie metryk przy użyciu statystyk. Funkcję można włączać lub wyłączać przy użyciu `telemetry.metrics.local` klucza w ConfigMap wdrożenia bramy samohostowanej z dodatkowymi opcjami. Poniżej znajduje się podział dostępnych opcji:

| Pole  | Domyślne | Opis |
| ------------- | ------------- | ------------- |
| Telemetria. Metrics. Local  | `none` | Włącza rejestrowanie z uwzględnieniem statystyk. Wartość może być `none` , `statsd` . |
| Telemetria. Metrics. local. re\fieldd. Endpoint  | n/d | Określa punkt końcowy z statystyką. |
| Telemetria. Metrics. local. re\fieldd. — próbkowanie  | n/d | Określa częstotliwość próbkowania metryk. Wartość może zawierać się w przedziale od 0 do 1. np., `0.5`|
| dane telemetryczne. Metrics. local. invisiond. tag-format  | n/d | [Format tagowania](https://github.com/prometheus/statsd_exporter#tagging-extensions)eksportu statystycznego. Wartość może być `none` , `librato` , `dogStatsD` , `influxDB` . |

Oto Przykładowa konfiguracja:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Zaktualizuj plik YAML wdrożenia bramy samodzielnej z powyższymi konfiguracjami i Zastosuj zmiany przy użyciu poniższego polecenia: 

```console
kubectl apply -f <file-name>.yaml
 ```

Aby pobrać najnowsze zmiany konfiguracji, uruchom ponownie wdrożenie bramy przy użyciu poniższego polecenia:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Wyświetl metryki

Teraz wszystko zostało wdrożone i skonfigurowane, Brama samoobsługowa powinna raportować metryki za pomocą statystyk. Prometheus pobierze metryki z statystyki. Przejdź do pulpitu nawigacyjnego Prometheus za pomocą `EXTERNAL-IP` i `PORT` usługi Prometheus. 

Wykonaj pewne wywołania interfejsu API za pomocą bramy samohostowanej, jeśli wszystko jest prawidłowo skonfigurowane, powinno być możliwe wyświetlenie poniższych metryk:

| Metric  | Opis |
| ------------- | ------------- |
| Żądania  | Liczba żądań interfejsu API w danym okresie |
| DurationInMS | Liczba milisekund od momentu odebrania żądania w bramie do momentu pełnego wysłania odpowiedzi |
| BackendDurationInMS | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy zaplecza (łączenie, wysyłanie i odbieranie bajtów)  |
| ClientDurationInMS | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy klienta (łączenie, wysyłanie i odbieranie bajtów)  |

## <a name="logs"></a>Dzienniki

Brama samoobsługowa domyślnie wyprowadza dzienniki do `stdout` i `stderr` . Dzienniki można łatwo wyświetlić przy użyciu następującego polecenia:

```console
kubectl logs <pod-name>
```

Jeśli Brama własna jest wdrożona w usłudze Azure Kubernetes, można włączyć [Azure monitor dla kontenerów](../azure-monitor/insights/container-insights-overview.md) do zbierania `stdout` i pobierania `stderr` obciążeń oraz wyświetlać dzienniki w log Analytics. 

Brama samoobsługowa obsługuje również wiele protokołów `localsyslog` , w tym, `rfc5424` i `journal` . Poniższa tabela podsumowuje wszystkie obsługiwane opcje. 

| Pole  | Domyślne | Opis |
| ------------- | ------------- | ------------- |
| Telemetria. logs. std  | `text` | Włącza rejestrowanie do strumieni standardowych. Wartość może być `none` , `text` , `json` |
| dane telemetryczne. logs. Local  | `none` | Włącza rejestrowanie lokalne. Wartość może być `none` , `auto` , `localsyslog` , `rfc5424` , `journal`  |
| Telemetria. logs. local. localsyslog. Endpoint  | n/d | Określa punkt końcowy localsyslog.  |
| Telemetria. logs. local. localsyslog.  | n/d | Określa [kod funkcji](https://en.wikipedia.org/wiki/Syslog#Facility)localsyslog. np., `7` 
| Telemetria. logs. local. RFC5424. Endpoint  | n/d | Określa punkt końcowy RFC5424.  |
| Telemetria. logs. local. RFC5424.  | n/d | Określa kod instrumentu na [RFC5424](https://tools.ietf.org/html/rfc5424). np., `7`  |
| Telemetria. logs. local. Journal. Endpoint  | n/d | Określa punkt końcowy dziennika.  |

Poniżej przedstawiono przykładową konfigurację rejestrowania lokalnego:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz temat [usługa Azure API Management](self-hosted-gateway-overview.md) samodzielna Brama — Omówienie
* Dowiedz się więcej o [konfigurowaniu i utrwalaniu dzienników w chmurze](how-to-configure-local-metrics-logs.md)
