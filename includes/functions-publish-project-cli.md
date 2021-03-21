---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93425084"
---
## <a name="deploy-the-function-project-to-azure"></a>Wdróż projekt funkcji na platformie Azure

Po pomyślnym utworzeniu aplikacji funkcji na platformie Azure Możesz teraz przystąpić do wdrażania projektu funkcji lokalnych przy użyciu polecenia [Func Azure functionapp Publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) .  

W poniższym przykładzie Zastąp `<APP_NAME>` nazwą swojej aplikacji.

```console
func azure functionapp publish <APP_NAME>
```

Polecenie Publikuj wyświetla wyniki podobne do następujących danych wyjściowych (obcięty dla uproszczenia):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
