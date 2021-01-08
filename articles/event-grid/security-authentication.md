---
title: Uwierzytelnianie dostarczania zdarzeń do programów obsługi zdarzeń (Azure Event Grid)
description: W tym artykule opisano różne sposoby uwierzytelniania dostarczania do programów obsługi zdarzeń w Azure Event Grid.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 8360aa49e3d83879499af79448ff9f85082f47ac
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015542"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Uwierzytelnianie dostarczania zdarzeń do programów obsługi zdarzeń (Azure Event Grid)
Ten artykuł zawiera informacje dotyczące uwierzytelniania dostarczania zdarzeń do programów obsługi zdarzeń. Przedstawiono w nim również sposób zabezpieczania punktów końcowych elementu webhook, które są używane do odbierania zdarzeń z Event Grid przy użyciu Azure Active Directory (Azure AD) lub wspólnego klucza tajnego.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Korzystanie z tożsamości przypisanych do systemu na potrzeby dostarczania zdarzeń
Tożsamość zarządzaną przez system można włączyć dla tematu lub domeny i użyć tożsamości do przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak kolejki Service Bus i tematy, Centra zdarzeń i konta magazynu.

Oto odpowiednie kroki: 

1. Utwórz temat lub domenę z tożsamością przypisaną do systemu lub zaktualizuj istniejący temat lub domenę, aby włączyć tożsamość. 
1. Dodaj tożsamość do odpowiedniej roli (na przykład Service Bus nadawcy danych) w miejscu docelowym (na przykład Kolejka Service Bus).
1. Podczas tworzenia subskrypcji zdarzeń należy włączyć użycie tożsamości w celu dostarczenia zdarzeń do miejsca docelowego. 

Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [dostarczanie zdarzeń przy użyciu tożsamości zarządzanej](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Uwierzytelnianie dostarczania zdarzeń do punktów końcowych elementu webhook
W poniższych sekcjach opisano sposób uwierzytelniania dostarczania zdarzeń do punktów końcowych elementu webhook. Musisz użyć mechanizmu uzgadniania poprawności, niezależnie od używanej metody. Aby uzyskać szczegółowe informacje, zobacz [dostarczanie zdarzeń elementu webhook](webhook-event-delivery.md) . 


### <a name="using-azure-active-directory-azure-ad"></a>Korzystanie z Azure Active Directory (Azure AD)
Możesz zabezpieczyć punkt końcowy elementu webhook, który jest używany do odbierania zdarzeń z Event Grid przy użyciu usługi Azure AD. Musisz utworzyć aplikację usługi Azure AD, utworzyć rolę i nazwę główną usługi w aplikacji, która autoryzuje Event Grid, i skonfiguruje subskrypcję zdarzeń do korzystania z aplikacji usługi Azure AD. Dowiedz się, jak [skonfigurować Azure Active Directory przy użyciu Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Używanie klucza tajnego klienta jako parametru zapytania
Punkt końcowy elementu webhook można także zabezpieczyć przez dodanie parametrów zapytania do docelowego adresu URL elementu webhook określonego w ramach tworzenia subskrypcji zdarzeń. Ustaw jeden z parametrów zapytania jako klucz tajny klienta, taki jak [token dostępu](https://en.wikipedia.org/wiki/Access_token) lub wspólny klucz tajny. Usługa Event Grid obejmuje wszystkie parametry zapytania w każdym żądaniu dostarczania zdarzeń do elementu webhook. Usługa elementu webhook może pobrać klucz tajny i sprawdzić jego poprawność. Jeśli klucz tajny klienta zostanie zaktualizowany, należy również zaktualizować subskrypcję zdarzeń. Aby uniknąć niepowodzeń dostarczania w ramach tego samego klucza tajnego, przed zaktualizowaniem subskrypcji zdarzeń przy użyciu nowego klucza tajnego upewnij się, że element webhook zaakceptuje zarówno stare, jak i nowe wpisy tajne. 

Ponieważ parametry zapytania mogą zawierać wpisy tajne klienta, są one obsługiwane z dodatkowymi zapytaniami. Są one przechowywane jako zaszyfrowane i nie są dostępne dla operatorów usług. Nie są one rejestrowane w ramach dzienników usługi/śladów. Podczas pobierania właściwości subskrypcji zdarzeń, parametry zapytania docelowego nie są domyślnie zwracane. Na przykład: parametr [--include-Full-Endpoint-URL](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) ma być używany w [interfejsie wiersza polecenia](/cli/azure?view=azure-cli-latest)platformy Azure.

Aby uzyskać więcej informacji na temat dostarczania zdarzeń do elementów webhook, zobacz [dostarczanie zdarzeń elementu webhook](webhook-event-delivery.md)

> [!IMPORTANT]
Azure Event Grid obsługuje tylko punkty końcowe elementu webhook **protokołu HTTPS** . 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Walidacja punktu końcowego z CloudEvents v 1.0
Jeśli znasz już Event Grid, może być świadome uzgadniania sprawdzania poprawności punktu końcowego w celu zapobiegania nadużyciom. CloudEvents v 1.0 implementuje własną [semantykę ochrony przed nadużyciami](webhook-event-delivery.md) przy użyciu metody **http Options** . Aby dowiedzieć się więcej na ten temat, zobacz elementy [webhook protokołu HTTP 1,1 dla usługi Event Delivery — wersja 1,0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). W przypadku korzystania ze schematu CloudEvents dla danych wyjściowych Event Grid używa ochrony nadużycia CloudEvents v 1.0 zamiast mechanizmu zdarzenia walidacji Event Grid. Aby uzyskać więcej informacji, zobacz [Używanie schematu CloudEvents v 1.0 z Event Grid](cloudevents-schema.md). 


## <a name="next-steps"></a>Następne kroki
Zobacz temat [uwierzytelnianie klientów publikowania](security-authenticate-publishing-clients.md) , aby dowiedzieć się więcej o uwierzytelnianiu klientów wysyłających zdarzenia do tematów lub domen. 
