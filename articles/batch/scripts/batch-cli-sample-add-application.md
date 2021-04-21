---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — dodawanie aplikacji w usłudze Batch
description: Ten przykładowy skrypt pokazuje, jak dodać aplikację do użycia z pulą Azure Batch lub zadaniem.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06afb59a76e763c25e943c3be1531372a6bd2aa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765274"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Przykład interfejsu wiersza polecenia: dodawanie aplikacji do konta usługi Azure Batch

Ten skrypt demonstruje sposób dodawania aplikacji do użycia z pulą lub zadaniem podrzędnym usługi Azure Batch. Aby skonfigurować aplikację w celu dodania jej do konta usługi Batch, spakuj plik wykonywalny razem ze wszystkimi zależnościami do pliku zip. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana. 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń.
Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto magazynu. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Tworzy konto usługi Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | Tworzy aplikację.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | Dodaje pakiet aplikacji do określonej aplikacji.  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | Aktualizuje właściwości aplikacji.  |
| [az group delete](/cli/azure/group#az_group_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
