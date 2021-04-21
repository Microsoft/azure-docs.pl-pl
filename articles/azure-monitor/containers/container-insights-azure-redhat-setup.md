---
title: Konfigurowanie Azure Red Hat OpenShift w wersji 3.x za pomocą usługi Container insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania monitorowania klastra Kubernetes przy użyciu Azure Monitor hostowanych Azure Red Hat OpenShift wersji 3 lub wyższej.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a2910655601548f39983547e12460d949901954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784681"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>Konfigurowanie usługi Azure Red Hat OpenShift w wersji 3 za pomocą usługi Container Insights

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 zostanie wycofana w czerwcu 2022 r.
>
> Od października 2020 r. nie będzie już można tworzyć nowych klastrów 3.11.
> Istniejące klastry 3.11 będą nadal działać do czerwca 2022 r., ale nie będą już obsługiwane po tej dacie.
>
> Postępuj zgodnie z tym [przewodnikiem, aby utworzyć Azure Red Hat OpenShift 4.](../../openshift/tutorial-create-cluster.md)
> Jeśli masz konkretne pytania, skontaktuj [się z nami.](mailto:aro-feedback@microsoft.com)

Usługa Container Insights zapewnia rozbudowane środowisko monitorowania klastrów Azure Kubernetes Service (AKS) i aparatu AKS. W tym artykule opisano sposób włączania monitorowania klastrów Kubernetes hostowanych na platformie [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) w wersji 3 i najnowszej obsługiwanej wersji 3 w celu uzyskania podobnego środowiska monitorowania.

>[!NOTE]
>Obsługa Azure Red Hat OpenShift jest obecnie funkcją w publicznej wersji zapoznawczej.
>

Szczegółowe informacje o kontenerach można włączyć dla nowych lub co najmniej jednego istniejącego wdrożenia usługi Azure Red Hat OpenShift przy użyciu następujących obsługiwanych metod:

