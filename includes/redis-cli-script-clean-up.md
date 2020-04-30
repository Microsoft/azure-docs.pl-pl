---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67183035"
---
## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładowego skryptu można użyć poniższego polecenia, aby usunąć grupę zasobów, wystąpienie usługi Azure cache for Redis oraz wszystkie powiązane zasoby w grupie zasobów.

```azurecli
az group delete --name contosoGroup
```