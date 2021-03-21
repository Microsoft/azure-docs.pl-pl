---
title: Jak zaktualizować usługi Container Insights dla metryk | Microsoft Docs
description: W tym artykule opisano sposób aktualizowania usługi Container Insights w celu włączenia funkcji metryk niestandardowych, która obsługuje eksplorowanie i zgłaszanie alertów dotyczących zagregowanych metryk.
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: edca7e4e8f6a9ea8dd9efdaafab8c906efd671b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708275"
---
# <a name="how-to-update-container-insights-to-enable-metrics"></a>Jak zaktualizować usługi Container Insights w celu włączenia metryk

Usługa Container Insights wprowadza obsługę zbierania metryk z usług Azure Kubernetes Services (AKS) i Azure ARC z włączonymi węzłami klastrów Kubernetes i piszą je w magazynie metryk Azure Monitor. Ta zmiana ma na celu dostarczenie ulepszonej osi czasu podczas prezentowania obliczeń agregacji (średni, Count, Max, min, sum) na wykresach wydajności, obsługę przypinania wykresów wydajności w Azure Portal pulpitów nawigacyjnych i obsługę alertów dotyczących metryk.

>[!NOTE]
>Ta funkcja nie obsługuje obecnie klastrów usługi Azure Red Hat OpenShift.
>

Następujące metryki są włączone w ramach tej funkcji:

| Przestrzeń nazw metryki | Metric | Opis |
|------------------|--------|-------------|
| Szczegółowe informacje. kontenery/węzły | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | Jako metryki *węzła* obejmują one *hosta* jako wymiar. Obejmują one również<br> Nazwa węzła jako wartość dla wymiaru *hosta* . |
| Szczegółowe informacje. kontenery/zasobniki | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | Jako *metryki* są to między innymi następujące: Dimensions-ControllerName, Kubernetes Namespace, Name, faz. |
| Szczegółowe informacje. kontenery/kontenery | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |
| Szczegółowe informacje. Container/persistentvolumes | pvUsageExceededPercentage | |

Aby zapewnić obsługę tych nowych funkcji, w wydaniu zostanie uwzględniony nowy Agent kontenerów w wersji **Microsoft/OMS: ciprod05262020** for AKS i Version **Microsoft/OMS: Ciprod09252020** for Azure ARC z włączonymi klastrami Kubernetes. Nowe wdrożenia programu AKS automatycznie uwzględniają tę zmianę konfiguracji i możliwości. Aktualizowanie klastra w celu obsługi tej funkcji można wykonać z poziomu Azure Portal, Azure PowerShell lub przy użyciu interfejsu wiersza polecenia platformy Azure. Przy użyciu Azure PowerShell i interfejsu wiersza polecenia. Możesz włączyć ten klaster lub wszystkie klastry w subskrypcji.

Każdy proces przypisuje rolę **wydawcy metryk monitorowania** do jednostki usługi klastra lub do pliku MSI przypisanego użytkownikowi dla dodatku monitorowania, aby dane zbierane przez agenta mogły być publikowane w zasobach klastrów. Wydawca metryk monitorowania ma uprawnienia tylko do wypychania metryk do zasobu, nie może zmienić stanu, zaktualizować zasobu ani odczytać jakichkolwiek danych. Aby uzyskać więcej informacji na temat roli, zobacz [monitorowanie metryk rola wydawcy](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher). Wymagania roli wydawcy metryk monitorowania nie mają zastosowania do klastrów Kubernetes z obsługą usługi Azure Arc.

> [!IMPORTANT]
> Uaktualnienie nie jest wymagane dla klastrów Kubernetes z obsługą usługi Azure ARC, ponieważ ma już minimalną wymaganą wersję agenta.

## <a name="prerequisites"></a>Wymagania wstępne

Przed aktualizacją klastra Potwierdź następujące kwestie:

