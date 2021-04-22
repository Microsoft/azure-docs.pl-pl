---
title: Konfigurowanie prywatnego punktu końcowego
titleSuffix: Azure Machine Learning
description: Użyj Azure Private Link, aby bezpiecznie uzyskać dostęp do Azure Machine Learning z sieci wirtualnej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: 5e13c97427736d60f300d2d7b502c6f3e15fb481
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861449"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Konfigurowanie Azure Private Link dla Azure Machine Learning roboczego

Z tego dokumentu dowiesz się, jak używać Azure Private Link z Azure Machine Learning roboczym. Aby uzyskać informacje na temat tworzenia sieci wirtualnej na Azure Machine Learning, zobacz Omówienie izolacji i [prywatności sieci wirtualnej](how-to-network-security-overview.md)

Azure Private Link umożliwia nawiązywanie połączenia z obszarem roboczym przy użyciu prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w sieci wirtualnej. Następnie możesz ograniczyć dostęp do obszaru roboczego tylko za pośrednictwem prywatnych adresów IP. Private Link zmniejsza ryzyko eksfiltracji danych. Aby dowiedzieć się więcej na temat prywatnych punktów końcowych, zobacz [Azure Private Link](../private-link/private-link-overview.md) artykułu.

> [!IMPORTANT]
> Azure Private Link nie ma wpływu na płaszczyznę sterowania platformy Azure (operacje zarządzania), takie jak usuwanie obszaru roboczego lub zarządzanie zasobami obliczeniowymi. Na przykład tworzenie, aktualizowanie lub usuwanie docelowego obiektu obliczeniowego. Te operacje są wykonywane za pośrednictwem publicznego Internetu w zwykły sposób. Operacje płaszczyzny danych, takie jak Azure Machine Learning studio, interfejsy API (w tym opublikowane potoki) lub zestaw SDK, używają prywatnego punktu końcowego.
>
> Jeśli używasz przeglądarki Mozilla Firefox, mogą wystąpić problemy podczas próby uzyskania dostępu do prywatnego punktu końcowego dla obszaru roboczego. Ten problem może być związany z systemem DNS za pośrednictwem protokołu HTTPS w Mozilla. Zalecamy użycie programu Microsoft Edge lub Google Chrome jako obejścia.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli planujesz używanie obszaru roboczego z obsługą linku prywatnego z kluczem zarządzanym przez klienta, musisz zażądać tej funkcji przy użyciu biletu pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Zarządzanie limitami przydziału i ich zwiększanie.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

* Aby utworzyć prywatny punkt końcowy, musisz mieć istniejącą sieć wirtualną. Przed dodaniem prywatnego punktu [końcowego](../private-link/disable-private-endpoint-network-policy.md) należy również wyłączyć zasady sieciowe dla prywatnych punktów końcowych.
## <a name="limitations"></a>Ograniczenia

* Korzystanie z Azure Machine Learning z łączem prywatnym nie jest dostępne w Azure Government regionach.
* Jeśli włączysz dostęp publiczny do obszaru roboczego zabezpieczonego za pomocą linku prywatnego i użyjemy usługi Azure Machine Learning studio za pośrednictwem publicznego Internetu, niektóre funkcje, takie jak projektant, mogą nie mieć dostępu do danych. Ten problem występuje, gdy dane są przechowywane w usłudze zabezpieczonej za siecią wirtualną. Na przykład konto usługi Azure Storage.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Tworzenie obszaru roboczego, który używa prywatnego punktu końcowego

Użyj jednej z następujących metod, aby utworzyć obszar roboczy z prywatnym punktem końcowym. Każda z tych metod __wymaga istniejącej sieci wirtualnej:__

> [!TIP]
> Jeśli chcesz jednocześnie utworzyć obszar roboczy, prywatny punkt końcowy i sieć wirtualną, zobacz Use [an Azure Resource Manager template to create a workspace for Azure Machine Learning](how-to-create-workspace-template.md)(Tworzenie obszaru roboczego dla usługi Azure Machine Learning ).

# <a name="python"></a>[Python](#tab/python)

Zestaw Azure Machine Learning SDK języka Python udostępnia [klasę PrivateEndpointConfig,](/python/api/azureml-core/azureml.core.privateendpointconfig) której można używać z poleceniem [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) do tworzenia obszaru roboczego z prywatnym punktem końcowym. Ta klasa wymaga istniejącej sieci wirtualnej.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rozszerzenie [interfejsu wiersza polecenia platformy Azure dla uczenia maszynowego](reference-azure-machine-learning-cli.md) udostępnia [polecenie az ml workspace create.](/cli/azure/ml/workspace#az_ml_workspace_create) Następujące parametry tego polecenia mogą służyć do tworzenia obszaru roboczego z siecią prywatną, ale wymaga istniejącej sieci wirtualnej:

* `--pe-name`: nazwa utworzonego prywatnego punktu końcowego.
* `--pe-auto-approval`: określa, czy połączenia prywatnego punktu końcowego z obszarem roboczym powinny być automatycznie zatwierdzane.
* `--pe-resource-group`: grupa zasobów do utworzenia prywatnego punktu końcowego. Musi to być ta sama grupa, która zawiera sieć wirtualną.
* `--pe-vnet-name`: istniejąca sieć wirtualna do utworzenia prywatnego punktu końcowego.
* `--pe-subnet-name`: nazwa podsieci do utworzenia prywatnego punktu końcowego. Wartość domyślna to `default`.

Te parametry są dodatkiem do innych parametrów wymaganych dla polecenia create. Na przykład następujące polecenie tworzy nowy obszar roboczy w regionie Zachodnie stany USA przy użyciu istniejącej grupy zasobów i sieci wirtualnej:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Karta __Sieć__ w Azure Machine Learning studio umożliwia skonfigurowanie prywatnego punktu końcowego. Jednak wymaga istniejącej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie obszarów roboczych w portalu.](how-to-manage-workspace.md)

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Dodawanie prywatnego punktu końcowego do obszaru roboczego

