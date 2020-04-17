---
title: Zabezpieczenia i uwierzytelnianie usługi Azure Event Grid
description: W tym artykule opisano różne sposoby uwierzytelniania dostępu do zasobów usługi Event Grid (elementów WebHook, subskrypcje, tematy niestandardowe)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532397"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Uwierzytelnianie dostępu do zasobów siatki zdarzeń

Usługa Azure Event Grid ma trzy typy uwierzytelniania:

- Dostarczanie zdarzenia elementu WebHook
- Subskrypcje zdarzeń
- Publikowanie tematów niestandardowych

## <a name="webhook-event-delivery"></a>Dostarczanie zdarzenia elementu WebHook

Webhooks są jednym z wielu sposobów, aby odbierać zdarzenia z usługi Azure Event Grid. Gdy nowe zdarzenie jest gotowe, usługa event grid posts żądanie HTTP do skonfigurowanego punktu końcowego ze zdarzeniem w treści żądania.

Podobnie jak wiele innych usług, które obsługują elementów webhook, usługa Event Grid wymaga udowodnienia własności punktu końcowego elementu Webhook przed rozpoczęciem dostarczania zdarzeń do tego punktu końcowego. To wymaganie zapobiega złośliwemu użytkownikowi zalewania punktu końcowego zdarzeniami. Korzystając z dowolnej z trzech usług platformy Azure wymienionych poniżej, infrastruktura platformy Azure automatycznie obsługuje tę weryfikację:

