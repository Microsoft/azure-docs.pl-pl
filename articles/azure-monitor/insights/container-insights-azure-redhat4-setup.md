---
title: Konfigurowanie usługi Azure Red Hat OpenShift v4. x z Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania monitorowania klastra Kubernetes przy użyciu Azure Monitor hostowanego na platformie Azure Red Hat OpenShift w wersji 4 lub nowszej.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196298"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Konfigurowanie usługi Azure Red Hat OpenShift v4. x z Azure Monitor dla kontenerów

Azure Monitor dla kontenerów zapewnia rozbudowane środowisko monitorowania dla klastrów usługi Azure Kubernetes Service (AKS) i AKS Engine. W tym artykule opisano sposób włączania monitorowania klastrów Kubernetes hostowanych na [platformie Azure Red Hat OpenShift](../../openshift/intro-openshift.md) w wersji 4. x w celu osiągnięcia podobnego środowiska monitorowania.

>[!NOTE]
>Obsługa usługi Azure Red Hat OpenShift jest w tej chwili funkcją w publicznej wersji zapoznawczej.
>

Azure Monitor dla kontenerów można włączyć dla jednego lub kilku istniejących wdrożeń systemu Azure Red Hat OpenShift v4. x przy użyciu następujących obsługiwanych metod:

