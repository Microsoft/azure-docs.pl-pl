---
title: Wyświetlanie dzienników kontrolera usługi Azure Kubernetes Service (AKS)
description: Informacje na temat włączania i wyświetlania dzienników dla węzła głównego Kubernetes w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: 504d6a5216f3345f22a601c4ae084488aeb97c8d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128968"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Włączanie i wyświetlanie dzienników węzła master platformy Kubernetes w usłudze Azure Kubernetes Service

Dzięki usłudze Azure Kubernetes Service (AKS) główne składniki, takie jak *polecenia-apiserver* i *polecenia-Manager* , są udostępniane jako usługa zarządzana. Można tworzyć węzły, które uruchamiają *kubelet* i środowisko uruchomieniowe kontenera, oraz zarządzać nimi, a następnie wdrażać aplikacje za pomocą zarządzanego serwera interfejsu API Kubernetes. Aby pomóc w rozwiązywaniu problemów dotyczących aplikacji i usług, może być konieczne wyświetlenie dzienników generowanych przez te składniki główne. W tym artykule pokazano, jak używać dzienników Azure Monitor do włączania i wykonywania zapytań dotyczących dzienników ze składników głównych Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga istniejącego klastra AKS uruchomionego na Twoim koncie platformy Azure. Jeśli nie masz jeszcze klastra AKS, utwórz go przy użyciu [interfejsu wiersza polecenia platformy Azure][cli-quickstart] lub [Azure Portal][portal-quickstart]. Dzienniki Azure Monitor współdziałają z klastrami AKS z obsługą RBAC i bez kontroli RBAC.

## <a name="enable-resource-logs"></a>Włączanie dzienników zasobów

Aby ułatwić zbieranie i przeglądanie danych z wielu źródeł, dzienniki Azure Monitor udostępniają język zapytań i aparat analityczny, który zapewnia wgląd w dane środowisko. Obszar roboczy służy do sortowania i analizowania danych oraz integracji z innymi usługami platformy Azure, takimi jak Application Insights i Security Center. Aby użyć innej platformy do analizowania dzienników, możesz zamiast tego wybrać opcję wysyłania dzienników zasobów do konta usługi Azure Storage lub centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [co to jest Azure monitor Logs?][log-analytics-overview].

Dzienniki Azure Monitor są włączone i zarządzane w Azure Portal. Aby włączyć zbieranie dzienników dla składników głównych Kubernetes w klastrze AKS, Otwórz Azure Portal w przeglądarce internetowej i wykonaj następujące czynności:

1. Wybierz grupę zasobów dla klastra AKS, na przykład grupa *zasobów*. Nie wybieraj grupy zasobów zawierającej poszczególne zasoby klastra AKS, takie jak *MC_myResourceGroup_myAKSCluster_eastus*.
1. Po lewej stronie wybierz pozycję **Ustawienia diagnostyczne**.
1. Wybierz klaster AKS, taki jak *myAKSCluster*, a następnie wybierz opcję **dodania ustawienia diagnostycznego**.
1. Wprowadź nazwę, na przykład *myAKSClusterLogs*, a następnie wybierz opcję **wysyłania do log Analytics**.
1. Wybierz istniejący obszar roboczy lub Utwórz nowy. W przypadku tworzenia obszaru roboczego Podaj nazwę obszaru roboczego, grupę zasobów i lokalizację.
1. Na liście dostępnych dzienników wybierz dzienniki, które chcesz włączyć. Typowe dzienniki obejmują *polecenia-apiserver*, *polecenia-Controller-Manager*i *polecenia-Scheduler*. Możesz włączyć dodatkowe dzienniki, takie jak *polecenia-Audit* i *cluster-autoscaleer*. Można zwrócić i zmienić zebrane dzienniki po włączeniu Log Analytics obszarów roboczych.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz** , aby włączyć zbieranie wybranych dzienników.

Poniższy przykładowy zrzut ekranu portalu pokazuje okno *Ustawienia diagnostyki* :