- Usługa Azure Logic Apps z [łącznikiem siatki zdarzeń](https://docs.microsoft.com/connectors/azureeventgrid/)
- Usługa Azure Automation za pośrednictwem [elementu webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Usługi platformy Azure z [wyzwalaczem siatki zdarzeń](../azure-functions/functions-bindings-event-grid.md)

Jeśli używasz innego typu punktu końcowego, takiego jak funkcja platformy Azure oparta na wyzwalaczu HTTP, kod punktu końcowego musi uczestniczyć w uzgadnianiu sprawdzania poprawności za pomocą usługi Event Grid. Usługa Event Grid obsługuje dwa sposoby sprawdzania poprawności subskrypcji.

1. **Uzgadnianie synchroniczne:** W momencie tworzenia subskrypcji zdarzeń usługa Event Grid wysyła zdarzenie sprawdzania poprawności subskrypcji do punktu końcowego. Schemat tego zdarzenia jest podobny do dowolnego innego zdarzenia w ucho. Część danych tego zdarzenia `validationCode` zawiera właściwość. Aplikacja sprawdza, czy żądanie sprawdzania poprawności jest dla oczekiwanej subskrypcji zdarzenia i zwraca kod sprawdzania poprawności w odpowiedzi synchronicznie. Ten mechanizm uzgadniania jest obsługiwany we wszystkich wersjach siatki zdarzeń.

2. **Uzgadnianie asynchroniczne:** W niektórych przypadkach nie można zwrócić ValidationCode w odpowiedzi synchronicznie. Na przykład jeśli używasz usługi innej [`Zapier`](https://zapier.com) firmy (np. lub [IFTTT),](https://ifttt.com/)nie można programowo odpowiedzieć kodem sprawdzania poprawności.

   Począwszy od wersji 2018-05-01-preview, usługa Event Grid obsługuje ręczne uzgadnianie sprawdzania poprawności. Jeśli tworzysz subskrypcję zdarzeń za pomocą zestawu SDK lub narzędzia korzystającego z interfejsu API w wersji 2018-05-01-preview lub nowszej, usługa Event Grid wysyła `validationUrl` właściwość w części danych zdarzenia sprawdzania poprawności subskrypcji. Aby zakończyć uzgadnianie, znajdź ten adres URL w danych zdarzenia i wykonaj żądanie GET do niego. Możesz użyć klienta REST lub przeglądarki internetowej.

   Podany adres URL jest ważny przez **5 minut**. W tym czasie stan inicjowania `AwaitingManualAction`obsługi administracyjnej subskrypcji zdarzeń jest . Jeśli ręczne sprawdzanie poprawności nie zostanie zakończone w ciągu 5 minut, stan inicjowania obsługi administracyjnej jest ustawiony na `Failed`. Przed rozpoczęciem ręcznego sprawdzania poprawności należy ponownie utworzyć subskrypcję zdarzenia.

   Ten mechanizm uwierzytelniania wymaga również punktu końcowego elementu webhook do zwrócenia kodu stanu HTTP 200, dzięki czemu wie, że POST dla zdarzenia sprawdzania poprawności został zaakceptowany, zanim będzie można umieścić w trybie ręcznej sprawdzania poprawności. Innymi słowy, jeśli punkt końcowy zwraca 200, ale nie zwraca odpowiedzi sprawdzania poprawności synchronicznie, tryb jest przesunięty do trybu ręcznego sprawdzania poprawności. Jeśli istnieje GET na adres URL sprawdzania poprawności w ciągu 5 minut, uzgadnianie sprawdzania poprawności jest uważane za pomyślne.

> [!NOTE]
> Używanie certyfikatów z podpisem własnym do sprawdzania poprawności nie jest obsługiwane. Zamiast tego należy użyć podpisanego certyfikatu z urzędu certyfikacji.

### <a name="validation-details"></a>Szczegóły sprawdzania poprawności

- W momencie tworzenia/aktualizowania subskrypcji zdarzeń siatka zdarzeń księguje zdarzenie sprawdzania poprawności subskrypcji do docelowego punktu końcowego.
- Zdarzenie zawiera wartość nagłówka "aeg-event-type: SubscriptionValidation".
- Treść zdarzenia ma ten sam schemat co inne zdarzenia w usztywnienia.
- Właściwość eventType zdarzenia `Microsoft.EventGrid.SubscriptionValidationEvent`to .
- Właściwość danych zdarzenia zawiera `validationCode` właściwość z losowo wygenerowanym ciągiem. Na przykład "validationCode: acb13...".
- Dane zdarzenia obejmują `validationUrl` również właściwość z adresem URL do ręcznego sprawdzania poprawności subskrypcji.
- Tablica zawiera tylko zdarzenie sprawdzania poprawności. Inne zdarzenia są wysyłane w osobnym żądaniu po echem z powrotem kod sprawdzania poprawności.
- Zestawy SDK EventGrid DataPlane mają klasy odpowiadające danym zdarzenia sprawdzania poprawności subskrypcji i odpowiedzi na sprawdzanie poprawności subskrypcji.

Przykład subscriptionvalidationEvent jest pokazany w poniższym przykładzie:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Aby udowodnić własność punktu końcowego, echo z powrotem kod sprawdzania poprawności w validationResponse właściwości, jak pokazano w poniższym przykładzie:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Należy zwrócić kod stanu odpowiedzi HTTP 200 OK. Protokół HTTP 202 Zaakceptowane nie jest rozpoznawany jako prawidłowa odpowiedź sprawdzania poprawności subskrypcji usługi Event Grid. Żądanie http musi zostać ukończone w ciągu 30 sekund. Jeśli operacja nie zakończy się w ciągu 30 sekund, operacja zostanie anulowana i może zostać ponownie przestrzeliona po 5 sekundach. Jeśli wszystkie próby nie powiodą się, zostanie on potraktowany jako błąd uzgadniania sprawdzania poprawności.

Można też ręcznie sprawdzić poprawność subskrypcji, wysyłając żądanie GET do adresu URL sprawdzania poprawności. Subskrypcja zdarzenia pozostaje w stanie oczekiwania do czasu weryfikacji. Adres URL sprawdzania poprawności używa portu 553. Jeśli reguły zapory blokują port 553, może być konieczne zaktualizowanie reguł w celu pomyślnego ręcznego uzgadniania.

Na przykład obsługi uzgadniania sprawdzania poprawności subskrypcji, zobacz [przykład języka C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Sprawdzanie poprawności zdarzeńsubsciption

Podczas tworzenia subskrypcji zdarzeń, jeśli widzisz komunikat o błędzie, takich jak "Próba sprawdzania\/poprawności podanego punktu końcowego https: /your-endpoint-here nie powiodło się. Aby uzyskać więcej informacji,\/odwiedź https: /aka.ms/esvalidation", wskazuje, że występuje błąd w uzgadnianiu sprawdzania poprawności. Aby rozwiązać ten błąd, sprawdź następujące aspekty:

- Wykonaj wpis HTTP do adresu URL elementu webhook z [przykładową treścią żądania SubscriptionValidationEvent](#validation-details) przy użyciu narzędzia Postman lub curl lub podobne.
- Jeśli element webhook implementuje mechanizm uzgadniania sprawdzania poprawności synchroniczowej, sprawdź, czy ValidationCode jest zwracany jako część odpowiedzi.
- Jeśli element webhook implementuje mechanizm uzgadniania asynchronii sprawdzania poprawności, sprawdź, czy adres HTTP POST zwraca 200 OK.
- Jeśli element webhook zwraca 403 (zabronione) w odpowiedzi, sprawdź, czy element webhook znajduje się za bramą aplikacji platformy Azure lub zaporą aplikacji sieci Web. Jeśli tak, to musisz wyłączyć te reguły zapory i zrobić POST HTTP ponownie:

  920300 (Żądanie braku nagłówka akceptują, możemy to naprawić)

  942430 (Ograniczone wykrywanie anomalii znaków SQL (args): liczba znaków specjalnych przekroczonych (12))

  920230 (wykryto wiele kodowania adresów URL)

  942130 (Wykryto atak iniekcji SQL: Tautologia SQL).

  931130 (Możliwy atak zdalnego dołączania plików (RFI) = odwołanie/łącze poza domeną)

### <a name="event-delivery-security"></a>Zabezpieczenia dostarczania zdarzeń

#### <a name="azure-ad"></a>Azure AD

Punkt końcowy elementu webhook można zabezpieczyć za pomocą usługi Azure Active Directory do uwierzytelniania i autoryzowania usługi Event Grid w celu publikowania zdarzeń w punktach końcowych. Musisz utworzyć aplikację usługi Azure Active Directory, utworzyć zasadę roli i usługi w aplikacji autoryzującej usługę Event Grid i skonfigurować subskrypcję zdarzeń do korzystania z aplikacji usługi Azure AD. [Dowiedz się, jak skonfigurować aad z siatką zdarzeń](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Parametry zapytania

Punkt końcowy elementu webhook można zabezpieczyć, dodając parametry zapytania do adresu URL elementu webhook podczas tworzenia subskrypcji zdarzeń. Ustaw jeden z tych parametrów kwerendy jako klucz tajny, taki jak [token dostępu.](https://en.wikipedia.org/wiki/Access_token) Element webhook można użyć klucza tajnego, aby rozpoznać zdarzenie pochodzi z usługi Event Grid z prawidłowymi uprawnieniami. Usługa Event Grid będzie zawierać te parametry zapytania w każdym dostarczaniu zdarzeń do elementu webhook.

Podczas edytowania subskrypcji zdarzeń parametry kwerendy nie są wyświetlane ani zwracane, chyba że parametr [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) jest używany w usłudze Azure [CLI.](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)

Na koniec należy pamiętać, że usługa Azure Event Grid obsługuje tylko punkty końcowe https webhook.

## <a name="event-subscription"></a>Subskrypcja zdarzeń

Aby zasubskrybować zdarzenie, należy udowodnić, że masz dostęp do źródła zdarzeń i programu obsługi. Udowodnienie, że jesteś właścicielem elementu WebHook została omówiona w poprzedniej sekcji. Jeśli używasz programu obsługi zdarzeń, który nie jest elementem WebHook (na przykład centrum zdarzeń lub magazynu kolejek), musisz uzyskać dostęp do zapisu do tego zasobu. To sprawdzenie uprawnień uniemożliwia nieautoryzowanemu użytkownikowi wysyłanie zdarzeń do zasobu.

Musisz mieć uprawnienie **Microsoft.EventGrid/EventSubscriptions/Write** na zasób, który jest źródłem zdarzenia. To uprawnienie jest potrzebne, ponieważ piszesz nową subskrypcję w zakresie zasobu. Wymagany zasób różni się w zależności od tego, czy subskrybujesz temat systemowy, czy temat niestandardowy. Oba typy są opisane w tej sekcji.

### <a name="system-topics-azure-service-publishers"></a>Tematy systemu (wydawcy usług platformy Azure)

W przypadku tematów systemowych potrzebne jest uprawnienie do zapisu nowej subskrypcji zdarzeń w zakresie zasobu publikującego zdarzenie. Format zasobu jest:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Na przykład, aby zasubskrybować zdarzenie na koncie magazynu o nazwie **myacct,** potrzebujesz uprawnienia Microsoft.EventGrid/EventSubscriptions/Write na:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tematy niestandardowe

W przypadku tematów niestandardowych potrzebne jest uprawnienie do zapisu nowej subskrypcji zdarzeń w zakresie tematu siatki zdarzeń. Format zasobu jest:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Na przykład, aby zasubskrybować niestandardowy temat o nazwie **mytopic**, potrzebujesz uprawnienia Microsoft.EventGrid/EventSubscriptions/Write na:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikowanie tematów niestandardowych

Tematy niestandardowe używają sygnatury dostępu współdzielonego (SAS) lub uwierzytelniania kluczy. Zalecamy sygnatury dostępu Współdzielonego, ale uwierzytelnianie za pomocą klucza zapewnia proste programowanie i jest zgodne z wieloma istniejącymi wydawcami pakietów webhook.

Wartość uwierzytelniania należy uwzględnić w nagłówku HTTP. W przypadku sygnatury dostępu Współdzielonego należy użyć **tokenu aeg-sas** dla wartości nagłówka. W przypadku uwierzytelniania za pomocą **klucza aeg-sas** dla wartości nagłówka.

### <a name="key-authentication"></a>Uwierzytelnianie za pomocą klucza

Uwierzytelnianie za klucze jest najprostszą formą uwierzytelniania. Użyj formatu:`aeg-sas-key: <your key>`

Na przykład należy przekazać klucz z:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Tokeny sygnatury dostępu Współdzielonego dla siatki zdarzeń obejmują zasób, czas wygaśnięcia i podpis. Format tokenu Sygnatury dostępu Współdzielonego jest: `r={resource}&e={expiration}&s={signature}`.

Zasób jest ścieżką dla tematu siatki zdarzeń, do którego wysyłasz zdarzenia. Na przykład prawidłowa ścieżka `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`zasobu to: . Aby wyświetlić wszystkie obsługiwane wersje interfejsu API, zobacz [Typy zasobów microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Podpis jest generowany z klucza.

Na przykład prawidłowa wartość **tokenu aeg-sas** jest:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Poniższy przykład tworzy token sygnatury dostępu Współdzielonego do użycia z siatką zdarzeń:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Wszystkie zdarzenia lub dane zapisane na dysku przez usługę Event Grid są szyfrowane przez klucz zarządzany przez firmę Microsoft, zapewniając, że jest zaszyfrowany w spoczynku. Ponadto maksymalny okres przechowywania zdarzeń lub danych wynosi 24 godziny w przestrzeganiu [zasad ponawiania prób w siatce zdarzeń.](delivery-and-retry.md) Usługa Event Grid automatycznie usunie wszystkie zdarzenia lub dane po 24 godzinach lub czas zdarzenia na żywo, w zależności od tego, która z tych wartości jest mniejsza.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Sprawdzanie poprawności punktu końcowego za pomocą cloudevents w wersji 1.0
Jeśli jesteś już zaznajomiony z event grid, może być świadomy uzgadniania sprawdzania poprawności punktu końcowego zdarzenia w celu zapobiegania nadużyciom. CloudEvents w wersji 1.0 implementuje własną [semantytykę ochrony przed nadużyciami](security-authentication.md#webhook-event-delivery) przy użyciu metody OPCJE HTTP. Więcej informacji na ten temat można znaleźć [tutaj](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Podczas korzystania ze schematu CloudEvents dla danych wyjściowych, Usługa Event Grid używa z cloudEvents v1.0 ochrony przed nadużyciami zamiast mechanizmu sprawdzania poprawności siatki zdarzeń.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do siatki zdarzeń, zobacz [Informacje o siatce zdarzeń](overview.md)
