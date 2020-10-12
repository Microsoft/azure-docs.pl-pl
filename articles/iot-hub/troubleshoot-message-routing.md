---
title: Rozwiązywanie problemów z routingiem komunikatów usługi Azure IoT
description: Jak przeprowadzić rozwiązywanie problemów dotyczących routingu komunikatów usługi Azure IoT
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 871a4c7d99fc44cf9868f19e41560e6e7a2e22f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793324"
---
# <a name="troubleshooting-message-routing"></a>Rozwiązywanie problemów z routingiem komunikatów

Ten artykuł zawiera wskazówki dotyczące monitorowania i rozwiązywania problemów z typowymi problemami i rozwiązaniami dotyczącymi IoT Hub [routingu komunikatów](iot-hub-devguide-messages-d2c.md). 

## <a name="monitoring-message-routing"></a>Monitorowanie routingu komunikatów

[Metryki IoT Hub](iot-hub-metrics.md) wyświetlają wszystkie metryki, które są domyślnie włączone dla IoT Hub. Zalecamy monitorowanie metryk związanych z routingiem i punktami końcowymi komunikatów w celu uzyskania przeglądu wysłanych komunikatów. Należy również włączyć [dzienniki diagnostyczne](iot-hub-monitor-resource-health.md) w Azure monitor ustawienia diagnostyczne, aby śledzić operacje dla **tras**. Te dzienniki diagnostyczne mogą być wysyłane do dzienników Azure Monitor, Event Hubs lub Azure Storage na potrzeby przetwarzania niestandardowego. Dowiedz się [, jak skonfigurować metryki i dzienniki diagnostyczne przy użyciu IoT Hub i korzystać z](tutorial-use-metrics-and-diags.md)nich.

