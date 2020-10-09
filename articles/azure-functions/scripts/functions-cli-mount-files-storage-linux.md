---
title: Instalowanie udziału plików w aplikacji funkcji języka Python — interfejs wiersza polecenia platformy Azure
description: Utwórz bezserwerową aplikację funkcji języka Python i zainstaluj istniejący udział plików przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ab9ba3219dc600187e73bbf124d62d3f51317a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87498212"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Instalowanie udziału plików w aplikacji funkcji języka Python przy użyciu interfejsu wiersza polecenia platformy Azure

Ten Azure Functions przykładowy skrypt tworzy aplikację funkcji i tworzy udział w Azure Files. Je instaluje udział, dzięki czemu dostęp do danych można uzyskać za pomocą funkcji.  

>[!NOTE]
>Utworzona aplikacja funkcji działa w języku Python w wersji 3,7. Azure Functions [obsługuje również język Python w wersji 3,6 i 3,8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). Przykłady są zapisywane dla powłoki bash i należy je zmodyfikować tak, aby były uruchamiane w wierszu polecenia systemu Windows. 

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy aplikację funkcji platformy Azure przy użyciu [planu zużycia](../functions-scale.md#consumption-plan).

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
