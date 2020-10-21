---
title: Domeny zdarzeń w Azure Event Grid
description: W tym artykule opisano, jak używać domen zdarzeń do zarządzania przepływem zdarzeń niestandardowych do różnych organizacji, klientów i aplikacji branżowych.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 9b313784cd006087f3c2f1354053540cc9224782
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328831"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Informacje o domenach zdarzeń związanych z zarządzaniem Event Grid tematy

W tym artykule opisano, jak używać domen zdarzeń do zarządzania przepływem zdarzeń niestandardowych do różnych organizacji, klientów i aplikacji branżowych. Użyj domen zdarzeń, aby:

* Zarządzanie wielodostępnymi architekturami zdarzeń na dużą skalę.
* Zarządzanie autoryzacją i uwierzytelnianiem.
* Podziel swoje tematy bez konieczności zarządzania każdym z nich.
* Unikaj indywidualnego publikowania w każdym z punktów końcowych tematu.

## <a name="event-domain-overview"></a>Przegląd domeny zdarzeń

Domena zdarzeń jest narzędziem do zarządzania dla dużej liczby tematów Event Grid związanych z tą samą aplikacją. Można go traktować jako meta-temat, który może zawierać tysiące poszczególnych tematów.

Domeny zdarzeń udostępniają te same architektury, które są używane przez usługi platformy Azure (np. magazyn i IoT Hub) do publikowania ich zdarzeń. Umożliwiają one publikowanie zdarzeń w tysiącach tematów. Domeny zapewniają również kontrolę autoryzacji i uwierzytelniania dla każdego tematu, aby można było podzielić dzierżawy.

### <a name="example-use-case"></a>Przykładowy przypadek użycia

Domeny zdarzeń można łatwo wyjaśnić przy użyciu przykładu. Załóżmy, że uruchomiono maszyny do budowy firmy Contoso, w której są wytwarzane ciągniki, urządzenia przeszukiwanie stosów oraz inne duże maszyny. W ramach działania firmy użytkownik wysyła informacje w czasie rzeczywistym do klientów dotyczących konserwacji sprzętu, kondycji systemów i aktualizacji umów. Wszystkie te informacje znajdują się w różnych punktach końcowych, w tym aplikacji, punktach końcowych klienta i w innej infrastrukturze, którą skonfigurowali klienci.

Domeny zdarzeń umożliwiają modelowanie maszyn konstrukcyjnych contoso jako pojedynczej jednostki zdarzeń. Każdy z klientów jest reprezentowany jako temat w ramach domeny. Uwierzytelnianie i autoryzacja są obsługiwane za pomocą Azure Active Directory. Każdy z klientów może subskrybować swój temat i uzyskać do nich swoje zdarzenia. Dostęp zarządzany przez domenę zdarzeń zapewnia dostęp tylko do ich tematu.

Zapewnia również pojedynczy punkt końcowy, w którym można opublikować wszystkie zdarzenia klienta w usłudze. Event Grid zajmie się zapewnieniem, że każdy temat wie o zdarzeniach objętych zakresem jego dzierżawy.

![Przykład konstruowania contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Zarządzanie dostępem

W przypadku domeny uzyskujesz szczegółowe uprawnienia do autoryzacji i kontroli uwierzytelniania poszczególnych tematów za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Za pomocą tych ról można ograniczyć dzierżawy w aplikacji tylko do tematów, do których mają być udzielane dostęp.

Usługa Azure RBAC w domenach zdarzeń działa tak samo jak [zarządzana kontrola dostępu](security-authorization.md) działa w pozostałej części Event Grid i na platformie Azure. Korzystając z usługi Azure RBAC, można tworzyć i wymuszać niestandardowe definicje ról w domenach zdarzeń.

### <a name="built-in-roles"></a>Wbudowane role

Event Grid ma dwie wbudowane definicje ról umożliwiające łatwiejsze korzystanie z domen zdarzeń przez funkcję RBAC platformy Azure. Role te są **EventGrid EventSubscription współautor (wersja zapoznawcza)** i **EventGrid EventSubscription Reader (wersja zapoznawcza**). Te role można przypisać do użytkowników, którzy muszą subskrybować tematy w domenie zdarzeń. Zakres przypisania roli należy określać tylko w temacie, do którego użytkownicy muszą subskrybować.

Informacje o tych rolach znajdują się w temacie [role wbudowane dla Event Grid](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Subskrybowanie tematów

Subskrybowanie zdarzeń w temacie w domenie zdarzeń jest takie samo jak [Tworzenie subskrypcji zdarzeń w temacie niestandardowym](./custom-event-quickstart.md) lub subskrybowanie zdarzenia z usługi platformy Azure.

### <a name="domain-scope-subscriptions"></a>Subskrypcje zakresu domeny

Domeny zdarzeń umożliwiają również korzystanie z subskrypcji zakresu domeny. Subskrypcja zdarzeń w domenie zdarzeń będzie odbierać wszystkie zdarzenia wysyłane do domeny niezależnie od tematu, do którego są wysyłane zdarzenia. Subskrypcje zakresu domeny mogą być przydatne w celach zarządzania i inspekcji.

## <a name="publishing-to-an-event-domain"></a>Publikowanie w domenie zdarzeń

Podczas tworzenia domeny zdarzeń otrzymujesz punkt końcowy publikowania podobny do tego, jeśli utworzono temat w Event Grid. 

Aby opublikować zdarzenia w dowolnym temacie w domenie zdarzeń, wypchnij zdarzenia do punktu końcowego domeny w taki [sam sposób jak w przypadku tematu niestandardowego](./post-to-custom-topic.md). Jedyną różnicą jest to, że należy określić temat, do którego ma zostać dostarczone zdarzenie.

Na przykład opublikowanie poniższej tablicy zdarzeń spowoduje wysłanie zdarzenia z `"id": "1111"` do tematu `foo` , podczas gdy zdarzenie z `"id": "2222"` zostałby przesłane do tematu `bar` :

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Domeny zdarzeń obsługują publikowanie w tematach. Zamiast publikować zdarzenia do każdego tematu, który można zarządzać pojedynczo, można opublikować wszystkie zdarzenia w punkcie końcowym domeny. Event Grid sprawdza, czy każde zdarzenie jest wysyłane do właściwego tematu.

## <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia
Oto limity i przydziały związane z domenami zdarzeń:

- 100 000 tematów na domenę zdarzeń 
- 100 domen zdarzeń na subskrypcję platformy Azure 
- 500 subskrypcje zdarzeń na temat w domenie zdarzeń
- subskrypcje dotyczące zakresu domeny 50 
- 5 000 zdarzeń na sekundę (do domeny)

Jeśli te limity nie są odpowiednie dla Ciebie, skontaktuj się z zespołem produktu, otwierając bilet pomocy technicznej lub wysyłając wiadomość e-mail na adres [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Cennik
Domeny zdarzeń używają tych samych [cen operacji](https://azure.microsoft.com/pricing/details/event-grid/) , które są używane przez wszystkie inne funkcje w Event Grid.

Operacje działają tak samo w domenach zdarzeń, jak w tematach niestandardowych. Każdy ruch przychodzący zdarzenia do domeny zdarzeń jest operacją, a każda próba dostarczenia zdarzenia jest operacją.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o konfigurowaniu domen zdarzeń, tworzeniu tematów, tworzeniu subskrypcji zdarzeń i publikowaniu zdarzeń, zobacz [Zarządzanie domenami zdarzeń](./how-to-event-domains.md).
