---
title: Samouczek — monitorowanie Apache Spark metryk na poziomie aplikacji za pomocą Prometheus i Grafana
description: Samouczek — informacje dotyczące wdrażania rozwiązania metryk aplikacji Apache Spark w klastrze usługi Azure Kubernetes Service (AKS) i Dowiedz się, jak zintegrować pulpity nawigacyjne Grafana.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 7a95b32449da6dead1c35241ff09af127e0fae43
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210188"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Samouczek: monitorowanie metryk Apache Spark na poziomie aplikacji za pomocą Prometheus i Grafana

## <a name="overview"></a>Omówienie

W tym samouczku dowiesz się, jak wdrożyć rozwiązanie Apache Spark metryki aplikacji w klastrze usługi Azure Kubernetes Service (AKS) i dowiedzieć się, jak zintegrować pulpity nawigacyjne Grafana.

To rozwiązanie służy do zbierania i wykonywania zapytań dotyczących Apache Spark danych metryk niemal w czasie rzeczywistym. Zintegrowane pulpity nawigacyjne Grafana umożliwiają diagnozowanie i monitorowanie aplikacji Apache Spark. Kod źródłowy i konfiguracje zostały otwarte w serwisie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

1.  [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
2.  [Helm Client 3.30 +](https://github.com/helm/helm/releases)
3.  [polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

Można też użyć [Azure Cloud Shell](https://shell.azure.com/), która zawiera już interfejs wiersza polecenia platformy Azure, Helm Client i polecenia kubectl z usługi Box.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Tworzenie wystąpienia usługi Azure Kubernetes Service (AKS)

Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć klaster Kubernetes w ramach subskrypcji.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Uwaga: ten krok można pominąć, jeśli masz już klaster AKS.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Tworzenie nazwy głównej usługi i przyznawanie uprawnień do obszaru roboczego Synapse

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Wynik powinien wyglądać następująco:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Zanotuj identyfikatory appId, Password i tenantID.

[![zrzut ekranu Przydziel uprawnienia srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Zaloguj się do [obszaru roboczego usługi Azure Synapse Analytics](https://web.azuresynapse.net/) jako administrator Synapse

2. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **zarządzaj > kontroli dostępu**

3. Kliknij przycisk Dodaj w lewym górnym rogu, aby **dodać przypisanie roli**

4. W obszarze Zakres wybierz pozycję **obszar roboczy** .

5. W obszarze Rola wybierz pozycję **Synapse obliczeniowy operator**

6. W obszarze Wybierz użytkownika wprowadź **<service_principal_name>** a następnie kliknij nazwę główną usługi

7. Kliknij przycisk **Zastosuj** (odczekaj 3 minuty, aby można było zastosować odpowiednie działania).

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Zainstaluj łącznik, serwer Prometheus, pulpit nawigacyjny Grafana

1. Dodaj repozytorium Synapse-Charts do klienta Helm.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Zainstaluj składniki za poorednictwem klienta Helm:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: Nazwa obszaru roboczego Synapse.
 - subscription_id: Identyfikator subskrypcji obszaru roboczego Synapse.
 - workspace_resource_group_name: Nazwa grupy zasobów obszaru roboczego Synapse.
 - tenant_id: Identyfikator dzierżawy obszaru roboczego Synapse.
 - service_principal_app_id: Nazwa główna usługi "appId"
 - service_principal_password: utworzone hasło nazwy głównej usługi.

## <a name="log-in-to-grafana"></a>Logowanie do Grafana

Pobierz domyślne hasło i adres Grafana. Hasło można zmienić w ustawieniach Grafana.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Pobierz adres IP usługi, skopiuj & wklej zewnętrzny adres IP do przeglądarki, a następnie zaloguj się przy użyciu nazwy użytkownika "admin" i hasła.

## <a name="use-grafana-dashboards"></a>Korzystanie z pulpitów nawigacyjnych Grafana

Znajdź pulpit nawigacyjny Synapse w lewym górnym rogu strony Grafana (Home-> Synapse Workspace/Synapse), spróbuj uruchomić przykładowy kod w programie Synapse Studio i zaczekaj kilka sekund na ściąganie metryk.

Ponadto można użyć pulpitów nawigacyjnych "Synapse obszar roboczy/obszar roboczy" i "Synapsee pule obszarów roboczych/platformy Spark", aby zapoznać się z obszarem roboczym i pulami Apache Spark.

## <a name="uninstall"></a>Odinstalowanie

Usuń składniki przez Helm polecenie w następujący sposób.

```bash
helm delete <release_name> -n <namespace>
```

Usuń klaster AKS.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Wprowadzenie do składników

Usługa Azure Synapse Analytics udostępnia [Wykres Helm](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) na podstawie operatora Prometheus i łącznika Synapse Prometheus. Wykres Helm zawiera pulpity nawigacyjne Prometheus Server, Grafana Server i Grafana dla Apache Spark metryk na poziomie aplikacji. Możesz użyć [Prometheus](https://prometheus.io/), popularnego systemu monitorowania typu open source, aby zebrać te metryki niemal w czasie rzeczywistym i używać [Grafana](https://github.com/grafana/grafana) do wizualizacji.

### <a name="synapse-prometheus-connector"></a>Łącznik Synapse Prometheus

Łącznik Synapse Prometheus ułatwia połączenie puli usługi Azure Synapse Apache Spark i serwera Prometheus. Implementuje:

1.  Uwierzytelnianie: usługa jest uwierzytelnianiem w usłudze AAD i umożliwia automatyczne odświeżanie tokenu usługi AAD podmiotu zabezpieczeń na potrzeby odnajdywania aplikacji, pozyskiwania metryk i innych funkcji.
2.  Apache Spark odnajdywania aplikacji: podczas przesyłania aplikacji w docelowym obszarze roboczym łącznik programu Synapse Prometheus może automatycznie odnajdywać te aplikacje.
3.  Apache Spark metadanych aplikacji: zbiera podstawowe informacje o aplikacji i eksportuje je do Prometheus.

Łącznik Synapse Prometheus jest wydawany jako obraz platformy Docker hostowany w [witrynie Microsoft Container Registry](https://github.com/microsoft/containerregistry). Jest to element Open Source, który znajduje się w [metrykach aplikacji platformy Azure Synapse Spark](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Serwer Prometheus

Prometheus to zestaw narzędzi do monitorowania i generowania alertów Open Source. Prometheus z CNCF (Cloud Native Computing Foundation) i stał się de facto standardem dla monitorowania natywnego w chmurze. Prometheus może pomóc nam zbierać, wysyłać zapytania i przechowywać duże ilości danych szeregów czasowych, a także łatwo ją zintegrować z Grafana. W tym rozwiązaniu wdrażamy składnik Prometheus na podstawie wykresu Helm.

### <a name="grafana-and-dashboards"></a>Grafana i pulpity nawigacyjne

Grafana to oprogramowanie do wizualizacji i analizy Open Source. Pozwala to na wykonywanie zapytań, wizualizowanie, tworzenie alertów i eksplorowanie metryk. Usługa Azure Synapse Analytics udostępnia zestaw domyślnych pulpitów nawigacyjnych Grafana do wizualizacji Apache Spark metryki na poziomie aplikacji.

Pulpit nawigacyjny "Synapse Workspace/obszar roboczy" zawiera widok poziomu obszaru roboczego dla wszystkich pul Apache Spark, liczby aplikacji, rdzeni procesora CPU itd.

[![obszar roboczy pulpitu nawigacyjnego zrzutu ekranu](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

Pulpit nawigacyjny "Synapse obszar roboczy/platforma Spark" zawiera metryki Apache Spark aplikacji uruchomionych w wybranej puli Apache Spark w danym okresie czasu.

[![Pulpit nawigacyjny zrzutu ekranu sparkpool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

Pulpit nawigacyjny "Synapse Workspace/aplikacja platformy Spark" zawiera wybraną aplikację Apache Spark.

[![aplikacja pulpitu nawigacyjnego zrzutu ekranu](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Powyższe szablony pulpitu nawigacyjnego zostały otwarte jako źródła w [metrykach aplikacji platformy Azure Synapse Spark](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).
