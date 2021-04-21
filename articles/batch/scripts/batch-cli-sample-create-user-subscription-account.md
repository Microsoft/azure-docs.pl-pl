---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie konta usługi Batch — subskrypcja użytkownika
description: Ten skrypt tworzy konto usługi Azure Batch w trybie subskrypcji użytkownika. To konto przydziela węzły obliczeniowe do subskrypcji.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9bd7b7ac3dbb52ebafa00499e64ec3cff0969a13
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768361"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Przykład interfejsu wiersza polecenia: tworzenie konta usługi Batch w trybie subskrypcji użytkownika

Ten skrypt tworzy konto usługi Azure Batch w trybie subskrypcji użytkownika. Konto, które przydziela węzły obliczeniowe w ramach subskrypcji, musi zostać uwierzytelnione za pośrednictwem tokenu usługi Azure Active Directory. Przydzielone węzły obliczeniowe są uwzględniane podczas obliczeń na potrzeby limitu przydziału procesorów wirtualnych (rdzeni) Twojej subskrypcji. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.  

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az role assignment create](/cli/azure/role) | Tworzy nowe przypisanie roli dla użytkownika, grupy lub jednostki usługi. |
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az keyvault create](/cli/azure/keyvault#az_keyvault_create) | Tworzy magazyn kluczy. |
| [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) | Aktualizuje zasady zabezpieczeń określonego magazynu kluczy. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Tworzy konto usługi Batch.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az group delete](/cli/azure/group#az_group_delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
