---
title: Zasady sieciowe usługi Azure Kubernetes | Microsoft Docs
description: Dowiedz się więcej na temat zasad sieciowych Kubernetes, aby zabezpieczyć klaster Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a68e1a3f60930e290e97084ff2ec9350b18e2873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594977"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Omówienie zasad sieciowych platformy Azure Kubernetes

Zasady sieciowe zapewniają mikrosegmenty dla zasobników, tak jak sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń), zapewniają mikrosegmentowanie dla maszyn wirtualnych. Implementacja Menedżera zasad sieciowych platformy Azure (znana także jako Azure NPM) obsługuje standardową specyfikację zasad sieci Kubernetes. Możesz użyć etykiet, aby wybrać grupę i zdefiniować listę reguł ruchu przychodzącego i wychodzącego, aby filtrować ruch do i z tych zasobników. Dowiedz się więcej na temat zasad sieciowych Kubernetes w [dokumentacji Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Omówienie zasad sieciowych Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Implementacja usługi Azure NPM działa w połączeniu z usługą Azure CNI, która zapewnia integrację sieci wirtualnych dla kontenerów. NPM jest obecnie obsługiwana tylko w systemie Linux. Implementacja wymusza Filtrowanie ruchu przez skonfigurowanie reguł Zezwalaj i Odmów adresów IP w systemie Linux dołączenie iptables na podstawie zdefiniowanych zasad. Te reguły są grupowane przy użyciu IPSets systemu Linux.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planowanie zabezpieczeń dla klastra Kubernetes
W przypadku implementowania zabezpieczeń klastra należy użyć grup zabezpieczeń sieci (sieciowych grup zabezpieczeń) do filtrowania ruchu wprowadzonego i wychodzącego z podsieci klastra (ruch północ-południe). Użyj usługi Azure NPM do ruchu między zasobnikami w klastrze (ruch wschodni wschód).

## <a name="using-azure-npm"></a>Korzystanie z usługi Azure NPM
Usługi Azure NPM można użyć w następujący sposób, aby zapewnić mikrosegmenty dla zasobników.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
NPM jest dostępna natywnie w AKS i można ją włączyć w momencie tworzenia klastra. Dowiedz się więcej na temat [zabezpieczania ruchu między różnymi sieciami przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)](../aks/use-network-policies.md).

### <a name="aks-engine"></a>AKS — silnik
AKS-Engine to narzędzie, które generuje szablon Azure Resource Manager na potrzeby wdrożenia klastra Kubernetes na platformie Azure. Konfiguracja klastra jest określona w pliku JSON, który jest przekazywany do narzędzia podczas generowania szablonu. Aby uzyskać więcej informacji na temat wszystkich obsługiwanych ustawień klastra, zobacz Microsoft Azure Container Service Engine — Cluster Definition (Usługa Microsoft Azure Container Service Engine — definicja klastra).

Aby włączyć zasady dla klastrów wdrożonych przy użyciu aparatu ACS, należy określić wartość ustawienia networkPolicy w pliku definicji klastra na "Azure".

#### <a name="example-configuration"></a>Przykładowa konfiguracja

