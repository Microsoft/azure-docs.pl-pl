---
title: 'Szybki start: tworzenie aplikacji Azure Dedicated HSM pomocą interfejsu wiersza polecenia platformy Azure'
description: Tworzenie, wyświetlanie, wyświetlanie, aktualizowanie i usuwanie dedykowanych modułów HSM platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 80d5bbb54715c5a1a5102f8991f366e273145edc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868955"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Szybki start: tworzenie aplikacji Azure Dedicated HSM pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób tworzenia interfejsu wiersza polecenia platformy Azure Azure Dedicated HSM zarządzania nim za pomocą rozszerzenia interfejsu wiersza polecenia platformy Azure [az dedicated-hsm.](/cli/azure/dedicated-hsm)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli [go nie masz,](https://azure.microsoft.com/free/) możesz utworzyć bezpłatne konto.
  
  Jeśli masz więcej niż jedną subskrypcję platformy Azure, ustaw subskrypcję do użycia na użytek rozliczeń za pomocą polecenia [az account set interfejsu](/cli/azure/account#az_account_set) wiersza polecenia platformy Azure.
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Wszystkie wymagania dotyczące dedykowanego modułu HSM, w tym rejestracji, zatwierdzania oraz sieci wirtualnej i maszyny wirtualnej do użycia na potrzeby aprowowania. Aby uzyskać więcej informacji o dedykowanych wymaganiach i wymaganiach wstępnych modułu HSM, zobacz Samouczek: wdrażanie modułów HSM w istniejącej sieci wirtualnej przy [użyciu interfejsu wiersza polecenia platformy Azure.](tutorial-deploy-hsm-cli.md)
  

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa [zasobów platformy Azure to](../azure-resource-manager/management/overview.md) logiczny kontener do wdrażania zasobów platformy Azure i zarządzania nimi jako grupą. Jeśli nie masz jeszcze grupy zasobów dla dedykowanego modułu HSM, utwórz grupę za pomocą [polecenia az group create.](/cli/azure/group#az_group_create) Poniższy przykład tworzy grupę zasobów o nazwie `myRG` w regionie świadczenia usługi `westus` Azure:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Tworzenie dedykowanego modułu HSM

Aby utworzyć dedykowany moduł HSM, użyj [polecenia az dedicated-hsm create.](/cli/azure/dedicated-hsm#az_dedicated_hsm_create) W poniższym przykładzie zaimekcjuje dedykowany moduł HSM o nazwie w regionie, grupie zasobów i `hsm1` `westus` `myRG` określonej subskrypcji, sieci wirtualnej i podsieci. Wymagane parametry to `name` , `location` i `resource group` .

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

Wdrożenie trwa około 25–30 minut.

## <a name="get-a-dedicated-hsm"></a>Uzyskiwanie dedykowanego modułu HSM

Aby uzyskać bieżący dedykowany moduł HSM, uruchom [polecenie az dedicated-hsm show.](/cli/azure/dedicated-hsm#az_dedicated_hsm_show) Poniższy przykład pobiera dedykowany `hsm1` moduł HSM w `myRG` grupie zasobów.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Aktualizowanie dedykowanego modułu HSM

Użyj polecenia [az dedicated-hsm update,](/cli/azure/dedicated-hsm#az_dedicated_hsm_update) aby zaktualizować dedykowany moduł HSM. Poniższy przykład aktualizuje dedykowany `hsm1` moduł HSM w `myRG` grupie zasobów i jego tagi:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Lista dedykowanych modułów HSM

Uruchom polecenie [az dedicated-hsm list,](/cli/azure/dedicated-hsm#az_dedicated_hsm_list) aby uzyskać informacje o bieżących dedykowanych modułach HSM. Poniższy przykład zawiera listę dedykowanych modułów HSM w `myRG` grupie zasobów:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Usuwanie dedykowanego modułu HSM

Aby usunąć dedykowany moduł HSM, użyj [polecenia az dedicated-hsm delete.](/cli/azure/dedicated-hsm#az_dedicated_hsm_delete) Poniższy przykład usuwa dedykowany `hsm1` moduł HSM z `myRG` grupy zasobów:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

Jeśli nie potrzebujesz już grupy zasobów utworzonej dla dedykowanego modułu HSM, możesz ją usunąć, uruchamiając [polecenie az group delete.](/cli/azure/group#az_group_delete) To polecenie usuwa grupę i wszystkie zasoby w tej grupie, w tym wszystkie, które nie są powiązane z dedykowanym modułem HSM. Poniższy przykład usuwa grupę `myRG` zasobów i wszystkie jej elementy:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Dedicated HSM, zobacz [Azure Dedicated HSM](overview.md).
