---
title: Tworzenie obszarów roboczych przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć nowy obszar roboczy Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 06/19/2020
ms.openlocfilehash: f22ef4d1ebd9c4d3c226556c4ef28a873edd80ea
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119260"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Tworzenie obszaru roboczego dla Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak utworzyć obszar roboczy Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure. Interfejs wiersza polecenia platformy Azure umożliwia zarządzanie zasobami platformy Azure. Rozszerzenie uczenia maszynowego do interfejsu wiersza polecenia dostarcza poleceń do pracy z zasobami Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* Aby korzystać z poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego**, wymagany jest [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Jeśli używasz [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), interfejs wiersza polecenia jest dostępny za pomocą przeglądarki i przebywa w chmurze.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Łączenie interfejsu wiersza polecenia z subskrypcją platformy Azure

> [!IMPORTANT]
> Jeśli używasz Azure Cloud Shell, możesz pominąć tę sekcję. Usługa Cloud Shell automatycznie uwierzytelnia użytkownika przy użyciu konta, które loguje się do subskrypcji platformy Azure.

Istnieje kilka sposobów uwierzytelniania w ramach subskrypcji platformy Azure z poziomu interfejsu wiersza polecenia. Najbardziej podstawowa jest możliwość interakcyjnego uwierzytelniania przy użyciu przeglądarki. Aby uwierzytelnić interaktywnie, Otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadzanie kodu autoryzacji.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Aby poznać inne metody uwierzytelniania, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Zainstaluj rozszerzenie uczenia maszynowego

Aby zainstalować rozszerzenie Uczenie maszynowe, użyj następującego polecenia:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning opiera się na następujących usługach lub jednostkach platformy Azure:

> [!IMPORTANT]
> Jeśli nie określisz istniejącej usługi platformy Azure, zostanie ona utworzona automatycznie podczas tworzenia obszaru roboczego. Zawsze należy określić grupę zasobów. Podczas dołączania własnego konta magazynu upewnij się, że jest włączona funkcja Azure BLOB i usługa Azure File oraz że hierarchiczna przestrzeń nazw (ADLS Gen 2) jest wyłączona. Możesz zawsze dołączyć własne konto magazynu później po utworzeniu obszaru roboczego jako magazynów danych.

| Usługa | Parametr określający istniejące wystąpienie |
| ---- | ---- |
| **Grupa zasobów platformy Azure** | `-g <resource-group-name>`
| **Konto usługi Azure Storage** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **W usłudze Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Obszar roboczy Azure Machine Learning należy utworzyć w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową. Aby __utworzyć nową grupę zasobów__, użyj następującego polecenia. Zamień na `<resource-group-name>` nazwę, która ma być używana dla tej grupy zasobów. Zamień na `<location>` region platformy Azure, który ma być używany dla tej grupy zasobów:

> [!TIP]
> Należy wybrać region, w którym Azure Machine Learning jest dostępna. Aby uzyskać więcej informacji, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpowiedź z tego polecenia jest podobna do następującej:

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

