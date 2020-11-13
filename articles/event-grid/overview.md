---
title: Co to jest usługa Azure Event Grid?
description: Wysyłaj dane zdarzenia ze źródła do procedury obsługi za pomocą usługi Azure Event Grid. Twórz aplikacje oparte na zdarzeniach i integruj z usługami platformy Azure.
ms.topic: overview
ms.date: 09/24/2020
ms.openlocfilehash: a4f614a22fdfa7d2272c70751f56f19145a10fa6
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576686"
---
# <a name="what-is-azure-event-grid"></a>Co to jest usługa Azure Event Grid?

Usługa Azure Event Grid umożliwia łatwe tworzenie aplikacji za pomocą architektur opartych na zdarzeniach. Wystarczy, że wybierzesz zasób platformy Azure, który chcesz zasubskrybować, i wskażesz procedurę obsługi zdarzeń lub punkt końcowy elementu WebHook, do którego ma zostać wysłane zdarzenie. Usługa Event Grid obsługuje zdarzenia pochodzące z usług platformy Azure, takich jak obiekty blob magazynu i grupy zasobów. Usługa Event Grid zapewnia również obsługę niestandardowych zdarzeń opartych na niestandardowych tematach. 

Za pomocą filtrów można kierować określone zdarzenia do innych punktów końcowych, wykonywać multiemisję do wielu punktów końcowych, i zapewnić niezawodne dostarczanie zdarzeń.

Azure Event Grid został wdrożony w celu zmaksymalizowania dostępności przez natywne rozmieszczenie w wielu domenach błędów w każdym regionie i w strefach dostępności (w regionach, które je obsługują). Aby zapoznać się z listą regionów obsługiwanych przez Event Grid, zobacz temat [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Ten artykuł zawiera omówienie usługi Azure Event Grid. Aby rozpocząć pracę z usługą Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md). 

:::image type="content" source="./media/overview/functional-model.png" alt-text="Model źródeł i procedur obsługi usługi Event Grid" lightbox="./media/overview/functional-model.png":::

Poniżej przedstawiono połączenie źródeł i procedur obsługi w usłudze Event Grid. Ilustracja nie zawiera pełnej listy obsługiwanych integracji.

## <a name="event-sources"></a>Źródła zdarzeń

Obecnie następujące usługi platformy Azure obsługują wysyłanie zdarzeń do Event Grid. Aby uzyskać więcej informacji na temat źródła na liście, wybierz link.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Usługi Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Usługa Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Grupa zasobów platformy Azure](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Subskrypcje platformy Azure](event-schema-subscriptions.md)

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

Aby uzyskać szczegółowe informacje o możliwościach poszczególnych procedur obsługi oraz zapoznać się z pokrewnymi artykułami, zobacz [Procedury obsługi zdarzeń](event-handlers.md). Aktualnie obsługa zdarzeń pochodzących z usługi Event Grid jest zapewniona w przypadku następujących usług platformy Azure: 

