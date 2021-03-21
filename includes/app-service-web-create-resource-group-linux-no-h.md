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
ms.openlocfilehash: d1f78034c2142bfb0fc787683b7efed22ae2698c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244836"
---
[!INCLUDE [resource group intro text](resource-group.md)]

W Cloud Shell Utwórz grupę zasobów za pomocą [`az group create`](/cli/azure/group#az-group-create) polecenia. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *West Europe* (Europa Zachodnia). Aby wyświetlić wszystkie obsługiwane lokalizacje dla usługi App Service w systemie Linux w warstwie **Podstawowa**, uruchom polecenie [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie. 

Po zakończeniu działania polecenia zostaną wyświetlone dane wyjściowe JSON z właściwościami grupy zasobów.