---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — uruchamianie zadania usługi Batch
description: Ten skrypt tworzy zadanie usługi Batch i dodaje serię zadań podrzędnych do tego zadania. Demonstruje również sposób monitorowania zadania i jego zadań podrzędnych.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e51fa96370b46c9a5a5d5ed6fd7593a06e45430
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768171"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Przykład interfejsu wiersza polecenia: uruchamianie zadania i zadań podrzędnych za pomocą usługi Azure Batch

Ten skrypt tworzy zadanie usługi Batch i dodaje serię zadań podrzędnych do tego zadania. Demonstruje również sposób monitorowania zadania i jego zadań podrzędnych. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana. 

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
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Tworzy konto usługi Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Tworzy pulę węzłów obliczeniowych.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Tworzy zadanie usługi Batch.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Dodaje zadanie podrzędne do określonego zadania usługi Batch.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Aktualizuje właściwości zadania usługi Batch.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Pobiera szczegóły określonego zadania usługi Batch.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Pobiera szczegóły zadania podrzędnego z określonego zadania usługi Batch.  |
| [az group delete](/cli/azure/group#az_group_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
