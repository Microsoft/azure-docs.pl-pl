---
title: Interfejs wiersza polecenia platformy Azure — tworzenie i weryfikowanie maszyny wirtualnej w laboratorium
description: Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia utworzenie maszyny wirtualnej w laboratorium i zweryfikowanie jej dostępności.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 779ddee7da4248ce11b906c2a4736fe7851603cf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786751"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Tworzenie maszyny wirtualnej i weryfikowanie jej dostępności w laboratorium za pomocą interfejsu wiersza polecenia platformy Azure w usłudze Azure DevTest Labs

Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia utworzenie maszyny wirtualnej w laboratorium. Maszyna wirtualna utworzona na podstawie obrazu z platformy handlowej z uwierzytelnianiem za pomocą protokołu SSH. Następnie skrypt weryfikuje, czy maszyna wirtualna jest dostępna do użycia. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create) | Tworzy maszynę wirtualną w laboratorium. |
| [az lab vm show](/cli/azure/lab/vm#az_lab_vm_show) | Wyświetla stan maszyny wirtualnej w laboratorium. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi Azure Lab Services można znaleźć w artykule [Przykłady interfejsu wiersza polecenia usługi Azure Lab Services](../samples-cli.md).
