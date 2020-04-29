---
title: Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu | Microsoft Docs
description: Dowiedz się, jak archiwizować dzienniki zasobów platformy Azure w celu długoterminowego przechowywania na koncie magazynu.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 787640ef08ee91220f42065af155c372247f0136
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804609"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą.  W tym artykule opisano zbieranie dzienników platformy na koncie usługi Azure Storage w celu zachowania danych do archiwizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jeszcze tego nie zrobiono, musisz [utworzyć konto usługi Azure Storage](../../storage/common/storage-account-create.md) . Konto magazynu nie musi znajdować się w tej samej subskrypcji co zasób wysyła dzienniki, dopóki użytkownik, który konfiguruje ustawienie, ma dostęp do obu subskrypcji.

> [!IMPORTANT]
> Aby wysłać dane do niezmiennego magazynu, należy ustawić niezmienne zasady dla konta magazynu zgodnie z opisem w temacie [Set and Manage niezmienności Policy for BLOB Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md). Należy wykonać wszystkie kroki opisane w tym artykule, w tym Włączanie chronionych zapisów obiektów BLOB.

> [!IMPORTANT]
> Konta Azure Data Lake Storage Gen2 nie są obecnie obsługiwane jako miejsce docelowe dla ustawień diagnostycznych, mimo że mogą być wymienione jako prawidłowe opcje w Azure Portal.


Nie należy używać istniejącego konta magazynu, które ma inne niemonitorowane dane, które są w nim przechowywane, dzięki czemu można lepiej kontrolować dostęp do danych. Jeśli archiwizowanie dzienników aktywności i dzienników zasobów odbywa się razem, możesz użyć tego samego konta magazynu, aby zachować wszystkie dane monitorowania w centralnej lokalizacji.

## <a name="create-a-diagnostic-setting"></a>Utwórz ustawienie diagnostyczne
Wyślij dzienniki platformy do magazynu i innych miejsc docelowych, tworząc ustawienia diagnostyczne dla zasobu platformy Azure. Aby uzyskać szczegółowe informacje [, zobacz Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure](diagnostic-settings.md) .


## <a name="collect-data-from-compute-resources"></a>Zbieranie danych z zasobów obliczeniowych
Ustawienia diagnostyczne będą zbierać dzienniki zasobów dla zasobów obliczeniowych platformy Azure, takich jak każdy inny zasób, ale nie system operacyjny gościa ani ich obciążenia. Aby zebrać te dane, zainstaluj [agenta Diagnostyka Azure systemu Windows](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schemat dzienników platformy na koncie magazynu

Po utworzeniu ustawienia diagnostycznego kontener magazynu jest tworzony na koncie magazynu zaraz po wystąpieniu zdarzenia w jednej z włączonych kategorii dzienników. Obiekty blob w kontenerze używają następującej konwencji nazewnictwa:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład obiekt BLOB sieciowej grupy zabezpieczeń może mieć nazwę podobną do następującej:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Każdy obiekt blob PT1H.json zawiera obiekt blob JSON ze zdarzeniami, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość minuta (m = 00) jest zawsze równa 00, ponieważ zdarzenia dziennika zasobów są podzielone na pojedyncze obiekty blob na godzinę.

W pliku PT1H. JSON każde zdarzenie jest przechowywane w następującym formacie. Spowoduje to użycie wspólnego schematu najwyższego poziomu, ale jest unikatowa dla każdej usługi platformy Azure, zgodnie z opisem w schemacie [dzienniki zasobów](diagnostic-logs-schema.md) i [schemat dziennika aktywności](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Dzienniki platformy są zapisywane w usłudze BLOB Storage przy użyciu [wierszy JSON](http://jsonlines.org/), gdzie każde zdarzenie jest wierszem, a znak nowego wiersza wskazuje nowe zdarzenie. Ten format został zaimplementowany w listopadzie 2018. Przed tą datą dzienniki zostały zapisane w magazynie obiektów BLOB jako tablica rekordów JSON, zgodnie z opisem w artykule [przygotowanie do zmiany formatu do Azure monitor dzienników platformy zarchiwizowanych na koncie magazynu](resource-logs-blob-format.md).

## <a name="next-steps"></a>Następne kroki

* [Przeczytaj więcej na temat dzienników zasobów](platform-logs-overview.md).
* [Utwórz ustawienie diagnostyczne, aby zbierać dzienniki i metryki na platformie Azure](diagnostic-settings.md).
* [Pobierz obiekty blob do analizy](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiwizuj dzienniki Azure Active Directory przy użyciu Azure monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
