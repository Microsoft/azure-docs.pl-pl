---
title: Domeny zdarzeń w Azure Event Grid
description: W tym artykule opisano sposób używania domen zdarzeń do zarządzania przepływem zdarzeń niestandardowych do różnych organizacji biznesowych, klientów lub aplikacji.
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 32c06ac55f667ec9807c7952127c2cf0f0384024
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374713"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Informacje o domenach zdarzeń do zarządzania Event Grid tematami

W tym artykule opisano sposób używania domen zdarzeń do zarządzania przepływem zdarzeń niestandardowych do różnych organizacji biznesowych, klientów lub aplikacji. Użyj domen zdarzeń, aby:

* Zarządzanie architekturami wielowątkowej obsługi zdarzeń na dużą skalę.
* Zarządzanie autoryzacją i uwierzytelnianiem.
* Partycjonowanie tematów bez konieczności zarządzania poszczególnymi tematami.
* Unikaj indywidualnego publikowania w poszczególnych punktach końcowych tematu.

## <a name="event-domain-overview"></a>Omówienie domeny zdarzeń

Domena zdarzeń to narzędzie do zarządzania dużą Event Grid tematów związanych z tę samą aplikacją. Można o nim myśleć jak o metamedycie, który może mieć tysiące pojedynczych tematów.

Domeny zdarzeń zapewniają tę samą architekturę, która jest używana przez usługi platformy Azure, takie jak Storage IoT Hub do publikowania ich zdarzeń. Umożliwiają one publikowanie zdarzeń w tysiącach tematów. Domeny zapewniają również autoryzację i kontrolę uwierzytelniania w poszczególnych tematach, dzięki czemu można partycjonować dzierżawy.

## <a name="example-use-case"></a>Przykładowy przypadek użycia
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Zarządzanie dostępem

W przypadku domeny można uzyskać uwierzytelnianie precyzyjne i kontrolę uwierzytelniania w poszczególnych tematach za pośrednictwem kontroli dostępu na podstawie ról (RBAC) platformy Azure. Za pomocą tych ról można ograniczyć każdą dzierżawę w aplikacji tylko do tematów, do których chcesz przyznać im dostęp.

Kontrola RBAC platformy Azure w [](security-authorization.md) domenach zdarzeń działa tak samo jak zarządzana kontrola dostępu w pozostałej Event Grid i na platformie Azure. Użyj kontroli RBAC platformy Azure, aby tworzyć i wymuszać niestandardowe definicje ról w domenach zdarzeń.

### <a name="built-in-roles"></a>Role wbudowane

Event Grid dwie wbudowane definicje ról, które ułatwiają pracę z domenami zdarzeń przy użyciu kontroli RBAC platformy Azure. Te role to Współautor subskrypcji zdarzeń **eventSubscription** w umiędzy EventGrid (wersja zapoznawcza) i Czytelnik subskrypcji zdarzeń **EventGrid (wersja zapoznawcza).** Te role przypisuje się użytkownikom, którzy muszą subskrybować tematy w domenie zdarzeń. Zakres przypisania roli obejmuje tylko temat, który użytkownicy muszą subskrybować.

Aby uzyskać informacje o tych rolach, [zobacz Wbudowane role dla Event Grid](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Subskrybowanie tematów

Subskrybowanie zdarzeń w temacie w domenie zdarzeń [](./custom-event-quickstart.md) jest takie samo jak tworzenie subskrypcji zdarzeń w temacie niestandardowym lub subskrybowanie zdarzenia z usługi platformy Azure.

> [!IMPORTANT]
> Temat domeny jest uznawany za **zasób zarządzany** automatycznie w Event Grid. Subskrypcję zdarzeń można utworzyć w zakresie tematu domeny bez tworzenia tematu domeny. W takim przypadku Event Grid automatycznie tworzy temat domeny w Twoim imieniu. Oczywiście nadal można ręcznie utworzyć temat domeny. To zachowanie pozwala martwić się o jeden zasób mniej podczas pracy z ogromną liczbą tematów dotyczących domeny. Po usunięciu ostatniej subskrypcji tematu domeny temat domeny jest również usuwany niezależnie od tego, czy temat domeny został utworzony ręcznie, czy automatycznie. 

### <a name="domain-scope-subscriptions"></a>Subskrypcje zakresu domeny

Domeny zdarzeń zezwalają również na subskrypcje w zakresie domeny. Subskrypcja zdarzeń w domenie zdarzeń będzie odbierać wszystkie zdarzenia wysyłane do domeny niezależnie od tematu, do który są wysyłane zdarzenia. Subskrypcje zakresu domeny mogą być przydatne do celów zarządzania i inspekcji.

## <a name="publishing-to-an-event-domain"></a>Publikowanie w domenie zdarzeń

Podczas tworzenia domeny zdarzeń punkt końcowy publikowania jest podobny do tego, który został utworzony w Event Grid. 

Aby opublikować zdarzenia w dowolnym temacie w domenie zdarzeń, wypchń zdarzenia do punktu końcowego domeny w taki sam sposób, jak w [przypadku tematu niestandardowego.](./post-to-custom-topic.md) Jedyna różnica polega na tym, że należy określić temat, do który ma zostać dostarczone zdarzenie.

Na przykład opublikowanie następującej tablicy zdarzeń wyśle zdarzenie z do tematu, podczas gdy zdarzenie z zostanie `"id": "1111"` `foo` wysłane do `"id": "2222"` tematu `bar` :

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

Domeny zdarzeń obsługują publikowanie w tematach. Zamiast publikować zdarzenia w poszczególnych tematach, które są indywidualnie zarządzane, można opublikować wszystkie zdarzenia w punkcie końcowym domeny. Event Grid, że każde zdarzenie jest wysyłane do poprawnego tematu.

## <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia
Poniżej podano limity i przydziały związane z domenami zdarzeń:

- 100 000 tematów na domenę zdarzeń 
- 100 domen zdarzeń na subskrypcję platformy Azure 
- 500 subskrypcji zdarzeń na temat w domenie zdarzeń
- 50 subskrypcji zakresu domeny 
- 5000 zdarzeń na sekundę szybkości pozyskiwania (do domeny)

Jeśli te limity nie odpowiadają Twoim potrzebom, otwórz bilet pomocy technicznej lub wyślij wiadomość e-mail na adres [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Ceny
Domeny zdarzeń używają tych samych cen [operacji,](https://azure.microsoft.com/pricing/details/event-grid/) które są Event Grid innych funkcji.

Operacje działają tak samo w domenach zdarzeń, jak w tematach niestandardowych. Każdy ruch przychodzący zdarzenia do domeny zdarzeń jest operacją, a każda próba dostarczenia dla zdarzenia jest operacją.



## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat konfigurowania domen zdarzeń, tworzenia tematów, tworzenia subskrypcji zdarzeń i publikowania zdarzeń, zobacz [Zarządzanie domenami zdarzeń](./how-to-event-domains.md).
