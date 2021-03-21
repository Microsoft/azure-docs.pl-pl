---
title: 'Szybki Start: tworzenie dedykowanego modułu HSM platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Tworzenie, wyświetlanie, wyświetlanie, aktualizowanie i usuwanie dedykowanych sprzętowych modułów zabezpieczeń platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020962"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Szybki Start: tworzenie dedykowanego modułu HSM platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób tworzenia dedykowanego modułu HSM platformy Azure i zarządzania nim za pomocą rozszerzenia [AZ dedykowany moduł HSM](/cli/azure/ext/hardware-security-modules/dedicated-hsm) interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli go nie masz, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/) .
  
  Jeśli masz więcej niż jedną subskrypcję platformy Azure, ustaw subskrypcję do użycia na potrzeby rozliczeń przy użyciu interfejsu wiersza polecenia platformy Azure [AZ Account Set](/cli/azure/account#az_account_set) .
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Wszystkie wymagania zostały spełnione w przypadku dedykowanego modułu HSM, w tym rejestracji, zatwierdzania i sieci wirtualnej i maszyny wirtualnej do użycia na potrzeby aprowizacji. Aby uzyskać więcej informacji na temat wymagań dotyczących dedykowanego modułu HSM i wymagań wstępnych, zobacz [Samouczek: wdrażanie sprzętowych modułów zabezpieczeń w istniejącej sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[Grupa zasobów platformy Azure](../azure-resource-manager/management/overview.md) to logiczny kontener służący do wdrażania zasobów platformy Azure i zarządzania nimi jako Grupa. Jeśli nie masz jeszcze grupy zasobów dla dedykowanego modułu HSM, utwórz ją za pomocą polecenia [AZ Group Create](/cli/azure/group#az_group_create) . Poniższy przykład tworzy grupę zasobów o nazwie `myRG` w regionie świadczenia `westus` usługi Azure:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Tworzenie dedykowanego modułu HSM

Aby utworzyć dedykowany moduł HSM, użyj polecenia [AZ dedykowanego modułu HSM Create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) . Poniższy przykład Inicjuje obsługę dedykowanego modułu HSM o nazwie `hsm1` w `westus` regionie, `myRG` grupie zasobów i określonej subskrypcji, sieci wirtualnej i podsieci. Wymagane parametry to `name` , `location` , i `resource group` .

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

Wdrożenie trwa około 25 do 30 minut.

## <a name="get-a-dedicated-hsm"></a>Uzyskiwanie dedykowanego modułu HSM

Aby uzyskać bieżący dedykowany moduł HSM, uruchom polecenie [AZ dedykowany moduł HSM show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) . Poniższy przykład pobiera `hsm1` dedykowany moduł HSM w `myRG` grupie zasobów.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Aktualizowanie dedykowanego modułu HSM

Użyj polecenia [AZ dedykowanego-HSM Update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) , aby zaktualizować dedykowany moduł HSM. Poniższy przykład aktualizuje `hsm1` dedykowany moduł HSM w `myRG` grupie zasobów i jej znaczniki:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Lista dedykowanych sprzętowych modułów zabezpieczeń

Uruchom polecenie [AZ dedykowanego modułu HSM](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) , aby uzyskać informacje na temat bieżących dedykowanych sprzętowych modułów zabezpieczeń. Poniższy przykład zawiera listę dedykowanych sprzętowych modułów zabezpieczeń w `myRG` grupie zasobów:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Usuwanie dedykowanego modułu HSM

Aby usunąć dedykowany moduł HSM, użyj polecenia [AZ dedykowanego modułu HSM Delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) . Poniższy przykład usuwa `hsm1` dedykowany moduł HSM z `myRG` grupy zasobów:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

Jeśli grupa zasobów utworzona dla dedykowanego modułu HSM nie jest już potrzebna, można ją usunąć, uruchamiając polecenie [AZ Group Delete](/cli/azure/group#az_group_delete) . To polecenie usuwa grupę i wszystkie znajdujące się w niej zasoby, w tym wszystkie niepowiązane z dedykowanym modułem HSM. Poniższy przykład usuwa `myRG` grupę zasobów i wszystko w niej:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dedykowanego modułu HSM platformy Azure, zobacz [dedykowany moduł HSM platformy Azure](overview.md).
