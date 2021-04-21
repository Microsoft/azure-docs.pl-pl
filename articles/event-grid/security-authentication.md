---
title: Uwierzytelnianie dostarczania zdarzeń do programów obsługi zdarzeń (Azure Event Grid)
description: W tym artykule opisano różne sposoby uwierzytelniania dostarczania do programów obsługi zdarzeń w Azure Event Grid.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7db258ee152e4b1c46362e74e0246b80513ca9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777261"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Uwierzytelnianie dostarczania zdarzeń do programów obsługi zdarzeń (Azure Event Grid)
Ten artykuł zawiera informacje na temat uwierzytelniania dostarczania zdarzeń do programów obsługi zdarzeń. Pokazano również, jak zabezpieczyć punkty końcowe webhook, które są używane do odbierania zdarzeń z usługi Event Grid przy użyciu usługi Azure Active Directory (Azure AD) lub wspólnego tajnego.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Używanie tożsamości przypisanych przez system do dostarczania zdarzeń
Możesz włączyć tożsamość zarządzaną przypisaną przez system dla tematu lub domeny i użyć tej tożsamości do przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak kolejki i tematy usługi Service Bus, centra zdarzeń i konta magazynu.

Oto konkretne kroki: 

1. Utwórz temat lub domenę z tożsamością przypisaną przez system lub zaktualizuj istniejący temat lub domenę, aby włączyć tożsamość. 
1. Dodaj tożsamość do odpowiedniej roli (na przykład Service Bus data sender) w miejscu docelowym (na przykład w Service Bus danych).
1. Podczas tworzenia subskrypcji zdarzeń włącz użycie tożsamości, aby dostarczać zdarzenia do miejsca docelowego. 

Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [Event delivery with a managed identity (Dostarczanie zdarzeń przy użyciu tożsamości zarządzanej).](managed-service-identity.md)


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Uwierzytelnianie dostarczania zdarzeń do punktów końcowych webhook
W poniższych sekcjach opisano sposób uwierzytelniania dostarczania zdarzeń do punktów końcowych elementy webhook. Niezależnie od stosowanej metody należy użyć mechanizmu weryfikacji uściśniania. Aby uzyskać szczegółowe informacje, zobacz [Webhook event delivery (Dostarczanie](webhook-event-delivery.md) zdarzeń dla webhook). 


### <a name="using-azure-active-directory-azure-ad"></a>Korzystanie Azure Active Directory (Azure AD)
Punkt końcowy webhook, który jest używany do odbierania zdarzeń z usługi Event Grid przy użyciu usługi Azure AD, można zabezpieczyć. Musisz utworzyć aplikację usługi Azure AD, utworzyć rolę i jednostkę usługi w aplikacji autoryzującej usługę Event Grid oraz skonfigurować subskrypcję zdarzeń do korzystania z aplikacji usługi Azure AD. Dowiedz się, jak [skonfigurować Azure Active Directory użyciu Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Używanie tajnego kluczem klienta jako parametru zapytania
Możesz również zabezpieczyć punkt końcowy element webhook, dodając parametry zapytania do docelowego adresu URL określonego w ramach tworzenia subskrypcji zdarzeń. Ustaw jeden z parametrów zapytania jako klucz tajny klienta, taki jak [token dostępu](https://en.wikipedia.org/wiki/Access_token) lub wspólny klucz tajny. Event Grid zawiera wszystkie parametry zapytania w każdym żądaniu dostarczenia zdarzenia do tego elementy webhook. Usługa webhook może pobrać i zweryfikować klucz tajny. Jeśli klucz tajny klienta zostanie zaktualizowany, należy również zaktualizować subskrypcję zdarzeń. Aby uniknąć błędów dostarczania podczas rotacji wpisów tajnych, przed zaktualizowaniem subskrypcji zdarzeń przy użyciu nowego wpisów tajnych należy przez ograniczony czas akceptować stare i nowe wpisy tajne. 

Ponieważ parametry zapytania mogą zawierać wpisy tajne klienta, są obsługiwane z dodatkową ostrożnością. Są one przechowywane jako zaszyfrowane i nie są dostępne dla operatorów usług. Nie są one rejestrowane w ramach dzienników/śladów usługi. Podczas pobierania właściwości subskrypcji zdarzeń parametry zapytania docelowego nie są zwracane domyślnie. Na przykład: [parametr --include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) ma być używany w interfejsie wiersza [polecenia](/cli/azure)platformy Azure.

Aby uzyskać więcej informacji na temat dostarczania zdarzeń do webhook, zobacz Webhook event delivery (Dostarczanie [zdarzeń dotyczących webhook)](webhook-event-delivery.md)

> [!IMPORTANT]
> Azure Event Grid obsługuje tylko punkty **końcowe** https webhook. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Walidacja punktu końcowego przy użyciu rozwiązania CloudEvents w wersji 1.0
Jeśli znasz już te Event Grid, możesz mieć świadomość, że proces weryfikacji punktu końcowego ma na celu zapobieganie nadużyciom. Rozwiązanie CloudEvents w wersji 1.0 implementuje własną semantykę ochrony przed [nadużyciami](webhook-event-delivery.md) przy użyciu **metody HTTP OPTIONS.** Aby dowiedzieć się więcej na ten temat, zobacz [HTTP 1.1 Web Hooks for event delivery - Version 1.0 (Web Hooks for event delivery — wersja 1.0).](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) W przypadku użycia schematu CloudEvents dla danych wyjściowych usługa Event Grid używa ochrony przed nadużyciami w usłudze CloudEvents w wersji 1.0 w miejsce mechanizmu Event Grid zdarzeń weryfikacji. Aby uzyskać więcej informacji, zobacz [Używanie schematu CloudEvents w wersji 1.0 z Event Grid](cloudevents-schema.md). 


## <a name="next-steps"></a>Następne kroki
Zobacz [Uwierzytelnianie klientów publikowania,](security-authenticate-publishing-clients.md) aby dowiedzieć się więcej na temat uwierzytelniania klientów publikujących zdarzenia w tematach lub domenach. 
