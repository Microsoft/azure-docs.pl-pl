---
title: Tworzenie definicji aplikacji zarządzanej — interfejs wiersza polecenia platformy Azure
description: Dostarcza przykład skryptu interfejsu wiersza polecenia platformy Azure, który tworzy definicję aplikacji zarządzanej w ramach subskrypcji.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: f4d5a0036ba44f7e0054db7ce820b91b0de629b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650191"
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
| [az managedapp definition create](https://docs.microsoft.com/cli/azure/managedapp/definition#az-managedapp-definition-create) | Tworzy definicję aplikacji zarządzanej. Należy podać pakiet, który zawiera wymagane pliki. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).