Aby uzyskać więcej informacji na temat pracy z grupami zasobów, zobacz [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Automatycznie twórz wymagane zasoby

Aby utworzyć nowy obszar roboczy, w którym __usługi są tworzone automatycznie__, użyj następującego polecenia:

> [!TIP]
> Polecenia w tej sekcji tworzą obszar roboczy wersja podstawowa. Aby utworzyć obszar roboczy przedsiębiorstwa, użyj `--sku enterprise` przełącznika przy użyciu `az ml workspace create` polecenia. Aby uzyskać więcej informacji na temat wersji Azure Machine Learning, zobacz [co to jest Azure Machine Learning](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> W nazwie obszaru roboczego nie jest rozróżniana wielkość liter.

Dane wyjściowe tego polecenia są podobne do następujących:

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

Aby utworzyć obszar roboczy, który korzysta z istniejących zasobów, należy podać identyfikator zasobów. Użyj następujących poleceń, aby uzyskać identyfikator dla usług:

> [!IMPORTANT]
> Nie musisz określać wszystkich istniejących zasobów. Można określić jeden lub więcej. Na przykład możesz określić istniejące konto magazynu, a w obszarze roboczym zostaną utworzone inne zasoby.

+ **Konto usługi Azure Storage**:`az storage account show --name <storage-account-name> --query "id"`

    Odpowiedź z tego polecenia jest podobna do poniższego tekstu i jest IDENTYFIKATORem konta magazynu:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Application Insights platformy Azure**:

    1. Zainstaluj rozszerzenie usługi Application Insights:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Pobierz identyfikator usługi Application Insight:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Odpowiedź z tego polecenia jest podobna do poniższego tekstu i jest IDENTYFIKATORem usługi Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**:`az keyvault show --name <key-vault-name> --query "ID"`

    Odpowiedź z tego polecenia jest podobna do poniższego tekstu i jest IDENTYFIKATORem magazynu kluczy:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Odpowiedź z tego polecenia jest podobna do poniższego tekstu i jest IDENTYFIKATORem rejestru kontenerów:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Aby można było używać [konta administratora](/azure/container-registry/container-registry-authentication#admin-account) z obszarem roboczym Azure Machine Learning, rejestr kontenerów musi być włączony.

Gdy masz identyfikatory zasobów, które mają być używane z obszarem roboczym, użyj `az workspace create -w <workspace-name> -g <resource-group-name>` polecenia Base i Dodaj parametry oraz identyfikatory dla istniejących zasobów programu. Na przykład następujące polecenie tworzy obszar roboczy, który używa istniejącego rejestru kontenerów:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Dane wyjściowe tego polecenia są podobne do następujących:

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

## <a name="list-workspaces"></a>Obszary robocze listy

Aby wyświetlić listę wszystkich obszarów roboczych dla subskrypcji platformy Azure, użyj następującego polecenia:

```azurecli-interactive
az ml workspace list
```

Dane wyjściowe tego polecenia są podobne do następujących:

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

Aby uzyskać więcej informacji, zobacz dokumentację funkcji [AZ ml Workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) .

## <a name="get-workspace-information"></a>Pobierz informacje o obszarze roboczym

Aby uzyskać informacje o obszarze roboczym, użyj następującego polecenia:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następujących:

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

Aby uzyskać więcej informacji, zobacz [AZ ml Workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) documentation.

## <a name="update-a-workspace"></a>Aktualizowanie obszaru roboczego

Aby zaktualizować obszar roboczy, użyj następującego polecenia:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następujących:

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

Więcej informacji można znaleźć w dokumentacji [AZ ml Workspace Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Udostępnianie obszaru roboczego innemu użytkownikowi

Aby udostępnić obszar roboczy innemu użytkownikowi w ramach subskrypcji, użyj następującego polecenia:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (RBAC) z Azure Machine Learning, zobacz [Zarządzanie użytkownikami i rolami](how-to-assign-roles.md).

Aby uzyskać więcej informacji, zobacz sekcję [AZ ml Workspace Share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) documentation.

## <a name="sync-keys-for-dependent-resources"></a>Synchronizuj klucze dla zasobów zależnych

Jeśli zmienisz klucze dostępu dla jednego z zasobów używanych w obszarze roboczym, będzie ono miało około godzinę, aby obszar roboczy mógł zostać zsynchronizowany z nowym kluczem. Aby wymusić natychmiastowe Synchronizowanie nowych kluczy w obszarze roboczym, użyj następującego polecenia:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Aby uzyskać więcej informacji na temat zmieniania kluczy, zobacz Ponowne [generowanie kluczy dostępu do magazynu](how-to-change-storage-access-key.md).

Więcej informacji można znaleźć w dokumentacji [AZ ml Workspace Sync-Keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Aby usunąć obszar roboczy, gdy nie jest już wymagany, użyj następującego polecenia:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Usunięcie obszaru roboczego nie powoduje usunięcia usługi Application Insights, konta magazynu, magazynu kluczy ani rejestru kontenerów używanego w obszarze roboczym.

Możesz również usunąć grupę zasobów, która spowoduje usunięcie obszaru roboczego i wszystkich innych zasobów platformy Azure w grupie zasobów. Aby usunąć grupę zasobów, użyj następującego polecenia:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Więcej informacji można znaleźć w dokumentacji [AZ ml Workspace Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Przeniesienie obszaru roboczego

> [!WARNING]
> Przeniesienie obszaru roboczego Azure Machine Learning do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem do nowej dzierżawy nie jest obsługiwane. Wykonanie tej operacji może spowodować błędy.

### <a name="deleting-the-azure-container-registry"></a>Usuwanie Azure Container Registry

W przypadku niektórych operacji Azure Machine Learning obszar roboczy używa Azure Container Registry (ACR). Zostanie automatycznie utworzone wystąpienie ACR, gdy jest ono najpierw wymagane.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozszerzenia interfejsu wiersza polecenia platformy Azure na potrzeby uczenia maszynowego, zobacz dokumentację [AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) .
