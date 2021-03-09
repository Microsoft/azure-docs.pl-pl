---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511082"
---
## <a name="trusted-microsoft-services"></a>Zaufane usługi firmy Microsoft
Po włączeniu ustawienia **Zezwalaj zaufanym usługom firmy Microsoft na pominięcie tej zapory** zostanie udzielony dostęp do zasobów Service Bus za pomocą następujących usług.

| Usługa zaufana | Obsługiwane scenariusze użycia | 
| --------------- | ------------------------- | 
| Azure Event Grid | Umożliwia Azure Event Grid wysyłanie zdarzeń do kolejek lub tematów w przestrzeni nazw Service Bus. Należy również wykonać następujące czynności: <ul><li>Włączanie tożsamości przypisanej do systemu dla tematu lub domeny</li><li>Dodawanie tożsamości do roli nadawca danych Azure Service Bus w przestrzeni nazw Service Bus</li><li>Następnie należy skonfigurować subskrypcję zdarzeń korzystającą z kolejki Service Bus lub tematu jako punkt końcowy do korzystania z tożsamości przypisanej do systemu.</li></ul> <p>Aby uzyskać więcej informacji, zobacz [dostarczanie zdarzeń przy użyciu tożsamości zarządzanej](../articles/event-grid/managed-service-identity.md)</p>|
| Usługa Azure API Management | <p>Usługa API Management umożliwia wysyłanie komunikatów do kolejki Service Bus/tematu w przestrzeni nazw Service Bus.</p><ul><li>Możesz wyzwolić niestandardowe przepływy pracy, wysyłając komunikaty do kolejki Service Bus/tematu, gdy interfejs API jest wywoływany przy użyciu [zasad wysyłania żądania](../articles/api-management/api-management-sample-send-request.md).</li><li>Możesz również traktować Service Bus kolejki/tematu jako zaplecza w interfejsie API. Aby zapoznać się z przykładowymi zasadami, zobacz [uwierzytelnianie przy użyciu tożsamości zarządzanej w celu uzyskania dostępu do kolejki lub tematu Service Bus](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml). Należy również wykonać następujące czynności:<ol><li>Włącz tożsamość przypisaną przez system w wystąpieniu API Management. Aby uzyskać instrukcje, zobacz [Korzystanie z tożsamości zarządzanych w usłudze Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Dodawanie tożsamości do roli **nadawca danych Azure Service Bus** w przestrzeni nazw Service Bus</li></ol></li></ul> | 