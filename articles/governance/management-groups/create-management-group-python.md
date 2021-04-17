---
title: 'Szybki start: tworzenie grupy zarządzania przy użyciu języka Python'
description: W tym przewodniku Szybki start użyjemy języka Python do utworzenia grupy zarządzania w celu zorganizowania zasobów w hierarchię zasobów.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9aec47e067ca62f4902df2dafb6a5d6d50a26d0e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533169"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Szybki start: tworzenie grupy zarządzania przy użyciu języka Python

Grupy zarządzania to kontenery, które ułatwiają zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z usługami [Azure Policy](../policy/overview.md) i kontrolą dostępu opartą [na rolach platformy Azure.](../../role-based-access-control/overview.md) Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](overview.md)

Ukończenie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkową konfigurację grup zarządzania.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez przypisanego do niego uprawnienia do zapisu grupy zarządzania, jeśli ochrona hierarchii [nie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) jest włączona. Ta nowa grupa zarządzania staje się elementem [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) podrzędnym głównej grupy zarządzania lub domyślnej grupy zarządzania, a twórca ma przypisanie roli "Właściciel". Usługa grupy zarządzania umożliwia tę możliwość, dzięki czemu przypisania ról nie są wymagane na poziomie głównym. Żaden użytkownik nie ma dostępu do głównej grupy zarządzania po jej utworzeniu. Aby uniknąć przeszkód w znalezieniu administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Dodawanie Resource Graph biblioteki

Aby umożliwić językowi Python zarządzanie grupami zarządzania, należy dodać bibliotekę. Ta biblioteka działa wszędzie tam, gdzie można używać języka Python, w tym [bash na Windows 10](/windows/wsl/install-win10) lub lokalnie zainstalowany.

1. Sprawdź, czy jest zainstalowany najnowszy język Python (co najmniej **3.8).** Jeśli nie jest jeszcze zainstalowany, pobierz go ze [strony](https://www.python.org/downloads/)Python.org .

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.5.1).** Jeśli nie jest jeszcze zainstalowany, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Interfejs wiersza polecenia platformy Azure jest wymagany, aby umożliwić językowi Python korzystanie **z** uwierzytelniania opartego na interfejsie wiersza polecenia w poniższych przykładach. Aby uzyskać informacje o innych opcjach, zobacz Authenticate using the Azure management libraries for Python (Uwierzytelnianie [za pomocą bibliotek zarządzania platformy Azure dla języka Python).](/azure/developer/python/azure-sdk-authenticate)

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az login
   ```

1. W swoim środowisku języka Python zainstaluj wymagane biblioteki dla grup zarządzania:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Jeśli język Python jest zainstalowany dla wszystkich użytkowników, te polecenia muszą być uruchamiane z poziomu konsoli z podwyższonym poziomem uprawnień.

1. Sprawdź, czy biblioteki zostały zainstalowane. `azure-mgmt-managementgroups` powinna mieć **wartość 0.2.0** lub wyższą, powinna mieć wartość `azure-mgmt-resource` **9.0.0** lub wyższą i powinna mieć wartość `azure-cli-core` **2.5.0 lub** wyższą.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Tworzenie grupy zarządzania

1. Utwórz skrypt języka Python i zapisz następujące źródło jako `mgCreate.py` :

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure za pomocą polecenia `az login` .

1. Wprowadź następujące polecenie w terminalu:

   ```bash
   py mgCreate.py
   ```

Wynikiem utworzenia grupy zarządzania jest dane wyjściowe w konsoli jako `LROPoller` obiekt .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć zainstalowane biblioteki ze środowiska języka Python, możesz to zrobić za pomocą następującego polecenia:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami przy użyciu grup zarządzania](./manage.md)
