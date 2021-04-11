---
title: Tworzenie obszarów roboczych przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć rozszerzenia interfejsu wiersza polecenia platformy Azure do uczenia maszynowego, aby utworzyć nowy obszar roboczy Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 5e9df582ce6eddd50fbecf02858f4afe611dcf18
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220219"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Tworzenie obszaru roboczego dla Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure


W tym artykule dowiesz się, jak utworzyć obszar roboczy Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure. Interfejs wiersza polecenia platformy Azure umożliwia zarządzanie zasobami platformy Azure. Rozszerzenie uczenia maszynowego do interfejsu wiersza polecenia dostarcza poleceń do pracy z zasobami Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* Aby korzystać z poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego**, wymagany jest [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

    Jeśli używasz [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), interfejs wiersza polecenia jest dostępny za pomocą przeglądarki i przebywa w chmurze.

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Łączenie interfejsu wiersza polecenia z subskrypcją platformy Azure

> [!IMPORTANT]
> Jeśli używasz Azure Cloud Shell, możesz pominąć tę sekcję. Usługa Cloud Shell automatycznie uwierzytelnia użytkownika przy użyciu konta, które loguje się do subskrypcji platformy Azure.

Istnieje kilka sposobów uwierzytelniania w ramach subskrypcji platformy Azure z poziomu interfejsu wiersza polecenia. Najłatwiej jest interaktywnie uwierzytelniać się za pomocą przeglądarki. Aby uwierzytelnić interaktywnie, Otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadzanie kodu autoryzacji.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Aby poznać inne metody uwierzytelniania, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning opiera się na następujących usługach lub jednostkach platformy Azure:

> [!IMPORTANT]
> Jeśli nie określisz istniejącej usługi platformy Azure, zostanie ona utworzona automatycznie podczas tworzenia obszaru roboczego. Zawsze należy określić grupę zasobów. Podczas dołączania własnego konta magazynu upewnij się, że spełnia ono następujące kryteria:
>
> * Konto magazynu _nie_ jest kontem premium (Premium_LRS i Premium_GRS)
> * Włączono zarówno funkcję Azure Blob, jak i usługę Azure File
> * Hierarchiczna przestrzeń nazw (ADLS Gen 2) jest wyłączona
>
> Te wymagania dotyczą tylko _domyślnego_ konta magazynu używanego przez obszar roboczy.

| Usługa | Parametr określający istniejące wystąpienie |
| ---- | ---- |
| **Grupa zasobów platformy Azure** | `-g <resource-group-name>`
| **Konto usługi Azure Storage** | `--storage-account <service-id>` |
| **Application Insights platformy Azure** | `--application-insights <service-id>` |
| **Usługa Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) nie obsługuje obecnie znaków Unicode w nazwach grup zasobów. Aby rozwiązać ten problem, należy użyć grupy zasobów, która nie zawiera tych znaków.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Obszar roboczy Azure Machine Learning należy utworzyć w grupie zasobów. Możesz wybrać istniejącą grupę zasobów lub utworzyć nową. Aby __utworzyć nową grupę zasobów__, użyj następującego polecenia. Zamień na `<resource-group-name>` nazwę, która ma być używana dla tej grupy zasobów. Zamień na `<location>` region platformy Azure, który ma być używany dla tej grupy zasobów:

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

Aby uzyskać więcej informacji na temat pracy z grupami zasobów, zobacz [AZ Group](/cli/azure/group).

### <a name="automatically-create-required-resources"></a>Automatycznie twórz wymagane zasoby

Aby utworzyć nowy obszar roboczy, w którym __usługi są tworzone automatycznie__, użyj następującego polecenia:

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

### <a name="virtual-network-and-private-endpoint"></a>Sieć wirtualna i prywatny punkt końcowy

> [!IMPORTANT]
> Korzystanie z obszaru roboczego Azure Machine Learning z linkiem prywatnym nie jest dostępne w regionach Azure Government.

Jeśli chcesz ograniczyć dostęp do obszaru roboczego do sieci wirtualnej, możesz użyć następujących parametrów:

* `--pe-name`: Nazwa tworzonego prywatnego punktu końcowego.
* `--pe-auto-approval`: Czy połączenia prywatnego punktu końcowego z obszarem roboczym powinny być automatycznie zatwierdzane.
* `--pe-resource-group`: Grupa zasobów, w której ma zostać utworzony prywatny punkt końcowy. Musi być tą samą grupą, która zawiera sieć wirtualną.
* `--pe-vnet-name`: Istniejąca sieć wirtualna do utworzenia prywatnego punktu końcowego w programie.
* `--pe-subnet-name`: Nazwa podsieci, w której ma zostać utworzony prywatny punkt końcowy. Wartość domyślna to `default`.

Aby uzyskać więcej informacji na temat korzystania z prywatnego punktu końcowego i sieci wirtualnej z obszarem roboczym, zobacz [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md).

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Klucz zarządzany przez klienta i duży obszar roboczy wpływ na działalność biznesową

Domyślnie metadane obszaru roboczego są przechowywane w wystąpieniu Azure Cosmos DBnym przez firmę Microsoft. Te dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

> [!NOTE]
> Azure Cosmos DB __nie__ jest używana do przechowywania informacji, takich jak wydajność modelu, informacje zarejestrowane przez eksperymenty lub informacje zarejestrowane w ramach wdrożeń modeli. Aby uzyskać więcej informacji na temat monitorowania tych elementów, zobacz sekcję [monitorowanie i rejestrowanie](concept-azure-machine-learning-architecture.md) artykułu architektura i koncepcje.

