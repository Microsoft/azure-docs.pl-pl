---
title: Tworzenie aplikacji funkcji bezserwerowej przy użyciu interfejsu wiersza polecenia platformy Azure
description: Tworzenie aplikacji funkcji do wykonywania bezserwerowego na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3a52d7ea8c940a33f4fbd2b9ad69f4f889615e7f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934343"
---
# <a name="create-a-function-app-for-serverless-code-execution"></a>Tworzenie aplikacji funkcji do bezserwerowego wykonywania kodu 

Ten przykładowy skrypt usługi Azure Functions tworzy aplikację funkcji, która jest kontenerem dla funkcji. Aplikacja funkcji jest tworzona przy użyciu [planu zużycia](../consumption-plan.md), który jest idealnym rozwiązaniem w przypadku obciążeń bezserwerowych sterowanych zdarzeniami.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy aplikację funkcji platformy Azure przy użyciu [planu zużycia](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto usługi Azure Storage. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
