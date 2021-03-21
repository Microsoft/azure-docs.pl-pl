---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070277"
---
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Użyj polecenia [AZ Group Create](/cli/azure/group#az_group_create) , aby utworzyć grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* .

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