Zamiast używać klucza zarządzanego przez firmę Microsoft, możesz użyć opcji podaj własny klucz. Spowoduje to utworzenie wystąpienia Azure Cosmos DB, które przechowuje metadane w ramach subskrypcji platformy Azure. Użyj `--cmk-keyvault` parametru, aby określić Azure Key Vault, który zawiera klucz, i `--resource-cmk-uri` określić adres URL klucza w ramach magazynu.

Przed użyciem `--cmk-keyvault` parametrów i `--resource-cmk-uri` należy najpierw wykonać następujące czynności:

1. Autoryzuj __aplikację Machine Learning__ (w temacie Zarządzanie tożsamościami i dostępem) z uprawnieniami współautora w ramach subskrypcji.
1. Wykonaj kroki opisane w temacie [Konfigurowanie kluczy zarządzanych przez klienta](../cosmos-db/how-to-setup-cmk.md) do:
    * Rejestrowanie dostawcy Azure Cosmos DB
    * Tworzenie i Konfigurowanie Azure Key Vault
    * Generowanie klucza

Nie musisz ręcznie tworzyć wystąpienia Azure Cosmos DB, po utworzeniu obszaru roboczego zostanie ono utworzone. To wystąpienie Azure Cosmos DB zostanie utworzone w oddzielnej grupie zasobów przy użyciu nazwy opartej na tym wzorcu: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Aby ograniczyć ilość danych zbieranych przez firmę Microsoft w obszarze roboczym, użyj `--hbi-workspace` parametru. 

> [!IMPORTANT]
> Wybór dużego wpływu na działalność biznesową można wykonać tylko podczas tworzenia obszaru roboczego. Tego ustawienia nie można zmienić po utworzeniu obszaru roboczego.

Aby uzyskać więcej informacji na temat kluczy zarządzanych przez klienta i obszarów roboczych o dużym znaczeniu biznesowym, zobacz [zabezpieczenia przedsiębiorstwa dla Azure Machine Learning](concept-data-encryption.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Korzystanie z istniejących zasobów

Aby utworzyć obszar roboczy, który korzysta z istniejących zasobów, należy podać identyfikator zasobów. Użyj następujących poleceń, aby uzyskać identyfikator dla usług:

> [!IMPORTANT]
> Nie musisz określać wszystkich istniejących zasobów. Można określić jeden lub więcej. Na przykład możesz określić istniejące konto magazynu, a w obszarze roboczym zostaną utworzone inne zasoby.

+ **Konto usługi Azure Storage**: `az storage account show --name <storage-account-name> --query "id"`

    Odpowiedź z tego polecenia jest podobna do poniższego tekstu i jest IDENTYFIKATORem konta magazynu:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Jeśli chcesz użyć istniejącego konta usługi Azure Storage, nie może ono być kontem Premium (Premium_LRS i Premium_GRS). Nie może ona również mieć hierarchicznej przestrzeni nazw (używane z Azure Data Lake Storage Gen2). _W przypadku konta magazynu w_ warstwie Premium ani hierarchicznej przestrzeni nazw nie są obsługiwane. Możesz użyć magazynu w warstwie Premium lub hierarchicznej przestrzeni nazw z kontami magazynu _innego niż domyślne_ .

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

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    Odpowiedź z tego polecenia jest podobna do poniższego tekstu i jest IDENTYFIKATORem magazynu kluczy:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Odpowiedź z tego polecenia jest podobna do poniższego tekstu i jest IDENTYFIKATORem rejestru kontenerów:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Aby można było używać [konta administratora](../container-registry/container-registry-authentication.md#admin-account) z obszarem roboczym Azure Machine Learning, rejestr kontenerów musi być włączony.

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

Aby uzyskać więcej informacji, zobacz dokumentację funkcji [AZ ml Workspace list](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-list) .

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

Aby uzyskać więcej informacji, zobacz [AZ ml Workspace show](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-show) documentation.

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

Więcej informacji można znaleźć w dokumentacji [AZ ml Workspace Update](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Udostępnianie obszaru roboczego innemu użytkownikowi

Aby udostępnić obszar roboczy innemu użytkownikowi w ramach subskrypcji, użyj następującego polecenia:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (Azure RBAC Azure Machine Learning) na platformie Azure, zobacz [Zarządzanie użytkownikami i rolami](how-to-assign-roles.md).

Aby uzyskać więcej informacji, zobacz sekcję [AZ ml Workspace Share](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-share) documentation.

## <a name="sync-keys-for-dependent-resources"></a>Synchronizuj klucze dla zasobów zależnych

Jeśli zmienisz klucze dostępu dla jednego z zasobów używanych w obszarze roboczym, będzie ono miało około godzinę, aby obszar roboczy mógł zostać zsynchronizowany z nowym kluczem. Aby wymusić natychmiastowe Synchronizowanie nowych kluczy w obszarze roboczym, użyj następującego polecenia:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Aby uzyskać więcej informacji na temat zmieniania kluczy, zobacz Ponowne [generowanie kluczy dostępu do magazynu](how-to-change-storage-access-key.md).

Więcej informacji można znaleźć w dokumentacji [AZ ml Workspace Sync-Keys](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

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

Więcej informacji można znaleźć w dokumentacji [AZ ml Workspace Delete](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-delete) .

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

Aby uzyskać więcej informacji na temat rozszerzenia interfejsu wiersza polecenia platformy Azure na potrzeby uczenia maszynowego, zobacz dokumentację [AZ ml](/cli/azure/ext/azure-cli-ml/ml) .