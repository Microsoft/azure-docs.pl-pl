---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — uruchamianie zadania usługi Batch
description: Ten skrypt tworzy zadanie usługi Batch i dodaje serię zadań podrzędnych do tego zadania. Demonstruje również sposób monitorowania zadania i jego zadań podrzędnych.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8efce1bc8dc32070b770769104b7fdc38e683cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500235"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Przykład interfejsu wiersza polecenia: uruchamianie zadania i zadań podrzędnych za pomocą usługi Azure Batch

Ten skrypt tworzy zadanie usługi Batch i dodaje serię zadań podrzędnych do tego zadania. Demonstruje również sposób monitorowania zadania i jego zadań podrzędnych. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

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
