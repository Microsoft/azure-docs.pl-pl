---
title: Przywracanie usuniętych aplikacji
description: Dowiedz się, jak przywrócić usuniętą aplikację w Azure App Service. Unikaj przypadkowego usunięcia aplikacji.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e894e0a8bd20d6a1c3c833a4c0a3656c0dcd0f05
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833170"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Przywracanie usuniętej aplikacji usługi App Service przy użyciu programu PowerShell

Jeśli przypadkowo usuniesz aplikację w programie Azure App Service, możesz ją przywrócić przy użyciu poleceń z modułu [Az programu PowerShell.](/powershell/azure/)

> [!NOTE]
> - Usunięte aplikacje są usuwane z systemu 30 dni po początkowym usunięciu. Po przeczyszczeniu aplikacji nie można jej odzyskać.
> - Cofanie funkcji nie jest obsługiwane w przypadku planu Zużycie.
> - Aplikacje usługi Apps Service działające w App Service Environment nie obsługują migawek. W związku z tym funkcje cofniania i klonowania nie są obsługiwane w przypadku App Service działających w App Service Environment.
>

## <a name="re-register-app-service-resource-provider"></a>Ponowne rejestrowanie App Service dostawcy zasobów

Niektórzy klienci mogą mieć problem z pobieraniem listy usuniętych aplikacji. Aby rozwiązać ten problem, uruchom następujące polecenie:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lista usuniętych aplikacji

Aby pobrać kolekcję usuniętych aplikacji, możesz użyć `Get-AzDeletedWebApp` .

Aby uzyskać szczegółowe informacje na temat określonej usuniętej aplikacji, możesz użyć:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Szczegółowe informacje obejmują:

- **DeletedSiteId:** unikatowy identyfikator aplikacji używany w scenariuszach, w których usunięto wiele aplikacji o tej samej nazwie
- **SubscriptionID:** subskrypcja zawierająca usunięty zasób
- **Lokalizacja:** Lokalizacja oryginalnej aplikacji
- **ResourceGroupName:** nazwa oryginalnej grupy zasobów
- **Nazwa:** nazwa oryginalnej aplikacji.
- **Miejsce:** nazwa miejsca.
- **Czas usunięcia:** kiedy aplikacja została usunięta  

## <a name="restore-deleted-app"></a>Przywracanie usuniętej aplikacji

>[!NOTE]
> `Restore-AzDeletedWebApp` Nie jest obsługiwana w przypadku aplikacji funkcji.

Po zidentyfikowaniu aplikacji, którą chcesz przywrócić, możesz ją przywrócić przy użyciu narzędzia `Restore-AzDeletedWebApp` .

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Miejsca wdrożenia nie są przywracane w ramach aplikacji. Jeśli musisz przywrócić miejsce przejściowe, użyj `-Slot <slot-name>`  flagi .
>

Dane wejściowe dla polecenia są:

- **Docelowa grupa zasobów:** docelowa grupa zasobów, w której aplikacja zostanie przywrócona
- **Nazwa:** nazwa aplikacji powinna być globalnie unikatowa.
- **TargetAppServicePlanName:** App Service planu połączonego z aplikacją

Domyślnie `Restore-AzDeletedWebApp` przywracana jest zarówno konfiguracja aplikacji, jak i dowolna zawartość. Jeśli chcesz przywrócić tylko zawartość, użyj `-RestoreContentOnly` flagi z tym poleceniem polecenia.

> [!NOTE]
> Jeśli aplikacja była hostowana na stronie, a następnie usunięta z App Service Environment, można ją przywrócić tylko wtedy, gdy App Service Environment nadal istnieje.
>

Pełną listę poleceń polecenia można znaleźć tutaj: [Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp).
