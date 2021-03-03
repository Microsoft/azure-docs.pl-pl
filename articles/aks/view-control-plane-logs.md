---
title: Wyświetlanie dzienników kontrolera usługi Azure Kubernetes Service (AKS)
description: Informacje na temat włączania i wyświetlania dzienników płaszczyzny kontroli Kubernetes w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 4027b2ca66b4d4319f7df347df6d671e6d48b772
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735135"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Włączanie i przeglądanie dzienników płaszczyzny kontroli Kubernetes w usłudze Azure Kubernetes Service (AKS)

W przypadku usługi Azure Kubernetes Service (AKS) składniki płaszczyzny kontroli, takie jak *polecenia-apiserver* i *polecenia-Manager* , są udostępniane jako usługa zarządzana. Można tworzyć węzły, które uruchamiają *kubelet* i środowisko uruchomieniowe kontenera, oraz zarządzać nimi, a następnie wdrażać aplikacje za pomocą zarządzanego serwera interfejsu API Kubernetes. Aby pomóc w rozwiązywaniu problemów dotyczących aplikacji i usług, może być konieczne wyświetlenie dzienników generowanych przez te składniki płaszczyzny kontroli. W tym artykule pokazano, jak używać dzienników Azure Monitor do włączania i wykonywania zapytań dotyczących dzienników ze składników płaszczyzny kontroli Kubernetes.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga istniejącego klastra AKS uruchomionego na Twoim koncie platformy Azure. Jeśli nie masz jeszcze klastra AKS, utwórz go przy użyciu [interfejsu wiersza polecenia platformy Azure][cli-quickstart] lub [Azure Portal][portal-quickstart]. Dzienniki Azure Monitor działają zarówno w przypadku Kubernetes RBAC, jak i kontroli RBAC platformy Azure, a nie z obsługą kontroli RBAC.

## <a name="enable-resource-logs"></a>Włączanie dzienników zasobów

Aby ułatwić zbieranie i przeglądanie danych z wielu źródeł, dzienniki Azure Monitor udostępniają język zapytań i aparat analityczny, który zapewnia wgląd w dane środowisko. Obszar roboczy służy do sortowania i analizowania danych oraz integracji z innymi usługami platformy Azure, takimi jak Application Insights i Security Center. Aby użyć innej platformy do analizowania dzienników, możesz zamiast tego wybrać opcję wysyłania dzienników zasobów do konta usługi Azure Storage lub centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [co to jest Azure monitor Logs?][log-analytics-overview].

Dzienniki Azure Monitor są włączone i zarządzane w Azure Portal. Aby włączyć zbieranie dzienników dla składników płaszczyzny kontroli Kubernetes w klastrze AKS, Otwórz Azure Portal w przeglądarce internetowej i wykonaj następujące czynności:

1. Wybierz grupę zasobów dla klastra AKS, na przykład grupa *zasobów*. Nie wybieraj grupy zasobów zawierającej poszczególne zasoby klastra AKS, takie jak *MC_myResourceGroup_myAKSCluster_eastus*.

2. Po lewej stronie wybierz pozycję **Ustawienia diagnostyczne**.

3. Wybierz klaster AKS, taki jak *myAKSCluster*, a następnie wybierz opcję **dodania ustawienia diagnostycznego**.
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="Zrzut ekranu przedstawiający Azure Portal w oknie przeglądarki z ustawieniami diagnostycznymi, które wskazują, że należy wybrać opcję &quot;Dodaj ustawienie diagnostyczne&quot;":::

4. Wprowadź nazwę, na przykład *myAKSClusterLogs*, a następnie wybierz opcję **wysłania do obszaru roboczego log Analytics**.

5. Wybierz istniejący obszar roboczy lub Utwórz nowy. W przypadku tworzenia obszaru roboczego Podaj nazwę obszaru roboczego, grupę zasobów i lokalizację.

6. Na liście dostępnych dzienników wybierz dzienniki, które chcesz włączyć. W tym przykładzie należy włączyć dzienniki *polecenia-Audit* i *polecenia-Audit-admin* . Typowe dzienniki obejmują *polecenia-apiserver*, *polecenia-Controller-Manager* i *polecenia-Scheduler*. Można zwrócić i zmienić zebrane dzienniki po włączeniu Log Analytics obszarów roboczych.

7. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz** , aby włączyć zbieranie wybranych dzienników.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Zrzut ekranu przedstawiający ekran &quot;Dodawanie ustawień diagnostycznych&quot; Azure Portal. Wybrano element docelowy elementu &quot;Send to Log Analytics Workspace&quot; i dzienniki &quot;polecenia-Audit&quot; i &quot;polecenia-Audit-admin&quot;":::

