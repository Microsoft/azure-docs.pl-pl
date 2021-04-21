---
title: Uzyskiwanie zarządzanej grupy zasobów i zmienianie & maszyn wirtualnych — interfejs wiersza polecenia platformy Azure
description: Udostępnia przykładowy skrypt interfejsu wiersza polecenia platformy Azure, który pobiera zarządzaną grupę zasobów w aplikacji zarządzanej platformy Azure. Skrypt zmienia rozmiar maszyn wirtualnych.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 179b1b64656d3f97778e183d57797e4b3660fece
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775443"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Pobieranie zasobów z zarządzanej grupy zasobów i zmienianie rozmiaru maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure

Ten skrypt pobiera zasoby z zarządzanej grupy zasobów i zmienia rozmiar maszyn wirtualnych w tej grupie zasobów.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do wdrożenia aplikacji zarządzanej. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az_managedapp_list) | Wyświetla listę aplikacji zarządzanych. Należy podać wartości zapytania, aby zawęzić wyniki. |
| [az resource list](/cli/azure/resource#az_resource_list) | Wyświetla listę zasobów. Należy podać grupę zasobów i wartości zapytania, aby zawęzić wyniki. |
| [az vm resize](/cli/azure/vm#az_vm_resize) | Aktualizuje rozmiar maszyny wirtualnej. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
