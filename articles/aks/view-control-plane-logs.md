---
title: Wyświetlanie dzienników Azure Kubernetes Service (AKS)
description: Dowiedz się, jak włączyć i wyświetlić dzienniki dla płaszczyzny sterowania kubernetes w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 395422ca97b43488a7d7ad1c7434b20ccc59f2b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767295"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Włączanie i przeglądanie dzienników płaszczyzny sterowania kubernetes w Azure Kubernetes Service (AKS)

W Azure Kubernetes Service (AKS) składniki płaszczyzny sterowania, takie jak *kube-apiserver* i *kube-controller-manager,* są udostępniane jako usługa zarządzana. Tworzysz węzły z uruchomionym środowiskiem *kubelet* i środowiskiem uruchomieniowym kontenera oraz zarządzasz nimi, a następnie wdrażasz aplikacje za pośrednictwem zarządzanego serwera interfejsu API Kubernetes. Aby ułatwić rozwiązywanie problemów z aplikacją i usługami, może być konieczne wyświetlenie dzienników generowanych przez te składniki płaszczyzny sterowania. W tym artykule pokazano, jak używać dzienników Azure Monitor do włączania dzienników ze składników płaszczyzny sterowania kubernetes i wykonywania zapytań o nie.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga istniejącego klastra usługi AKS uruchomionego na koncie platformy Azure. Jeśli nie masz jeszcze klastra usługi AKS, utwórz go przy użyciu interfejsu wiersza polecenia platformy [Azure][cli-quickstart] lub Azure Portal [.][portal-quickstart] Azure Monitor RBAC platformy Kubernetes, RBAC platformy Azure i klastry usługi AKS bez włączonej kontroli RBAC.

## <a name="enable-resource-logs"></a>Włączanie dzienników zasobów

Aby ułatwić zbieranie i przeglądanie danych z wielu źródeł, dzienniki Azure Monitor zapewniają język zapytań i aparat analityczny, który zapewnia wgląd w środowisko. Obszar roboczy służy do sortowania i analizowania danych oraz może integrować się z innymi usługami platformy Azure, takimi jak Application Insights i Security Center. Aby użyć innej platformy do analizowania dzienników, można zamiast tego wysłać dzienniki zasobów do konta usługi Azure Storage lub centrum zdarzeń. Aby uzyskać więcej informacji, [zobacz Co to są Azure Monitor dzienniki?][log-analytics-overview].

Azure Monitor są włączone i zarządzane w Azure Portal. Aby włączyć zbieranie dzienników dla składników płaszczyzny sterowania Kubernetes w klastrze usługi AKS, otwórz Azure Portal w przeglądarce internetowej i wykonaj następujące czynności:

1. Wybierz grupę zasobów dla klastra usługi AKS, na przykład *myResourceGroup.* Nie wybieraj grupy zasobów zawierającej poszczególne zasoby klastra usługi AKS, takie *jak MC_myResourceGroup_myAKSCluster_eastus*.

2. Po lewej stronie wybierz pozycję **Ustawienia diagnostyczne.**

3. Wybierz klaster usługi AKS, taki *jak myAKSCluster,* a następnie wybierz **pozycję Dodaj ustawienie diagnostyczne.**
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="Zrzut ekranu Azure Portal w oknie przeglądarki przedstawiający ustawienia diagnostyczne z informacją o tym, że należy wybrać pozycję &quot;Dodaj ustawienie diagnostyczne&quot;":::

4. Wprowadź nazwę, taką jak *myAKSClusterLogs,* a następnie wybierz opcję Wyślij do **obszaru roboczego usługi Log Analytics.**

5. Wybierz istniejący obszar roboczy lub utwórz nowy. Jeśli tworzysz obszar roboczy, podaj nazwę obszaru roboczego, grupę zasobów i lokalizację.

6. Na liście dostępnych dzienników wybierz dzienniki, które chcesz włączyć. W tym przykładzie włącz dzienniki *kube-audit i* *kube-audit-admin.* Typowe dzienniki obejmują *kube-apiserver,* *kube-controller-manager* i *kube-scheduler.* Zebrane dzienniki można zwrócić i zmienić po włączeniu obszarów roboczych usługi Log Analytics.

7. Gdy wszystko będzie gotowe, **wybierz pozycję Zapisz,** aby włączyć zbieranie wybranych dzienników.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Zrzut ekranu Azure Portal ekranu &quot;Dodawanie ustawienia diagnostycznego&quot;. Wybrano miejsce docelowe opcji &quot;Wyślij do obszaru roboczego usługi Log Analytics&quot; i dzienników &quot;kube-audit&quot; i &quot;kube-audit-admin&quot;":::

