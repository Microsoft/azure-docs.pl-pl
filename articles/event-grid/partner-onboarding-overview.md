---
title: Omówienie dołączania partnerów (Azure Event Grid)
description: Zawiera omówienie sposobu dołączania jako Event Grid partnera.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 40d0afe0aaeb40412948eb304a36a3627566551b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869693"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Omówienie dołączania partnerów (Azure Event Grid)

W tym artykule opisano, jak prywatnie korzystać z zasobów Azure Event Grid partnerów i jak stać się publicznie dostępnym typem tematu partnera.

Nie potrzebujesz specjalnych uprawnień, aby rozpocząć korzystanie z typów zasobów Event Grid skojarzonych z publikowaniem zdarzeń jako Event Grid partnera. W rzeczywistości można ich obecnie używać do prywatnego publikowania zdarzeń we własnych subskrypcjach platformy Azure i testowania modelu zasobów, jeśli rozważasz zostać partnerem.

> [!NOTE]
> Aby uzyskać instrukcje krok po kroku dotyczące dołączania jako partner programu Event Grid przy użyciu programu Azure Portal, zobacz Jak do dołączać jako partner Event Grid [(Azure Portal).](onboard-partner.md) 

## <a name="how-partner-events-work"></a>Jak działają zdarzenia partnerów
Funkcja Zdarzeń partnerów korzysta z istniejącej architektury, która jest już używana przez program Event Grid do publikowania zdarzeń z zasobów platformy Azure, takich jak usługi Azure Storage i Azure IoT Hub, i udostępnia te narzędzia publicznie wszystkim osobom. Korzystanie z tych narzędzi jest domyślnie prywatne tylko dla Twojej subskrypcji platformy Azure. Aby udostępnić wydarzenia publicznie, wypełnij formularz i skontaktuj się z [zespołem Event Grid](mailto:gridpartner@microsoft.com).

Funkcja Zdarzeń partnerów umożliwia publikowanie zdarzeń w Azure Event Grid na potrzeby użycia wielowątkowego.

## <a name="onboarding-and-event-publishing-overview"></a>Omówienie dołączania i publikowania zdarzeń

### <a name="partner-flow"></a>Przepływ partnera

