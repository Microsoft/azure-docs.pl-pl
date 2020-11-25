---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018448"
---
## <a name="deployment-customization"></a>Dostosowanie wdrożenia

W procesie wdrażania przyjęto założenie, że wypchnięci plik. zip zawiera gotowy do uruchomienia aplikację. Domyślnie żadne dostosowania nie są uruchamiane. Aby włączyć te same procesy kompilacji, które są uzyskiwane z ciągłą integracją, Dodaj następujące elementy do ustawień aplikacji:

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

W przypadku korzystania z polecenia. zip push Deployment to ustawienie domyślnie ma **wartość false** . Wartość domyślna to **true** w przypadku wdrożeń ciągłej integracji. Po ustawieniu na **wartość true** ustawienia związane z wdrożeniem są używane podczas wdrażania. Te ustawienia można skonfigurować jako ustawienia aplikacji lub w pliku konfiguracji wdrożenia, który znajduje się w katalogu głównym pliku zip. Aby uzyskać więcej informacji, zobacz temat [repozytorium i ustawienia związane z wdrażaniem](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) w odwołaniu do wdrożenia.