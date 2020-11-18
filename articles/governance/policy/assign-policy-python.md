---
title: 'Szybki Start: nowe przypisanie zasad przy użyciu języka Python'
description: W tym przewodniku szybki start utworzysz przypisanie Azure Policy w celu zidentyfikowania niezgodnych zasobów przy użyciu języka Python.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: aa67b3a2bae2beb62daa6e64871411ee760f802b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832204"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu języka Python

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. W tym przewodniku Szybki start utworzysz przypisanie zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu zidentyfikujesz maszyny wirtualne, które nie są _zgodne_.

Biblioteka języka Python służy do zarządzania zasobami platformy Azure z poziomu wiersza polecenia lub skryptów. W tym przewodniku wyjaśniono, jak utworzyć przypisanie zasad przy użyciu biblioteki języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Dodawanie biblioteki zasad

Aby włączyć środowisko Python do pracy z Azure Policy, należy dodać bibliotekę. Ta biblioteka działa wszędzie tam, gdzie można korzystać z języka Python, [w tym bash w systemie Windows 10](/windows/wsl/install-win10) lub zainstalowanym lokalnie.

1. Sprawdź, czy jest zainstalowana najnowsza wersja języka Python (co najmniej **3,8**). Jeśli nie jest jeszcze zainstalowany, Pobierz go pod adresem [Python.org](https://www.python.org/downloads/).

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.5.1**). Jeśli nie jest jeszcze zainstalowana, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Interfejs wiersza polecenia platformy Azure jest wymagany, aby można było włączyć w języku Python użycie **uwierzytelniania opartego na interfejsie wiersza polecenia** w poniższych przykładach. Aby uzyskać informacje o innych opcjach, zobacz [uwierzytelnianie przy użyciu bibliotek zarządzania platformy Azure dla języka Python](/azure/developer/python/azure-sdk-authenticate).

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az login
   ```

1. W wybranym środowisku języka Python Zainstaluj wymagane biblioteki na potrzeby grafu zasobów platformy Azure:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Jeśli język Python jest zainstalowany dla wszystkich użytkowników, polecenia te należy uruchomić z poziomu konsoli z podwyższonym poziomem uprawnień.

1. Sprawdź, czy biblioteki zostały zainstalowane. `azure-mgmt-policyinsights` powinna być **0.5.0** lub wyższa, `azure-mgmt-resource` powinna być **9.0.0** lub większa i `azure-cli-core` powinna być **2.5.0** lub wyższa.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz **maszyny wirtualne inspekcji, które nie używają definicji Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Ta definicja zasad identyfikuje zasoby, które nie spełniają warunków określonych w definicji zasad.

Uruchom następujący kod, aby utworzyć nowe przypisanie zasad:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

Poprzednie polecenia korzystają z następujących informacji:

Szczegóły przypisania:
- **display_name** — nazwa wyświetlana przypisania zasad. W takim przypadku używasz _maszyn wirtualnych inspekcji bez przypisania dysków zarządzanych_.
- **policy_definition_id** — ścieżka definicji zasad, na podstawie której tworzysz przypisanie. W takim przypadku jest IDENTYFIKATORem _maszyn wirtualnych inspekcji definicji zasad, które nie korzystają z dysków zarządzanych_. W tym przykładzie definicja zasad jest wbudowana, a ścieżka nie zawiera informacji o grupie zarządzania lub subskrypcji.
- **zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może ona przedziały od grupy zarządzania do pojedynczego zasobu. Pamiętaj, aby zastąpić `{scope}` jednym z następujących wzorców:
  - Grupa zarządzania: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Ramach `/subscriptions/{subscriptionId}`
  - Grupa zasobów: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Zasoby `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **Opis** — dokładniejsze wyjaśnienie tego, czym są zasady lub dlaczego są przypisane do tego zakresu.

Tworzenie przypisania:

- Zakres — ten zakres określa miejsce, w którym ma zostać zapisane przypisanie zasad. Zakres ustawiony w szczegółach przypisania musi istnieć w tym zakresie.
- Nazwa —rzeczywista nazwa przypisania. W tym przykładzie użyto nazwy _audit-vm-manageddisks_.
- Przypisanie zasad — obiekt **PolicyAssignment** języka Python utworzony w poprzednim kroku.

Teraz możesz zidentyfikować niezgodne zasoby, aby zrozumieć stan zgodności środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Skorzystaj z poniższych informacji w celu zidentyfikowania zasobów niezgodnych z utworzonym przypisaniem zasad. Uruchom następujący kod:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

Zamień na `{subscriptionId}` subskrypcję, dla której chcesz zobaczyć wyniki zgodności dla tego przypisania zasad. Listę innych zakresów i sposobów podsumowywania danych można znaleźć w temacie [metody State Policy](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

Wyniki powinny wyglądać podobnie do następujących:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Wyniki są zgodne z informacjami wyświetlanymi na karcie **zgodność zasobów** w przypisaniu zasad w widoku Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące polecenie:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

Zamień na ten `{scope}` sam zakres, który został użyty do utworzenia przypisania zasad.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej o przypisywaniu definicji zasad w celu sprawdzenia, czy nowe zasoby są zgodne, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
