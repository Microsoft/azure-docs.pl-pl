---
title: Przegląd dołączania partnera (Azure Event Grid)
description: Zawiera przegląd, w jaki sposób można dołączyć jako partnera Event Grid.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: d16337eada829a817110abf7842f763972d0718f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109343"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Przegląd dołączania partnera (Azure Event Grid)

W tym artykule opisano, jak prywatnie używać zasobów partnerskich Azure Event Grid i jak stać się dostępny publicznie typ tematu partnera.

Nie potrzebujesz specjalnego uprawnienia, aby rozpocząć korzystanie z Event Grid typów zasobów skojarzonych z publikowaniem zdarzeń jako partnerem Event Grid. W rzeczywistości można z nich korzystać, aby publikować zdarzenia prywatnie we własnych subskrypcjach platformy Azure i testować model zasobów, jeśli jest rozważany partner.

> [!NOTE]
> Aby uzyskać instrukcje krok po kroku dotyczące sposobu dołączania jako partnera Event Grid przy użyciu Azure Portal, zobacz [jak dołączyć jako partnera Event Grid (Azure Portal)](onboard-partner.md). 

## <a name="how-partner-events-work"></a>Jak działają zdarzenia partnerskie
Funkcja zdarzenia partnerskie przyjmuje istniejącą architekturę, która Event Grid już używana do publikowania zdarzeń z zasobów platformy Azure, takich jak Azure Storage i Azure IoT Hub, i udostępnia te narzędzia publicznie użytkownikom do użytku. Korzystanie z tych narzędzi jest domyślnie prywatne wyłącznie do subskrypcji platformy Azure. Aby zapewnić dostępność wydarzeń publicznie, Wypełnij formularz i [skontaktuj się z zespołem Event Grid](mailto:gridpartner@microsoft.com).

Funkcja zdarzenia partnera umożliwia publikowanie zdarzeń w celu Azure Event Grid na potrzeby użycia wielodostępnego.

## <a name="onboarding-and-event-publishing-overview"></a>Przegląd funkcji dołączania i publikowania zdarzeń

### <a name="partner-flow"></a>Przepływ partnerski