Użyj jednej z następujących metod, aby dodać prywatny punkt końcowy do istniejącego obszaru roboczego:

> [!WARNING]
>
> Jeśli masz jakiekolwiek istniejące docelowe obiekty obliczeniowe skojarzone z tym obszarem roboczym i nie znajdują się one za tą samą siecią wirtualną, w których utworzono prywatny punkt końcowy, nie będą one działać.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Aby uzyskać więcej informacji na temat klas i metod używanych w tym przykładzie, zobacz [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) [i Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rozszerzenie [interfejsu wiersza polecenia platformy Azure dla uczenia maszynowego](reference-azure-machine-learning-cli.md) udostępnia [polecenie az ml workspace private-endpoint add.](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_add)

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

W obszarze Azure Machine Learning w portalu wybierz pozycję Połączenia __prywatnego punktu końcowego,__ a następnie wybierz __pozycję + Prywatny punkt końcowy.__ Użyj pól, aby utworzyć nowy prywatny punkt końcowy.

* Po wybraniu __regionu__ wybierz ten sam region co sieć wirtualną. 
* Podczas __wybierania typu zasobu__ użyj __opcji Microsoft.MachineLearningServices/workspaces.__ 
* W obszarze __Zasób__ ustaw nazwę obszaru roboczego.

Na koniec wybierz __pozycję Utwórz,__ aby utworzyć prywatny punkt końcowy.

---

## <a name="remove-a-private-endpoint"></a>Usuwanie prywatnego punktu końcowego

Użyj jednej z następujących metod, aby usunąć prywatny punkt końcowy z obszaru roboczego:

# <a name="python"></a>[Python](#tab/python)

Użyj [Workspace.delete_private_endpoint_connection,](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) aby usunąć prywatny punkt końcowy.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rozszerzenie [interfejsu wiersza polecenia platformy Azure dla uczenia maszynowego](reference-azure-machine-learning-cli.md) udostępnia [polecenie az ml workspace private-endpoint delete.](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete)

# <a name="portal"></a>[Portal](#tab/azure-portal)

W obszarze Azure Machine Learning w portalu wybierz pozycję Połączenia prywatnego punktu końcowego, a następnie wybierz punkt końcowy, który chcesz usunąć. Na koniec wybierz pozycję __Usuń__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Korzystanie z obszaru roboczego za pośrednictwem prywatnego punktu końcowego

Ponieważ komunikacja z obszarem roboczym jest dozwolona tylko z sieci wirtualnej, wszystkie środowiska projektowe, które używają tego obszaru roboczego, muszą być członkami sieci wirtualnej. Na przykład maszyna wirtualna w sieci wirtualnej.

> [!IMPORTANT]
> Aby uniknąć tymczasowych zakłóceń łączności, firma Microsoft zaleca opróżnianie pamięci podręcznej DNS na maszynach łączących się z obszarem roboczym po włączeniu Private Link. 

Aby uzyskać informacje na temat usługi Azure Virtual Machines, zobacz [dokumentację Virtual Machines .](../virtual-machines/index.yml)

## <a name="enable-public-access"></a>Włączanie dostępu publicznego

W niektórych sytuacjach możesz zezwolić innej osobie na łączenie się z zabezpieczonym obszarem roboczym za pośrednictwem publicznego punktu końcowego, a nie za pośrednictwem sieci wirtualnej. Po skonfigurowaniu obszaru roboczego przy użyciu prywatnego punktu końcowego możesz opcjonalnie włączyć dostęp publiczny do obszaru roboczego. Nie powoduje to usunięcia prywatnego punktu końcowego. Cała komunikacja między składnikami sieci wirtualnej jest nadal zabezpieczona. Umożliwia dostęp publiczny tylko do obszaru roboczego, oprócz dostępu prywatnego za pośrednictwem sieci wirtualnej.

> [!WARNING]
> Podczas nawiązywania połączenia za pośrednictwem publicznego punktu końcowego niektóre funkcje programu Studio nie będą w stanie uzyskać dostępu do danych. Ten problem występuje, gdy dane są przechowywane w usłudze zabezpieczonej za siecią wirtualną. Na przykład konto usługi Azure Storage. Należy również pamiętać, że funkcje jupyter/JupyterLab/RStudio wystąpienia obliczeniowego i uruchamianie notesów nie będzie działać.

Aby włączyć publiczny dostęp do prywatnego obszaru roboczego z obsługą linków, należy wykonać następujące kroki:

# <a name="python"></a>[Python](#tab/python)

Użyj [Workspace.delete_private_endpoint_connection,](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) aby usunąć prywatny punkt końcowy.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rozszerzenie [interfejsu wiersza polecenia platformy Azure dla uczenia maszynowego](reference-azure-machine-learning-cli.md) udostępnia [polecenie az ml workspace update.](/cli/azure/ml/workspace#az_ml_workspace_update) Aby włączyć publiczny dostęp do obszaru roboczego, dodaj parametr `--allow-public-access true` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Obecnie nie ma możliwości włączenia tej funkcji przy użyciu portalu.

---


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat zabezpieczania Azure Machine Learning wirtualnego, zobacz artykuł Omówienie izolacji i prywatności [sieci wirtualnej.](how-to-network-security-overview.md)

* Jeśli planujesz używanie niestandardowego rozwiązania DNS w sieci wirtualnej, zobacz jak używać obszaru roboczego [z niestandardowym serwerem DNS.](how-to-custom-dns.md)