1. Utwórz dzierżawę platformy Azure, jeśli jeszcze jej nie masz.
1. Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć nową Event Grid `partnerRegistration` . Ten zasób zawiera informacje, takie jak nazwa wyświetlana, opis, adres URI konfiguracji i tak dalej.

    ![Tworzenie tematu partnera](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Utwórz co najmniej jedną przestrzeń nazw partnera w każdym regionie, w którym chcesz publikować zdarzenia. Usługa Event Grid apowiuje punkt końcowy publikowania (na przykład `https://contoso.westus-1.eventgrid.azure.net/api/events` ) i klucze dostępu.

    ![Tworzenie przestrzeni nazw partnera](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Udostępnij klientom sposób rejestrowania w systemie, że chcą mieć temat partnera.
1. Skontaktuj się z Event Grid, aby dowiedzieć się, że typ tematu partnera ma zostać publiczny.

### <a name="customer-flow"></a>Przepływ klienta

1. Klient odwiedzi witrynę Azure Portal, aby zanotować identyfikator subskrypcji platformy Azure i grupę zasobów, w której ma zostać utworzony temat partnera.
1. Klient żąda tematu partnera za pośrednictwem systemu. W odpowiedzi tworzysz tunel zdarzeń do przestrzeni nazw partnera.
1. Event Grid temat **oczekujące** partnera w subskrypcji i grupie zasobów platformy Azure klienta.

    ![Tworzenie kanału zdarzeń](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Klient aktywuje temat partnera za pośrednictwem Azure Portal. Zdarzenia mogą teraz przepływać z usługi do subskrypcji platformy Azure klienta.

    ![Aktywowanie tematu partnera](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Model zasobów
Następujący model zasobów jest dla zdarzeń partnera.

### <a name="partner-registrations"></a>Rejestracje partnerów
* Zasobów: `partnerRegistrations`
* Używane przez: Partnerzy
* Opis: przechwytuje globalne metadane partnera oprogramowania jako usługi (SaaS) (na przykład nazwę, nazwę wyświetlaną, opis, adres URI konfiguracji).
    
    Tworzenie lub aktualizowanie rejestracji partnera jest operacją samoobsługową dla partnerów. Ta samoobsługowa możliwość umożliwia partnerom tworzenie i testowanie pełnego przepływu.
    
    Tylko zatwierdzone przez firmę Microsoft rejestracje partnerów są wykrywane przez klientów.
* Zakres: utworzony w ramach subskrypcji platformy Azure partnera. Metadane są widoczne dla klientów po ich publicznie publicznie.

### <a name="partner-namespaces"></a>Przestrzenie nazw partnerów
* Zasobów: `partnerNamespaces`
* Używane przez: Partnerzy
* Opis: udostępnia zasób regionalny do publikowania zdarzeń klientów. Każda przestrzeń nazw partnera ma punkt końcowy publikowania i klucze uwierzytelniania. Przestrzeń nazw to również sposób, w jaki partner żąda tematu partnera dla danego klienta i wyświetla listę aktywnych klientów.
* Zakres: mieszka w subskrypcji partnera.

### <a name="event-channel"></a>Kanał zdarzeń
* Zasobów: `partnerNamespaces/eventChannels`
* Używane przez: Partnerzy
* Opis: Kanały zdarzeń są duplikatem tematu partnera klienta. Tworząc kanał zdarzeń i określając subskrypcję platformy Azure i grupę zasobów klienta w metadanych, możesz zasygnalizować, Event Grid utworzyć temat partnerski dla klienta. Event Grid tworzy Azure Resource Manager w celu utworzenia odpowiedniego tematu partnerskiego w subskrypcji klienta. Temat partnera jest tworzony w stanie oczekiwania. Istnieje połączenie jeden do jednego między poszczególnymi kanałami zdarzeń i tematami partnerskimi.
* Zakres: mieszka w subskrypcji partnera.

### <a name="partner-topics"></a>Tematy partnerów
* Zasobów: `partnerTopics`
* Używane przez: Klienci
* Opis: Tematy partnerów są podobne do tematów niestandardowych i tematów systemowych w Event Grid. Każdy temat partnera jest skojarzony z określonym źródłem (na przykład `Contoso:myaccount` ) i określonym typem tematu partnera (na przykład Contoso). Klienci tworzą subskrypcje zdarzeń w temacie partnera, aby przekierowywować zdarzenia do różnych programów obsługi zdarzeń.

    Klienci nie mogą bezpośrednio utworzyć tego zasobu. Jedynym sposobem utworzenia tematu partnera jest operacja partnera tworząca kanał zdarzeń.
* Zakres: mieszka w subskrypcji klienta.

### <a name="partner-topic-types"></a>Typy tematów partnerów
* Zasobów: `partnerTopicTypes`
* Używane przez: Klienci
* Opis: Typy tematów partnerskich to typy zasobów dla całej dzierżawy, które umożliwiają klientom odnajdywanie listy zatwierdzonych typów tematów partnerów. Adres URL wygląda następująco: https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Zakres: Globalny

## <a name="publish-events-to-event-grid"></a>Publikowanie zdarzeń w Event Grid
Po utworzeniu przestrzeni nazw partnera w regionie świadczenia usługi Azure otrzymasz regionalny punkt końcowy i odpowiednie klucze uwierzytelniania. Publikowanie partii zdarzeń w tym punkcie końcowym dla wszystkich kanałów zdarzeń klienta w tej przestrzeni nazw. Na podstawie pola źródłowego w zdarzeniu Azure Event Grid każde zdarzenie za pomocą odpowiednich tematów partnerów.

### <a name="event-schema-cloudevents-v10"></a>Schemat zdarzeń: CloudEvents 1.0
Publikowanie zdarzeń w Azure Event Grid przy użyciu schematu CloudEvents 1.0. Event Grid obsługuje zarówno tryb ustrukturyzowany, jak i tryb wsadowy. CloudEvents 1.0 jest jedynym obsługiwanym schematem zdarzeń dla przestrzeni nazw partnerów.

### <a name="example-flow"></a>Przykładowy przepływ

1.  Usługa publikowania obsługuje żądania POST protokołu HTTP na adres `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
1.  W żądaniu dołącz wartość nagłówka o nazwie aeg-sas-key, która zawiera klucz do uwierzytelniania. Ten klucz jest aprowowany podczas tworzenia przestrzeni nazw partnera. Na przykład prawidłową wartością nagłówka jest aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
1.  Ustaw nagłówek Content-Type na "application/cloudevents-batch+json; charset=UTF-8a".
1.  Uruchom zapytanie HTTP POST do adresu URL publikowania z partią zdarzeń, które odpowiadają temu regionowi. Na przykład:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Po opublikowaniu w punkcie końcowym przestrzeni nazw partnera otrzymasz odpowiedź. Odpowiedź jest standardowym kodem odpowiedzi HTTP. Niektóre typowe odpowiedzi to:

| Wynik                             | Reakcja              |
|------------------------------------|-----------------------|
| Powodzenie                            | 200 OK                |
| Dane zdarzenia mają niepoprawny format    | 400 Nieprawidłowe żądanie       |
| Nieprawidłowy klucz dostępu                 | 401 Brak autoryzacji      |
| Nieprawidłowy punkt końcowy                 | 404 — Nie znaleziono         |
| Tablica lub zdarzenie przekracza limity rozmiaru | 413 Ładunek jest za duży |

## <a name="references"></a>Odwołania

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Szablon usługi ARM](/azure/templates/microsoft.eventgrid/allversions)
  * [Schemat szablonu usługi ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [Interfejsy API REST](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [Rozszerzenie interfejsu wiersza polecenia](/cli/azure/)

### <a name="sdks"></a>Zestawy SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [Js](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Przejdź](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Następne kroki
- [Omówienie tematów partnerów](partner-events-overview.md)
- [Formularz dołączania tematów partnerów](https://aka.ms/gridpartnerform)
- [Temat partnera Auth0](auth0-overview.md)
- [Jak używać tematu partnera Auth0](auth0-how-to.md)
