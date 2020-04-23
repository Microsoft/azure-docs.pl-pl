---
title: Autoryzacja dostępu za pomocą usługi Azure Active Directory
description: Ten artykuł zawiera informacje dotyczące autoryzowania dostępu do zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 6216b56c8e8f0de4f9cd60306bbf9c5ed49a11ad
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025207"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autoryzowanie dostępu do zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory
Usługa Azure Event Hubs obsługuje korzystanie z usługi Azure Active Directory (Azure AD) w celu autoryzowania żądań do zasobów usługi Event Hubs. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień podmiotowi zabezpieczeń, który może być użytkownikiem lub podmiotem usługi aplikacji. Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [Opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik lub aplikacja) próbuje uzyskać dostęp do zasobu Usługi Event Hubs, żądanie musi być autoryzowane. Dzięki usłudze Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Po pierwsze tożsamość podmiotu zabezpieczeń jest uwierzytelniona i zwracany jest token OAuth 2.0. Nazwa zasobu, aby `https://eventhubs.azure.net/`zażądać tokenu jest . Dla klientów platformy Kafka zasób, aby zażądać tokenu jest `https://<namespace>.servicebus.windows.net`.
 1. Następnie token jest przekazywany jako część żądania do usługi Usługi Event Hubs, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawiera token dostępu OAuth 2.0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w jednostce platformy Azure, takiej jak maszyna wirtualna Azure, zestaw skalowania maszyny wirtualnej lub aplikacja funkcji platformy Azure, może użyć tożsamości zarządzanej, aby uzyskać dostęp do zasobów. Aby dowiedzieć się, jak uwierzytelniać żądania złożone przez zarządzaną tożsamość w usłudze Centrum zdarzeń, zobacz [Uwierzytelnianie dostępu do zasobów usługi Azure Event Hubs za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure.](authenticate-managed-identity.md) 

Krok autoryzacji wymaga przypisania co najmniej jednej roli RBAC do podmiotu zabezpieczeń. Usługa Azure Event Hubs udostępnia role RBAC, które obejmują zestawy uprawnień dla zasobów centrum zdarzeń. Role, które są przypisane do podmiotu zabezpieczeń określić uprawnienia, które podmiot zabezpieczeń będzie mieć. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [Wbudowane role RBAC dla usługi Azure Event Hubs](#built-in-rbac-roles-for-azure-event-hubs). 

Aplikacje natywne i aplikacje sieci web, które żądają usługi Event Hubs, mogą również autoryzować za pomocą usługi Azure AD. Aby dowiedzieć się, jak zażądać tokenu dostępu i używać go do autoryzowania żądań dotyczących zasobów usługi Event Hubs, zobacz [Uwierzytelnianie dostępu do usługi Azure Event Hubs za pomocą usługi Azure AD z aplikacji.](authenticate-application.md) 

## <a name="assign-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC dla praw dostępu
Usługa Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC).](../role-based-access-control/overview.md) Usługa Azure Event Hubs definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do danych centrum zdarzeń i można również zdefiniować role niestandardowe dostępu do danych.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp może być ograniczony do poziomu subskrypcji, grupy zasobów, obszaru nazw Centrum zdarzeń lub dowolnego zasobu pod nim. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem lub podmiotem usługi aplikacji lub [tożsamością zarządzaną dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Wbudowane role RBAC dla centrów zdarzeń platformy Azure
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych usługi Event Hubs przy użyciu usługi Azure AD i OAuth:

- [Właściciel danych usługi Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)Użyj tej roli, aby zapewnić pełny dostęp do zasobów usługi Event Hubs.
- [Nadawca danych usługi Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)użyj tej roli, aby nadać dostęp do zasobów usługi Event Hubs.
- [Odbiorca danych usługi Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)Użyj tej roli, aby nadać zasobom usługi Event Hubs korzystanie z zasobów.

## <a name="resource-scope"></a>Zakres zasobu 
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, na których można uzyskać dostęp do zasobów usługi Event Hubs, zaczynając od najwęższego zakresu:

- **Grupa konsumentów:** W tym zakresie przypisanie roli ma zastosowanie tylko do tej jednostki. Obecnie witryna Azure portal nie obsługuje przypisywania roli RBAC do podmiotu zabezpieczeń na tym poziomie. 
- **Centrum zdarzeń:** Przypisanie roli ma zastosowanie do encji Centrum zdarzeń i grupy konsumentów w jej obszarze.
- **Obszar nazw:** Przypisanie roli obejmuje całą topologię centrów zdarzeń w obszarze nazw i do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów:** Przypisanie roli ma zastosowanie do wszystkich zasobów centrum zdarzeń w grupie zasobów.
- **Subskrypcja:** Przypisanie roli ma zastosowanie do wszystkich zasobów centrum zdarzeń we wszystkich grupach zasobów w ramach subskrypcji.

> [!NOTE]
> - Należy pamiętać, że przydziały ról RBAC może potrwać do pięciu minut do propagacji. 
> - Ta zawartość dotyczy zarówno centrów zdarzeń, jak i centrów zdarzeń dla platformy Apache Kafka. Aby uzyskać więcej informacji na temat centrów zdarzeń dla platformy Pomocy technicznej platformy Kafka, zobacz [Centra zdarzeń dla platformy Kafka — zabezpieczenia i uwierzytelnianie](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Aby uzyskać więcej informacji na temat definiowania ról wbudowanych, zobacz [Rozumienie definicji ról](../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje dotyczące tworzenia niestandardowych ról RBAC, zobacz [Tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach platformy Azure](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Samples
- [Przykłady witryn Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Te przykłady używają starej biblioteki **Microsoft.Azure.EventHubs,** ale można łatwo zaktualizować go do korzystania z najnowszej biblioteki **Azure.Messaging.EventHubs.** Aby przenieść przykład ze starej biblioteki do nowej, zobacz [Przewodnik do migracji z witryny Microsoft.Azure.EventHubs do usługi Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Przykłady witryn Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Ten przykład został zaktualizowany, aby użyć najnowszej biblioteki **Azure.Messaging.EventHubs.**
- [Centra zdarzeń dla platformy Kafka — próbki OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak przypisać wbudowaną rolę RBAC do podmiotu zabezpieczeń, zobacz [Uwierzytelnij dostęp do zasobów Centrum zdarzeń przy użyciu usługi Azure Active Directory](authenticate-application.md).
- Dowiedz [się, jak tworzyć role niestandardowe za pomocą funkcji RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Dowiedz [się, jak korzystać z usługi Azure Active Directory z usługą EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Zobacz następujące artykuły pokrewne:

- [Uwierzytelnij żądania do usługi Azure Event Hubs z aplikacji przy użyciu usługi Azure Active Directory](authenticate-application.md)
- [Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs](authenticate-managed-identity.md)
- [Uwierzytelnij żądania w centrach zdarzeń platformy Azure przy użyciu podpisów dostępu współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzowanie dostępu do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego](authorize-access-shared-access-signature.md)
