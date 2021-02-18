---
title: Jak zarządzać Azure Monitor dla agenta kontenerów | Microsoft Docs
description: W tym artykule opisano zarządzanie najbardziej typowymi zadaniami konserwacji przy użyciu Log Analytics agenta kontenera używanego przez Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: b656b0cc89e40dd732def4ebf56dceae69a033b0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620040"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Jak zarządzać Azure Monitor dla agenta kontenerów

Azure Monitor kontenerów używa zastosowanej wersji agenta Log Analytics dla systemu Linux. Po początkowym wdrożeniu dostępne są zadania rutynowe lub opcjonalne, które mogą być konieczne do wykonania w trakcie jego cyklu życia. W tym artykule szczegółowo opisano, jak ręcznie uaktualnić agenta i wyłączyć zbieranie zmiennych środowiskowych z określonego kontenera. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Jak uaktualnić Azure Monitor dla agenta kontenerów

Azure Monitor kontenerów używa zastosowanej wersji agenta Log Analytics dla systemu Linux. Po wydaniu nowej wersji agenta Agent jest automatycznie uaktualniany do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS) i na platformie Azure Red Hat OpenShift w wersji 3. x. W przypadku [hybrydowego klastra Kubernetes](container-insights-hybrid-setup.md) i usługi Azure Red Hat OpenShift w wersji 4. x Agent nie jest zarządzany i należy ręcznie uaktualnić agenta.

