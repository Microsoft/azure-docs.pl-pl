---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0e487b3ab3663c765c052f2064201865508ef57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102510751"
---
## <a name="trusted-microsoft-services"></a>Zaufane usługi firmy Microsoft
Po włączeniu ustawienia **Zezwalaj zaufanym usługom firmy Microsoft na pominięcie tej zapory** zostanie udzielony dostęp do zasobów Event Hubs za pomocą następujących usług.

| Usługa zaufana | Obsługiwane scenariusze użycia | 
| --------------- | ------------------------- | 
| Azure Event Grid | Umożliwia Azure Event Grid wysyłanie zdarzeń do centrów zdarzeń w przestrzeni nazw Event Hubs. Należy również wykonać następujące czynności: <ul><li>Włączanie tożsamości przypisanej do systemu dla tematu lub domeny</li><li>Dodawanie tożsamości do roli nadawca danych usługi Azure Event Hubs w przestrzeni nazw Event Hubs</li><li>Następnie należy skonfigurować subskrypcję zdarzeń korzystającą z centrum zdarzeń jako punkt końcowy do korzystania z tożsamości przypisanej do systemu.</li></ul> <p>Aby uzyskać więcej informacji, zobacz [dostarczanie zdarzeń przy użyciu tożsamości zarządzanej](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (ustawienia diagnostyczne) | Umożliwia Azure Monitor wysyłanie informacji diagnostycznych do centrów zdarzeń w przestrzeni nazw Event Hubs. Azure Monitor można odczytywać z centrum zdarzeń, a także zapisywać dane w centrum zdarzeń. |
| Usługa Azure Stream Analytics | Zezwala na zadanie Azure Stream Analytics odczytywać dane z ([dane wejściowe](../articles/stream-analytics/stream-analytics-add-inputs.md)) lub zapisywać dane do centrów zdarzeń ([output](../articles/stream-analytics/event-hubs-output.md)) w Event Hubs przestrzeni nazw. <p>**Ważne**: zadanie Stream Analytics powinno być skonfigurowane do korzystania z **tożsamości zarządzanej** w celu uzyskania dostępu do centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych do uzyskiwania dostępu do centrum zdarzeń z zadania Azure Stream Analytics (wersja zapoznawcza)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Umożliwia IoT Hub wysyłanie komunikatów do centrów zdarzeń w przestrzeni nazw centrum zdarzeń. Należy również wykonać następujące czynności: <ul><li>Włączanie tożsamości przypisanej do systemu dla Centrum IoT Hub</li><li>Dodaj tożsamość do roli nadawca danych usługi Azure Event Hubs w przestrzeni nazw Event Hubs.</li><li>Następnie skonfiguruj IoT Hub używające centrum zdarzeń jako niestandardowego punktu końcowego do korzystania z uwierzytelniania opartego na tożsamości.</li></ul>
| Usługa Azure API Management | <p>Usługa API Management umożliwia wysyłanie zdarzeń do centrum zdarzeń w przestrzeni nazw Event Hubs.</p> <ul><li>Możesz wyzwolić niestandardowe przepływy pracy, wysyłając zdarzenia do centrum zdarzeń, gdy interfejs API jest wywoływany przy użyciu [zasad wysyłania żądania](../articles/api-management/api-management-sample-send-request.md).</li><li>Centrum zdarzeń można również traktować jako zaplecze w interfejsie API. Aby zapoznać się z przykładowymi zasadami, zobacz [uwierzytelnianie przy użyciu tożsamości zarządzanej w celu uzyskania dostępu do centrum zdarzeń](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml). Należy również wykonać następujące czynności:<ol><li>Włącz tożsamość przypisaną przez system w wystąpieniu API Management. Aby uzyskać instrukcje, zobacz [Korzystanie z tożsamości zarządzanych w usłudze Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Dodawanie tożsamości do roli **nadawca danych usługi Azure Event Hubs** w przestrzeni nazw Event Hubs</li></ol></li></ul> | 
