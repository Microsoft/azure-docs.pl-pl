---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 520a32ed44951a711dcb1d0975fb452829530c4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244569"
---
[!INCLUDE [resource group intro text](resource-group.md)]

W Cloud Shell Utwórz grupę zasobów za pomocą [`az group create`](/cli/azure/group) polecenia. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *West Europe* (Europa Zachodnia). Aby wyświetlić wszystkie obsługiwane lokalizacje dla usługi App Service w warstwie **Bezpłatna**, uruchom polecenie [`az appservice list-locations --sku FREE`](/cli/azure/appservice).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie. 

Po zakończeniu działania polecenia zostaną wyświetlone dane wyjściowe JSON z właściwościami grupy zasobów.