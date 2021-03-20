---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183035"
---
## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładowego skryptu można użyć poniższego polecenia, aby usunąć grupę zasobów, wystąpienie usługi Azure cache for Redis oraz wszystkie powiązane zasoby w grupie zasobów.

```azurecli
az group delete --name contosoGroup
```