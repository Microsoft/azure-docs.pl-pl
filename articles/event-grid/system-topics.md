---
title: Tematy systemowe w Azure Event Grid
description: Opisuje tematy systemowe w Azure Event Grid.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b3a6e7528da2a11c2f91007425ab8beecaf920c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297287"
---
# <a name="system-topics-in-azure-event-grid"></a>Tematy systemowe w Azure Event Grid
Temat systemowy w Event Grid reprezentuje jedno lub więcej zdarzeń opublikowanych przez usługi platformy Azure, takie jak Azure Storage i Azure Event Hubs. Na przykład temat systemowy może reprezentować **wszystkie zdarzenia obiektu BLOB** lub tylko **utworzone** zdarzenia BLOB i **usunięte obiekty blob** dla **określonego konta magazynu**. W tym przykładzie, gdy obiekt BLOB jest przekazywany do konta magazynu, usługa Azure Storage publikuje zdarzenie **utworzone obiektu BLOB** w temacie system w Event Grid, który następnie przekazuje zdarzenie do [subskrybentów](event-handlers.md) tematu, którzy odbierają i przetwarzają zdarzenia. 

> [!NOTE] 
> Tylko usługi platformy Azure mogą publikować zdarzenia w tematach systemu. W związku z tym nie możesz uzyskać punktów końcowych ani kluczy dostępu, których można użyć do publikowania zdarzeń, takich jak w przypadku niestandardowych tematów lub domen.

## <a name="azure-services-that-support-system-topics"></a>Usługi platformy Azure, które obsługują system tematów
Oto bieżąca lista usług platformy Azure, które obsługują tworzenie na nich tematów systemowych.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
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

## <a name="system-topics-as-azure-resources"></a>Tematy systemowe jako zasoby platformy Azure
W przeszłości temat systemowy był niejawny i nie był narażony na prostotę. Tematy systemowe są teraz widoczne jako zasoby platformy Azure i zapewniają następujące możliwości:

- [Wyświetl tematy systemowe w Azure Portal](create-view-manage-system-topics.md#view-all-system-topics)
- Eksportowanie szablonów Menedżer zasobów dla tematów systemowych i subskrypcji zdarzeń w Azure Portal
- [Konfigurowanie dzienników diagnostycznych dla tematów systemowych](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Konfigurowanie alertów dotyczących błędów publikowania i dostarczania 

## <a name="lifecycle-of-system-topics"></a>Cykl życia tematów systemu
Temat systemowy można utworzyć na dwa sposoby: 

- Utwórz [subskrypcję zdarzeń dla zasobu platformy Azure jako zasób rozszerzenia](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate), który automatycznie tworzy temat systemu o nazwie w formacie: `<Azure resource name>-<GUID>` . Temat systemowy utworzony w ten sposób jest automatycznie usuwany po usunięciu ostatniej subskrypcji zdarzeń dla tematu. 
- Utwórz temat systemowy dla zasobu platformy Azure, a następnie Utwórz subskrypcję zdarzeń dla tego tematu systemu. Korzystając z tej metody, można określić nazwę tematu systemu. Temat systemowy nie jest usuwany automatycznie po usunięciu ostatniej subskrypcji zdarzenia. Należy ręcznie usunąć ten element. 

    Gdy używasz Azure Portal, zawsze używasz tej metody. Podczas tworzenia subskrypcji zdarzeń przy użyciu [strony **zdarzenia** zasobu platformy Azure](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)zostanie najpierw utworzony temat systemowy, a następnie zostanie utworzona subskrypcja tematu. Najpierw można utworzyć temat systemu przy użyciu [strony **Tematy dotyczące systemu Event Grid** ](create-view-manage-system-topics.md#create-a-system-topic) , a następnie utworzyć subskrypcję dla tego tematu. 

Korzystając z szablonu [CLI](create-view-manage-system-topics-cli.md), [rest](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)lub [Azure Resource Manager](create-view-manage-system-topics-arm.md), można wybrać jedną z powyższych metod. Zalecamy najpierw utworzyć temat systemowy, a następnie utworzyć subskrypcję w temacie, ponieważ jest to Najnowsza metoda tworzenia tematów systemowych.

Tworzenie tematu systemu kończy się niepowodzeniem, jeśli zasady platformy Azure zostały skonfigurowane w taki sposób, że usługa Event Grid nie może go utworzyć. Na przykład mogą istnieć zasady, które umożliwiają tworzenie tylko niektórych typów zasobów (na przykład: Azure Storage, Azure Event Hubs itd.) w ramach subskrypcji. 

## <a name="location-and-resource-group-for-a-system-topic"></a>Lokalizacja i Grupa zasobów tematu systemowego
W przypadku źródeł zdarzeń platformy Azure, które znajdują się w określonym regionie/lokalizacji, temat systemowy jest tworzony w tej samej lokalizacji, w której znajduje się źródło zdarzeń platformy Azure. Jeśli na przykład utworzysz subskrypcję zdarzeń dla magazynu obiektów blob platformy Azure w regionie Wschodnie stany USA, temat systemowy zostanie utworzony w regionie Wschodnie stany USA. W przypadku globalnych źródeł zdarzeń platformy Azure, takich jak subskrypcje platformy Azure, grupy zasobów lub Azure Maps, Event Grid tworzy temat systemowy w lokalizacji **globalnej** . 

Ogólnie rzecz biorąc, temat systemowy jest tworzony w tej samej grupie zasobów, w której znajduje się źródło zdarzeń platformy Azure. W przypadku subskrypcji zdarzeń utworzonych w ramach zakresu subskrypcji platformy Azure temat systemowy jest tworzony w grupie zasobów **EventGrid domyślne** w regionie **zachodnie stany USA 2** . Jeśli grupa zasobów nie istnieje, Azure Event Grid ją utworzyć przed utworzeniem tematu systemowego. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Tworzenie i wyświetlanie tematów systemowych oraz zarządzanie nimi za pomocą Azure Portal](create-view-manage-system-topics.md).
- [Tworzenie i wyświetlanie Event Grid tematów systemowych oraz zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](create-view-manage-system-topics-cli.md)
- [Tworzenie Event Grid tematów systemowych przy użyciu szablonów Azure Resource Manager](create-view-manage-system-topics-arm.md)
