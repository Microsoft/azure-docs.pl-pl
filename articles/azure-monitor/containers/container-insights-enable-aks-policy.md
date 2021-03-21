---
title: Włącz dodatek monitorowania AKS przy użyciu Azure Policy
description: Opisuje sposób włączania AKS monitorowania przy użyciu zasad niestandardowych platformy Azure.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713902"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Włącz dodatek monitorowania AKS przy użyciu Azure Policy
W tym artykule opisano sposób włączania AKS monitorowania przy użyciu zasad niestandardowych platformy Azure. Zasady niestandardowe z dodatkiem do monitorowania można przypisywać w zakresie subskrypcji lub grupy zasobów. Jeśli klaster usługi Azure Log Analytics Workspace i AKS znajdują się w różnych subskrypcjach, zarządzana tożsamość używana przez przypisanie zasad musi mieć uprawnienia roli wymagane zarówno w przypadku subskrypcji, jak i najmniejszej ilości zasobów w obszarze roboczym Log Analytics. Analogicznie, jeśli zasady są objęte zakresem grupy zasobów, tożsamość zarządzana powinna mieć uprawnienia roli wymagane w obszarze roboczym Log Analytics, jeśli obszar roboczy nie znajduje się w wybranym zakresie grupy zasobów.

Dodatek monitorowania wymaga następujących ról w zarządzanej tożsamości używanej przez Azure Policy:

 - [Azure-Kubernetes-Service-współautor-role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [log-Analytics — współautor](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Tworzenie i przypisywanie definicji zasad za pomocą Azure Portal

### <a name="create-policy-definition"></a>Utwórz definicję zasad

1. Pobierz definicję zasad niestandardowych platformy Azure, aby włączyć dodatek monitorowania AKS.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. Przejdź do https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions i Utwórz definicję zasad, podając następujące szczegóły w oknie dialogowym Tworzenie definicji zasad.
 
    - **Lokalizacja definicji**: wybierz subskrypcję platformy Azure, w której ma zostać zapisana definicja zasad.
    - **Name**: *(wersja zapoznawcza) AKS-monitoring-dodatek*
    - **Opis**: *zasady niestandardowe platformy Azure umożliwiające włączenie dodatku monitorowania do klastrów usługi Azure Kubernetes w określonym zakresie*
    - **Kategoria**: wybierz opcję *Użyj istniejącej* i wybierz pozycję *Kubernetes* z listy rozwijanej.
    - **Reguła zasad**: Usuń istniejące przykładowe reguły i skopiuj zawartość *azurepolicy.js* pobranej w kroku #1 powyżej.

### <a name="assign-policy-definition-to-specified-scope"></a>Przypisz definicję zasad do określonego zakresu

> [!NOTE]
>  Tożsamość zarządzana zostanie utworzona automatycznie i zostanie przypisana określona rola w definicji zasad.

1. Wybierz właśnie utworzoną *AKS monitorowania definicji zasad (wersja zapoznawcza)* .
4. Kliknij przycisk *Assign** * i określ **zakres** , do którego mają być przypisane zasady. 
5. Kliknij przycisk **dalej** i podaj identyfikator zasobu obszaru roboczego usługi Azure log Analytics. Identyfikator zasobu powinien mieć format `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. Utwórz zadanie korygowania w przypadku, gdy chcesz zastosować zasady do istniejących klastrów AKS w wybranym zakresie.
7. Kliknij przycisk **Przegląd + Utwórz** opcję, aby utworzyć przypisanie zasad.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Tworzenie i przypisywanie definicji zasad przy użyciu interfejsu wiersza polecenia platformy Azure

### <a name="create-policy-definition"></a>Utwórz definicję zasad

1. Pobieranie niestandardowych reguł definicji zasad i plików parametrów platformy Azure za pomocą następujących poleceń:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Utwórz definicję zasad przy użyciu następującego polecenia:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Przypisz definicję zasad do określonego zakresu

- Utwórz przypisanie zasad przy użyciu następującego polecenia:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure Policy](../../governance/policy/overview.md).
- Dowiedz się, jak [działa korygowanie zabezpieczeń](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).
- Dowiedz się więcej o usłudze [Container Insights](./container-insights-overview.md).
- Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).