---
title: Tworzenie obszarów roboczych za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć interfejsu wiersza polecenia platformy Azure do utworzenia nowego obszaru roboczego usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 9a7d0b75140c50df61ff63f350e5b312a6a684c7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617779"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Tworzenie obszaru roboczego dla usługi Azure Machine Learning za pomocą interfejsu wiersza polecenia platformy Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak utworzyć obszar roboczy usługi Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure. Narzędzie interfejsu wiersza polecenia platformy Azure udostępnia polecenia służące do zarządzania zasobami platformy Azure. Rozszerzenie uczenia maszynowego do interfejsu wiersza polecenia do pracy z zasobami usługi Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).

* Aby użyć poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego,** potrzebujesz [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Jeśli używasz [usługi Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)interfejs wiersza polecenia jest dostępny za pośrednictwem przeglądarki i mieszka w chmurze.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Łączenie interfejsu wiersza polecenia z subskrypcją platformy Azure

> [!IMPORTANT]
> Jeśli używasz usługi Azure Cloud Shell, możesz pominąć tę sekcję. Powłoka w chmurze automatycznie uwierzytelnia cię przy użyciu konta, które logujesz się do subskrypcji platformy Azure.

Istnieje kilka sposobów, które można uwierzytelnić do subskrypcji platformy Azure z interfejsu wiersza polecenia. Najbardziej podstawowe jest interaktywne uwierzytelnienie za pomocą przeglądarki. Aby uwierzytelnić się interaktywnie, otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadzanie kodu autoryzacyjnego.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Aby uzyskać inne metody uwierzytelniania, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Instalowanie rozszerzenia uczenia maszynowego

Aby zainstalować rozszerzenie uczenia maszynowego, użyj następującego polecenia:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy usługi Azure Machine Learning opiera się na następujących usługach lub jednostkach platformy Azure:

> [!IMPORTANT]
> Jeśli nie określisz istniejącej usługi platformy Azure, zostanie utworzona automatycznie podczas tworzenia obszaru roboczego. Należy zawsze określić grupę zasobów.

| Usługa | Parametr do określania istniejącego wystąpienia |
| ---- | ---- |
| **Grupa zasobów platformy Azure** | `-g <resource-group-name>`
| **Konto usługi Azure Storage** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **W usłudze Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Obszar roboczy usługi Azure Machine Learning musi zostać utworzony wewnątrz grupy zasobów. Można użyć istniejącej grupy zasobów lub utworzyć nową. Aby __utworzyć nową grupę zasobów,__ użyj następującego polecenia. Zamień `<resource-group-name>` na nazwę używana dla tej grupy zasobów. Zamień `<location>` region platformy Azure do użycia dla tej grupy zasobów:

> [!TIP]
> Należy wybrać region, w którym usługa Azure Machine Learning jest dostępna. Aby uzyskać więcej informacji, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpowiedź z tego polecenia jest podobna do następującej JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Aby uzyskać więcej informacji na temat pracy z grupami zasobów, zobacz [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Automatyczne tworzenie wymaganych zasobów

Aby utworzyć nowy obszar __roboczy,__ w którym usługi są tworzone automatycznie, użyj następującego polecenia:

> [!TIP]
> Polecenia w tej sekcji tworzą podstawowy obszar roboczy edycji. Aby utworzyć obszar roboczy `--sku enterprise` przedsiębiorstwa, `az ml workspace create` użyj przełącznika z poleceniem. Aby uzyskać więcej informacji na temat wersji usługi Azure Machine Learning, zobacz [Co to jest usługa Azure Machine Learning.](overview-what-is-azure-ml.md#sku)

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Nazwa obszaru roboczego jest niewrażliwa na wielkości liter.

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>Korzystanie z istniejących zasobów

Aby utworzyć obszar roboczy, który używa istniejących zasobów, należy podać identyfikator zasobów. Użyj następujących poleceń, aby uzyskać identyfikator dla usług:

> [!IMPORTANT]
> Nie trzeba określać wszystkie istniejące zasoby. Można określić jeden lub więcej. Na przykład można określić istniejące konto magazynu, a obszar roboczy utworzy inne zasoby.

+ **Konto usługi Azure Storage:**`az storage account show --name <storage-account-name> --query "id"`

    Odpowiedź z tego polecenia jest podobna do następującego tekstu i jest identyfikatorem konta magazynu:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Usługa Azure Application Insights**:

    1. Zainstaluj rozszerzenie wglądu w aplikacje:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Uzyskaj identyfikator usługi wglądu w aplikacje:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Odpowiedź z tego polecenia jest podobna do następującego tekstu i jest identyfikatorem usługi aplikacji insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Usługa Azure Key Vault**:`az keyvault show --name <key-vault-name> --query "ID"`

    Odpowiedź z tego polecenia jest podobna do następującego tekstu i jest identyfikatorem magazynu kluczy:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Rejestr kontenerów platformy Azure:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Odpowiedź z tego polecenia jest podobna do następującego tekstu i jest identyfikatorem rejestru kontenerów:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Rejestr kontenerów musi mieć [włączone konto administratora,](/azure/container-registry/container-registry-authentication#admin-account) zanim będzie można go używać z obszarem roboczym usługi Azure Machine Learning.

Po uzyskaniu identyfikatorów zasobów, których chcesz użyć w obszarze roboczym, użyj `az workspace create -w <workspace-name> -g <resource-group-name>` polecenia podstawowego i dodaj parametry i identyfikatory dla istniejących zasobów. Na przykład następujące polecenie tworzy obszar roboczy, który używa istniejącego rejestru kontenerów:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Lista obszarów roboczych

Aby wyświetlić listę wszystkich obszarów roboczych dla subskrypcji platformy Azure, użyj następującego polecenia:

```azurecli-interactive
az ml workspace list
```

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Aby uzyskać więcej informacji, zobacz [dokumentację listy obszaru roboczego az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)

## <a name="get-workspace-information"></a>Uzyskaj informacje o obszarze roboczym

Aby uzyskać informacje o obszarze roboczym, użyj następującego polecenia:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Aby uzyskać więcej informacji, zobacz [az ml workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) dokumentacji.

## <a name="update-a-workspace"></a>Aktualizowanie obszaru roboczego

Aby zaktualizować obszar roboczy, użyj następującego polecenia:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Aby uzyskać więcej informacji, zobacz dokumentację [aktualizacji obszaru roboczego az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)

## <a name="share-a-workspace-with-another-user"></a>Udostępnianie obszaru roboczego innemu użytkownikowi

Aby udostępnić obszar roboczy innemu użytkownikowi w ramach subskrypcji, użyj następującego polecenia:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (RBAC) za pomocą usługi Azure Machine Learning, zobacz [Zarządzanie użytkownikami i rolami](how-to-assign-roles.md).

Aby uzyskać więcej informacji, zobacz dokumentację [udziału obszaru roboczego az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)

## <a name="sync-keys-for-dependent-resources"></a>Synchronizowanie kluczy dla zasobów zależnych

Jeśli zmienisz klucze dostępu dla jednego z zasobów używanych przez obszar roboczy, użyj następującego polecenia, aby zsynchronizować nowe klucze z obszarem roboczym:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Aby uzyskać więcej informacji na temat zmieniania kluczy, zobacz [Ponowne generowanie kluczy dostępu do magazynu](how-to-change-storage-access-key.md).

Aby uzyskać więcej informacji, zobacz [dokumentację klawiszy synchronizacji obszaru roboczego az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Aby usunąć obszar roboczy po tym, jak nie jest już potrzebny, użyj następującego polecenia:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Usunięcie obszaru roboczego nie powoduje usunięcia wglądu aplikacji, konta magazynu, magazynu kluczy ani rejestru kontenerów używanego przez obszar roboczy.

Można również usunąć grupę zasobów, która usuwa obszar roboczy i wszystkie inne zasoby platformy Azure w grupie zasobów. Aby usunąć grupę zasobów, użyj następującego polecenia:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Aby uzyskać więcej informacji, zobacz dokumentację [usuwania obszaru roboczego az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Przenoszenie obszaru roboczego

> [!WARNING]
> Przenoszenie obszaru roboczego usługi Azure Machine Learning do innej subskrypcji lub przenoszenie subskrypcji właścicielskiej do nowej dzierżawy nie jest obsługiwane. Może to spowodować błędy.

### <a name="deleting-the-azure-container-registry"></a>Usuwanie rejestru kontenerów platformy Azure

Obszar roboczy usługi Azure Machine Learning używa usługi Azure Container Registry (ACR) dla niektórych operacji. Automatycznie utworzy wystąpienie ACR, gdy po raz pierwszy będzie potrzebne.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozszerzenia interfejsu wiersza polecenia platformy Azure dla uczenia maszynowego, zobacz [dokumentację az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)
