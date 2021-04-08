---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693407"
---
Opcja protokołu AMQP-over-WebSockets jest uruchamiana przez port TCP 443, podobnie jak interfejs API protokołu HTTP/REST, ale w przeciwnym razie jest identyczny z zwykłym AMQP. Ta opcja ma nieco wyższy czas oczekiwania na połączenie z powodu rozliczania i nieznacznie większego obciążenia jako kompromisu dla udostępniania portu HTTPS. W przypadku wybrania tego trybu port TCP 443 jest wystarczający do komunikacji. Poniższe opcje umożliwiają wybranie trybu AMQP lub AMQP obiektów WebSockets:

| Język | Opcja   |
| -------- | ----- |
| .NET     | [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) właściwość z właściwością [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype) lub [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) z [modelem com. Microsoft. Azure. ServiceBus. pierwotne. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) lub [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Węzeł  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) ma `webSocket` argument konstruktora. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) z [transportem. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) lub [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |