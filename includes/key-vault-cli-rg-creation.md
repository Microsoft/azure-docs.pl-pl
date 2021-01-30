---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070277"
---
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Użyj polecenia [AZ Group Create](/cli/azure/group#az_group_create) , aby utworzyć grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* .

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
