---
title: Przywróć usunięte aplikacje
description: Dowiedz się, jak przywrócić usuniętą aplikację w Azure App Service. Unikaj kłopotliwej przypadkowo usuniętej aplikacji.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 20c220bcb44a1a47e308f57d1466aee2773111a4
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985686"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Przywracanie usuniętej aplikacji usługi App Service przy użyciu programu PowerShell

Jeśli przypadkowo usuniesz aplikację w Azure App Service, możesz ją przywrócić za pomocą poleceń z [modułu AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> - Usunięte aplikacje są przeczyszczane z systemu 30 dni po początkowym usunięciu. Gdy aplikacja zostanie przeczyszczona, nie można jej odzyskać.
> - Funkcja cofania usunięcia nie jest obsługiwana w przypadku planu zużycia.
> - Aplikacje usługi Apps działające w App Service Environment nie obsługują migawek. W związku z tym usuwanie funkcjonalności i klonowanie nie jest obsługiwane w przypadku aplikacji App Service działających w App Service Environment.
>

## <a name="re-register-app-service-resource-provider"></a>Zarejestruj ponownie dostawcę zasobów App Service

Niektórzy klienci mogą napotkać problem polegający na tym, że pobieranie listy usuniętych aplikacji zakończy się niepowodzeniem. Aby rozwiązać ten problem, uruchom następujące polecenie:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Wyświetlanie listy usuniętych aplikacji

Aby pobrać kolekcję usuniętych aplikacji, można użyć programu `Get-AzDeletedWebApp` .

Aby uzyskać szczegółowe informacje dotyczące konkretnej usuniętej aplikacji, można użyć:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Szczegółowe informacje obejmują:

- **DeletedSiteId**: unikatowy identyfikator aplikacji używany w scenariuszach, w których usunięto wiele aplikacji o tej samej nazwie
- **Identyfikator subskrypcji**: subskrypcja zawierająca usunięty zasób
- **Lokalizacja**: Lokalizacja oryginalnej aplikacji
- **ResourceGroupName**: nazwa oryginalnej grupy zasobów
- **Nazwa**: nazwa oryginalnej aplikacji.
- **Gniazdo**: Nazwa gniazda.
- **Godzina usunięcia**: Kiedy aplikacja została usunięta  

## <a name="restore-deleted-app"></a>Przywróć usuniętą aplikację

>[!NOTE]
> `Restore-AzDeletedWebApp`nie jest obsługiwana w przypadku aplikacji funkcji.

Gdy aplikacja, którą chcesz przywrócić, została zidentyfikowana, możesz ją przywrócić za pomocą polecenia `Restore-AzDeletedWebApp` .

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Gniazda wdrożenia nie są przywracane jako część aplikacji. Jeśli musisz przywrócić miejsce przejściowe, użyj `-Slot <slot-name>` flagi.
>

Dane wejściowe polecenia są następujące:

- **Docelowa Grupa zasobów**: docelowa Grupa zasobów, w której aplikacja zostanie przywrócona
- **Nazwa**: Nazwa aplikacji powinna być globalnie unikatowa.
- **TargetAppServicePlanName**: plan App Service połączony z aplikacją

Domyślnie `Restore-AzDeletedWebApp` program przywraca zarówno konfigurację aplikacji, jak i dowolną zawartość. Jeśli chcesz tylko przywrócić zawartość, użyj `-RestoreContentOnly` flagi z tym polecenia cmdlet.

> [!NOTE]
> Jeśli aplikacja była hostowana, a następnie usunięta z App Service Environment, może zostać przywrócona tylko wtedy, gdy odpowiednie App Service Environment nadal istnieją.
>

Pełne odwołanie polecenia cmdlet można znaleźć tutaj: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
