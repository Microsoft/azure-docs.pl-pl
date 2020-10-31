---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — Pula systemu Windows w usłudze Batch
description: Ten skrypt demonstruje niektóre polecenia dostępne w interfejsie wiersza polecenia platformy Azure służące do tworzenia puli węzłów obliczeniowych z systemem Windows w usłudze Azure Batch i zarządzania nią.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: fb18f9d8777c17d31a3ab246603df0d9fa162467
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100943"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>Przykład interfejsu wiersza polecenia: tworzenie puli systemu Windows w usłudze Azure Batch i zarządzanie nią

Ten skrypt demonstruje niektóre polecenia dostępne w interfejsie wiersza polecenia platformy Azure służące do tworzenia puli węzłów obliczeniowych z systemem Windows w usłudze Azure Batch i zarządzania nią. Pulę systemu Windows można skonfigurować na dwa sposoby: za pomocą konfiguracji usług Cloud Services lub konfiguracji maszyny wirtualnej. W tym przykładzie przedstawiono sposób tworzenia puli systemu Windows za pomocą konfiguracji usług Cloud Services.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten samouczek wymaga wersji 2.0.20 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Tworzy konto usługi Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Przeprowadza uwierzytelnianie na określonym koncie usługi Batch na potrzeby dalszej interakcji z interfejsem wiersza polecenia. |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Tworzy pulę węzłów obliczeniowych.  |
| [az batch pool set](/cli/azure/batch/pool#az-batch-pool-set) | Aktualizuje właściwości puli.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-enable) | Włącza automatyczne skalowanie puli i stosuje formułę.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Wyświetla właściwości puli.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-disable) | Wyłącza automatyczne skalowanie puli. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
