---
title: Funkcja platformy Azure jako procedura obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, jak można używać usługi Azure Functions jako obsługi zdarzeń dla zdarzeń Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 3ff3c0013cb7a373461b997b9922612763461b8d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598108"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Funkcja platformy Azure jako procedura obsługi zdarzeń dla zdarzeń Event Grid

Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Program obsługi wykonuje akcję, aby przetworzyć zdarzenie. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń, a **Azure Functions** jest jednym z nich. 

Użyj **Azure Functions** w architekturze bezserwerowej w celu reagowania na zdarzenia z Event Grid. W przypadku używania funkcji platformy Azure jako procedury obsługi Użyj wyzwalacza Event Grid zamiast ogólnego wyzwalacza HTTP. Event Grid automatycznie weryfikuje wyzwalacze Event Grid. W przypadku ogólnych wyzwalaczy HTTP należy samodzielnie zaimplementować [odpowiedź na weryfikację](webhook-event-delivery.md) .

Aby uzyskać więcej informacji, zobacz [Event Grid wyzwalacz dla Azure Functions](../azure-functions/functions-bindings-event-grid.md) w celu omówienia korzystania z wyzwalacza Event Grid w funkcjach.

## <a name="tutorials"></a>Samouczki

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: obsługa zdarzeń za pomocą funkcji](custom-event-to-function.md) | Wysyła niestandardowe zdarzenie do funkcji do przetworzenia. |
| [Samouczek: Automatyzowanie zmiany rozmiarów załadowanych obrazów przy użyciu Event Grid](resize-images-on-storage-blob-upload-event.md) | Użytkownicy mogą przekazywać obrazy za poorednictwem aplikacji sieci Web do konta magazynu. Po utworzeniu obiektu blob magazynu Event Grid wysyła zdarzenie do aplikacji funkcji, która zmienia rozmiar przekazanego obrazu. |
| [Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy Event Hubs tworzy plik przechwytywania, Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik przechwytywania i migruje dane do magazynu danych. |
| [Samouczek: przykłady dotyczące Azure Event Grid integracji Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid wysyła komunikaty z Service Bus tematu do aplikacji funkcji i aplikacji logiki. |


## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) . 