Jeśli uaktualnienie agenta nie powiedzie się w przypadku klastra hostowanego w systemie AKS lub Azure Red Hat OpenShift w wersji 3. x, w tym artykule opisano również proces ręcznego uaktualniania agenta. Aby postępować zgodnie z wydaną wersją, zobacz [anonse dotyczące wersji agentów](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-aks-cluster"></a>Uaktualnianie agenta w klastrze AKS

Proces uaktualniania agenta w klastrach AKS składa się z dwóch prostych kroków do przodu. Pierwszym krokiem jest wyłączenie monitorowania za pomocą Azure Monitor dla kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure. Wykonaj kroki opisane w artykule [wyłączanie monitorowania](container-insights-optout.md?#azure-cli) . Korzystając z interfejsu wiersza polecenia platformy Azure, można usunąć agenta z węzłów w klastrze bez wywierania wpływu na rozwiązanie i odpowiadające im dane przechowywane w obszarze roboczym. 

>[!NOTE]
>Podczas wykonywania tej czynności konserwacyjnej węzły w klastrze nie przesyłają dalej zebranych danych, a w widokach wydajności nie będą wyświetlane dane między momentem usunięcia agenta i zainstalowania nowej wersji. 
>

Aby zainstalować nową wersję agenta, wykonaj kroki opisane w temacie [Włączanie monitorowania przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli), aby ukończyć ten proces.  

Po ponownym włączeniu monitorowania może upłynąć około 15 minut, zanim będzie możliwe wyświetlenie zaktualizowanych metryk kondycji klastra. Aby sprawdzić, czy Agent został uaktualniony pomyślnie, możesz wykonać jedną z:

* Uruchom polecenie: `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` . W wyświetlonym stanie Zwróć uwagę na wartość w obszarze **obraz** dla omsagent w sekcji *kontenery* w danych wyjściowych.
* Na karcie **węzły** wybierz węzeł klastra i w okienku **Właściwości** po prawej stronie Sprawdź wartość w obszarze **tag obrazu agenta**.

Wyświetlana wersja agenta powinna być zgodna z najnowszą wersją wymienioną na stronie [historia wersji](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) .

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Uaktualnij agenta w hybrydowym klastrze Kubernetes

Wykonaj następujące kroki, aby uaktualnić agenta programu w klastrze Kubernetes z systemem:

* Samozarządzane klastry Kubernetes hostowane na platformie Azure przy użyciu aparatu AKS.
* Samozarządzane klastry Kubernetes hostowane na Azure Stack lub lokalnie za pomocą aparatu AKS.
* Red Hat OpenShift w wersji 4. x.

Jeśli obszar roboczy Log Analytics działa na platformie Azure, uruchom następujące polecenie:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Jeśli obszar roboczy Log Analytics jest na platformie Azure w Chinach, uruchom następujące polecenie:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Jeśli obszar roboczy Log Analytics jest w instytucji rządowych USA platformy Azure, uruchom następujące polecenie:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Uaktualnij agenta na platformie Azure Red Hat OpenShift v4

Wykonaj następujące kroki, aby uaktualnić agenta programu w klastrze Kubernetes działającym na platformie Azure Red Hat OpenShift w wersji 4. x. 

>[!NOTE]
>Usługa Azure Red Hat OpenShift w wersji 4. x obsługuje tylko uruchamianie w chmurze komercyjnej platformy Azure.
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

Aby uzyskać szczegółowe informacje na temat korzystania z jednostki usługi za pomocą tego polecenia, zobacz **Używanie nazwy głównej usługi** w artykule [Włączanie monitorowania klastra Kubernetes z włączoną funkcją Azure Arc](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) .

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Agent uaktualnienia z włączonym usługą Azure Arc Kubernetes

Wykonaj następujące polecenie, aby uaktualnić agenta w klastrze Kubernetes z włączoną funkcją Azure Arc.

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $azureArcClusterResourceId
```

Aby uzyskać szczegółowe informacje na temat korzystania z jednostki usługi za pomocą tego polecenia, zobacz **Używanie nazwy głównej usługi** w artykule [Włączanie monitorowania klastra Kubernetes z włączoną funkcją Azure Arc](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) .


## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Jak wyłączyć zbieranie zmiennych środowiskowych w kontenerze

Azure Monitor dla kontenerów zbiera zmienne środowiskowe z kontenerów uruchomionych w obszarze pod i wyświetla je w okienku właściwości wybranego kontenera w widoku **kontenerów** . Możesz kontrolować to zachowanie, wyłączając kolekcję dla określonego kontenera albo podczas wdrażania klastra Kubernetes, albo po ustawieniu zmiennej środowiskowej *AZMON_COLLECT_ENV*. Ta funkcja jest dostępna w wersji agenta — ciprod11292018 lub nowszej.  

Aby wyłączyć zbieranie zmiennych środowiskowych w nowym lub istniejącym kontenerze, należy ustawić dla zmiennej **AZMON_COLLECT_ENV** wartość **false** w pliku konfiguracji YAML wdrożenia Kubernetes. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

Uruchom następujące polecenie, aby zastosować zmianę do klastrów Kubernetes innych niż Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>` . Aby edytować ConfigMap i zastosować tę zmianę dla klastrów Red Hat OpenShift platformy Azure, uruchom polecenie:

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Spowoduje to otwarcie domyślnego edytora tekstu. Po ustawieniu zmiennej Zapisz plik w edytorze.

Aby sprawdzić, czy zmiana konfiguracji zadziałała, wybierz kontener w widoku **kontenery** w Azure monitor dla kontenerów, a następnie w panelu właściwości rozwiń pozycję **zmienne środowiskowe**.  Sekcja powinna zawierać tylko utworzoną wcześniej zmienną- **AZMON_COLLECT_ENV = false**. W przypadku wszystkich innych kontenerów w sekcji zmienne środowiskowe powinny być wystawione wszystkie odnalezione zmienne środowiskowe.

Aby ponownie włączyć odnajdywanie zmiennych środowiskowych, Zastosuj ten sam proces wcześniej i zmień wartość z **false** na **true**, a następnie ponownie uruchom `kubectl` polecenie w celu zaktualizowania kontenera.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Następne kroki

W przypadku wystąpienia problemów podczas uaktualniania agenta zapoznaj się z [przewodnikiem rozwiązywania problemów](container-insights-troubleshoot.md) , aby uzyskać pomoc techniczną.