- W przypadku istniejącego klastra z Azure Portal lub przy użyciu Azure Resource Manager szablonu.
- W przypadku nowego klastra przy użyciu Azure Resource Manager lub podczas tworzenia nowego klastra przy użyciu interfejsu wiersza [polecenia platformy Azure.](/cli/azure/openshift#az_openshift_create)

## <a name="supported-and-unsupported-features"></a>Obsługiwane i nieobsługiwane funkcje

Funkcja Container Insights obsługuje Azure Red Hat OpenShift zgodnie z opisem w [artykule Omówienie,](container-insights-overview.md) z wyjątkiem następujących funkcji:

- Dane na żywo (wersja zapoznawcza)
- [Zbieranie metryk z](container-insights-update-metrics.md) węzłów klastra i zasobników oraz przechowywanie ich w bazie Azure Monitor metryk klastra

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy usługi Log Analytics](../logs/design-logs-deployment.md).

    Usługa Container Insights obsługuje obszar roboczy usługi Log Analytics w regionach wymienionych w obszarze Produkty platformy Azure [według regionów.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Aby utworzyć własny obszar roboczy, można go utworzyć za pomocą Azure Resource Manager [,](../logs/resource-manager-workspace.md)za pomocą programu [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub w Azure Portal [.](../logs/quick-create-workspace.md)

- Aby włączyć funkcje usługi Container Insights i uzyskać do nich dostęp, musisz być co najmniej członkiem roli Współautor platformy *Azure* w subskrypcji platformy Azure i członkiem roli Współautor usługi [*Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym usługi Log Analytics skonfigurowanym z usługą Container Insights.

- Aby wyświetlić dane monitorowania, jesteś członkiem uprawnienia roli czytelnika usługi [*Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) z obszarem roboczym usługi Log Analytics skonfigurowanym za pomocą usługi Container Insights.

## <a name="identify-your-log-analytics-workspace-id"></a>Identyfikowanie identyfikatora obszaru roboczego usługi Log Analytics

 Aby zintegrować usługę z istniejącym obszarem roboczym usługi Log Analytics, zacznij od zidentyfikowania pełnego identyfikatora zasobu obszaru roboczego usługi Log Analytics. Identyfikator zasobu obszaru roboczego jest wymagany dla parametru po włączeniu monitorowania przy użyciu Azure Resource Manager `workspaceResourceId` szablonu.

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

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Włączanie dla nowego klastra przy użyciu szablonu Azure Resource Manager klastra

Wykonaj poniższe kroki, aby wdrożyć klaster Azure Red Hat OpenShift z włączonym monitorowaniem. Przed przystąpieniem do pracy zapoznaj się z samouczkiem Tworzenie [klastra Azure Red Hat OpenShift,](../../openshift/tutorial-create-cluster.md) aby zrozumieć zależności, które należy skonfigurować w celu poprawnego skonfigurowania środowiska.

Ta metoda zawiera dwa szablony JSON. Jeden szablon określa konfigurację wdrażania klastra z włączonym monitorowaniem, a drugi zawiera wartości parametrów skonfigurowane w celu określenia następujących wartości:

- Identyfikator Azure Red Hat OpenShift klastra.

- Grupa zasobów, w których jest wdrażany klaster.

- [Azure Active Directory dzierżawy](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) zanotował po wykonaniu kroków tworzenia już utworzonej dzierżawy.

- [Azure Active Directory aplikacji klienckiej](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) zanotował po wykonaniu kroków tworzenia aplikacji, która została już utworzona.

- [Azure Active Directory klucz tajny klienta](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) zanotował po wykonaniu kroków tworzenia już utworzonego.

- [Grupa zabezpieczeń usługi Azure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) zanotowana po wykonaniu kroków tworzenia już utworzonej grupy.

- Identyfikator zasobu istniejącego obszaru roboczego usługi Log Analytics. Aby dowiedzieć się, jak uzyskać te informacje, zobacz Identyfikowanie identyfikatora obszaru roboczego usługi [Log Analytics.](#identify-your-log-analytics-workspace-id)

- Liczba węzłów głównych do utworzenia w klastrze.

- Liczba węzłów obliczeniowych w profilu puli agentów.

- Liczba węzłów infrastruktury w profilu puli agentów.

Jeśli nie masz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

- [Wdrażanie zasobów przy użyciu szablonów Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się używać interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i używać go lokalnie. Musisz mieć uruchomiony interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować swoją wersję, `az --version` uruchom . Jeśli musisz zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

1. Pobierz i zapisz plik szablonu Azure Resource Manager pliku parametrów w folderze lokalnym, aby utworzyć klaster z dodatku do monitorowania za pomocą następujących poleceń:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Logowanie do platformy Azure

    ```azurecli
    az login
    ```

    Jeśli masz dostęp do wielu subskrypcji, uruchom zastąpienie `az account set -s {subscription ID}` `{subscription ID}` subskrypcją, której chcesz użyć.

3. Utwórz grupę zasobów dla klastra, jeśli jeszcze jej nie masz. Aby uzyskać listę regionów platformy Azure, które obsługują platformę OpenShift na platformie Azure, zobacz [Obsługiwane regiony.](../../openshift/supported-resources.md#azure-regions)

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Edytuj plik parametrów JSON,newClusterWithMonitoringParam.js **i** zaktualizuj następujące wartości:

    - *Lokalizacji*
    - *nazwa_klastra*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. W poniższym kroku wdrożono klaster z włączonym monitorowaniem przy użyciu interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Dane wyjściowe są podobne do następujących:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Włączanie dla istniejącego klastra

Wykonaj poniższe kroki, aby włączyć monitorowanie klastra Azure Red Hat OpenShift wdrożonego na platformie Azure. Można to zrobić z Azure Portal lub przy użyciu podanych szablonów.

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu Azure Portal lub na stronie głównej wybierz pozycję **Azure Monitor**. W sekcji **Szczegółowe informacje** wybierz pozycję **Kontenery**.

3. Na stronie **Monitoruj — kontenery** wybierz pozycję **Nie monitorowane klastry.**

4. Na liście nie monitorowanych klastrów znajdź klaster na liście i kliknij pozycję **Włącz**. Wyniki na liście można zidentyfikować, szukając wartości **ARO** w kolumnie **TYP KLASTRA**.

5. Jeśli masz istniejący obszar roboczy usługi Log Analytics w tej samej subskrypcji co klaster, na stronie Dołączanie do usługi **Container Insights** wybierz go z listy rozwijanej.  
    Lista wstępnie wybiera domyślny obszar roboczy i lokalizację, w których klaster jest wdrażany w subskrypcji.

    ![Włączanie monitorowania nie monitorowanych klastrów](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, wykonaj instrukcje z tematu Tworzenie obszaru roboczego [usługi Log Analytics.](../logs/quick-create-workspace.md) Pamiętaj, aby utworzyć obszar roboczy w tej samej subskrypcji, w ramach którego wdrożono klaster RedHat OpenShift.

Po włączeniu monitorowania wyświetlenie metryk kondycji klastra może potrwać około 15 minut.

### <a name="enable-using-an-azure-resource-manager-template"></a>Włączanie przy użyciu Azure Resource Manager szablonu

Ta metoda zawiera dwa szablony JSON. Jeden szablon określa konfigurację włączania monitorowania, a drugi zawiera wartości parametrów, które można skonfigurować w celu określenia następujących wartości:

- Identyfikator zasobu klastra Azure RedHat OpenShift.

- Grupa zasobów, w która jest wdrażany klaster.

- Obszar roboczy usługi Log Analytics. Aby dowiedzieć się, jak uzyskać te informacje, zobacz Identyfikowanie identyfikatora obszaru roboczego usługi [Log Analytics.](#identify-your-log-analytics-workspace-id)

Jeśli nie masz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

- [Wdrażanie zasobów przy użyciu szablonów Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się używać interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i używać go lokalnie. Musisz mieć uruchomiony interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować swoją wersję, `az --version` uruchom . Jeśli musisz zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

1. Pobierz plik szablonu i parametru, aby zaktualizować klaster przy użyciu dodatku do monitorowania przy użyciu następujących poleceń:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Logowanie do platformy Azure

    ```azurecli
    az login
    ```

    Jeśli masz dostęp do wielu subskrypcji, uruchom zastąpienie `az account set -s {subscription ID}` `{subscription ID}` subskrypcją, której chcesz użyć.

3. Określ subskrypcję klastra usługi Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Uruchom następujące polecenie, aby zidentyfikować lokalizację klastra i identyfikator zasobu:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Edytuj plik parametrów JSON **iexistingClusterParam.jswartości** *aroResourceId* *i aroResourceLocation.* Wartość **workspaceResourceId** to pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, który zawiera nazwę obszaru roboczego.

6. Aby wdrożyć za pomocą interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Dane wyjściowe są podobne do następujących:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Następne kroki

- Po włączeniu monitorowania w celu zbierania kondycji i wykorzystania zasobów klastra RedHat OpenShift i uruchomionych na nich obciążeń dowiedz się, jak używać [usługi](container-insights-analyze.md) Container Insights.

- Domyślnie konteneryzowany agent zbiera dzienniki kontenerów stdout/stderr wszystkich kontenerów uruchomionych we wszystkich przestrzeniach nazw z wyjątkiem kube-system. Aby skonfigurować zbieranie dzienników kontenerów specyficzne dla określonej przestrzeni nazw lub przestrzeni nazw, przejrzyj konfigurację agenta [usługi Container Insights,](container-insights-agent-config.md) aby skonfigurować żądane ustawienia zbierania danych w pliku konfiguracji ConfigMap.

- Aby zdyskułować i przeanalizować metryki Prometheus z klastra, zapoznaj się z konfigurowaniem wycinki metryk [Prometheus](container-insights-prometheus-integration.md)

- Aby dowiedzieć się, jak zatrzymać monitorowanie klastra za pomocą usługi Container Insights, zobacz [Jak zatrzymać](./container-insights-optout-openshift-v3.md)monitorowanie klastra Azure Red Hat OpenShift klastra .