1. Utwórz dzierżawę platformy Azure, jeśli jeszcze jej nie masz.
1. Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć nowy Event Grid `partnerRegistration` . Ten zasób zawiera informacje, takie jak nazwa wyświetlana, opis, identyfikator URI Instalatora itd.

    ![Tworzenie tematu partnera](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Utwórz co najmniej jedną przestrzeń nazw partnera w każdym regionie, w której chcesz opublikować zdarzenia. Usługa Event Grid udostępnia punkt końcowy publikowania (na przykład `https://contoso.westus-1.eventgrid.azure.net/api/events` ) i klucze dostępu.

    ![Tworzenie przestrzeni nazw partnerskiej](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Umożliwianie klientom rejestrowania w systemie, który ma temat partnera.
1. Skontaktuj się z zespołem Event Grid, aby dowiedzieć się, czy chcesz, aby Twój temat partnera stał się publiczny.

### <a name="customer-flow"></a>Przepływ klienta

1. Klient odwiedzi Azure Portal, aby zwrócić uwagę na identyfikator subskrypcji platformy Azure i grupę zasobów, w których ma zostać utworzony temat partnera.
1. Klient żąda tematu partnera za pośrednictwem systemu. W odpowiedzi utworzysz tunel zdarzenia do przestrzeni nazw partnera.
1. Event Grid tworzy temat **oczekujących** partnerów w ramach subskrypcji i grupy zasobów klienta platformy Azure.

    ![Tworzenie kanału zdarzeń](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Klient aktywuje temat partnera za pośrednictwem Azure Portal. Zdarzenia mogą teraz przepływać od usługi do subskrypcji platformy Azure klienta.

    ![Aktywuj temat partnera](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Model zasobów
Poniższy model zasobów jest przeznaczony dla zdarzeń partnerskich.

### <a name="partner-registrations"></a>Rejestracje partnerów
* Zasoby `partnerRegistrations`
* Używane przez: Partnerzy
* Opis: przechwytuje globalne metadane partnera "oprogramowanie jako usługa" (SaaS) (na przykład nazwę, nazwę wyświetlaną, opis, identyfikator URI Instalatora).
    
    Tworzenie lub aktualizowanie rejestracji partnera jest operacją samoobsługową dla partnerów. Ta funkcja samoobsługowego zapewnia partnerom możliwość kompilowania i testowania kompletnego kompleksowego przepływu.
    
    Klienci mogą wykrywać wyłącznie rejestracje zatwierdzone przez firmę Microsoft.
* Zakres: utworzono w ramach subskrypcji platformy Azure partnera. Metadane są widoczne dla klientów po ich publicznym udostępnieniu.

### <a name="partner-namespaces"></a>Przestrzenie nazw partnerów
* Zasoby `partnerNamespaces`
* Używane przez: Partnerzy
* Opis: udostępnia zasób regionalny do publikowania zdarzeń klienta w programie. Każda przestrzeń nazw partnerów ma punkt końcowy publikowania i klucze uwierzytelniania. Przestrzeń nazw zawiera również informacje o tym, jak partner żąda tematu partnera dla danego klienta i wyświetla listę aktywnych klientów.
* Zakres: przebywa w subskrypcji partnera.

### <a name="event-channel"></a>Kanał zdarzenia
* Zasoby `partnerNamespaces/eventChannels`
* Używane przez: Partnerzy
* Opis: kanały zdarzeń stanowią duplikat tematu partnera klienta. Tworząc kanał zdarzenia i określając subskrypcję platformy Azure i grupę zasobów klienta w metadanych, należy zasygnalizować Event Grid, aby utworzyć temat partnera dla klienta. Event Grid wystawia połączenie Azure Resource Manager w celu utworzenia odpowiedniego tematu partnera w ramach subskrypcji klienta. Temat partnera jest tworzony w stanie oczekiwania. Istnieje link jeden do jednego między każdym kanałem zdarzeń a tematem partnera.
* Zakres: przebywa w subskrypcji partnera.

### <a name="partner-topics"></a>Tematy partnerów
* Zasoby `partnerTopics`
* Używane przez: klienci
* Opis: Tematy dotyczące partnerów są podobne do tematów niestandardowych i tematów systemowych w Event Grid. Każdy temat partnera jest skojarzony z określonym źródłem (na przykład `Contoso:myaccount` ) i określonym typem tematu partnera (na przykład contoso). Klienci tworzą subskrypcje zdarzeń w temacie partnera, aby kierować zdarzenia do różnych programów obsługi zdarzeń.

    Klienci nie mogą bezpośrednio utworzyć tego zasobu. Jedynym sposobem utworzenia tematu partnera jest przeprowadzenie operacji partnerskiej, która tworzy kanał zdarzenia.
* Zakres: przebywa w subskrypcji klienta.

### <a name="partner-topic-types"></a>Typy tematów partnerów
* Zasoby `partnerTopicTypes`
* Używane przez: klienci
* Opis: typy tematów partnerskich to typy zasobów dla całej dzierżawy, które umożliwiają klientom odnajdywanie listy zatwierdzonych typów tematów partnerów. Adres URL wygląda następująco https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Zakres: globalny

## <a name="publish-events-to-event-grid"></a>Publikuj zdarzenia do Event Grid
Podczas tworzenia przestrzeni nazw partnerów w regionie świadczenia usługi Azure uzyskasz regionalny punkt końcowy oraz odpowiednie klucze uwierzytelniania. Publikuj partie zdarzeń w tym punkcie końcowym dla wszystkich kanałów zdarzeń klienta w tej przestrzeni nazw. Na podstawie pola źródłowego w zdarzeniu Azure Event Grid mapuje każde zdarzenie z odpowiednimi tematami partnera.

### <a name="event-schema-cloudevents-v10"></a>Schemat zdarzenia: CloudEvents v 1.0
Publikuj zdarzenia do Azure Event Grid przy użyciu schematu CloudEvents 1,0. Event Grid obsługuje tryb strukturalny i tryb wsadowy. CloudEvents 1,0 jest jedynym obsługiwanym schematem zdarzeń dla przestrzeni nazw partnerów.

### <a name="example-flow"></a>Przykładowy przepływ

1.  Usługa publikowania wykonuje wpis HTTP do `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
1.  W żądaniu Dołącz wartość nagłówka o nazwie AEG-SAS-Key, która zawiera klucz do uwierzytelniania. Ten klucz jest inicjowany podczas tworzenia przestrzeni nazw partnera. Na przykład prawidłowa wartość nagłówka to AEG-SAS-Key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = =.
1.  Ustaw nagłówek Content-Type na wartość "Application/cloudevents-Batch + JSON; charset = UTF-8a.
1.  Uruchom zapytanie HTTP POST do adresu URL publikowania, używając partii zdarzeń odpowiadających temu regionowi. Na przykład:

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

Po opublikowaniu w punkcie końcowym obszaru nazw partnera otrzymujesz odpowiedź. Odpowiedź jest standardowym kodem odpowiedzi HTTP. Niektóre typowe odpowiedzi to:

| Wynik                             | Reakcja              |
|------------------------------------|-----------------------|
| Powodzenie                            | 200 OK                |
| Dane zdarzenia mają niepoprawny format    | 400 Nieprawidłowe żądanie       |
| Nieprawidłowy klucz dostępu                 | 401 Brak autoryzacji      |
| Nieprawidłowy punkt końcowy                 | 404 — Nie znaleziono         |
| Tablica lub zdarzenie przekraczają limity rozmiaru | ładunek 413 zbyt duży |

## <a name="references"></a>Odwołania

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Szablon usługi ARM](/azure/templates/microsoft.eventgrid/allversions)
  * [Schemat szablonu ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [Interfejsy API REST](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [Rozszerzenie interfejsu wiersza polecenia](/cli/azure/ext/eventgrid/)

### <a name="sdks"></a>Zestawy SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Przejdź](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Następne kroki
- [Przegląd tematów dotyczących partnerów](partner-events-overview.md)
- [Formularz dołączania tematów partnerskich](https://aka.ms/gridpartnerform)
- [Rozwiązanie Auth0 partnera](auth0-overview.md)
- [Jak korzystać z tematu partnera rozwiązanie Auth0](auth0-how-to.md)
