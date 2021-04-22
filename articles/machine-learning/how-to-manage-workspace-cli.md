---
title: Tworzenie obszarów roboczych przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć nowy obszar roboczy przy użyciu rozszerzenia interfejsu wiersza polecenia platformy Azure na Azure Machine Learning maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 8a00f5474fb73677125b85e48fcc2a42f34fdeb0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876407"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Tworzenie obszaru roboczego dla usługi Azure Machine Learning interfejsu wiersza polecenia platformy Azure


Z tego artykułu dowiesz się, jak utworzyć obszar roboczy Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure. Interfejs wiersza polecenia platformy Azure udostępnia polecenia do zarządzania zasobami platformy Azure. Rozszerzenie uczenia maszynowego interfejsu wiersza polecenia udostępnia polecenia do pracy Azure Machine Learning zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj bezpłatną [lub płatną](https://aka.ms/AMLFree)wersję Azure Machine Learning.

* Aby używać poleceń interfejsu wiersza polecenia w tym dokumencie ze środowiska **lokalnego,** potrzebny jest interfejs wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli).

    Jeśli używasz interfejsu [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), interfejs wiersza polecenia jest dostępny za pośrednictwem przeglądarki i znajduje się w chmurze.

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Łączenie interfejsu wiersza polecenia z subskrypcją platformy Azure

> [!IMPORTANT]
> Jeśli używasz tej Azure Cloud Shell, możesz pominąć tę sekcję. Usługa Cloud Shell automatycznie uwierzytelnia Cię przy użyciu konta, które logujesz się do subskrypcji platformy Azure.

Istnieje kilka sposobów uwierzytelniania w subskrypcji platformy Azure za pomocą interfejsu wiersza polecenia. Najprostszym sposobem jest interaktywne uwierzytelnianie przy użyciu przeglądarki. Aby uwierzytelnić się interaktywnie, otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie należy otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie i [https://aka.ms/devicelogin](https://aka.ms/devicelogin) wprowadzanie kodu autoryzacji.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Aby uzyskać informacje o innych metodach uwierzytelniania, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure.](/cli/azure/authenticate-azure-cli)

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar Azure Machine Learning opiera się na następujących usługach lub jednostkach platformy Azure:

> [!IMPORTANT]
> Jeśli nie określisz istniejącej usługi platformy Azure, zostanie ona utworzona automatycznie podczas tworzenia obszaru roboczego. Zawsze należy określić grupę zasobów. Podczas dołączania własnego konta magazynu upewnij się, że spełnia ono następujące kryteria:
>
> * Konto magazynu nie _jest kontem_ Premium (Premium_LRS i Premium_GRS)
> * Włączono możliwości usługi Azure Blob i Azure File
> * Hierarchiczna przestrzeń nazw (ADLS Gen 2) jest wyłączona
>
> Te wymagania są tylko dla _domyślnego konta_ magazynu używanego przez obszar roboczy.

| Usługa | Parametr określający istniejące wystąpienie |
| ---- | ---- |
| **Grupa zasobów platformy Azure** | `-g <resource-group-name>`
| **Konto usługi Azure Storage** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Usługa Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) nie obsługuje obecnie znaków Unicode w nazwach grup zasobów. Aby rozwiązać ten problem, użyj grupy zasobów, która nie zawiera tych znaków.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Obszar Azure Machine Learning musi zostać utworzony wewnątrz grupy zasobów. Możesz wybrać istniejącą grupę zasobów lub utworzyć nową. Aby __utworzyć nową grupę zasobów,__ użyj następującego polecenia. Zastąp `<resource-group-name>` nazwą używaną dla tej grupy zasobów. `<location>`Zamień na region platformy Azure, który ma być używać dla tej grupy zasobów:

