---
title: Jak aktualizować Azure Monitor kontenerów dla metryk | Microsoft Docs
description: W tym artykule opisano sposób aktualizowania Azure Monitor dla kontenerów w celu włączenia funkcji metryk niestandardowych, która obsługuje eksplorowanie i zgłaszanie alertów dotyczących zagregowanych metryk.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: d299fc5e6b0c41188fac1fa19bb66387263c12e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84298265"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Jak zaktualizować usługę Azure Monitor dla kontenerów w celu włączenia metryk

Azure Monitor dla kontenerów wprowadza obsługę zbierania metryk z węzłów klastrów usługi Azure Kubernetes Services (AKS) i ich tworzenia do magazynu metryk Azure Monitor. Ta zmiana ma na celu dostarczenie ulepszonej osi czasu podczas prezentowania obliczeń agregacji (średni, Count, Max, min, sum) na wykresach wydajności, obsługę przypinania wykresów wydajności w Azure Portal pulpitów nawigacyjnych i obsługę alertów dotyczących metryk.

>[!NOTE]
>Ta funkcja nie obsługuje obecnie klastrów usługi Azure Red Hat OpenShift.
>

Następujące metryki są włączone w ramach tej funkcji:

| Przestrzeń nazw metryki | Metric | Opis |
|------------------|--------|-------------|
| Szczegółowe informacje. kontenery/węzły | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount | Są to metryki *węzłów* i obejmują *hosta* jako wymiar, a także zawierają<br> Nazwa węzła jako wartość dla wymiaru *hosta* . |
| Szczegółowe informacje. kontenery/zasobniki | podCount | Są to *metryki* poniżej i zawierają następujące elementy: Dimensions-ControllerName, Kubernetes Namespace, Name, faz. |

Aktualizowanie klastra w celu obsługi tych nowych funkcji można wykonać z poziomu Azure Portal, Azure PowerShell lub przy użyciu interfejsu wiersza polecenia platformy Azure. Za pomocą Azure PowerShell i interfejsu wiersza polecenia można włączyć tę opcję dla klastra lub dla wszystkich klastrów w ramach subskrypcji. Nowe wdrożenia programu AKS automatycznie uwzględniają tę zmianę konfiguracji i możliwości.

Każdy proces przypisuje rolę **wydawcy metryk monitorowania** do jednostki usługi klastra lub do pliku MSI przypisanego użytkownikowi dla dodatku monitorowania, aby dane zbierane przez agenta mogły być publikowane w zasobach klastrów. Wydawca metryk monitorowania ma uprawnienia tylko do wypychania metryk do zasobu, nie może zmienić stanu, zaktualizować zasobu ani odczytać jakichkolwiek danych. Aby uzyskać więcej informacji na temat roli, zobacz [monitorowanie metryk rola wydawcy](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem Potwierdź następujące kwestie:

* Metryki niestandardowe są dostępne tylko w ramach podzestawów regionów świadczenia usługi Azure. Lista obsługiwanych regionów jest udokumentowana [tutaj](../platform/metrics-custom-overview.md#supported-regions).
* Musisz być członkiem roli **[właściciela](../../role-based-access-control/built-in-roles.md#owner)** w zasobie klastra AKS, aby włączyć kolekcję metryk wydajności niestandardowych węzła i pod. 

Jeśli zdecydujesz się na korzystanie z interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Uaktualnianie klastra z Azure Portal

W przypadku istniejących klastrów AKS monitorowanych przez Azure Monitor kontenerów, po wybraniu klastra w celu wyświetlenia jego kondycji z widoku wiele klastrów w Azure Monitor lub bezpośrednio z klastra przez wybranie **szczegółowych** informacji z okienka po lewej stronie, w górnej części portalu powinien zostać wyświetlony transparent.

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

1. Uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure. Edytuj wartości identyfikatora **subskrypcji**, **resourceGroupName**i **ClusterName** przy użyciu wartości na stronie **Przegląd AKS** dla klastra AKS.  Aby uzyskać wartość **clientIdOfSPN**, jest zwracana po uruchomieniu polecenia, `az aks show` jak pokazano w poniższym przykładzie.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Aby uzyskać wartość dla **clientIdOfSPNOrMsi**, możesz uruchomić polecenie, `az aks show` jak pokazano w poniższym przykładzie. Jeśli obiekt **servicePrincipalProfile** ma prawidłową wartość *ClientID* , można użyć tej wartości. W przeciwnym razie, jeśli jest ustawiona na *MSI*, należy przekazać ClientID z `addonProfiles.omsagent.identity.clientId` .

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

2. Uruchom następujące polecenie, używając Azure PowerShell. Edytuj wartości identyfikatora **subskrypcji**, **resourceGroupName**i **ClusterName** przy użyciu wartości na stronie **Przegląd AKS** dla klastra AKS.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    Zmiana konfiguracji może potrwać kilka sekund. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Weryfikuj aktualizację

Po zainicjowaniu aktualizacji przy użyciu jednej z opisanych wcześniej metod można użyć Eksploratora metryk Azure Monitor i sprawdzić, czy w **przestrzeni nazw metryk** są wyświetlane **szczegółowe informacje** . Jeśli tak jest, oznacza to, że można rozpocząć konfigurowanie [alertów dotyczących metryk](../platform/alerts-metric.md) lub Przypinanie wykresów do [pulpitów nawigacyjnych](../../azure-portal/azure-portal-dashboards.md).  
