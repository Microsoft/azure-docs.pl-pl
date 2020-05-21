---
title: Dołącz do Azure Event Grid partnera
description: Dołącz jako typ tematu partnera Azure Event Grid. Poznaj model zasobów i przepływ publikowania tematów dotyczących partnerów.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 3c2c2e3d5a2ef48ddc212fc0df4906c91071d803
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725957"
---
# <a name="become-and-event-grid-partner"></a>Zostań i Event Grid partnera

W tym artykule opisano, jak prywatnie używać zasobów partnerskich Event Grid i jak stać się dostępny publicznie typ tematu partnera.

Nie potrzebujesz specjalnego uprawnienia, aby rozpocząć korzystanie z Event Grid typów zasobów skojarzonych z publikowaniem zdarzeń jako partnerem Event Grid. W rzeczywistości można używać ich dzisiaj do publikowania zdarzeń prywatnych w ramach własnych subskrypcji platformy Azure, a także do testowania modelu zasobów, jeśli jest rozważany partner.

## <a name="becoming-an-event-grid-partner"></a>Zostań partnerem Event Grid

Jeśli interesuje Cię publiczny partner Event Grid, Zacznij od wypełniania [tego formularza](https://aka.ms/gridpartnerform), a następnie skontaktować się z zespołem Event Grid pod adresem [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com) .

## <a name="how-partner-topics-work"></a>Jak działają tematy partnerów
Tematy dotyczące partnerów przyjmują istniejącą architekturę, która Event Grid już używa programu w celu publikowania zdarzeń z zasobów platformy Azure, takich jak Storage i IoT Hub, i udostępnienia tych narzędzi publicznie innym użytkownikom. Korzystanie z tych narzędzi jest domyślnie prywatne wyłącznie do subskrypcji platformy Azure. Aby zapewnić dostępność wydarzeń publicznie, Wypełnij powyższy formularz i [skontaktuj się z zespołem Event Grid](mailto:gridpartner@microsoft.com).

Tematy dotyczące partnerów umożliwiają publikowanie zdarzeń w celu Azure Event Grid na użytek wielodostępności.

### <a name="onboarding-and-event-publishing-overview"></a>Przegląd funkcji dołączania i publikowania zdarzeń

#### <a name="partner-flow"></a>Przepływ partnerski

1. Utwórz dzierżawę platformy Azure, jeśli jeszcze jej nie masz.
1. Przy użyciu interfejsu wiersza polecenia Utwórz nowy Event Grid `partnerRegistration` . Ten zasób zawiera informacje, takie jak nazwa wyświetlana, opis, identyfikator URI Instalatora i tak dalej.

    ![Tworzenie tematu partnera](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Utwórz co najmniej jeden `partnerNamespaces` w każdym regionie, w którym chcesz opublikować zdarzenia. W ramach tej metody usługa Event Grid zainicjuje punkt końcowy publikowania (na przykład https://contoso.westus-1.eventgrid.azure.net/api/events) i klucze dostępu).

    ![Utwórz przestrzeń nazw partnera](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Umożliwianie klientom rejestrowania w systemie, który ma być tematem partnera.
1. Skontaktuj się z zespołem Event Grid, aby poinformować nas, że chcesz, aby Twój typ tematu partnera stał się publiczny.

#### <a name="customer-flow"></a>Przepływ klienta

1. Klient będzie odwiedzał Azure Portal, aby zanotować Identyfikator subskrypcji platformy Azure i grupę zasobów, które mają zostać utworzone w temacie partnera.
1. Klient będzie żądał tematu partnera za pośrednictwem systemu. W odpowiedzi utworzysz tunel zdarzenia w przestrzeni nazw partnera.
1. Event Grid utworzy temat **oczekujący** partner w subskrypcji platformy Azure i grupie zasobów klienta.

    ![Utwórz kanał zdarzenia](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Klient aktywuje temat partnera za pośrednictwem Azure Portal. Zdarzenia mogą teraz przepływać od usługi do subskrypcji platformy Azure klienta.

    ![Aktywuj temat partnera](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Model zasobów

Poniżej znajduje się model zasobów tematów dotyczących partnerów.

### <a name="partner-registrations"></a>Rejestracje partnerów
* Zasoby`partnerRegistrations`
* Używane przez: Partnerzy
* Opis: przechwytuje globalne metadane partnera SaaS (na przykład nazwę, nazwę wyświetlaną, opis, identyfikator URI Instalatora).
    
    Tworzenie/aktualizowanie rejestracji partnera jest operacją samoobsługową dla partnerów. Ta funkcja samoobsługowego zapewnia partnerom możliwość kompilowania i testowania kompletnego końca do przepływu końcowego.
    
    Klienci mogą odnajdywać tylko zatwierdzone partnerRegistrations firmy Microsoft.
* Zakres: utworzono w ramach subskrypcji platformy Azure partnera. Metadane widoczne dla klientów po ich publicznym udostępnieniu.

### <a name="partner-namespaces"></a>Przestrzenie nazw partnerów
* Zasób: partnerNamespaces
* Używane przez: Partnerzy
* Opis: udostępnia zasób regionalny do publikowania zdarzeń klienta w programie. Każda przestrzeń nazw partnerów ma punkt końcowy publikowania i klucze uwierzytelniania. Przestrzeń nazw zawiera również informacje o tym, jak partner żąda tematu partnera dla danego klienta i wyświetla listę aktywnych klientów.
* Zakres: przebywa w ramach subskrypcji partnera.

### <a name="event-channel"></a>Kanał zdarzenia
* Zasoby`partnerNamespaces/eventChannels`
* Używane przez: Partnerzy
* Opis: tunele zdarzeń stanowią duplikat tematu partnera klienta. Tworząc tunel zdarzeń i określając subskrypcję platformy Azure klienta i grupę zasobów w metadanych, nastąpi zasygnalizowanie Event Grid w celu utworzenia tematu partnera dla klienta. Event Grid wystawia połączenie ARM w celu utworzenia odpowiedniego partnerTopic w subskrypcji klienta. Temat partnera zostanie utworzony w stanie "oczekiwanie". Istnieje Link 1-1 między eventTunnel i partnerTopic.
* Zakres: przebywa w ramach subskrypcji partnera.

### <a name="partner-topics"></a>Tematy dotyczące partnerów
* Zasoby`partnerTopics`
* Używane przez: klienci
* Opis: Tematy dotyczące partnerów są podobne do tematu niestandardowego i tematu systemu w Event Grid. Każdy temat partnera jest skojarzony z określonym "źródłem" (na przykład `Contoso:myaccount` ) i określonym typem tematu partnera (na przykład "contoso"). Klienci tworzą subskrypcje zdarzeń w temacie partnera, aby kierować zdarzenia do różnych programów obsługi zdarzeń.

    Klienci nie mogą bezpośrednio utworzyć tego zasobu. Jedynym sposobem utworzenia tematu partnera jest utworzenie tunelu zdarzeń przez operację partnera.
* Zakres: przebywa w subskrypcji klienta.

### <a name="partner-topic-types"></a>Typy tematów partnerów
* Zasoby`partnerTopicTypes`
* Używane przez: klienci
* Opis: typy tematów partnerskich to typy zasobów dla całej dzierżawy, które umożliwiają klientom odnajdywanie listy zatwierdzonych typów tematów partnerów. Adres URL będzie wyglądać następująco:https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Zakres: globalny

## <a name="publishing-events-to-event-grid"></a>Publikowanie zdarzeń do Event Grid
Po utworzeniu partnerNamespace w regionie świadczenia usługi Azure uzyskasz regionalny punkt końcowy oraz odpowiednie klucze uwierzytelniania. Publikuj partie zdarzeń w tym punkcie końcowym dla wszystkich tuneli zdarzeń klienta w tej przestrzeni nazw. W oparciu o pole "Źródło" w zdarzeniu Azure Event Grid mapuje każde zdarzenie z odpowiednimi tematami partnera.

### <a name="event-schema-cloudevents-v10"></a>Schemat zdarzenia: CloudEvents v 1.0
Publikuj zdarzenia do Azure Event Grid przy użyciu schematu CloudEvents 1,0. Event Grid obsługuje tryb strukturalny i tryb wsadowy. CloudEvents 1,0 jest jedynym obsługiwanym schematem zdarzeń dla przestrzeni nazw partnerów.

### <a name="example-flow"></a>Przykładowy przepływ

1.  Usługa publikowania wykonuje wpis HTTP do https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01 .
2.  W żądaniu Dołącz wartość nagłówka o nazwie AEG-SAS-Key, która zawiera klucz do uwierzytelniania. Ten klucz jest inicjowany podczas tworzenia partnerNamespace. Na przykład prawidłowa wartość nagłówka to AEG-SAS-Key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = =.
3.  Ustaw nagłówek Content-Type na wartość "Application/cloudevents-Batch + JSON; charset = UTF-8 ".
4.  Wykonaj wpis HTTP w powyższym adresie URL publikowania, używając partii zdarzeń odpowiadających temu regionowi. Na przykład:

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

Po opublikowaniu w punkcie końcowym partnerNamespace otrzymasz odpowiedź. Odpowiedź jest standardowym kodem odpowiedzi HTTP. Niektóre typowe odpowiedzi to:

| Wynik                             | Odpowiedź              |
|------------------------------------|-----------------------|
| Powodzenie                            | 200 OK                |
| Dane zdarzenia mają niepoprawny format    | 400 Nieprawidłowe żądanie       |
| Nieprawidłowy klucz dostępu                 | 401 — nieautoryzowane      |
| Nieprawidłowy punkt końcowy                 | 404 — Nie znaleziono         |
| Tablica lub zdarzenie przekraczają limity rozmiaru | ładunek 413 zbyt duży |

## <a name="reference"></a>Dokumentacja

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Szablon ARM](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [Schemat szablonu ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [Interfejsy API REST](https://docs.microsoft.com/rest/api/eventgrid/partnernamespaces)
  * [Rozszerzenie interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>Zestawy SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Przejdź](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Następne kroki
- [Przegląd tematów dotyczących partnerów](partner-topics-overview.md)
- [Formularz dołączania tematów partnerskich](https://aka.ms/gridpartnerform)
- [Rozwiązanie Auth0 partnera](auth0-overview.md)
- [Jak korzystać z tematu partnera rozwiązanie Auth0](auth0-how-to.md)
