---
title: Synchronizuj zawartość z folderu w chmurze
description: Dowiedz się, jak wdrożyć aplikację do Azure App Service za pośrednictwem synchronizacji zawartości z folderu w chmurze, w tym OneDrive lub Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051243"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizuj zawartość z folderu w chmurze w celu Azure App Service
W tym artykule pokazano, jak zsynchronizować zawartość do [Azure App Service](./overview.md) z usługi Dropbox i OneDrive. 

Dzięki podejściu do synchronizacji zawartości pracujesz z kodem aplikacji i zawartością w wywskazanym folderze w chmurze, aby upewnić się, że jest on gotowy do wdrożenia, a następnie zsynchronizuj się z App Service kliknięciem przycisku. 

Ze względu na różnice podstawowe w interfejsach API **Usługa OneDrive dla firm** nie jest w tej chwili obsługiwana.

> [!NOTE]
> Strona **centrum deweloperskiego (klasyczna)** w Azure Portal, która jest starym środowiskiem wdrażania, będzie przestarzała w marcu 2021. Ta zmiana nie wpłynie na żadne istniejące ustawienia wdrożenia w aplikacji. można nadal zarządzać wdrażaniem aplikacji na stronie **centrum wdrażania** .

## <a name="enable-content-sync-deployment"></a>Włącz wdrożenie synchronizacji zawartości

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania aplikacji App Service.

1. W menu po lewej stronie kliknij pozycję Ustawienia **centrum wdrażania**  >  . 

1. W obszarze **Źródło** wybierz pozycję **OneDrive** lub **Dropbox**.

1. Kliknij przycisk **Autoryzuj** i postępuj zgodnie z monitami o autoryzację. 

    ![Pokazuje, jak autoryzować usługę OneDrive lub Dropbox w centrum wdrażania w Azure Portal.](media/app-service-deploy-content-sync/choose-source.png)

    Musisz tylko raz autoryzować usługę OneDrive lub Dropbox dla Twojego konta platformy Azure. Aby autoryzować inne konto usługi OneDrive lub Dropbox dla aplikacji, kliknij przycisk **Zmień konto**.

1. W **folderze** wybierz folder do zsynchronizowania. Ten folder zostanie utworzony w usłudze OneDrive lub Dropbox za pomocą następującej wskazanej ścieżki zawartości. 
   
    * **Usługa OneDrive**: `Apps\Azure Web Apps`
    * Usługa **Dropbox**:`Apps\Azure`
    
1. Kliknij pozycję **Zapisz**.

## <a name="synchronize-content"></a>Synchronizuj zawartość

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania aplikacji App Service.

1. W menu po lewej stronie kliknij pozycję **Deployment Center**  >  **redeploy/Sync**. 

    ![Pokazuje, jak zsynchronizować folder w chmurze z App Service.](media/app-service-deploy-content-sync/synchronize.png)
   
1. Kliknij przycisk **OK** , aby potwierdzić synchronizację.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Rozpocznij synchronizację, uruchamiając następujące polecenie i zastępując \<group-name> \<app-name> :

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Rozpocznij synchronizację, uruchamiając następujące polecenie i zastępując \<group-name> \<app-name> :

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Wyłącz wdrożenie synchronizacji zawartości

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania aplikacji App Service.

1. W menu po lewej stronie kliknij pozycję Ustawienia **centrum wdrażania**  >    >  **Rozłącz**. 

    ![Pokazuje, jak rozłączyć synchronizację folderu w chmurze z aplikacją App Service w Azure Portal.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdróż z lokalnego repozytorium git](deploy-local-git.md)