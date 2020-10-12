---
title: Ponowne wdrażanie maszyn wirtualnych z systemem Windows na platformie Azure | Microsoft Docs
description: Jak ponownie wdrożyć maszyny wirtualne z systemem Windows na platformie Azure, aby zmniejszyć liczbę problemów z połączeniem RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: afbea39a080e1dd768a14d6e0eacda1bad23c5a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074420"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Ponowne wdrażanie maszyny wirtualnej z systemem Windows w nowym węźle platformy Azure
Jeśli nastąpiło problemy związane z rozwiązywaniem problemów Pulpit zdalny (RDP) lub dostępem aplikacji do maszyny wirtualnej platformy Azure opartej na systemie Windows, ponowne wdrożenie maszyny wirtualnej może pomóc. Po ponownym wdrożeniu maszyny wirtualnej platforma Azure zamknie maszynę wirtualną, przenosi ją do nowego węzła w ramach infrastruktury platformy Azure, a następnie włączy ją z powrotem, zachowując wszystkie opcje konfiguracji i skojarzone zasoby. W tym artykule pokazano, jak ponownie wdrożyć maszynę wirtualną przy użyciu Azure PowerShell lub Azure Portal.

> [!NOTE]
> Po ponownym wdrożeniu maszyny wirtualnej dysk tymczasowy zostanie utracony, a dynamiczne adresy IP skojarzone z interfejsem sieci wirtualnej zostaną zaktualizowane. 


## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Upewnij się, że na maszynie zainstalowano najnowszą wersję Azure PowerShell 1. x. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).

W poniższym przykładzie wdrożono maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` :

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, możesz znaleźć konkretną pomoc dotyczącą [rozwiązywania problemów z połączeniami RDP](troubleshoot-rdp-connection.md) lub [szczegółowymi krokami rozwiązywania problemów RDP](detailed-troubleshoot-rdp.md). Jeśli nie możesz uzyskać dostępu do aplikacji uruchomionej na maszynie wirtualnej, możesz również odczytać [problemy związane z rozwiązywaniem problemów z aplikacjami](./troubleshoot-app-connection.md).