## <a name="log-categories"></a>Kategorie dzienników

Oprócz wpisów utworzonych przez Kubernetes, dzienniki inspekcji projektu również mają wpisy z AKS.

Dzienniki inspekcji są zapisywane w trzech kategoriach: *polecenia-Audit*, *polecenia-Audit-admin* i *Guard*.

- Kategoria *polecenia-Audit* zawiera wszystkie dane dziennika inspekcji dla każdego zdarzenia inspekcji, w tym *Get*, *list*, *Create*, *Update*, *delete*, *patch* i *post*.
- Kategoria *polecenia-Audit-admin* jest podzbiorem kategorii dziennika *inspekcji polecenia* . *polecenia-Audit — administrator* znacznie zmniejsza liczbę dzienników, wykluczając zdarzenia *pobierania* *i inspekcji* z dziennika.
- Kategoria *Guard* jest zarządzana przez usługi Azure AD i inspekcje RBAC platformy Azure. W przypadku zarządzanej usługi Azure AD: token w programie, informacje o użytkowniku. W przypadku usługi Azure RBAC: przeglądy dostępu w i na zewnątrz.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Zaplanuj test pod względem klastra AKS

Aby wygenerować niektóre dzienniki, Utwórz nowy element pod w klastrze AKS. Poniższy przykład manifestu YAML może służyć do tworzenia podstawowego wystąpienia NGINX. Utwórz plik o nazwie `nginx.yaml` w wybranym edytorze i wklej następującą zawartość:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Utwórz pod za pomocą polecenia [polecenia kubectl Create][kubectl-create] i określ plik YAML, jak pokazano w następującym przykładzie:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Wyświetlanie zebranych dzienników

Włączenie i wyświetlenie dzienników diagnostycznych może potrwać do 10 minut.

> [!NOTE]
> Jeśli potrzebujesz wszystkich danych dziennika inspekcji pod kątem zgodności lub innych celów, Zbierz i Zapisz je w niedrogim magazynie, takim jak BLOB Storage. Kategoria dziennika *polecenia-Audit-admin* służy do zbierania i zapisywania zrozumiałego zestawu danych dziennika inspekcji na potrzeby monitorowania i wysyłania alertów.

W Azure Portal przejdź do klastra AKS, a następnie wybierz pozycję **dzienniki** po lewej stronie. Zamknij okno *przykładowe zapytania* , jeśli pojawia się.

Po lewej stronie wybierz pozycję **dzienniki**. Aby wyświetlić dzienniki *inspekcji polecenia* , w polu tekstowym wprowadź następujące zapytanie:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Jest możliwe zwrócenie wielu dzienników. Aby przekroczyć zakres zapytania, aby wyświetlić dzienniki dotyczące NGINX powyżej utworzonego w poprzednim kroku, Dodaj dodatkową instrukcję *WHERE* , aby wyszukać *Nginx* , jak pokazano w poniższym przykładowym zapytaniu:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Aby wyświetlić dzienniki *polecenia-Audit-admin* , w polu tekstowym wprowadź następujące zapytanie:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

W tym przykładzie zapytanie pokazuje wszystkie zadania tworzenia w *polecenia-Audit-admin*. Prawdopodobnie zwrócono wiele wyników, aby przekroczyć zakres zapytania, aby wyświetlić dzienniki dotyczące NGINX powyżej utworzonego w poprzednim kroku, Dodaj dodatkową instrukcję *WHERE* , aby wyszukać *Nginx* , jak pokazano w poniższym przykładowym zapytaniu.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Aby uzyskać więcej informacji na temat wykonywania zapytań i filtrowania danych dziennika, zobacz [Wyświetlanie lub analizowanie danych zbieranych za pomocą wyszukiwania dzienników usługi log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schemat zdarzeń dziennika

AKS rejestruje następujące zdarzenia:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Puls][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Wydajność][log-schema-perf]

## <a name="log-roles"></a>Role dziennika

| Rola                     | Opis |
|--------------------------|-------------|
| *aksService*             | Nazwa wyświetlana w dzienniku inspekcji dla operacji płaszczyzny kontroli (z hcpService) |
| *masterclient*           | Nazwa wyświetlana w dzienniku inspekcji dla MasterClientCertificate, certyfikat uzyskany z AZ AKS Get-Credentials |
| *nodeclient*             | Nazwa wyświetlana dla ClientCertificate, która jest używana przez węzły agenta |

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i przeglądania dzienników dla składników płaszczyzny kontroli Kubernetes w klastrze AKS. Aby przeprowadzić dalsze monitorowanie i rozwiązywanie problemów, można także [wyświetlić dzienniki Kubelet][kubelet-logs] i [włączyć dostęp do węzła SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf