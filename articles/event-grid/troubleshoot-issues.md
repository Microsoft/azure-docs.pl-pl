---
title: Rozwiązywanie problemów dotyczących Event Grid
description: W tym artykule przedstawiono różne sposoby rozwiązywania problemów Azure Event Grid problemów
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 9c52ba8561c10dd94ec6ef51c78b8534c6c58e96
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417598"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Rozwiązywanie problemów dotyczących Azure Event Grid
Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów Azure Event Grid. 

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Włącz ustawienia diagnostyczne dla tematów Event Grid lub domen w celu przechwytywania i wyświetlania dzienników błędów publikowania i dostarczania. Aby uzyskać więcej informacji, zobacz [dzienniki diagnostyczne](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Metryki
Możesz wyświetlić metryki dla tematów Event Grid i subskrypcji i utworzyć na nich alerty. Aby uzyskać więcej informacji, zobacz [Event Grid metryki](monitor-event-delivery.md).

## <a name="alerts"></a>Alerty
Utwórz alerty dotyczące Azure Event Grid metryk i operacji dziennika aktywności. Aby uzyskać więcej informacji, zobacz [alerty dotyczące Event Grid metryk i dzienników aktywności](set-alerts.md).

## <a name="subscription-validation-issues"></a>Problemy z walidacją subskrypcji
Podczas tworzenia subskrypcji zdarzeń może zostać wyświetlony komunikat o błędzie z informacją, że weryfikacja podanego punktu końcowego nie powiodła się. Rozwiązywanie problemów z walidacją subskrypcji można znaleźć w temacie [Rozwiązywanie problemów dotyczących subskrypcji Event Grid](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Problemy z łącznością sieciową
Istnieją różne przyczyny, dla których aplikacje klienckie nie mogą nawiązać połączenia z Event Grid tematem/domeną. Problemy z łącznością mogą być stałe lub przejściowe. Aby dowiedzieć się, jak rozwiązać te problemy, zobacz [Rozwiązywanie problemów z łącznością](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Kody błędów
Jeśli pojawią się komunikaty o błędach z kodami błędów, takimi jak 400, 409 i 403, zobacz [Rozwiązywanie problemów Event Grid błędów](troubleshoot-errors.md). 

## <a name="distributed-tracing-net"></a>Śledzenie rozproszone (.NET)
Biblioteka Event Grid .NET obsługuje dystrybucję śledzenia. Aby stosować wskazówki dotyczące dystrybuowania śledzenia [CloudEvents](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) , biblioteka ustawia `traceparent` i `tracestate` w [ExtensionAttributes](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization/CloudEvent.cs#L126) , `CloudEvent` gdy włączone jest śledzenie rozproszone. Aby dowiedzieć się więcej na temat włączania rozproszonego śledzenia w aplikacji, zapoznaj się z [dokumentacją śledzenia rozproszonego](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)zestawu Azure SDK.

### <a name="sample"></a>Przykład
Zobacz [przykład licznika wierszy](/samples/azure/azure-sdk-for-net/line-counter/). Ta przykładowa aplikacja ilustruje użycie usług Storage, Event Hubs i Event Grid, a także klientów z integracją ASP.NET Core, śledzeniem rozproszonym i usługami hostowanymi. Umożliwia użytkownikom przekazywanie pliku do obiektu BLOB, który wyzwala zdarzenie Event Hubs zawierające nazwę pliku. Procesor Event Hubs odbiera zdarzenie, a następnie aplikacja pobiera obiekt BLOB i zlicza liczbę wierszy w pliku. Aplikacja wyświetli link do strony zawierającej liczbę wierszy. Po kliknięciu linku CloudEvent zawierający nazwę pliku jest publikowany przy użyciu Event Grid.

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, Opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