* Metryki niestandardowe są dostępne tylko w ramach podzestawów regionów świadczenia usługi Azure. Lista obsługiwanych regionów jest udokumentowana [tutaj](../essentials/metrics-custom-overview.md#supported-regions).

* Musisz być członkiem roli **[właściciela](../../role-based-access-control/built-in-roles.md#owner)** w zasobie klastra AKS, aby włączyć kolekcję metryk wydajności niestandardowych węzła i pod. To wymaganie nie dotyczy klastrów Kubernetes z obsługą usługi Azure Arc.

Jeśli zdecydujesz się na korzystanie z interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Uaktualnianie klastra z Azure Portal

W przypadku istniejących klastrów AKS monitorowanych przez usługi Container Insights po wybraniu klastra w celu wyświetlenia jego kondycji z widoku wiele klastrów w Azure Monitor lub bezpośrednio z klastra, wybierając pozycję **szczegółowe** dane w okienku po lewej stronie, zobaczysz transparent w górnej części portalu.

![Transparent uaktualniania klastra AKS w Azure Portal](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Kliknięcie pozycji **Włącz** spowoduje zainicjowanie procesu uaktualniania klastra. Ten proces może potrwać kilka sekund, a postęp można śledzić w obszarze powiadomienia z menu.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Uaktualnianie wszystkich klastrów przy użyciu bash w powłoce poleceń platformy Azure

Wykonaj następujące kroki, aby zaktualizować wszystkie klastry w ramach subskrypcji przy użyciu usługi Bash w usłudze Azure Command Shell.

1. Uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.  Edytuj wartość identyfikatora **subskrypcji** przy użyciu wartości ze strony **Przegląd AKS** dla klastra AKS.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    Zmiana konfiguracji może potrwać kilka sekund. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Uaktualnianie na klaster przy użyciu interfejsu wiersza polecenia platformy Azure

Wykonaj następujące kroki, aby zaktualizować konkretny klaster w ramach subskrypcji przy użyciu interfejsu wiersza polecenia platformy Azure.

1. Uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure. Edytuj wartości identyfikatora **subskrypcji**, **resourceGroupName** i **ClusterName** przy użyciu wartości na stronie **Przegląd AKS** dla klastra AKS.  Aby uzyskać wartość **clientIdOfSPN**, jest zwracana po uruchomieniu polecenia, `az aks show` jak pokazano w poniższym przykładzie.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Aby uzyskać wartość dla **clientIdOfSPNOrMsi**, możesz uruchomić polecenie, `az aks show` jak pokazano w poniższym przykładzie. Jeśli obiekt **servicePrincipalProfile** ma prawidłową wartość *ClientID* , można go użyć. W przeciwnym razie, jeśli jest ustawiona na *MSI*, należy przekazać ClientID z `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Uaktualnij wszystkie klastry przy użyciu Azure PowerShell

Wykonaj następujące kroki, aby zaktualizować wszystkie klastry w subskrypcji przy użyciu Azure PowerShell.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) skrypt **mdm_onboarding_atscale.ps1** i Zapisz go w folderze lokalnym z naszego repozytorium GitHub.
2. Uruchom następujące polecenie, używając Azure PowerShell.  Edytuj wartość identyfikatora **subskrypcji** przy użyciu wartości ze strony **Przegląd AKS** dla klastra AKS.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    Zmiana konfiguracji może potrwać kilka sekund. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Uaktualnianie na klaster przy użyciu Azure PowerShell

Wykonaj następujące kroki, aby zaktualizować konkretny klaster przy użyciu Azure PowerShell.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) skrypt **mdm_onboarding.ps1** i Zapisz go w folderze lokalnym z naszego repozytorium GitHub.

2. Uruchom następujące polecenie, używając Azure PowerShell. Edytuj wartości identyfikatora **subskrypcji**, **resourceGroupName** i **ClusterName** przy użyciu wartości na stronie **Przegląd AKS** dla klastra AKS.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    Zmiana konfiguracji może potrwać kilka sekund. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Weryfikuj aktualizację

Po zainicjowaniu aktualizacji przy użyciu jednej z opisanych wcześniej metod można użyć Eksploratora metryk Azure Monitor i sprawdzić, czy w **przestrzeni nazw metryk** są wyświetlane **szczegółowe informacje** . Jeśli tak jest, możesz zacząć konfigurować [alerty metryczne](../alerts/alerts-metric.md) lub przypinać wykresy do [pulpitów nawigacyjnych](../../azure-portal/azure-portal-dashboards.md).  