Zalecamy również włączenie [trasy rezerwowej](iot-hub-devguide-messages-d2c.md#fallback-route) , jeśli chcesz zachować komunikaty, które nie pasują do zapytania na żadnej trasie. Można je zachować w [wbudowanym punkcie końcowym](iot-hub-devguide-messages-read-builtin.md) dla skonfigurowanej liczby dni przechowywania. 

## <a name="top-issues"></a>Najważniejsze problemy

Poniżej przedstawiono najczęstsze problemy związane z routingiem komunikatów. Aby rozpocząć rozwiązywanie problemów, kliknij problem, aby zapoznać się ze szczegółowymi krokami.

* [Komunikaty z urządzeń nie są kierowane zgodnie z oczekiwaniami](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Nagle zatrzymałem pobieranie komunikatów w wbudowanym punkcie końcowym Event Hubs](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>Komunikaty z urządzeń nie są kierowane zgodnie z oczekiwaniami

Aby rozwiązać ten problem, Przeanalizuj poniższe elementy.

#### <a name="the-routing-metrics-for-this-endpoint"></a>Metryki routingu dla tego punktu końcowego
Wszystkie [metryki IoT Hub](iot-hub-devguide-endpoints.md) związane z routingiem są poprzedzone *routingiem*. Możesz połączyć informacje z wielu metryk, aby zidentyfikować główną przyczynę problemów. Na przykład użyj **usługi dostarczania routingu** metryk, aby zidentyfikować liczbę komunikatów dostarczonych do punktu końcowego lub porzuconych, gdy nie są one zgodne z kwerendami na żadnej trasie, a trasa rezerwowa została wyłączona. Sprawdź metrykę **opóźnienia routingu** , aby sprawdzić, czy opóźnienie dostarczania komunikatów jest stałe lub zwiększa się. Rosnące opóźnienie może wskazywać na problem z określonym punktem końcowym, a firma Microsoft zaleca sprawdzanie [kondycji punktu końcowego](#the-health-of-the-endpoint). Te metryki routingu zawierają również [Wymiary](iot-hub-metrics.md#dimensions) , które zawierają szczegóły dotyczące metryki, takie jak typ punktu końcowego, nazwa określonego punktu końcowego i powód, dla którego wiadomość nie została dostarczona.

#### <a name="the-diagnostic-logs-for-any-operational-issues"></a>Dzienniki diagnostyczne dla wszelkich problemów operacyjnych 
Zapoznaj się z [dziennikami diagnostycznymi](iot-hub-monitor-resource-health.md#routes) **trasy** , aby uzyskać więcej informacji na temat [operacji](#operation-names) routingu i punktów końcowych lub zidentyfikować błędy i odpowiedni [Kod błędu](#common-error-codes) , aby dokładniej zrozumieć problem. Na przykład nazwa operacji **RouteEvaluationError** w dzienniku wskazuje, że nie można ocenić trasy z powodu problemu z formatem wiadomości. Aby wyeliminować problem, Skorzystaj z porad dostarczonych dla konkretnych [nazw operacji](#operation-names) . Gdy zdarzenie jest rejestrowane jako błąd, dziennik również zawiera więcej informacji na temat przyczyny niepowodzenia oceny. Na przykład, jeśli nazwa operacji to **EndpointUnhealthy**, [kody błędów](#common-error-codes) 403004 wskazuje, że punkt końcowy kończy się poza miejscem.

#### <a name="the-health-of-the-endpoint"></a>Kondycja punktu końcowego
Użyj interfejsu API REST [Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) , aby uzyskać [stan kondycji](iot-hub-devguide-endpoints.md#custom-endpoints) punktów końcowych. Interfejs API *kondycji Get Endpoint* zawiera również informacje dotyczące ostatniego wysłania komunikatu do punktu końcowego, [ostatniego znanego błędu](#last-known-errors-for-iot-hub-routing-endpoints), ostatniego znanego błędu i ostatniej próby wysłania dla tego punktu końcowego. Należy zastosować możliwe środki zaradcze dla określonego [ostatniego znanego błędu](#last-known-errors-for-iot-hub-routing-endpoints).

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Nagle zatrzymałem pobieranie komunikatów w wbudowanym punkcie końcowym

Aby rozwiązać ten problem, Przeanalizuj poniższe elementy.

#### <a name="was-a-new-route-created"></a>Czy utworzono nową trasę?
Po utworzeniu trasy dane przestają przepływać do wbudowanego punktu końcowego, chyba że trasa zostanie utworzona do tego punktu końcowego. Aby zapewnić, że komunikaty będą nadal przepływać do wbudowanego punktu końcowego w przypadku dodania nowej trasy, skonfiguruj trasę do punktu końcowego *zdarzenia* . 

#### <a name="was-the-fallback-route-disabled"></a>Czy ta trasa rezerwowa jest wyłączona?
Trasa rezerwowa wysyła wszystkie komunikaty, które nie spełniają warunków zapytania na żadnej z istniejących tras do [wbudowanych Event Hubs](iot-hub-devguide-messages-read-builtin.md) (komunikaty/zdarzenia), które są zgodne z [Event Hubs](https://docs.microsoft.com/azure/event-hubs/). Jeśli jest włączona funkcja routingu wiadomości, można włączyć funkcję trasy rezerwowej. Jeśli nie ma tras do wbudowanego punktu końcowego, a trasa rezerwowa jest włączona, tylko komunikaty, które nie pasują do żadnych warunków zapytania dotyczące tras, będą wysyłane do wbudowanego punktu końcowego. Ponadto jeśli wszystkie istniejące trasy zostaną usunięte, należy włączyć trasy rezerwowe, aby odbierać wszystkie dane w wbudowanym punkcie końcowym.

Można włączyć/wyłączyć trasę alternatywną w bloku routing wiadomości Azure Portal->. Można również użyć Azure Resource Manager do [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) , aby użyć niestandardowego punktu końcowego dla trasy rezerwowej.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>Ostatnie znane błędy dla punktów końcowych routingu IoT Hub

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-diagnostic-logs"></a>Kieruje dzienniki diagnostyczne

Poniżej przedstawiono nazwy operacji i kody błędów, które są rejestrowane w [dziennikach diagnostycznych](iot-hub-monitor-resource-health.md#routes).

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Nazwy operacji

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Typowe kody błędów

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
