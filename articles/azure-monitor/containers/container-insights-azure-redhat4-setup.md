---
title: Konfigurowanie Azure Red Hat OpenShift w wersji 4.x za pomocą usługi Container Insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania monitorowania klastra Kubernetes przy użyciu Azure Monitor hostowanej w Azure Red Hat OpenShift w wersji 4 lub nowszej.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 11c702d1f46725a12e90a01dc1b38467344a1123
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784645"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Konfigurowanie Azure Red Hat OpenShift w wersji 4.x za pomocą usługi Container Insights

Usługa Container Insights zapewnia rozbudowane środowisko monitorowania dla klastrów Azure Kubernetes Service (AKS) i aparatu usługi AKS. W tym artykule opisano sposób osiągnięcia podobnego środowiska monitorowania przez włączenie monitorowania klastrów Kubernetes hostowanych w Azure Red Hat OpenShift [wersji](../../openshift/intro-openshift.md) 4.x.

>[!NOTE]
>Obsługa Azure Red Hat OpenShift jest obecnie funkcją w publicznej wersji zapoznawczej.
>

Usługę Container Insights można włączyć dla co najmniej jednego istniejącego wdrożenia usługi Azure Red Hat OpenShift w wersji 4.x przy użyciu obsługiwanych metod opisanych w tym artykule.