Poniższy Przykładowa konfiguracja JSON tworzy nową sieć wirtualną i podsieć, a następnie wdraża w niej klaster Kubernetes przy użyciu usługi Azure CNI. Zalecamy, aby edytować plik JSON przy użyciu "Notatnika". 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Zrób to samodzielnie (możesz) Kubernetes klastrów na platformie Azure
 W przypadku klastrów możesz najpierw zainstaluj wtyczkę CNI i włącz ją na każdej maszynie wirtualnej w klastrze. Aby uzyskać szczegółowe instrukcje, zobacz [Wdrażanie wtyczki dla samodzielnie wdrażanego klastra Kubernetes](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Po wdrożeniu klastra uruchom następujące polecenie, `kubectl` Aby pobrać i zastosować *zestaw* Azure npm DAEMON do klastra.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Rozwiązanie to również Open Source, a kod jest dostępny w [repozytorium sieci kontenera platformy Azure](https://github.com/Azure/azure-container-networking/tree/master/npm).

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Monitorowanie i wizualizacja konfiguracji sieci za pomocą usługi Azure NPM
Usługa Azure NPM zawiera Prometheuse metryki, które umożliwiają monitorowanie i lepsze zrozumienie konfiguracji. Udostępnia wbudowane wizualizacje w Azure Portal lub Grafana Labs. Możesz rozpocząć zbieranie tych metryk przy użyciu Azure Monitor lub serwera Prometheus.

### <a name="benefits-of-azure-npm-metrics"></a>Zalety metryk usługi Azure NPM
Wcześniej użytkownicy mogli dowiedzieć się więcej o konfiguracji sieci za pomocą polecenia w `iptables -L` węźle klastra, co daje pełne i trudne zrozumienie danych wyjściowych. Metryki NPM zapewniają następujące korzyści związane z zasadami sieci, regułami dołączenie iptables i IPSets.
- Zapewnia wgląd w relacje między trzema a wymiarem czasu, aby debugować konfigurację.
- Liczba wpisów we wszystkich IPSets i każdy IPSet.
- Czas potrzebny na zastosowanie zasad z dokładnością na poziomie IPTable/IPSet.
 
### <a name="supported-metrics"></a>Obsługiwane metryki
Poniżej znajduje się lista obsługiwanych metryk:

|Nazwa metryki |Opis  |Typ metryki Prometheus  |Etykiety  |
|---------|---------|---------|---------|
|`npm_num_policies`     |Liczba zasad sieciowych          |Miernik         |-         |
|`npm_num_iptables_rules`     | liczba reguł dołączenie iptables     | Miernik        |-         |         
|`npm_num_ipsets`     |Liczba IPSets         |Miernik            |-         |
|`npm_num_ipset_entries`     |Liczba wpisów adresów IP we wszystkich IPSets         |Miernik         |-         |
|`npm_add_policy_exec_time`     |środowisko uruchomieniowe do dodawania zasad sieciowych         |Podsumowanie         |quantile (0,5, 0,9 lub 0,99)         |
|`npm_add_iptables_rule_exec_time`     |środowisko uruchomieniowe do dodawania reguły dołączenie iptables         |Podsumowanie         |quantile (0,5, 0,9 lub 0,99)         |
|`npm_add_ipset_exec_time`     |środowisko uruchomieniowe do dodawania elementu IPSet         |Podsumowanie         |quantile (0,5, 0,9 lub 0,99)         |
|`npm_ipset_counts` doświadczonych     |Liczba wpisów w ramach poszczególnych IPSet         |GaugeVec         |Ustaw nazwę & skrótu         |

Różne poziomy quantile w metrykach "exec_time" ułatwiają odróżnienie między ogólnymi i najgorszymi scenariuszami przypadku.

Istnieje również Metryka "exec_time_count" i "exec_time_sum" dla każdej metryki podsumowania "exec_time".

Metryki mogą być odłączane za pomocą Azure Monitor dla kontenerów lub za pomocą Prometheus.

### <a name="setup-for-azure-monitor"></a>Instalator dla Azure Monitor
Pierwszym krokiem jest włączenie Azure Monitor dla kontenerów dla klastra Kubernetes. Kroki można znaleźć w temacie [Azure monitor for Containers — Omówienie](../azure-monitor/containers/container-insights-overview.md). Po włączeniu Azure Monitor dla kontenerów Skonfiguruj [Azure monitor dla kontenerów ConfigMap](https://aka.ms/container-azm-ms-agentconfig) , aby umożliwić integrację npm i zbieranie metryk Prometheus npm. Usługa Azure monitor dla kontenerów ConfigMap zawiera ```integrations``` sekcję z ustawieniami umożliwiającą zbieranie metryk npm. Te ustawienia są domyślnie wyłączone w ConfigMap. Włączenie ustawienia podstawowe ```collect_basic_metrics = true``` spowoduje zebranie podstawowych metryk npm. Włączenie ustawienia zaawansowanego ```collect_advanced_metrics = true``` spowoduje zebranie zaawansowanych metryk oprócz metryk podstawowych. 

Po edycji ConfigMap Zapisz ją lokalnie i Zastosuj ConfigMap do klastra w następujący sposób.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Poniżej znajduje się fragment kodu z [usługi Azure monitor dla kontenerów ConfigMap](https://aka.ms/container-azm-ms-agentconfig), który pokazuje integrację npm z zaawansowaną kolekcją metryk.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Metryki zaawansowane są opcjonalne, a włączenie tych funkcji spowoduje automatyczne włączenie kolekcji metryk podstawowych. Zaawansowane metryki obecnie obejmują tylko `npm_ipset_counts`

Dowiedz się więcej [na temat ustawień kolekcji kontenerów dla usługi Azure monitor na mapie konfiguracji](../azure-monitor/containers/container-insights-agent-config.md)

### <a name="visualization-options-for-azure-monitor"></a>Opcje wizualizacji dla Azure Monitor
Po włączeniu zbierania metryk NPM można wyświetlić metryki w Azure Portal przy użyciu usługi Container Insights lub Grafana.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Wyświetlanie w Azure Portal w usłudze Insights dla klastra
Otwórz witrynę Azure Portal. Po uzyskaniu wglądu w dane klastra przejdź do "skoroszytów" i Otwórz pozycję Konfiguracja "Menedżer zasad sieciowych (NPM)".

Oprócz wyświetlania skoroszytu (obrazy poniżej) można również bezpośrednio wysyłać zapytania o metryki Prometheus w sekcji "Logs". Na przykład to zapytanie zwróci wszystkie zbierane metryki.
| gdzie TimeGenerated > temu (5h) | gdzie nazwa zawiera "npm_"

Możesz również badać Log Analytics bezpośrednio dla metryk. Dowiedz się więcej na temat [wprowadzenie z Zapytaniami log Analytics](../azure-monitor/containers/container-insights-log-search.md) 

#### <a name="viewing-in-grafana-dashboard"></a>Wyświetlanie na pulpicie nawigacyjnym Grafana
Skonfiguruj serwer Grafana i Skonfiguruj źródło danych Log Analytics zgodnie z opisem w [tym miejscu](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource). Następnie zaimportuj [pulpit nawigacyjny Grafana z zapleczem log Analytics](https://grafana.com/grafana/dashboards/10956) do programu Grafana Labs.

Pulpit nawigacyjny zawiera wizualizacje podobne do skoroszytu platformy Azure. Możesz dodać panele do wykresu & wizualizacje metryk NPM z tabeli InsightsMetrics.

### <a name="setup-for-prometheus-server"></a>Konfiguracja serwera Prometheus
Niektórzy użytkownicy mogą zdecydować się na zbieranie metryk z serwerem Prometheus, a nie Azure Monitor dla kontenerów. Wystarczy dodać dwa zadania do konfiguracji wycinków, aby zebrać metryki NPM.

Aby zainstalować prosty serwer Prometheus, Dodaj to repozytorium Helm do klastra
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
następnie Dodaj serwer
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
gdzie `prometheus-server-scrape-config.yaml` składa się z
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


Możesz również zastąpić `azure-npm-node-metrics` zadanie poniższą zawartością lub uwzględnić je w istniejącym zadaniu dla Kubernetes:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Opcje wizualizacji dla Prometheus
W przypadku korzystania z serwera Prometheus jest obsługiwany tylko pulpit nawigacyjny Grafana. 

Jeśli jeszcze tego nie zrobiono, skonfiguruj serwer Grafana i Skonfiguruj źródło danych Prometheus. Następnie zaimportuj nasz [pulpit nawigacyjny Grafana z zapleczem Prometheus](https://grafana.com/grafana/dashboards/13000) do laboratorium Grafana Labs.

Wizualizacje dla tego pulpitu nawigacyjnego są identyczne z pulpitem nawigacyjnym z zaplecem usługi Container Insights/Log Analytics.

### <a name="sample-dashboards"></a>Przykładowe pulpity nawigacyjne
Poniżej przedstawiono przykładowy pulpit nawigacyjny dla metryk NPM w usłudze Container Insights (CI) i Grafana

#### <a name="ci-summary-counts"></a>Liczba podsumowań elementów konfiguracji
![Liczba podsumowań skoroszytów platformy Azure](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>Liczba CI w czasie
[![Liczba skoroszytów platformy Azure z upływem czasu](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>Wpisy IPSet CI
[![Wpisy IPSet w skoroszycie platformy Azure](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>Quantiles środowiska uruchomieniowego elementu konfiguracji
![Quantiles środowiska uruchomieniowego skoroszytu platformy Azure](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Liczba podsumowań pulpitu nawigacyjnego Grafana
![Liczba podsumowań pulpitu nawigacyjnego Grafana](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Liczba pulpitów nawigacyjnych Grafana z upływem czasu
[![Liczba pulpitów nawigacyjnych Grafana z upływem czasu](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Wpisy IPSet pulpitu nawigacyjnego Grafana
[![Wpisy IPSet pulpitu nawigacyjnego Grafana](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana środowiska uruchomieniowego pulpitu nawigacyjnego quantiles
[![Grafana środowiska uruchomieniowego pulpitu nawigacyjnego quantiles](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Dowiedz się więcej na temat [sieci kontenerów](container-networking-overview.md).
- [Wdróż wtyczkę](deploy-container-networking.md) dla klastrów Kubernetes lub kontenerów platformy Docker.

