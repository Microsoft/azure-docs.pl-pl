---
title: Zestawy SDK Azure Event Grid
description: Opisuje zestawy SDK dla Azure Event Grid. Te zestawy SDK umożliwiają zarządzanie, publikowanie i użycie.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: 5e93448ef703be88583cd59ae5eee9d1993c4054
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322533"
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

| Język programowania | SDK | 
| -------------------- | ---------- | ---------- | 
| .NET | Stabilny zestaw SDK: [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>Zestaw SDK wersji zapoznawczej: [Azure. Messaging. EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | Stabilny zestaw SDK: [Azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>Zestaw SDK wersji zapoznawczej: [Azure-Messaging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [Azure-eventgrid](https://pypi.org/project/azure-eventgrid/#history) (Zobacz najnowsze wersje stabilne i w wersji wstępnej na stronie **historia wersji** ). |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) (przejdź do karty **wersje** , aby zobaczyć najnowsze, stabilne i w wersji beta pakiety). | 
| Przejdź | [Zestaw Azure SDK dla języka Go](https://github.com/Azure/azure-sdk-for-go) | | 
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) | | 


## <a name="next-steps"></a>Następne kroki

* Przykładowe aplikacje można znaleźć w temacie [Event Grid Samples](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Aby zapoznać się z wprowadzeniem do Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać Event Grid poleceń w interfejsie wiersza polecenia platformy Azure, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/eventgrid).
* Aby uzyskać Event Grid polecenia w programie PowerShell, zobacz [PowerShell](/powershell/module/az.eventgrid).
