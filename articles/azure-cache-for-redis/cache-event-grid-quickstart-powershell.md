---
title: 'Szybki start: przekieruj zdarzenia Azure Cache for Redis do internetowego punktu końcowego przy użyciu programu PowerShell'
description: Użyj Azure Event Grid, aby subskrybować zdarzenia Azure Cache for Redis, wysyłać zdarzenia do webhook i obsługiwać zdarzenia w aplikacji internetowej.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fa1ae9a7a2200944bd5da0211be88ba4955e3d03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833890"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Szybki start: przekieruj zdarzenia Azure Cache for Redis do internetowego punktu końcowego przy użyciu programu PowerShell

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym przewodniku Szybki start użyjesz usługi Azure PowerShell do subskrybowania Azure Cache for Redis zdarzeń, wyzwalania zdarzenia i wyświetlania wyników. 

Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Jednak w celu uproszczenia tego przewodnika Szybki start wyślesz zdarzenia do aplikacji internetowej, która będzie zbierać i wyświetlać komunikaty. Po ukończeniu kroków opisanych w tym przewodniku Szybki start zobaczysz, że dane zdarzenia zostały wysłane do aplikacji internetowej.

## <a name="setup"></a>Konfiguracja

Ten przewodnik Szybki start wymaga najnowszej wersji Azure PowerShell. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia i postępuj zgodnie z `Connect-AzAccount` instrukcjami na ekranie, aby się uwierzytelnić.

```powershell
Connect-AzAccount
```

W tym przykładzie **użyto westus2** i przechowuje wybór w zmiennej do użycia w całym.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Event Grid są wdrażane jako pojedyncze zasoby platformy Azure i muszą być aprowowane w ramach grupy zasobów platformy Azure. Grupa zasobów to kolekcja logiczna, w której zasoby platformy Azure są wdrażane i zarządzane.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie **gridResourceGroup** w lokalizacji **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Tworzenie wystąpienia pamięci podręcznej Azure Cache for Redis 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Aby uzyskać więcej informacji na temat tworzenia wystąpienia pamięci podręcznej w programie PowerShell, zobacz [Azure PowerShell informacje](/powershell/module/az.rediscache/new-azrediscache). 

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem tematu utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. Aby uprościć ten przewodnik Szybki Start, wdrożysz [wstępnie zbudowaną aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer), która będzie wyświetlać komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

Zastąp `<your-site-name>` unikatową nazwą aplikacji internetowej. Nazwa aplikacji internetowej musi być unikatowa, ponieważ stanowi część wpisu DNS.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Powinna być widoczna witryna internetowa bez żadnych aktualnie wyświetlanych komunikatów.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Pusta Event Grid podglądu.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Subskrybowanie Azure Cache for Redis zdarzeń

W tym kroku zasubskrybujesz temat, aby poinformować Event Grid, które zdarzenia chcesz śledzić. Poniższy przykład subskrybuje utworzone wystąpienie pamięci podręcznej i przekazuje adres URL z aplikacji internetowej jako punkt końcowy dla powiadomień o zdarzeniach. Punkt końcowy dla aplikacji internetowej musi zawierać sufiks `/api/updates/`.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja internetowa zawiera kod do sprawdzania poprawności subskrypcji.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Wyzwalanie zdarzenia z Azure Cache for Redis

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Aby uzyskać więcej informacji na temat importowania w programie PowerShell, zobacz [Azure PowerShell informacje](/powershell/module/az.rediscache/import-azrediscache). 

Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Wyświetl aplikację sieci Web, aby wyświetlić właśnie wysłane zdarzenie.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli planujesz kontynuować pracę z tym wystąpieniem Azure Cache for Redis subskrypcją zdarzeń, nie czyść zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, użyj następującego polecenia, aby usunąć zasoby utworzone w tym przewodniku Szybki start.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć tematy i subskrypcje zdarzeń, dowiedz się więcej na temat Azure Cache for Redis zdarzeń i Event Grid, które mogą Ci pomóc:

- [Reagowanie na Azure Cache for Redis zdarzeń](cache-event-grid.md)
- [Event Grid — informacje](../event-grid/overview.md)