## <a name="log-categories"></a>Kategorie dzienników

Oprócz wpisów napisanych przez platformę Kubernetes dzienniki inspekcji projektu również mają wpisy z konta AKS.

Dzienniki inspekcji są rejestrowane w trzech kategoriach: *kube-audit,* *kube-audit-admin* i *guard*.

- Kategoria *kube-audit* zawiera wszystkie dane dziennika inspekcji dla każdego zdarzenia inspekcji, w tym get *,* *list*, *create,* *update,* *delete,* *patch* i *post*.
- Kategoria *kube-audit-admin* jest podzbiorem *kategorii dzienników inspekcji kube.* *Kube-audit-admin* znacznie zmniejsza liczbę dzienników,  wykluczając zdarzenia inspekcji get i *list* z dziennika.
- Kategorią *ochrony* są inspekcje zarządzane przez usługi Azure AD i RBAC platformy Azure. W przypadku zarządzanej usługi Azure AD: token in, informacje o użytkowniku. W przypadku kontroli dostępu na platformie Azure: przeglądy dostępu do i na zewnątrz.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Planowanie zasobnika testowego w klastrze usługi AKS

Aby wygenerować dzienniki, utwórz nowy zasobnik w klastrze usługi AKS. Poniższy przykładowy manifest YAML może służyć do tworzenia podstawowego wystąpienia serwera NGINX. Utwórz plik o `nginx.yaml` nazwie w edytorze i wklej następującą zawartość:

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

Utwórz zasobnik za pomocą [polecenia kubectl create][kubectl-create] i określ plik YAML, jak pokazano w poniższym przykładzie:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Wyświetlanie zebranych dzienników

Może upłynieć do 10 minut, aż dzienniki diagnostyczne zostaną włączone i wyświetlone.

> [!NOTE]
> Jeśli potrzebujesz wszystkich danych dziennika inspekcji do celów zgodności lub w innych celach, zbierz i przechowuj je w niedrogim magazynie, takim jak blob storage. Użyj kategorii *dzienników kube-audit-admin,* aby zebrać i zapisać znaczący zestaw danych dziennika inspekcji na potrzeby monitorowania i alertów.

W okienku Azure Portal przejdź do klastra usługi AKS i wybierz pozycję **Dzienniki** po lewej stronie. Zamknij okno *Przykładowe zapytania,* jeśli zostanie wyświetlone.

Po lewej stronie wybierz pozycję **Dzienniki.** Aby wyświetlić *dzienniki kube-audit,* wprowadź następujące zapytanie w polu tekstowym:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Prawdopodobnie zwracanych jest wiele dzienników. Aby zawęzać zakres zapytania w celu wyświetlenia dzienników dotyczących zasobnika NGINX utworzonego w poprzednim kroku, dodaj dodatkową instrukcje *where,* aby wyszukać *nginx,* jak pokazano w poniższym przykładzie zapytania:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Aby wyświetlić *dzienniki kube-audit-admin,* wprowadź następujące zapytanie w polu tekstowym:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

W tym przykładzie zapytanie pokazuje wszystkie zadania tworzenia w *kontrolce kube-audit-admin*. Prawdopodobnie zwracanych jest wiele wyników. Aby zawęzać zakres zapytania w celu wyświetlenia dzienników dotyczących zasobnika NGINX utworzonego w poprzednim kroku, dodaj dodatkową instrukcje *where,* aby wyszukać *nginx,* jak pokazano w poniższym przykładzie zapytania.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Aby uzyskać więcej informacji na temat sposobu wykonywania zapytań i filtrowania danych dziennika, zobacz Wyświetlanie lub analizowanie danych zebranych za pomocą funkcji [przeszukiwania dzienników usługi Log Analytics.][analyze-log-analytics]

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
* [Perf][log-schema-perf]

## <a name="log-roles"></a>Role dziennika

| Rola                     | Opis |
|--------------------------|-------------|
| *aksService*             | Nazwa wyświetlana w dzienniku inspekcji dla operacji płaszczyzny sterowania (z hcpService) |
| *masterclient*           | Nazwa wyświetlana w dzienniku inspekcji dla certyfikatu MasterClientCertificate, który można pobrać z az aks get-credentials |
| *nodeclient*             | Nazwa wyświetlana certyfikatu ClientCertificate, która jest używana przez węzły agentów |

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób włączania i przeglądania dzienników składników płaszczyzny sterowania Kubernetes w klastrze usługi AKS. Aby monitorować i rozwiązywać problemy, możesz również wyświetlić dzienniki [rozwiązania Kubelet][kubelet-logs] i włączyć [dostęp do węzła SSH.][aks-ssh]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
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