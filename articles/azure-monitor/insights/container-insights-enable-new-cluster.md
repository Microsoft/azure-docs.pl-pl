---
title: Monitoruj nowy klaster usługi Azure Kubernetes Service (AKS) | Microsoft Docs
description: Dowiedz się, jak włączyć monitorowanie dla nowego klastra usługi Azure Kubernetes Service (AKS) przy użyciu subskrypcji kontenerów Azure Monitor.
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 19c4a88cee8776136593b041e94dd14c7c9c28d6
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561643"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Włącz monitorowanie nowego klastra usługi Azure Kubernetes Service (AKS)

W tym artykule opisano sposób konfigurowania Azure Monitor kontenerów do monitorowania zarządzanego klastra Kubernetes hostowanego w [usłudze Azure Kubernetes Service](../../aks/index.yml) , która jest przygotowywana do wdrożenia w ramach subskrypcji.

Można włączyć monitorowanie klastra AKS przy użyciu jednej z obsługiwanych metod:

* Interfejs wiersza polecenia platformy Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Włączanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby włączyć monitorowanie nowego klastra AKS utworzonego za pomocą interfejsu wiersza polecenia platformy Azure, wykonaj kroki opisane w artykule szybki start w sekcji [Tworzenie klastra AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Jeśli zdecydujesz się na korzystanie z interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.74 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli zainstalowano rozszerzenie wiersza polecenia AKS-Preview w wersji 0.4.12 lub nowszej, Usuń wszelkie zmiany wprowadzone w celu włączenia rozszerzenia w wersji zapoznawczej, ponieważ może ono zastąpić domyślne zachowanie interfejsu wiersza polecenia platformy Azure, ponieważ funkcja AKS Preview nie jest dostępna w chmurze usługi Azure US governmnet.

## <a name="enable-using-terraform"></a>Włącz korzystanie z Terraform

Jeśli [wdrażasz nowy klaster AKS przy użyciu Terraform](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks), określisz argumenty wymagane w profilu, [Aby utworzyć obszar roboczy log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) , jeśli nie wybrano, aby określić istniejący. 

>[!NOTE]
>Jeśli zdecydujesz się na korzystanie z Terraform, musisz mieć uruchomiony program Terraform platformy Azure RM w wersji 1.17.0 lub nowszej.

Aby dodać Azure Monitor kontenerów do obszaru roboczego, zapoznaj się z tematem [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) i Ukończ profil, dołączając [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) i określ **oms_agent**. 

Po włączeniu monitorowania i zakończeniu wszystkich zadań konfiguracji można monitorować wydajność klastra na jeden z dwóch sposobów:

* Bezpośrednio w klastrze AKS, wybierając pozycję **kondycja** w okienku po lewej stronie.
* Wybierając kafelek **monitoruj informacje o kontenerze** na stronie klastra AKS dla wybranego klastra. W obszarze Azure Monitor w lewym okienku wybierz pozycję **kondycja**. 

  ![Opcje wybierania Azure Monitor dla kontenerów w AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra. 

## <a name="verify-agent-and-solution-deployment"></a>Weryfikowanie wdrożenia agenta i rozwiązania
W przypadku agenta w wersji *06072018* lub nowszej można sprawdzić, czy zarówno Agent, jak i rozwiązanie zostały pomyślnie wdrożone. W przypadku wcześniejszych wersji agenta można zweryfikować tylko wdrożenie agenta.

### <a name="agent-version-06072018-or-later"></a>Agent w wersji 06072018 lub nowszej
Uruchom następujące polecenie, aby sprawdzić, czy Agent został wdrożony pomyślnie. 

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, co oznacza, że zostało prawidłowo wdrożone:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Aby zweryfikować wdrożenie rozwiązania, uruchom następujące polecenie:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, co oznacza, że zostało prawidłowo wdrożone:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Wersja agenta wcześniejsza niż 06072018

Aby sprawdzić, czy wersja agenta Log Analytics wydana przed *06072018* zostanie wdrożona prawidłowo, uruchom następujące polecenie:  

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, co oznacza, że zostało prawidłowo wdrożone:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Wyświetl konfigurację przy użyciu interfejsu wiersza polecenia
Użyj `aks show` polecenia, aby uzyskać szczegółowe informacje, takie jak rozwiązanie włączone lub nie, co to jest identyfikator zasobu obszaru roboczego log Analytics i szczegóły podsumowania klastra.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje w formacie JSON o rozwiązaniu.  Wyniki polecenia powinny zawierać profil dodatku monitorowania i podobne do następujących przykładowych danych wyjściowych:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Następne kroki

* Jeśli wystąpią problemy podczas próby dołączenia rozwiązania, zapoznaj się z [przewodnikiem rozwiązywania problemów](container-insights-troubleshoot.md)

* Po włączeniu monitorowania w celu zbierania danych o kondycji i użyciu zasobów klastra AKS oraz obciążeń na nich uruchomionych należy dowiedzieć się, [jak używać](container-insights-analyze.md) Azure monitor do kontenerów.

