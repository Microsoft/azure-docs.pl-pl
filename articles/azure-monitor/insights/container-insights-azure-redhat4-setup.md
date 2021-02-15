---
title: Konfigurowanie usługi Azure Red Hat OpenShift v4. x z Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania monitorowania klastra Kubernetes przy użyciu Azure Monitor hostowanego na platformie Azure Red Hat OpenShift w wersji 4 lub nowszej.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e6668ac22c6c0f53c7511cfb76bf50c5474f3a76
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521632"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Konfigurowanie usługi Azure Red Hat OpenShift v4. x z Azure Monitor dla kontenerów

Azure Monitor for Containers oferuje bogate środowisko monitorowania dla klastrów usługi Azure Kubernetes Service (AKS) i AKS Engine. W tym artykule opisano sposób osiągnięcia podobnego środowiska monitorowania przez włączenie monitorowania klastrów Kubernetes hostowanych na [platformie Azure Red Hat OpenShift](../../openshift/intro-openshift.md) w wersji 4. x.

>[!NOTE]
>Obsługa usługi Azure Red Hat OpenShift jest w tej chwili funkcją w publicznej wersji zapoznawczej.
>

Możesz włączyć Azure Monitor dla kontenerów dla jednego lub kilku istniejących wdrożeń systemu Azure Red Hat OpenShift v4. x przy użyciu obsługiwanych metod opisanych w tym artykule.

