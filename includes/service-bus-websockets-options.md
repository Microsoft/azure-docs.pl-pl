---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304425"
---
Opcja protokołu AMQP-over-WebSockets jest uruchamiana przez port TCP 443, podobnie jak interfejs API protokołu HTTP/REST, ale w przeciwnym razie jest identyczny z zwykłym AMQP. Ta opcja ma większe opóźnienie połączeń początkowych z powodu dwukierunkowego rozliczania i nieco większego obciążenia jako kompromisu w przypadku udostępniania portu HTTPS. W przypadku wybrania tego trybu port TCP 443 jest wystarczający do komunikacji. Poniższe opcje umożliwiają wybranie trybu AMQP WebSockets. 

| Język | Opcja   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | Utwórz [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) przy użyciu konstruktora, który przyjmuje [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) jako parametr. Ustaw [ServiceBusClientOptions. TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) na [ServiceBusTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) |
| .NET (Microsoft.Azure.ServiceBus)    | Podczas tworzenia obiektów klienta należy użyć konstruktorów przyjmujących parametry [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype), [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)lub [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) . <p>Dla konstrukcji, która przyjmuje `transportType` jako parametr, ustaw parametr na [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Dla konstruktora, który przyjmuje `ServiceBusConnection` jako parametr, należy ustawić [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) na [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Jeśli używasz `ServiceBusConnectionStringBuilder` , użyj konstruktorów, które umożliwiają określenie opcji `transportType` .</p> |
| Java (com. Azure. Messaging. ServiceBus)     | Podczas tworzenia klientów ustaw wartość [ServiceBusClientBuilder. TransportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) na [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Java (com. Microsoft. Azure. ServiceBus)    | Podczas tworzenia klientów ustaw wartość `transportType` w polu [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_)  na [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| JavaScript  | Podczas tworzenia obiektów klienta Service Bus Użyj `webSocketOptions` właściwości w [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions). |
| Python | Podczas tworzenia Service Bus klientów Ustaw [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) na [TransportType. AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) |