![Włączanie Log Analyticsego obszaru roboczego dla dzienników Azure Monitor klastra AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Zaplanuj test pod względem klastra AKS

Aby wygenerować niektóre dzienniki, Utwórz nowy element pod w klastrze AKS. Poniższy przykład manifestu YAML może służyć do tworzenia podstawowego wystąpienia NGINX. Utwórz plik o nazwie `nginx.yaml` w wybranym edytorze i wklej następującą zawartość:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
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

Włączenie dzienników diagnostycznych w obszarze roboczym Log Analytics może potrwać kilka minut. W Azure Portal wybierz grupę zasobów dla obszaru roboczego Log Analytics, na przykład grupa *zasobów*, a następnie wybierz zasób usługi log Analytics, taki jak *myAKSLogs*.

![Wybierz obszar roboczy Log Analytics dla klastra AKS](media/view-master-logs/select-log-analytics-workspace.png)

Po lewej stronie wybierz pozycję **dzienniki**. Aby wyświetlić *polecenia-apiserver*, w polu tekstowym wprowadź następujące zapytanie:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Dla serwera interfejsu API jest możliwe zwrócenie wielu dzienników. Aby przekroczyć zakres zapytania, aby wyświetlić dzienniki dotyczące NGINX powyżej utworzonego w poprzednim kroku, Dodaj dodatkową instrukcję *WHERE* , aby wyszukać wartości */Nginx* , jak pokazano w poniższym przykładowym zapytaniu:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Są wyświetlane określone dzienniki dla NGINX pod, jak pokazano na poniższym przykładowym zrzucie ekranu:

![Wyniki zapytania usługi log Analytics dla przykładowej NGINX pod](media/view-master-logs/log-analytics-query-results.png)

Aby wyświetlić dodatkowe dzienniki, można zaktualizować zapytanie dla nazwy *kategorii* do *polecenia-Controller-Manager* lub *polecenia-Scheduler*, w zależności od tego, jakie dodatkowe dzienniki są włączane. Dodatkowe instrukcje *WHERE* mogą być następnie używane do uściślenia szukanych zdarzeń.

Aby uzyskać więcej informacji na temat wykonywania zapytań i filtrowania danych dziennika, zobacz [Wyświetlanie lub analizowanie danych zbieranych za pomocą wyszukiwania dzienników usługi log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schemat zdarzeń dziennika

Aby ułatwić analizowanie danych dziennika, w poniższej tabeli przedstawiono schemat używany dla każdego zdarzenia:

| Nazwa pola               | Opis |
|--------------------------|-------------|
| *Identyfikator*             | Zasób platformy Azure, który wygenerował dziennik |
| *pierwszym*                   | Sygnatura czasowa przekazywania dziennika |
| *kategorii*               | Nazwa kontenera/składnika generującego dziennik |
| *operationName*          | Zawsze *Microsoft. ContainerService/managedClusters/diagnosticLogs/Read* |
| *Właściwości. log*         | Pełny tekst dziennika ze składnika |
| *Właściwości. Stream*      | *stderr* lub *stdout* |
| *Właściwości. pod*         | Pod nazwą, z której pochodzi dziennik |
| *Properties. containerID* | Identyfikator kontenera platformy Docker, z którego pochodzi ten dziennik |

## <a name="log-roles"></a>Role dziennika

| Rola                     | Opis |
|--------------------------|-------------|
| *aksService*             | Nazwa wyświetlana w dzienniku inspekcji dla operacji płaszczyzny kontroli (z hcpService) |
| *masterclient*           | Nazwa wyświetlana w dzienniku inspekcji dla MasterClientCertificate, certyfikat uzyskany z AZ AKS Get-Credentials |
| *nodeclient*             | Nazwa wyświetlana dla ClientCertificate, która jest używana przez węzły agenta |

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i przeglądania dzienników dla składników głównych Kubernetes w klastrze AKS. Aby przeprowadzić dalsze monitorowanie i rozwiązywanie problemów, można także [wyświetlić dzienniki Kubelet][kubelet-logs] i [włączyć dostęp do węzła SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
