---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — szyfrowanie maszyny wirtualnej z systemem Linux
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — szyfrowanie maszyny wirtualnej z systemem Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 610bc83925fb543d835df357960c5977860c0189
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479653"
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Szyfrowanie maszyny wirtualnej z systemem Linux na platformie Azure

Ten skrypt tworzy bezpieczny magazyn Azure Key Vault, klucze szyfrowania, jednostkę usługi Azure Active Directory i maszynę wirtualną z systemem Linux. Maszyna wirtualna jest następnie szyfrowana przy użyciu klucza szyfrowania z magazynu Key Vault i poświadczeń jednostki usługi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, magazynu Azure Key Vault, jednostki usługi, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az keyvault create](/cli/azure/keyvault) | Tworzy magazyn Azure Key Vault do przechowywania zabezpieczonych danych, takich jak klucze szyfrowania. |
| [az keyvault key create](/cli/azure/keyvault/key) | Tworzy klucz szyfrowania w magazynie Key Vault. |
| [az ad sp create-for-rbac](/cli/azure/ad/sp) | Tworzy jednostkę usługi Azure Active Directory używaną do bezpiecznego uwierzytelniania i sterowania dostępem do kluczy szyfrowania. |
| [az keyvault set-policy](/cli/azure/keyvault) | Ustawia uprawnienia w magazynie Key Vault w celu udzielenia jednostce usługi dostępu do kluczy szyfrowania. |
| [az vm create](/cli/azure/vm) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az vm encryption enable](/cli/azure/vm/encryption) | Włącza szyfrowanie na maszynie wirtualnej przy użyciu poświadczeń jednostki usługi i klucza szyfrowania. |
| [az vm encryption show](/cli/azure/vm/encryption) | Wyświetla stan procesu szyfrowania maszyny wirtualnej. |
| [az group delete](/cli/azure/vm/extension) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
