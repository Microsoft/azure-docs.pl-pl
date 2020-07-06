---
title: Zestawy SDK Azure Event Grid
description: Opisuje zestawy SDK dla Azure Event Grid. Te zestawy SDK umożliwiają zarządzanie, publikowanie i użycie.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "60822845"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid zestawy SDK do zarządzania i publikowania

Event Grid udostępnia zestawy SDK, które umożliwiają programowe zarządzanie zasobami i wysyłanie zdarzeń.

## <a name="management-sdks"></a>Zestawy SDK zarządzania

Zestawy SDK zarządzania umożliwiają tworzenie, aktualizowanie i usuwanie tematów i subskrypcji w usłudze Event Grid. Obecnie dostępne są następujące zestawy SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Przejdź](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Węzeł](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Zestawy SDK płaszczyzny danych

Zestawy SDK płaszczyzny danych umożliwiają ogłaszanie zdarzeń w tematach przez poopiekę o uwierzytelnianiu, tworzeniu zdarzenia i asynchronicznym księgowaniu w określonym punkcie końcowym. Umożliwiają one również korzystanie z zdarzeń pierwszej firmy. Obecnie dostępne są następujące zestawy SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Przejdź](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Węzeł](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Następne kroki

* Przykładowe aplikacje można znaleźć w temacie [Event Grid Samples](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Aby zapoznać się z wprowadzeniem do Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać Event Grid poleceń w interfejsie wiersza polecenia platformy Azure, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/eventgrid).
* Aby uzyskać Event Grid polecenia w programie PowerShell, zobacz [PowerShell](/powershell/module/az.eventgrid).
