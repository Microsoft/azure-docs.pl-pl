---
title: Autoryzacja dostępu za pomocą usługi Azure Active Directory
description: Ten artykuł zawiera informacje dotyczące autoryzowania dostępu do zasobów Event Hubs przy użyciu Azure Active Directory.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d794b03fdbb5429983788c74cbb05a7c13bf2d76
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910801"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory
Usługa Azure Event Hubs obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań Event Hubs zasobów. Korzystając z usługi Azure AD, możesz użyć kontroli dostępu opartej na rolach (Azure RBAC), aby przyznać uprawnienia podmiotowi zabezpieczeń, który może być użytkownikiem lub podmiotem usługi aplikacji. Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik lub aplikacja) próbuje uzyskać dostęp do zasobu Event Hubs, żądanie musi być autoryzowane. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Najpierw jest uwierzytelniana tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0. Nazwa zasobu do żądania tokenu jest taka `https://eventhubs.azure.net/` sama dla wszystkich chmur/dzierżawców. W przypadku klientów Kafka zasób do żądania tokenu to `https://<namespace>.servicebus.windows.net` .
 1. Następnie token jest przesyłany w ramach żądania do usługi Event Hubs, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja funkcji platformy Azure, może używać tożsamości zarządzanej do uzyskiwania dostępu do zasobów. Aby dowiedzieć się, jak uwierzytelniać żądania wysyłane przez zarządzaną tożsamość do usługi Event Hubs, zobacz temat [uwierzytelnianie dostępu do zasobów usługi azure Event Hubs za pomocą Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](authenticate-managed-identity.md). 

Krok autoryzacji wymaga, aby co najmniej jedna rola platformy Azure była przypisana do podmiotu zabezpieczeń. Usługa Azure Event Hubs udostępnia role platformy Azure, które obejmują zestawy uprawnień dla Event Hubs zasobów. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Aby uzyskać więcej informacji na temat ról platformy Azure, zobacz [wbudowane role platformy Azure dla usługi azure Event Hubs](#azure-built-in-roles-for-azure-event-hubs). 

Aplikacje natywne i aplikacje sieci Web, które wysyłają żądania do Event Hubs mogą również autoryzować się z usługą Azure AD. Aby dowiedzieć się, jak zażądać tokenu dostępu i używać go do autoryzacji żądań dotyczących zasobów Event Hubs, zobacz temat [uwierzytelnianie dostępu do usługi azure Event Hubs za pomocą usługi Azure AD z poziomu aplikacji](authenticate-application.md). 

## <a name="assign-azure-roles-for-access-rights"></a>Przypisywanie ról platformy Azure na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md). Usługa Azure Event Hubs definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do danych centrum zdarzeń, a także definiuje role niestandardowe do uzyskiwania dostępu do danych.

Gdy rola platformy Azure zostanie przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, przestrzeni nazw Event Hubs lub dowolnego zasobu w ramach tego elementu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem lub główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Wbudowane role platformy Azure dla usługi Azure Event Hubs
Platforma Azure udostępnia następujące wbudowane role platformy Azure do autoryzowania dostępu do danych Event Hubs przy użyciu usługi Azure AD i uwierzytelniania OAuth:

| Rola | Opis | 
| ---- | ----------- | 
| [Właściciel danych Event Hubs platformy Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Ta rola umożliwia pełen dostęp do zasobów Event Hubs. |
| [Nadawca danych Event Hubs platformy Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Ta rola służy do nadawania dostępu Event Hubs do zasobów. |
| [Usługa Azure Event Hubs Data Receiver](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Ta rola umożliwia nadanie dostępu do zasobów Event Hubs lub otrzymywanie do nich. |

Aby uzyskać wbudowane role rejestru schematu, zobacz [role rejestru schematu](schema-registry-overview.md#azure-role-based-access-control).

## <a name="resource-scope"></a>Zakres zasobu 
Przed przypisaniem roli platformy Azure do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów Event Hubs, rozpoczynając od najwęższego zakresu:

- **Grupa konsumentów** : w tym zakresie przypisanie roli ma zastosowanie tylko do tej jednostki. Obecnie Azure Portal nie obsługuje przypisywania roli platformy Azure do podmiotu zabezpieczeń na tym poziomie. 
- **Centrum zdarzeń** : przypisanie roli dotyczy jednostki centrum zdarzeń i grupy konsumentów.
- **Przestrzeń nazw** : przypisanie roli obejmuje całą topologię Event Hubs w przestrzeni nazw oraz do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów** : przypisanie roli dotyczy wszystkich zasobów Event Hubs w grupie zasobów.
- **Subskrypcja** : przypisanie roli dotyczy wszystkich zasobów Event Hubs we wszystkich grupach zasobów w subskrypcji.

> [!NOTE]
> - Należy pamiętać, że propagacja ról platformy Azure może potrwać do 5 minut. 
> - Ta zawartość dotyczy zarówno Event Hubs, jak i Event Hubs do Apache Kafka. Aby uzyskać więcej informacji na temat Event Hubs obsługi Kafka, zobacz [Event Hubs for Kafka-Security and Authentication](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Aby uzyskać więcej informacji na temat sposobu definiowania wbudowanych ról, zobacz [Omówienie definicji ról](../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje na temat tworzenia ról niestandardowych platformy Azure, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Samples
- [Przykłady Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Te przykłady używają starej biblioteki **Microsoft. Azure. EventHubs** , ale można ją łatwo zaktualizować do korzystania z najnowszej biblioteki **Azure. Messaging. EventHubs** . Aby przenieść przykład z używania starej biblioteki do nowej, zapoznaj się z [przewodnikiem migrowania z Microsoft. Azure. EventHubs do platformy Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Przykłady dla platformy Azure. Messaging. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Ten przykład został zaktualizowany, aby można było użyć najnowszej biblioteki **Azure. Messaging. EventHubs** .
- [Event Hubs dla przykładów Kafka-OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak przypisać wbudowaną rolę platformy Azure do podmiotu zabezpieczeń, zobacz temat [uwierzytelnianie dostępu do zasobów Event Hubs przy użyciu Azure Active Directory](authenticate-application.md).
- Dowiedz się [, jak tworzyć role niestandardowe przy użyciu funkcji RBAC platformy Azure](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Dowiedz się [, jak używać Azure Active Directory z usługą EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Zapoznaj się z następującymi artykułami:

- [Uwierzytelniaj żądania do Event Hubs platformy Azure z aplikacji przy użyciu Azure Active Directory](authenticate-application.md)
- [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md)
- [Uwierzytelnianie żądań na platformie Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md)
