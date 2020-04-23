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
ms.openlocfilehash: add73e1399b11d4bd8f039c72bb4e5e9f2b82cfa
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085247"
---
W aplikacji Cloud Shell utwórz plan [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest) usługi app service za pomocą polecenia.

<!-- [!INCLUDE [app-service-plan](app-service-plan.md)] -->

W poniższym przykładzie jest tworzony plan usługi App Service o nazwie `myAppServicePlan` przy użyciu warstwy cenowej **Bezpłatna**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Po utworzeniu planu usługi App Service interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następujących:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>