- W przypadku istniejącego klastra przy użyciu dostarczonego skryptu bash i działającego w [interfejsie wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Obsługiwane i nieobsługiwane funkcje

Azure Monitor for Containers obsługuje monitorowanie usługi Azure Red Hat OpenShift v4. x zgodnie z opisem w artykule dotyczącym [przeglądu](container-insights-overview.md) , z wyjątkiem następujących funkcji:

- Dane dynamiczne (wersja zapoznawcza)
- [Zbieranie metryk](container-insights-update-metrics.md) z węzłów klastra i z magazynów oraz przechowywanie ich w bazie danych metryk Azure monitor

## <a name="prerequisites"></a>Wymagania wstępne

- Interfejs wiersza polecenia platformy Azure w wersji 2.0.72 lub nowszej

- [Helm 3](https://helm.sh/docs/intro/install/) Narzędzie interfejsu wiersza polecenia

- [Bash w wersji 4](https://www.gnu.org/software/bash/)

- Narzędzie wiersza polecenia [polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Aby włączyć i uzyskać dostęp do funkcji w Azure Monitor dla kontenerów, musisz być członkiem roli *współautor* platformy Azure w ramach subskrypcji platformy Azure i członkiem roli [*współautor Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) obszaru roboczego log Analytics skonfigurowanym przy użyciu Azure monitor dla kontenerów.

- Aby wyświetlić dane monitorowania, należy być członkiem uprawnienia roli [*czytelnik log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) z obszarem roboczym log Analytics skonfigurowanym za pomocą Azure monitor dla kontenerów.

## <a name="enable-for-an-existing-cluster"></a>Włącz dla istniejącego klastra

Wykonaj następujące kroki, aby włączyć monitorowanie usługi Azure Red Hat OpenShift w wersji 4 i nowszej wdrożonej na platformie Azure przy użyciu podanego skryptu bash.

1. Logowanie do platformy Azure

    ```azurecli
    az login
    ```

2. Pobierz i Zapisz skrypt do folderu lokalnego, który konfiguruje klaster przy użyciu dodatku do monitorowania za pomocą następujących poleceń:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Aby zidentyfikować **kontekst polecenia** klastra, po pomyślnym `oc login` przejściu do klastra uruchom polecenie `kubectl config current-context` i skopiuj wartość.

### <a name="integrate-with-an-existing-workspace"></a>Integracja z istniejącym obszarem roboczym

Poniższy krok umożliwia monitorowanie klastra przy użyciu pobranego wcześniej skryptu bash. Aby zintegrować z istniejącym obszarem roboczym Log Analytics, wykonaj następujące kroki, aby najpierw zidentyfikować pełny identyfikator zasobu Log Analytics obszaru roboczego, `workspaceResourceId` który jest wymagany dla tego parametru, a następnie uruchom polecenie, aby włączyć dodatek monitorowania względem określonego obszaru roboczego. Jeśli nie masz obszaru roboczego do określenia, możesz przejść do kroku 5 i pozwolić skryptowi na utworzenie nowego obszaru roboczego.

1. Wyświetl listę wszystkich subskrypcji, do których masz dostęp, za pomocą następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Skopiuj wartość identyfikatora **subskrypcji**.

2. Przejdź do subskrypcji, w której znajduje się obszar roboczy Log Analytics, przy użyciu następującego polecenia:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Poniższy przykład wyświetla listę obszarów roboczych w Twoich subskrypcjach w domyślnym formacie JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych Znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego Log Analytics obszaru roboczego pod **identyfikatorem**pola.

4. Uruchom następujące polecenie, aby włączyć monitorowanie, zastępując wartość `workspaceResourceId` parametru: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    Przykład:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

### <a name="integrate-with-default-workspace"></a>Integracja z domyślnym obszarem roboczym

Poniższy krok umożliwia monitorowanie klastra usługi Azure Red Hat OpenShift v4. x przy użyciu pobranego skryptu bash. W tym przykładzie nie trzeba tworzyć ani określać istniejącego obszaru roboczego. To polecenie upraszcza proces przez utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra, jeśli jeszcze nie istnieje w regionie. Utworzony domyślny obszar roboczy jest podobny do formatu *DefaultWorkspace-\<GUID>\<-region>*.  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

Widok wiele klastrów w Azure Monitor dla kontenerów wyróżnia klastry usługi Azure Red Hat OpenShift, które nie obsługują monitorowania na karcie **Niemonitorowane klastry** . Opcja **Włącz** obok klastra nie inicjuje dołączania monitorowania z portalu. Nastąpi przekierowanie do tego artykułu, aby ręcznie włączyć monitorowanie zgodnie z krokami opisanymi w tym artykule.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu Azure Portal lub na stronie głównej wybierz pozycję **Azure monitor**. W sekcji **szczegółowe informacje** wybierz pozycję **kontenery**.

3. Na stronie **monitorowanie kontenerów** wybierz pozycję **Niemonitorowane klastry**.

4. Na liście niemonitorowanych klastrów Znajdź klaster na liście i kliknij pozycję **Włącz**. Wyniki można zidentyfikować na liście, wyszukując wartość **ARO** w kolumnie **Typ klastra**. Po kliknięciu przycisku **Włącz**nastąpi przekierowanie do tego artykułu.

## <a name="next-steps"></a>Następne kroki

- Po włączeniu monitorowania w celu zbierania informacji o kondycji i użyciu zasobów w klastrze RedHat OpenShift w wersji 4. x oraz obciążeniach działających na nich, Dowiedz się, [jak używać](container-insights-analyze.md) Azure monitor kontenerów.

- Domyślnie agent kontenera zbiera dzienniki kontenerów stdout/stderr wszystkich kontenerów uruchomionych we wszystkich przestrzeniach nazw z wyjątkiem polecenia-system. Aby skonfigurować zbieranie dzienników kontenerów specyficzne dla konkretnej przestrzeni nazw lub przestrzeni nazw, przejrzyj [konfigurację agenta usługi Container Insights](container-insights-agent-config.md) , aby skonfigurować żądane ustawienia zbierania danych do pliku konfiguracji ConfigMap.

- Aby wyrównać odpadków i analizować metryki Prometheus z klastra, zapoznaj się z tematem [Konfigurowanie wycinków metryk Prometheus](container-insights-prometheus-integration.md)

- Aby dowiedzieć się, jak zatrzymać monitorowanie klastra za pomocą Azure Monitor dla kontenerów, zobacz [Jak zatrzymać monitorowanie klastra Red Hat OpenShift platformy Azure](container-insights-optout-openshift.md).
