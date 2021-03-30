---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — Tworzenie konta usługi Batch — subskrypcja użytkownika
description: Ten skrypt tworzy konto usługi Azure Batch w trybie subskrypcji użytkownika. To konto przydziela węzły obliczeniowe do subskrypcji.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: c9b8ba2ef782dcdc99cb18698175b8b53a53f0dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93076779"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Przykład interfejsu wiersza polecenia: tworzenie konta usługi Batch w trybie subskrypcji użytkownika

Ten skrypt tworzy konto usługi Azure Batch w trybie subskrypcji użytkownika. Konto, które przydziela węzły obliczeniowe w ramach subskrypcji, musi zostać uwierzytelnione za pośrednictwem tokenu usługi Azure Active Directory. Przydzielone węzły obliczeniowe są uwzględniane podczas obliczeń na potrzeby limitu przydziału procesorów wirtualnych (rdzeni) Twojej subskrypcji. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten samouczek wymaga wersji 2.0.20 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.  

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
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | Tworzy magazyn kluczy. |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | Aktualizuje zasady zabezpieczeń określonego magazynu kluczy. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Tworzy konto usługi Batch.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
