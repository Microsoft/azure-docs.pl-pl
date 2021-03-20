---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — uruchamianie zadania usługi Batch
description: Ten skrypt tworzy zadanie usługi Batch i dodaje serię zadań podrzędnych do tego zadania. Demonstruje również sposób monitorowania zadania i jego zadań podrzędnych.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b67925f48a9d2dbe0b4559d46d783b500e7a0773
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100919"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Przykład interfejsu wiersza polecenia: uruchamianie zadania i zadań podrzędnych za pomocą usługi Azure Batch

Ten skrypt tworzy zadanie usługi Batch i dodaje serię zadań podrzędnych do tego zadania. Demonstruje również sposób monitorowania zadania i jego zadań podrzędnych. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten samouczek wymaga wersji 2.0.20 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Tworzy konto usługi Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Tworzy pulę węzłów obliczeniowych.  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Tworzy zadanie usługi Batch.  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | Dodaje zadanie podrzędne do określonego zadania usługi Batch.  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Aktualizuje właściwości zadania usługi Batch.  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | Pobiera szczegóły określonego zadania usługi Batch.  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | Pobiera szczegóły zadania podrzędnego z określonego zadania usługi Batch.  |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
