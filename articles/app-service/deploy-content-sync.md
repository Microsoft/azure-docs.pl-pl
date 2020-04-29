---
title: Synchronizuj zawartość z folderu w chmurze
description: Dowiedz się, jak wdrożyć aplikację do Azure App Service za pośrednictwem synchronizacji zawartości z folderu w chmurze, w tym OneDrive lub Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77482976"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizuj zawartość z folderu w chmurze w celu Azure App Service
W tym artykule pokazano, jak zsynchronizować zawartość do [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) z usługi Dropbox i OneDrive. 

Wdrożenie synchronizacji zawartości na żądanie jest obsługiwane przez [Aparat wdrażania App Service kudu](https://github.com/projectkudu/kudu/wiki). Możesz korzystać z kodu aplikacji i zawartości w wyznaczeniym folderze w chmurze, a następnie synchronizować się z App Service przy użyciu przycisku. Synchronizacja zawartości używa serwera kompilacji kudu. 

## <a name="enable-content-sync-deployment"></a>Włącz wdrożenie synchronizacji zawartości

Aby włączyć synchronizację zawartości, przejdź do strony aplikacji App Service w [Azure Portal](https://portal.azure.com).

W menu po lewej stronie kliknij pozycję **Deployment Center** > **OneDrive** lub **Dropbox** > **Autoryzuj**. Postępuj zgodnie z monitami o autoryzację. 

![](media/app-service-deploy-content-sync/choose-source.png)

Wystarczy tylko autoryzować usługę OneDrive lub Dropbox. Jeśli masz już autoryzację, po prostu kliknij pozycję **Kontynuuj**. Możesz zmienić autoryzowane konto usługi OneDrive lub Dropbox, klikając pozycję **Zmień konto**.

![](media/app-service-deploy-content-sync/continue.png)

Na stronie **Konfigurowanie** wybierz folder, który chcesz synchronizować. Ten folder zostanie utworzony w usłudze OneDrive lub Dropbox za pomocą następującej wskazanej ścieżki zawartości. 
   
* **Usługa OneDrive**:`Apps\Azure Web Apps`
* Usługa **Dropbox**:`Apps\Azure`

Po zakończeniu kliknij przycisk **Kontynuuj**.

Na stronie **Podsumowanie** Sprawdź opcje i kliknij przycisk **Zakończ**.

## <a name="synchronize-content"></a>Synchronizuj zawartość

Jeśli chcesz synchronizować zawartość w folderze w chmurze za pomocą App Service, Wróć do strony **centrum wdrażania** i kliknij pozycję **Synchronizuj**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Ze względu na różnice podstawowe w interfejsach API **Usługa OneDrive dla firm** nie jest w tej chwili obsługiwana. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Wyłącz wdrożenie synchronizacji zawartości

Aby wyłączyć synchronizację zawartości, przejdź do strony aplikacji App Service w [Azure Portal](https://portal.azure.com).

W menu po lewej stronie kliknij pozycję **centrum** > wdrażania**Odłącz**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdróż z lokalnego repozytorium git](deploy-local-git.md)
