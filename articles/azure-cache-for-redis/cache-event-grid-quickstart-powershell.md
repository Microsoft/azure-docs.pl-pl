---
title: 'Szybki Start: kierowanie pamięci podręcznej Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu programu PowerShell'
description: Użyj Azure Event Grid, aby subskrybować usługę Azure cache dla zdarzeń Redis, wysyłać zdarzenia do elementu webhook i obsługiwać zdarzenia w aplikacji sieci Web.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 615f3b023ded6583dfedca99f561d09689b86b51
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055532"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Szybki Start: kierowanie pamięci podręcznej Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu programu PowerShell

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym przewodniku szybki start użyjesz Azure PowerShell, aby subskrybować usługę Azure cache dla zdarzeń Redis, wyzwalać zdarzenie i przeglądać wyniki. 

Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Aby uprościć ten przewodnik Szybki Start, będziesz wysyłać zdarzenia do aplikacji sieci Web, która będzie zbierać i wyświetlać komunikaty. Po wykonaniu kroków opisanych w tym przewodniku szybki start zobaczysz, że dane zdarzenia zostały wysłane do aplikacji sieci Web.

## <a name="setup"></a>Konfigurowanie

Ten przewodnik Szybki Start wymaga uruchomienia najnowszej wersji programu Azure PowerShell. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Connect-AzAccount
```

W tym przykładzie użyto **westus2** i przechowuje wybór w zmiennej do użycia w ciągu.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tematy Event Grid są wdrażane jako indywidualne zasoby platformy Azure i muszą być obsługiwane w ramach grupy zasobów platformy Azure. Grupa zasobów to logiczna kolekcja, w której zasoby platformy Azure są wdrażane i zarządzane.

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
Aby uzyskać więcej informacji na temat tworzenia wystąpienia pamięci podręcznej w programie PowerShell, zobacz [informacje dotyczące Azure PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache?view=azps-5.2.0). 

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

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Pusta witryna Event Grid Viewer.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Subskrybowanie usługi Azure cache dla zdarzenia Redis

W tym kroku zasubskrybujesz temat, aby poinformować Event Grid, które zdarzenia mają być śledzone. Poniższy przykład subskrybuje utworzone wystąpienie pamięci podręcznej i przekazuje adres URL z aplikacji sieci Web jako punkt końcowy dla powiadomienia o zdarzeniu. Punkt końcowy dla aplikacji internetowej musi zawierać sufiks `/api/updates/`.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja internetowa zawiera kod do sprawdzania poprawności subskrypcji.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Przeglądarka Azure Event Grid.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Wyzwalanie zdarzenia z usługi Azure cache for Redis

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
Aby uzyskać więcej informacji na temat importowania w programie PowerShell, zobacz [informacje dotyczące Azure PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/import-azrediscache?view=azps-5.2.0). 

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
Jeśli planujesz kontynuować pracę z tą usługą Azure cache for Redis i subskrypcją zdarzeń, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, użyj następującego polecenia, aby usunąć zasoby utworzone w ramach tego przewodnika Szybki Start.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć tematy i subskrypcje zdarzeń, Dowiedz się więcej o usłudze Azure cache dla zdarzeń Redis i jakie Event Grid mogą pomóc:

- [Oddziałanie do usługi Azure cache dla zdarzeń Redis](cache-event-grid.md)
- [Event Grid — informacje](../event-grid/overview.md)