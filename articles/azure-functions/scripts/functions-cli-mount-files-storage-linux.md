---
title: Mount a file share to a Python function app - Azure CLI
description: Utwórz bez serwera aplikację funkcji języka Python i zainstaluj istniejący udział plików przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762285"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Mount a file share to a Python function app using Azure CLI

Ten Azure Functions przykładowy skrypt tworzy aplikację funkcji i udział w Azure Files. Zainstaluje udział, aby funkcje uzyskiwały dostęp do danych.  

>[!NOTE]
>Utworzona aplikacja funkcji działa w języku Python w wersji 3.7. Azure Functions [obsługuje również język Python w wersjach 3.6 i 3.8.](../functions-reference-python.md#python-version)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana. 

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy aplikację funkcji w Azure Functions użyciu [planu Zużycie.](../consumption-plan.md)

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto usługi Azure Storage. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Tworzy aplikację funkcji. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | Tworzy udział Azure Files magazynu. | 
| [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) | Tworzy katalog w udziału. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | Zainstaluje udział w aplikacji funkcji. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | Pokazuje udziały plików zainstalowane w aplikacji funkcji. | 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