W przypadku istniejącego klastra uruchom ten [skrypt bash w interfejsie wiersza polecenia platformy Azure](/cli/azure/openshift#az-openshift-create&preserve-view=true).

## <a name="supported-and-unsupported-features"></a>Obsługiwane i nieobsługiwane funkcje

Azure Monitor for Containers obsługuje monitorowanie usługi Azure Red Hat OpenShift v4. x zgodnie z opisem w temacie [Azure monitor for Containers — Omówienie](container-insights-overview.md), z wyjątkiem następujących funkcji:

- Dane dynamiczne (wersja zapoznawcza)
- [Zbieranie metryk](container-insights-update-metrics.md) z węzłów klastra i zasobników i przechowywanie ich w bazie danych metryk Azure monitor

## <a name="prerequisites"></a>Wymagania wstępne

- Interfejs wiersza polecenia platformy Azure w wersji 2.0.72 lub nowszej  

- Narzędzie interfejsu wiersza polecenia [Helm 3](https://helm.sh/docs/intro/install/)

- [Bash w wersji 4](https://www.gnu.org/software/bash/)

- Narzędzie wiersza polecenia [polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- [Obszar roboczy usługi Log Analytics](../platform/design-logs-deployment.md).

    Azure Monitor dla kontenerów obsługuje obszar roboczy Log Analytics w regionach wymienionych w produktach platformy Azure [według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Aby utworzyć własny obszar roboczy, można go utworzyć za pomocą [Azure Resource Manager](../samples/resource-manager-workspace.md), za pomocą [programu PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub [Azure Portal](../learn/quick-create-workspace.md).

- Aby włączyć i uzyskać dostęp do funkcji usługi Azure Monitor dla kontenerów, musisz mieć co najmniej rolę *współautor* platformy Azure w ramach subskrypcji platformy Azure i rolę [*współautor log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym log Analytics, skonfigurowanym za pomocą Azure monitor dla kontenerów.

- Aby wyświetlić dane monitorowania, należy mieć rolę [*czytnika log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym log Analytics, skonfigurowany przy użyciu Azure monitor dla kontenerów.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Włączanie monitorowania dla istniejącego klastra

Aby włączyć monitorowanie klastra Red Hat OpenShift w wersji 4 lub nowszej, który został wdrożony na platformie Azure za pomocą podanego skryptu bash, wykonaj następujące czynności:

1. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

    ```azurecli
    az login
    ```

1. Pobierz i Zapisz w folderze lokalnym skrypt, który konfiguruje klaster przy użyciu dodatku do monitorowania, uruchamiając następujące polecenie:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Aby zidentyfikować *kubeContext* klastra, uruchom następujące polecenia.

    ```
    adminUserName=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminUsername' -o tsv)
    adminPassword=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminPassword' -o tsv)
    apiServer=$(az aro show -g $clusterResourceGroup -n $clusterName --query apiserverProfile.url -o tsv)
    oc login $apiServer -u $adminUserName -p $adminPassword
    # openshift project name for azure monitor for containers
    openshiftProjectName="azure-monitor-for-containers"
    oc new-project $openshiftProjectName
    # get the kube config context
    kubeContext=$(oc config current-context)
    ```

1. Skopiuj wartość do późniejszego użycia.

### <a name="integrate-with-an-existing-workspace"></a>Integracja z istniejącym obszarem roboczym

W tej sekcji można włączyć monitorowanie klastra przy użyciu pobranego wcześniej skryptu bash. Aby przeprowadzić integrację z istniejącym obszarem roboczym Log Analytics, Zacznij od zidentyfikowania pełnego identyfikatora zasobu Log Analytics obszaru roboczego, który jest wymagany dla tego `logAnalyticsWorkspaceResourceId` parametru, a następnie uruchom polecenie, aby włączyć dodatek monitorowania względem określonego obszaru roboczego.

Jeśli nie masz obszaru roboczego do określenia, możesz przejść do sekcji [integracja z domyślnym obszarem roboczym](#integrate-with-the-default-workspace) i pozwolić skryptowi na utworzenie nowego obszaru roboczego.

1. Aby wyświetlić listę wszystkich subskrypcji, do których masz dostęp, uruchamiając następujące polecenie:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Skopiuj wartość identyfikatora **subskrypcji**.

1. Przejdź do subskrypcji, która obsługuje obszar roboczy Log Analytics, uruchamiając następujące polecenie:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Aby wyświetlić listę obszarów roboczych w ramach subskrypcji w domyślnym formacie JSON, należy uruchomić następujące polecenie:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. W danych wyjściowych Znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego Log Analytics obszaru roboczego pod **identyfikatorem** pola.

1. Aby włączyć monitorowanie, uruchom następujące polecenie. Zastąp wartości `azureAroV4ClusterResourceId` `logAnalyticsWorkspaceResourceId` parametrów, i `kubeContext` .

    ```bash
    export azureAroV4ClusterResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>”
    export logAnalyticsWorkspaceResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
    export kubeContext="<kubeContext name of your ARO v4 cluster>"  
    ```

    Oto polecenie, które należy uruchomić po wprowadzeniu 3 zmiennych przy użyciu poleceń eksportu:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext --workspace-id $logAnalyticsWorkspaceResourceId`

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

### <a name="integrate-with-the-default-workspace"></a>Integracja z domyślnym obszarem roboczym

W tej sekcji można włączyć monitorowanie klastra Red Hat OpenShift v4. x platformy Azure przy użyciu pobranego skryptu bash.

W tym przykładzie nie trzeba już tworzyć ani określać istniejącego obszaru roboczego. To polecenie upraszcza proces przez utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra, jeśli jeszcze nie istnieje w regionie.

Tworzony domyślny obszar roboczy ma format *DefaultWorkspace- \<GUID> - \<Region>*.  

Zastąp wartości `azureAroV4ClusterResourceId` `kubeContext` parametrów i.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
export kubeContext="<kubeContext name of your ARO v4 cluster>"
```

Na przykład:

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext`

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

### <a name="enable-monitoring-from-the-azure-portal"></a>Włącz monitorowanie z poziomu Azure Portal

Widok wiele klastrów w Azure Monitor dla kontenerów wyróżnia klastry usługi Azure Red Hat OpenShift, które nie obsługują monitorowania na karcie **Niemonitorowane klastry** . Opcja **Włącz** obok klastra nie inicjuje dołączania monitorowania z portalu. Nastąpi przekierowanie do tego artykułu, aby ręcznie włączyć monitorowanie, wykonując czynności opisane wcześniej w tym artykule.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W lewym okienku lub stronie głównej wybierz pozycję **Azure monitor**.

1. W sekcji **szczegółowe informacje** wybierz pozycję **kontenery**.

1. Na stronie **monitorowanie kontenerów** wybierz opcję **Niemonitorowane klastry**.

1. Na liście niemonitorowanych klastrów wybierz klaster, a następnie wybierz pozycję **Włącz**.

    Wyniki można zidentyfikować na liście, wyszukując wartość **ARO** w kolumnie **Typ klastra** . Po wybraniu opcji **Włącz** nastąpi przekierowanie do tego artykułu.

## <a name="next-steps"></a>Następne kroki

- Teraz, gdy włączono monitorowanie w celu zbierania danych dotyczących kondycji i wykorzystania zasobów w klastrze RedHat OpenShift w wersji 4. x i uruchomionych na nich obciążeń, Dowiedz się, [jak używać](container-insights-analyze.md) Azure monitor kontenerów.

- Domyślnie agent kontenera zbiera dzienniki kontenerów *stdout* i *stderr* wszystkich kontenerów, które działają we wszystkich przestrzeniach nazw z wyjątkiem polecenia-system. Aby skonfigurować kolekcję dzienników kontenerów specyficzną dla konkretnej przestrzeni nazw lub przestrzeni nazw, przejrzyj [konfigurację agenta usługi Container Insights](container-insights-agent-config.md) w celu skonfigurowania ustawień zbierania danych dla pliku konfiguracji *ConfigMap* .

- Aby wyrównać odpadków i analizować metryki Prometheus z klastra, zapoznaj się z tematem [Konfigurowanie wycinków metryk Prometheus](container-insights-prometheus-integration.md).

- Aby dowiedzieć się, jak zatrzymać monitorowanie klastra przy użyciu Azure Monitor dla kontenerów, zobacz [Jak zatrzymać monitorowanie klastra Red Hat OpenShift platformy Azure](./container-insights-optout-openshift-v3.md).
