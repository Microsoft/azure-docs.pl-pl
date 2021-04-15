---
title: 'Szybki start: nowe przypisanie zasad przy użyciu języka Python'
description: W tym przewodniku Szybki start użyjemy języka Python do utworzenia Azure Policy w celu zidentyfikowania niezgodnych zasobów.
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 2c9a0d6bb00d82748505304264aeaefa409c4b06
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379404"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Szybki start: tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu języka Python

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. W tym przewodniku Szybki start utworzysz przypisanie zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu zidentyfikujesz maszyny wirtualne, które są _niezgodne._

Biblioteka języka Python służy do zarządzania zasobami platformy Azure z wiersza polecenia lub w skryptach. W tym przewodniku wyjaśniono, jak utworzyć przypisanie zasad za pomocą biblioteki języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Dodawanie biblioteki zasad

Aby umożliwić językowi Python pracę Azure Policy, należy dodać bibliotekę. Ta biblioteka działa wszędzie tam, gdzie można używać języka Python, w tym [bash na Windows 10](/windows/wsl/install-win10) lub lokalnie zainstalowany.

1. Sprawdź, czy jest zainstalowany najnowszy język Python (co najmniej **3.8).** Jeśli nie jest jeszcze zainstalowany, pobierz go ze [strony](https://www.python.org/downloads/)Python.org .

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.5.1).** Jeśli nie jest jeszcze zainstalowany, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Interfejs wiersza polecenia platformy Azure jest wymagany, aby umożliwić językowi Python korzystanie **z** uwierzytelniania opartego na interfejsie wiersza polecenia w poniższych przykładach. Aby uzyskać informacje o innych opcjach, zobacz Authenticate using the Azure management libraries for Python (Uwierzytelnianie [za pomocą bibliotek zarządzania platformy Azure dla języka Python).](/azure/developer/python/azure-sdk-authenticate)

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az login
   ```

1. W swoim środowisku języka Python zainstaluj wymagane biblioteki dla Azure Policy:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Jeśli język Python jest zainstalowany dla wszystkich użytkowników, te polecenia muszą być uruchamiane z poziomu konsoli z podwyższonym poziomem uprawnień.

1. Sprawdź, czy biblioteki zostały zainstalowane. `azure-mgmt-policyinsights` powinna mieć **wartość 0.5.0** lub wyższą, powinna mieć wartość `azure-mgmt-resource` **9.0.0** lub wyższą i powinna mieć wartość `azure-cli-core` **2.5.0 lub** wyższą.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku Szybki start utworzysz przypisanie zasad i przypiszesz definicję **Audit VMs that do not use managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Ta definicja zasad identyfikuje zasoby, które nie spełniają warunków określonych w definicji zasad.

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
- **display_name** — nazwa wyświetlana przypisania zasad. W tym przypadku używasz przypisania Audit VMs without managed disks Assignment (Przejmij maszyny _wirtualne bez dysków zarządzanych)._
- **policy_definition_id** — ścieżka definicji zasad, na podstawie której tworzysz przypisanie. W tym przypadku jest to identyfikator definicji zasad Audit VMs that do not use managed disks (Przejmij maszyny _wirtualne, które nie korzystają z dysków zarządzanych)._ W tym przykładzie definicja zasad jest wbudowana, a ścieżka nie zawiera informacji o grupie zarządzania ani subskrypcji.
- **zakres** — zakres określa, w jakich zasobach lub grupach zasobów jest wymuszane przypisanie zasad. Może to być zakres od grupy zarządzania do pojedynczego zasobu. Pamiętaj, aby zastąpić `{scope}` jeden z następujących wzorców:
  - Grupa zarządzania: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Subskrypcje: `/subscriptions/{subscriptionId}`
  - Grupa zasobów: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Zasobów: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description** — bardziej szczegółowe wyjaśnienie działania zasad i przyczyny ich przypisania do tego zakresu.

Tworzenie przypisania:

- Zakres — ten zakres określa miejsce zapisania przypisania zasad. Zakres ustawiony w szczegółach przypisania musi istnieć w tym zakresie.
- Nazwa —rzeczywista nazwa przypisania. W tym przykładzie użyto nazwy _audit-vm-manageddisks_.
- Przypisanie zasad — obiekt **PolicyAssignment języka** Python utworzony w poprzednim kroku.

Teraz możesz zidentyfikować niezgodne zasoby, aby poznać stan zgodności środowiska.

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

Zastąp `{subscriptionId}` subskrypcją, dla której chcesz wyświetlić wyniki zgodności dla tego przypisania zasad. Aby uzyskać listę innych zakresów i sposobów podsumowania danych, zobacz [Metody stanu zasad](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

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

Wyniki są zgodne z tym, co widzisz na karcie **Zgodność** zasobów przypisania zasad w widoku Azure Portal zasobów.

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

Zastąp `{scope}` wartości tym samym zakresem, który został użyty do utworzenia przypisania zasad.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej na temat przypisywania definicji zasad w celu sprawdzenia, czy nowe zasoby są zgodne, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
