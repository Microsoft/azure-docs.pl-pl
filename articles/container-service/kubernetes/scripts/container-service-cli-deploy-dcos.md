---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie klastra DC/OS usługi ACS
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie klastra DC/OS usługi ACS
author: iainfoulds
tags: acs, azure-container-service
keywords: Docker, kontenery, mikrousługi, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: b01d8d58da5a25ca9aa3d1ac16f10495fde8fc2b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76270544"
---
# <a name="deprecated-create-an-azure-container-service-dcos-cluster"></a>(PRZESTARZAŁE) Tworzenie klastra DC/OS usługi Azure Container Service

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

W tym przykładzie tworzony jest klaster usługi Azure Container Service z systemem DCOS.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az-acs-create) | Tworzy klaster usługi ACS. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładów skryptów interfejsu wiersza polecenia usługi Azure Container Service można znaleźć w [dokumentacji usługi Azure Container Service](../cli-samples.md).
