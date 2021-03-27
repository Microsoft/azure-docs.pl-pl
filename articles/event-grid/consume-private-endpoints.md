---
title: Dostarczanie zdarzeń za pomocą usługi linku prywatnego
description: W tym artykule opisano sposób obejścia ograniczenia nie można dostarczać zdarzeń za pomocą usługi link prywatny.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 9df78e1cc7734ba9e455ed686286658006f9445e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629295"
---
# <a name="deliver-events-using-private-link-service"></a>Dostarczanie zdarzeń za pomocą usługi linku prywatnego
Obecnie nie jest możliwe dostarczanie zdarzeń przy użyciu [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md). Oznacza to, że nie ma żadnej obsługi, jeśli masz ścisłe wymagania izolacji sieciowej, gdy ruch zdarzeń nie może opuścić prywatnego obszaru adresów IP. 

## <a name="use-managed-identity"></a>Korzystanie z tożsamości zarządzanej
Jeśli jednak wymagania wymagają bezpiecznego sposobu wysyłania zdarzeń za pomocą zaszyfrowanego kanału i znanej tożsamości nadawcy (w tym przypadku Event Grid) przy użyciu publicznej przestrzeni adresów IP, można dostarczyć zdarzenia do Event Hubs, Service Bus lub usługi Azure Storage za pomocą tematu niestandardowego usługi Azure Event Grid lub domeny z tożsamością zarządzaną przez system. Aby uzyskać szczegółowe informacje dotyczące dostarczania zdarzeń przy użyciu tożsamości zarządzanej, zobacz [dostarczanie zdarzeń przy użyciu tożsamości zarządzanej](managed-service-identity.md). 

Następnie możesz użyć prywatnego linku skonfigurowanego w Azure Functions lub elementu webhook wdrożonego w sieci wirtualnej do ściągania zdarzeń. Zobacz przykład: [łączenie z prywatnymi punktami końcowymi przy użyciu Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Dostarczanie za pośrednictwem usługi link prywatny":::


W ramach tej konfiguracji ruch przechodzi przez publiczny adres IP/Internet z Event Grid do Event Hubs, Service Bus lub Azure Storage, ale kanał może być zaszyfrowany i używana jest tożsamość zarządzana Event Grid. Jeśli skonfigurujesz Azure Functions lub element webhook wdrożony w sieci wirtualnej tak, aby używał Event Hubs, Service Bus lub usługi Azure Storage za pośrednictwem prywatnego linku, ta sekcja ruchu będzie oczywista na platformie Azure.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Dostarczanie zdarzeń do Event Hubs przy użyciu tożsamości zarządzanej
Aby dostarczyć zdarzenia do centrów zdarzeń w przestrzeni nazw Event Hubs przy użyciu tożsamości zarządzanej, wykonaj następujące kroki:

1. Włącz tożsamość przypisaną przez system: [Tematy systemowe](enable-identity-system-topics.md), [Tematy niestandardowe i domeny](enable-identity-custom-topics-domains.md).  
1. [Dodaj tożsamość do roli **nadawca danych usługi Azure Event Hubs** w przestrzeni nazw Event Hubs](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Włącz opcję **Zezwalaj na używanie zaufanych usług firmy Microsoft do pomijania tego ustawienia zapory** w przestrzeni nazw Event Hubs](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. [Skonfiguruj subskrypcję zdarzeń](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) korzystającą z centrum zdarzeń jako punkt końcowy do korzystania z tożsamości przypisanej do systemu.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Dostarczanie zdarzeń do Service Bus przy użyciu tożsamości zarządzanej
Aby dostarczyć zdarzenia do Service Bus kolejek lub tematów w przestrzeni nazw Service Bus przy użyciu tożsamości zarządzanej, wykonaj następujące kroki:

1. Włącz tożsamość przypisaną przez system: [Tematy systemowe](enable-identity-system-topics.md), [Tematy niestandardowe i domeny](enable-identity-custom-topics-domains.md). 
1. [Dodawanie tożsamości do roli **nadawca danych Azure Service Bus**](/service-bus-messaging/service-bus-managed-service-identity#azure-built-in-roles-for-azure-service-bus) w przestrzeni nazw Service Bus
1. [Włącz opcję **Zezwalaj na używanie zaufanych usług firmy Microsoft do pomijania tego ustawienia zapory** w przestrzeni nazw Service Bus](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. [Skonfiguruj subskrypcję zdarzeń](managed-service-identity.md) korzystającą z kolejki Service Bus lub tematu jako punktu końcowego do korzystania z tożsamości przypisanej do systemu.

## <a name="deliver-events-to-storage"></a>Dostarczanie zdarzeń do magazynu 
Aby dostarczyć zdarzenia do kolejek magazynu przy użyciu tożsamości zarządzanej, wykonaj następujące kroki:

1. Włącz tożsamość przypisaną przez system: [Tematy systemowe](enable-identity-system-topics.md), [Tematy niestandardowe i domeny](enable-identity-custom-topics-domains.md). 
1. [Dodaj tożsamość do roli **nadawca komunikatu dane kolejki magazynu**](../storage/common/storage-auth-aad-rbac-portal.md) w kolejce usługi Azure Storage.
1. [Skonfiguruj subskrypcję zdarzeń](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) korzystającą z kolejki Service Bus lub tematu jako punktu końcowego do korzystania z tożsamości przypisanej do systemu.


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat dostarczania zdarzeń przy użyciu tożsamości zarządzanej, zobacz [dostarczanie zdarzeń przy użyciu tożsamości zarządzanej](managed-service-identity.md). 
