---
title: Tworzenie definicji aplikacji zarządzanej — interfejs wiersza polecenia platformy Azure
description: Zawiera przykładowy skrypt interfejsu wiersza polecenia platformy Azure, który tworzy definicję aplikacji zarządzanej w subskrypcji.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2430b14ce3a3ba578787cefa85d95475c3e9b920
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775461"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Tworzenie definicji aplikacji zarządzanej za pomocą interfejsu wiersza polecenia platformy Azure

Ten skrypt publikuje definicję aplikacji zarządzanej w katalogu usług. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia do utworzenia definicji aplikacji zarządzanej. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az_managedapp_definition_create) | Tworzy definicję aplikacji zarządzanej. Należy podać pakiet, który zawiera wymagane pliki. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
