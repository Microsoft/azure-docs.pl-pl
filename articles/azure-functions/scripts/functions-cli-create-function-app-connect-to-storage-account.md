---
title: Tworzenie aplikacji funkcji z podłączonym magazynem — interfejs wiersza polecenia platformy Azure
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie funkcji platformy Azure łączącej się z usługą Azure Storage
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13120ad4478febf9281ff423a3a7a8f8f3b25845
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934411"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Tworzenie aplikacji funkcji z nazwanym połączeniem konta magazynu 

Ten przykładowy skrypt usługi Azure Functions tworzy aplikację funkcji i łączy funkcję z kontem usługi Azure Storage. Ustawienie utworzonej aplikacji, które zawiera parametry połączenia, może być używane z [wyzwalaczem lub powiązaniem magazynu](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykładowy skrypt tworzy aplikację funkcji platformy Azure i dodaje do jej ustawień parametry połączenia z usługą Storage.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów w lokalizacji. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzenie konta magazynu |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji w [planie zużycia](../consumption-plan.md)bezserwerowego. |
| [AZ Storage account show-Connection-String](/cli/azure/storage/account#az-storage-account-show-connection-string) | Pobiera parametry połączenia dla konta. |
| [AZ functionapp config AppSettings Set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Ustawia parametry połączenia jako ustawienia aplikacji w aplikacji funkcji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
