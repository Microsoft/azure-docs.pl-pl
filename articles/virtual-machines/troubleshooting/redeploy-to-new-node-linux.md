---
title: Ponowne wdrażanie Linux Virtual Machines na platformie Azure | Microsoft Docs
description: Jak ponownie wdrożyć maszyny wirtualne z systemem Linux na platformie Azure, aby zmniejszyć liczbę problemów z połączeniem SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d553fb6b2061f987e3e098ae47ebca9cd3f60984
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203409"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Ponowne wdrażanie maszyny wirtualnej z systemem Linux w nowym węźle platformy Azure
Jeśli napotykasz problemy z rozwiązywaniem problemów z protokołem SSH lub dostępem aplikacji do maszyny wirtualnej z systemem Linux na platformie Azure, możesz ponownie wdrożyć maszynę wirtualną. Po ponownym wdrożeniu maszyny wirtualnej jest ona przenoszona do nowego węzła w ramach infrastruktury platformy Azure, a następnie ponownie ją włącza. Wszystkie opcje konfiguracji i skojarzone zasoby są zachowywane. W tym artykule pokazano, jak ponownie wdrożyć maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal.

> [!NOTE]
> Po ponownym wdrożeniu maszyny wirtualnej dysk tymczasowy zostanie utracony, a dynamiczne adresy IP skojarzone z interfejsem sieci wirtualnej zostaną zaktualizowane. 


## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Zainstaluj najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

Wdróż ponownie maszynę wirtualną za pomocą narzędzia [AZ VM redeploy](/cli/azure/vm). Poniższy przykład ponownie wdraża maszynę wirtualną o nazwie *myVM* w grupie zasobów o nazwie Moja *zasobów*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Zainstaluj [najnowszy klasyczny interfejs wiersza polecenia platformy Azure](/cli/azure/install-classic-cli) i zaloguj się do konta platformy Azure. Upewnij się, że jesteś w trybie Menedżer zasobów ( `azure config mode arm` ).

Poniższy przykład ponownie wdraża maszynę wirtualną o nazwie *myVM* w grupie zasobów o nazwie Moja *zasobów*:

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, możesz znaleźć konkretną pomoc dotyczącą [rozwiązywania problemów z połączeniami SSH](troubleshoot-ssh-connection.md) lub [szczegółowymi krokami rozwiązywania problemów SSH](detailed-troubleshoot-ssh-connection.md). Jeśli nie możesz uzyskać dostępu do aplikacji uruchomionej na maszynie wirtualnej, możesz również odczytać [problemy związane z rozwiązywaniem problemów z aplikacjami](troubleshoot-app-connection.md).