W przypadku istniejącego klastra uruchom ten skrypt [powłoki Bash w interfejsie wiersza polecenia platformy Azure.](/cli/azure/openshift#az_openshift_create&preserve-view=true)

## <a name="supported-and-unsupported-features"></a>Obsługiwane i nieobsługiwane funkcje

Funkcja Container Insights obsługuje monitorowanie Azure Red Hat OpenShift w wersji 4.x, jak opisano w tece [Omówienie usługi Container Insights,](container-insights-overview.md)z wyjątkiem następujących funkcji:

- Dane na żywo (wersja zapoznawcza)
- [Zbieranie metryk z](container-insights-update-metrics.md) węzłów klastra i zasobników oraz przechowywanie ich w bazie Azure Monitor metryk klastra

## <a name="prerequisites"></a>Wymagania wstępne

- Interfejs wiersza polecenia platformy Azure w wersji 2.0.72 lub nowszej  

- Narzędzie [interfejsu wiersza polecenia Helm 3](https://helm.sh/docs/intro/install/)

- Najnowsza wersja interfejsu [wiersza polecenia openshift](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)

- [Bash w wersji 4](https://www.gnu.org/software/bash/)

- Narzędzie [wiersza polecenia Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- [Obszar roboczy usługi Log Analytics](../logs/design-logs-deployment.md).

    Usługa Container Insights obsługuje obszar roboczy usługi Log Analytics w regionach wymienionych w obszarze Produkty platformy Azure [według regionów.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Aby utworzyć własny obszar roboczy, można go utworzyć za pomocą Azure Resource Manager [,](../logs/resource-manager-workspace.md)za pomocą programu [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub w Azure Portal [.](../logs/quick-create-workspace.md)

- Aby włączyć funkcje usługi Container Insights i uzyskać do nich dostęp, musisz mieć co najmniej rolę Współautor platformy *Azure* w subskrypcji platformy Azure oraz rolę Współautor usługi [*Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym usługi Log Analytics skonfigurowaną za pomocą usługi Container Insights.

- Aby wyświetlić dane monitorowania, musisz mieć rolę czytelnika usługi [*Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym usługi Log Analytics skonfigurowaną za pomocą usługi Container Insights.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Włączanie monitorowania istniejącego klastra

Aby włączyć monitorowanie Azure Red Hat OpenShift w wersji 4 lub nowszej wdrożonej na platformie Azure przy użyciu dostarczonego skryptu powłoki Bash, wykonaj następujące czynności:

1. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

    ```azurecli
    az login
    ```

1. Pobierz i zapisz w folderze lokalnym skrypt, który konfiguruje klaster przy użyciu dodatku do monitorowania, uruchamiając następujące polecenie:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Połącz się z klastrem ARO w wersji 4, korzystając z instrukcji z [tematu Samouczek: nawiązywanie](../../openshift/tutorial-connect-cluster.md)połączenia z klastrem Azure Red Hat OpenShift 4.


### <a name="integrate-with-an-existing-workspace"></a>Integracja z istniejącym obszarem roboczym

W tej sekcji włączysz monitorowanie klastra przy użyciu pobranego wcześniej skryptu powłoki Bash. Aby zintegrować usługę z istniejącym obszarem roboczym usługi Log Analytics, rozpocznij od zidentyfikowania pełnego identyfikatora zasobu obszaru roboczego usługi Log Analytics, który jest wymagany dla parametru, a następnie uruchom polecenie , aby włączyć monitorowanie dodatku dla określonego obszaru `logAnalyticsWorkspaceResourceId` roboczego.

Jeśli nie masz obszaru roboczego do określenia, [](#integrate-with-the-default-workspace) możesz przejść do sekcji Integracja z domyślnym obszarem roboczym i pozwolić skryptowi na utworzenie nowego obszaru roboczego.

1. Wyliczysz wszystkie subskrypcje, do których masz dostęp, uruchamiając następujące polecenie:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Skopiuj wartość **subscriptionid**.

1. Przejdź do subskrypcji, która hostuje obszar roboczy usługi Log Analytics, uruchamiając następujące polecenie:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Wyświetl listę obszarów roboczych w subskrypcjach w domyślnym formacie JSON, uruchamiając następujące polecenie:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. W danych wyjściowych znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego obszaru roboczego usługi Log Analytics pod **identyfikatorem pola**.

1. Aby włączyć monitorowanie, uruchom następujące polecenie. Zastąp wartości parametrów `azureAroV4ClusterResourceId` `logAnalyticsWorkspaceResourceId` i .

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Oto polecenie, które należy uruchomić po wypełniniu 3 zmiennych poleceniami eksportu:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

Po włączeniu monitorowania wyświetlenie metryk kondycji klastra może potrwać około 15 minut.

### <a name="integrate-with-the-default-workspace"></a>Integracja z domyślnym obszarem roboczym

W tej sekcji włączysz monitorowanie klastra Azure Red Hat OpenShift w wersji 4.x przy użyciu pobranego skryptu powłoki Bash.

W tym przykładzie nie trzeba wstępnie tworzyć ani określać istniejącego obszaru roboczego. To polecenie upraszcza proces przez utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra, jeśli jeszcze nie istnieje w regionie.

Utworzony domyślny obszar roboczy ma format *DefaultWorkspace— \<GUID> - \<Region>*.  

Zastąp wartość `azureAroV4ClusterResourceId` parametru .

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Na przykład:

'bash enable-monitoring.sh --resource-id $azureAroV 4ClusterResourceId 

Po włączeniu monitorowania wyświetlenie metryk kondycji klastra może potrwać około 15 minut.

### <a name="enable-monitoring-from-the-azure-portal"></a>Włączanie monitorowania z Azure Portal

Widok wielu klastrów w obszarze Szczegółowe informacje o kontenerach wyróżnia klastry Azure Red Hat OpenShift, które nie mają włączonego monitorowania, na karcie **Klastry niemonitorowane.** Opcja **Włącz** obok klastra nie inicjuje dołączania monitorowania z portalu. Nastąpi przekierowanie do tego artykułu, aby ręcznie włączyć monitorowanie, zgodnie z instrukcjami opisanymi wcześniej w tym artykule.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie lub na stronie głównej wybierz **pozycję Azure Monitor**.

1. W sekcji **Szczegółowe informacje** wybierz pozycję **Kontenery.**

1. Na stronie **Monitoruj — kontenery** wybierz pozycję **Klastry niemonitorowane.**

1. Na liście nie monitorowanych klastrów wybierz klaster, a następnie wybierz pozycję **Włącz.**

    Wyniki na liście można zidentyfikować, szukając wartości **ARO** w kolumnie **Typ** klastra. Po wybraniu opcji **Włącz** nastąpi przekierowanie do tego artykułu.

## <a name="next-steps"></a>Następne kroki

- Teraz, po włączeniu monitorowania w celu zbierania kondycji i wykorzystania zasobów klastra RedHat OpenShift w wersji 4.x i uruchomionych na nich obciążeń, dowiedz się, jak używać usługi [Container](container-insights-analyze.md) Insights.

- Domyślnie konteneryzowany agent zbiera dzienniki *kontenerów stdout* i *stderr* wszystkich kontenerów uruchomionych we wszystkich przestrzeniach nazw z wyjątkiem kube-system. Aby skonfigurować kolekcję dzienników kontenerów specyficzną dla określonej przestrzeni nazw lub przestrzeni nazw, zapoznaj się z konfiguracją agenta usługi [Container Insights,](container-insights-agent-config.md) aby skonfigurować ustawienia zbierania danych dla pliku konfiguracji *ConfigMap.*

- Aby zdyskułować i przeanalizować metryki Prometheus z klastra, zapoznaj się z konfigurowaniem zrównania metryk [Prometheus.](container-insights-prometheus-integration.md)

- Aby dowiedzieć się, jak zatrzymać monitorowanie klastra przy użyciu usługi Container Insights, zobacz [Jak zatrzymać monitorowanie](./container-insights-optout-openshift-v3.md)klastra Azure Red Hat OpenShift klastra.
