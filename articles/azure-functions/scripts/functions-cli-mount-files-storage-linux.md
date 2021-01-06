---
title: Instalowanie udziału plików w aplikacji funkcji języka Python — interfejs wiersza polecenia platformy Azure
description: Utwórz bezserwerową aplikację funkcji języka Python i zainstaluj istniejący udział plików przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 89cf1422c70a49497a1bc43e42248612e0c8dd83
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930909"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Instalowanie udziału plików w aplikacji funkcji języka Python przy użyciu interfejsu wiersza polecenia platformy Azure

Ten Azure Functions przykładowy skrypt tworzy aplikację funkcji i tworzy udział w Azure Files. Je instaluje udział, dzięki czemu dostęp do danych można uzyskać za pomocą funkcji.  

>[!NOTE]
>Utworzona aplikacja funkcji działa w języku Python w wersji 3,7. Azure Functions [obsługuje również język Python w wersji 3,6 i 3,8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy aplikację funkcji platformy Azure przy użyciu [planu zużycia](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto usługi Azure Storage. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Tworzy udział Azure Files na koncie magazynu. | 
| [AZ Storage Directory Create](/cli/azure/storage/directory#az-storage-directory-create) | Tworzy katalog w udziale. |
| [AZ webapp config Storage-account Add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Instaluje udział w aplikacji funkcji. |
| [AZ webapp config Storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Pokazuje udziały plików zainstalowane w aplikacji funkcji. | 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
