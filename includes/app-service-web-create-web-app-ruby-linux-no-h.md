---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 07/10/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 01c1c7c0bdf8e55b002b73682407a3e03ee30b8f
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085788"
---
Utwórz [aplikację](../articles/app-service/containers/app-service-linux-intro.md) internetową `myAppServicePlan` w planie usługi app service. 

W powłoki chmury można [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) użyć polecenia. W poniższym przykładzie zastąp ciąg `<app-name>` globalnie unikatową nazwą aplikacji (prawidłowe znaki to `a-z`, `0-9` i `-`). Środowisko uruchomieniowe ma ustawioną wartość `RUBY|2.3`. Aby wyświetlić wszystkie obsługiwane środowiska [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest)wykonawcze, uruchom plik . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Utworzona została nowa pusta aplikacja internetowa z włączonym wdrażaniem git.

> [!NOTE]
> Adres URL zdalnego repozytorium Git jest wyświetlany we właściwości `deploymentLocalGitUrl` w formacie `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Zapisz ten adres URL, ponieważ będzie on potrzebny później.
>