> [!TIP]
> Należy wybrać region, w którym Azure Machine Learning jest dostępna. Aby uzyskać więcej informacji, [zobacz Products available by region (Dostępne produkty według regionów).](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpowiedź z tego polecenia jest podobna do następującego JSON:

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

Aby uzyskać więcej informacji na temat pracy z grupami zasobów, zobacz [az group](/cli/azure/group).

### <a name="automatically-create-required-resources"></a>Automatyczne tworzenie wymaganych zasobów

Aby utworzyć nowy obszar roboczy, w którym __usługi są tworzone automatycznie,__ użyj następującego polecenia:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> W nazwie obszaru roboczego nie jest uwzględniania ich litera.

Dane wyjściowe tego polecenia są podobne do następujących danych JSON:

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
> Korzystanie z Azure Machine Learning z linkiem prywatnym nie jest dostępne w Azure Government regionach.

Jeśli chcesz ograniczyć dostęp do obszaru roboczego do sieci wirtualnej, możesz użyć następujących parametrów:

* `--pe-name`: nazwa utworzonego prywatnego punktu końcowego.
* `--pe-auto-approval`: określa, czy połączenia prywatnego punktu końcowego z obszarem roboczym powinny być automatycznie zatwierdzane.
* `--pe-resource-group`: grupa zasobów do utworzenia prywatnego punktu końcowego. Musi to być ta sama grupa, która zawiera sieć wirtualną.
* `--pe-vnet-name`: istniejąca sieć wirtualna do utworzenia prywatnego punktu końcowego.
* `--pe-subnet-name`: nazwa podsieci do utworzenia prywatnego punktu końcowego. Wartość domyślna to `default`.

Aby uzyskać więcej informacji na temat korzystania z prywatnego punktu końcowego i sieci wirtualnej w obszarze roboczym, zobacz Omówienie izolacji i [prywatności sieci wirtualnej.](how-to-network-security-overview.md)

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Klucz zarządzany przez klienta i obszar roboczy o dużym wpływie na firmę

Domyślnie metadane obszaru roboczego są przechowywane w wystąpieniu Azure Cosmos DB zarządzanym przez firmę Microsoft. Te dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

> [!NOTE]
> Azure Cosmos DB __są__ używane do przechowywania informacji, takich jak wydajność modelu, informacje rejestrowane przez eksperymenty lub informacje zarejestrowane z wdrożeń modelu. Aby uzyskać więcej informacji na temat monitorowania tych elementów, zobacz sekcję [Monitorowanie i rejestrowanie](concept-azure-machine-learning-architecture.md) w artykule architektura i pojęcia.

Zamiast używać klucza zarządzanego przez firmę Microsoft, możesz użyć własnego klucza. Powoduje to utworzenie Azure Cosmos DB, które przechowuje metadane w subskrypcji platformy Azure. Użyj parametru , aby Azure Key Vault klucz i określić adres URL klucza `--cmk-keyvault` `--resource-cmk-uri` w magazynie.

Przed użyciem `--cmk-keyvault` parametrów i należy najpierw wykonać następujące `--resource-cmk-uri` czynności:

1. Autoryzowanie __Machine Learning aplikacji__ (w uchęce Zarządzanie tożsamościami i dostępem) przy użyciu uprawnień współautora w ramach subskrypcji.
1. Wykonaj kroki opisane w te [tematu Configure customer-managed keys (Konfigurowanie kluczy zarządzanych przez klienta),](../cosmos-db/how-to-setup-cmk.md) aby:
    * Rejestrowanie Azure Cosmos DB rejestracji
    * Tworzenie i konfigurowanie Azure Key Vault
    * Generowanie klucza

Nie musisz ręcznie tworzyć wystąpienia Azure Cosmos DB, które zostanie utworzone automatycznie podczas tworzenia obszaru roboczego. To Azure Cosmos DB zostanie utworzone w oddzielnej grupie zasobów przy użyciu nazwy opartej na tym wzorcu: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Aby ograniczyć dane zbierane przez firmę Microsoft w Twoim obszarze roboczym, użyj `--hbi-workspace` parametru . 

> [!IMPORTANT]
> Wybór wysokiego wpływu na działalność biznesową można wykonać tylko podczas tworzenia obszaru roboczego. Nie można zmienić tego ustawienia po utworzeniu obszaru roboczego.

Aby uzyskać więcej informacji na temat kluczy zarządzanych przez klienta i obszaru roboczego o dużym wpływie na działalność biznesową, zobacz [Zabezpieczenia przedsiębiorstwa dla Azure Machine Learning](concept-data-encryption.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Korzystanie z istniejących zasobów

Aby utworzyć obszar roboczy, który używa istniejących zasobów, należy podać identyfikator zasobów. Użyj następujących poleceń, aby uzyskać identyfikator usług:

> [!IMPORTANT]
> Nie trzeba określać wszystkich istniejących zasobów. Można określić co najmniej jedną wartość. Możesz na przykład określić istniejące konto magazynu, a obszar roboczy utworzy inne zasoby.

+ **Konto usługi Azure Storage:**`az storage account show --name <storage-account-name> --query "id"`

    Odpowiedź z tego polecenia jest podobna do następującego tekstu i jest identyfikatorem konta magazynu:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Jeśli chcesz użyć istniejącego konta usługi Azure Storage, nie może to być konto premium (Premium_LRS i Premium_GRS). Nie może również mieć hierarchicznej przestrzeni nazw (używanej z Azure Data Lake Storage Gen2). Domyślnym kontem magazynu obszaru roboczego  nie jest obsługiwana ani usługa Premium Storage, ani hierarchiczna przestrzeń nazw. Z kontami magazynu innego niż _domyślne_ można używać magazynu w chmurze Premium Storage lub hierarchicznej przestrzeni nazw.

+ **Azure Application Insights:**

    1. Zainstaluj rozszerzenie usługi Application Insights:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Pobierz identyfikator usługi Application Insights:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Odpowiedź z tego polecenia jest podobna do następującego tekstu i jest identyfikatorem usługi Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault:**`az keyvault show --name <key-vault-name> --query "ID"`

    Odpowiedź z tego polecenia jest podobna do następującego tekstu i jest identyfikatorem magazynu kluczy:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Odpowiedź z tego polecenia jest podobna do następującego tekstu i jest identyfikatorem rejestru kontenerów:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Rejestr kontenerów musi mieć włączone [konto administratora,](../container-registry/container-registry-authentication.md#admin-account) zanim będzie można go używać z Azure Machine Learning roboczym.

Po podaniu identyfikatorów dla zasobów, których chcesz używać z obszarem roboczym, użyj polecenia podstawowego i dodaj parametry i identyfikatory dla `az workspace create -w <workspace-name> -g <resource-group-name>` istniejących zasobów. Na przykład następujące polecenie tworzy obszar roboczy, który używa istniejącego rejestru kontenerów:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Dane wyjściowe tego polecenia są podobne do następujących danych JSON:

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

Dane wyjściowe tego polecenia są podobne do następujących danych JSON:

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

Aby uzyskać więcej informacji, zobacz [dokumentację az ml workspace list.](/cli/azure/ml/workspace#az_ml_workspace_list)

## <a name="get-workspace-information"></a>Uzyskiwanie informacji o obszarze roboczym

Aby uzyskać informacje o obszarze roboczym, użyj następującego polecenia:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następujących danych JSON:

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

Aby uzyskać więcej informacji, zobacz dokumentację [az ml workspace show.](/cli/azure/ml/workspace#az_ml_workspace_show)

## <a name="update-a-workspace"></a>Aktualizowanie obszaru roboczego

Aby zaktualizować obszar roboczy, użyj następującego polecenia:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następujących danych JSON:

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

Aby uzyskać więcej informacji, zobacz dokumentację [dotyczącą aktualizacji obszaru roboczego az ml.](/cli/azure/ml/workspace#az_ml_workspace_update)

## <a name="share-a-workspace-with-another-user"></a>Udostępnianie obszaru roboczego iniekcją użytkownika

Aby udostępnić obszar roboczy inowi użytkownikowi w ramach subskrypcji, użyj następującego polecenia:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (RBAC) na platformie Azure przy użyciu Azure Machine Learning, zobacz [Zarządzanie użytkownikami i rolami.](how-to-assign-roles.md)

Aby uzyskać więcej informacji, zobacz dokumentację [az ml workspace share.](/cli/azure/ml/workspace#az_ml_workspace_share)

## <a name="sync-keys-for-dependent-resources"></a>Klucze synchronizacji dla zasobów zależnych

Jeśli zmienisz klucze dostępu dla jednego z zasobów używanych przez obszar roboczy, synchronizacja obszaru roboczego z nowym kluczem może potrwać około godziny. Aby wymusić natychmiastową synchronizację nowych kluczy w obszarze roboczym, użyj następującego polecenia:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Aby uzyskać więcej informacji na temat zmieniania kluczy, zobacz [Regenerate storage access keys (Ponowne generowanie kluczy dostępu do magazynu).](how-to-change-storage-access-key.md)

Aby uzyskać więcej informacji, zobacz dokumentację [az ml workspace sync-keys.](/cli/azure/ml/workspace#az_ml_workspace_sync-keys)

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Aby usunąć obszar roboczy, gdy nie będzie już potrzebny, użyj następującego polecenia:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Usunięcie obszaru roboczego nie powoduje usunięcia usługi Application Insights, konta magazynu, magazynu kluczy ani rejestru kontenerów używanego przez obszar roboczy.

Możesz również usunąć grupę zasobów, co spowoduje usunięcie obszaru roboczego i wszystkich innych zasobów platformy Azure w grupie zasobów. Aby usunąć grupę zasobów, użyj następującego polecenia:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Aby uzyskać więcej informacji, zobacz dokumentację [az ml workspace delete.](/cli/azure/ml/workspace#az_ml_workspace_delete)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Przenoszenie obszaru roboczego

> [!WARNING]
> Przenoszenie Azure Machine Learning obszaru roboczego do innej subskrypcji lub przenoszenie subskrypcji jako właściciel do nowej dzierżawy nie jest obsługiwane. Może to spowodować błędy.

### <a name="deleting-the-azure-container-registry"></a>Usuwanie Azure Container Registry

Obszar Azure Machine Learning używa Azure Container Registry (ACR) w przypadku niektórych operacji. Spowoduje to automatyczne utworzenie wystąpienia usługi ACR, gdy będzie ono potrzebne po raz pierwszy.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozszerzenia interfejsu wiersza polecenia platformy Azure dla uczenia maszynowego, zobacz [dokumentację polecenia az ml.](/cli/azure/ml)