* [Azure Automation](handler-webhooks.md#azure-automation)
* [Azure Functions](handler-functions.md)
* [Event Hubs](handler-event-hubs.md)
* [Połączenia hybrydowe przekaźnika](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Automatyzacja (dawniej znana jako Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](handler-service-bus.md)
* [Queue Storage](handler-storage-queues.md)
* [Elementy webhook](handler-webhooks.md)

## <a name="concepts"></a>Pojęcia

Oto pięć pojęć związanych z usługą Azure Event Grid, które ułatwiają rozpoczęcie pracy:

* **Zdarzenia** — co miało miejsce.
* **Źródła zdarzeń** — gdzie wystąpiło zdarzenie.
* **Tematy** — punkt końcowy, do którego wydawcy wysyłają zdarzenia.
* **Subskrypcje zdarzeń** — punkt końcowy lub wbudowany mechanizm przekierowywania zdarzeń, czasami do więcej niż jednej procedury obsługi. Subskrypcje są również używane przez procedury obsługi w celu inteligentnego filtrowania zdarzeń przychodzących.
* **Procedura obsługi zdarzeń** — aplikacja lub usługa reagująca na zdarzenie.

Aby uzyskać więcej informacji dotyczących tych pojęć, zobacz [Concepts in Azure Event Grid (Pojęcia używane w usłudze Azure Event Grid)](concepts.md).

## <a name="capabilities"></a>Możliwości

Oto główne funkcje usługi Azure Event Grid:

* **Prostota** — wystarczy wskazać element i go kliknąć, aby skierować zdarzenia z zasobu platformy Azure do dowolnej procedury obsługi zdarzeń lub punktu końcowego.
* **Zaawansowane filtrowanie** — filtrowanie zdarzeń według typu lub ścieżki publikowania gwarantuje, że do procedur obsługi trafiają tylko istotne zdarzenia.
* **Wielokierunkowość** — jedno zdarzenie może być subskrybowane przez kilka punktów końcowych, co pozwala wysyłać kopie zdarzenia do wszystkich potrzebnych miejsc.
* **Niezawodność** — 24-godzinne ponawianie z wykładniczym wycofywaniem gwarantuje dostarczanie zdarzeń.
* **Płatność za zdarzenia** — płacisz tylko za rzeczywiste użycie usługi Event Grid.
* **Wysoka przepływność** — Tworzenie obciążeń o dużej pojemności na Event Grid.
* **Wbudowane zdarzenia** — wbudowane zdarzenia definiowane przez zasoby pozwalają szybko rozpocząć pracę.
* **Zdarzenia niestandardowe** — używanie Event Grid do kierowania, filtrowania i niezawodnego dostarczania niestandardowych zdarzeń w aplikacji.

Porównanie usług Event Grid, Event Hubs i Service Bus można znaleźć w temacie [Choose between Azure services that deliver messages (Wybieranie usługi platformy Azure służącej do dostarczania komunikatów)](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Do czego można wykorzystać usługę Event Grid?

Usługa Azure Event Grid udostępnia kilka funkcji, które znacząco usprawniają pracę bezserwerową, automatyzację operacji oraz [integrację](https://azure.com/integration): 

### <a name="serverless-application-architectures"></a>Architektury aplikacji bez użycia serwera

![Architektura aplikacji bez użycia serwera](./media/overview/serverless_web_app.png)

Usługa Event Grid łączy źródła danych i procedury obsługi zdarzeń. Usługi Event Grid można na przykład użyć, aby wyzwolić funkcję bezserwerową, która analizuje obrazy po dodaniu do kontenera magazynu obiektów blob. 

### <a name="ops-automation"></a>Automatyzacja operacji

![Automatyzacja operacji](./media/overview/Ops_automation.png)

Usługa Event Grid umożliwia przyspieszenie automatyzacji i uproszczenie wymuszania zasad. Na przykład użyj Event Grid do powiadomienia Azure Automation, gdy zostanie utworzona maszyna wirtualna lub baza danych w usłudze Azure SQL. Te zdarzenia mogą zostać wykorzystane do automatycznego sprawdzania, czy konfiguracje usługi są zgodne, umieszczania metadanych w narzędziach do obsługi operacji, tagowania maszyn wirtualnych lub zachowywania elementów roboczych.

### <a name="application-integration"></a>Integracja aplikacji

![Integracja aplikacji z platformą Azure](./media/overview/app_integration.png)

Usługa Event Grid łączy aplikację z innymi usługami. Przykładowo możesz utworzyć temat niestandardowy, aby wysłać dane zdarzeń aplikacji do usługi Event Grid i wykorzystać niezawodne dostarczanie, zaawansowany routing i bezpośrednią integrację z platformą Azure. Możesz też użyć usług Event Grid i Logic Apps do przetwarzania danych w dowolnym miejscu, bez pisania kodu. 

## <a name="how-much-does-event-grid-cost"></a>Ile kosztuje korzystanie z usługi Event Grid?

Usługa Azure Event Grid korzysta z modelu płatności za zdarzenia, w którym płaci się wyłącznie za użyte zasoby. Pierwsze 100 000 operacji w danym miesiącu jest bezpłatnych. Operacje są zdefiniowane jako ruch przychodzący zdarzenia, próby dostarczenia subskrypcji, wywołania zarządzania oraz filtrowanie według sufiksu tematu. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Następne kroki

* [Route Storage Blob events (Kierowanie zdarzeń usługi Storage Blob)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Informacje na temat odpowiadania na zdarzenia usługi Storage Blob za pomocą usługi Event Grid.
* [Create and subscribe to custom events (Tworzenie i subskrybowanie zdarzeń niestandardowych)](custom-event-quickstart.md)  
  Ten przewodnik Szybki start po usłudze Azure Event Grid pozwoli Ci szybko zacząć wysyłać własne zdarzenia do dowolnego punktu końcowego.
* [Using Logic Apps as an Event Handler (Używanie usługi Logic Apps jako procedury obsługi zdarzeń)](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Samouczek dotyczący tworzenia aplikacji korzystającej z usługi Logic Apps w celu umożliwienia reagowania na zdarzenia wypychane przez usługę Event Grid.
* [Przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md)  
  Samouczek, który używa Azure Functions do przesyłania strumieniowego danych z Event Hubs do usługi Azure Synapse Analytics.
* [Event Grid REST API reference (Dokumentacja interfejsu API REST usługi Event Grid)](/rest/api/eventgrid)  
  Zawiera informacje na temat zarządzania subskrypcjami zdarzeń, routingiem i filtrowaniem.