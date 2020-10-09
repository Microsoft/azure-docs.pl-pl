---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — dodawanie aplikacji w usłudze Batch
description: Ten przykładowy skrypt pokazuje, jak dodać aplikację do użycia z pulą Azure Batch lub zadaniem.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: fa4f273949e59c66292f5742501be9c2ad6a9fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494455"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Przykład interfejsu wiersza polecenia: dodawanie aplikacji do konta usługi Azure Batch

Ten skrypt demonstruje sposób dodawania aplikacji do użycia z pulą lub zadaniem podrzędnym usługi Azure Batch. Aby skonfigurować aplikację w celu dodania jej do konta usługi Batch, spakuj plik wykonywalny razem ze wszystkimi zależnościami do pliku zip. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń.
Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Tworzy konto usługi Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Tworzy aplikację.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Dodaje pakiet aplikacji do określonej aplikacji.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Aktualizuje właściwości aplikacji.  |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
