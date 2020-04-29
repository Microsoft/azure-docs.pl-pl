---
title: Azure Event Grid zabezpieczenia i uwierzytelnianie
description: W tym artykule opisano różne sposoby uwierzytelniania dostępu do zasobów Event Grid (webhook, subskrypcje, tematy niestandardowe)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532397"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Uwierzytelnianie dostępu do zasobów Event Grid

Azure Event Grid ma trzy typy uwierzytelniania:

- Dostarczanie zdarzeń elementu webhook
- Subskrypcje zdarzeń
- Publikowanie tematów niestandardowych

## <a name="webhook-event-delivery"></a>Dostarczanie zdarzeń elementu webhook

Elementy webhook to jeden z wielu sposobów odbierania zdarzeń z Azure Event Grid. Gdy nowe zdarzenie jest gotowe, usługa Event Grid ogłasza żądanie HTTP do skonfigurowanego punktu końcowego przy użyciu zdarzenia w treści żądania.

Podobnie jak w przypadku wielu innych usług, które obsługują elementy webhook, Event Grid wymaga potwierdzenia własności punktu końcowego elementu webhook przed rozpoczęciem dostarczania zdarzeń do tego punktu końcowego. To wymaganie uniemożliwia złośliwemu użytkownikowi przelanie punktu końcowego ze zdarzeniami. W przypadku korzystania z jednej z trzech usług platformy Azure wymienionych poniżej infrastruktura platformy Azure automatycznie obsługuje tę weryfikację:

- Azure Logic Apps za pomocą [łącznika Event Grid](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation za pośrednictwem [elementu webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions z [wyzwalaczem Event Grid](../azure-functions/functions-bindings-event-grid.md)

Jeśli używasz innego typu punktu końcowego, takiego jak oparty na wyzwalaczu HTTP funkcja platformy Azure, kod punktu końcowego musi uczestniczyć w uzgadnianiu walidacji z Event Grid. Event Grid obsługuje dwa sposoby walidacji subskrypcji.

1. **Uzgadnianie synchroniczne**: w momencie tworzenia subskrypcji zdarzeń Event Grid wysyła do punktu końcowego zdarzenie weryfikacji subskrypcji. Schemat tego zdarzenia jest podobny do dowolnego innego zdarzenia Event Grid. Część danych tego zdarzenia zawiera `validationCode` właściwość. Aplikacja weryfikuje, czy żądanie weryfikacji dotyczy oczekiwanej subskrypcji zdarzeń, i zwraca kod weryfikacyjny w odpowiedzi synchronicznie. Ten mechanizm uzgadniania jest obsługiwany we wszystkich wersjach Event Grid.

2. **Uzgadnianie asynchroniczne**: w niektórych przypadkach nie można zwrócić ValidationCode w odpowiedzi synchronicznie. Na przykład w przypadku korzystania z usługi innej firmy (takiej jak [`Zapier`](https://zapier.com) lub [IFTTT](https://ifttt.com/)) nie można programowo odpowiedzieć na kod weryfikacyjny.

   Począwszy od wersji 2018-05-01-Preview, Event Grid obsługuje ręczne uzgadnianie walidacji. Jeśli tworzysz subskrypcję zdarzeń za pomocą zestawu SDK lub narzędzia korzystającego z interfejsu API w wersji 2018-05-01-Preview lub nowszej, Event Grid `validationUrl` wysyła właściwość w części danych zdarzenia walidacji subskrypcji. Aby ukończyć uzgadnianie, Znajdź ten adres URL w danych zdarzenia i wykonaj żądanie GET. Możesz użyć klienta REST lub przeglądarki sieci Web.

   Podany adres URL jest prawidłowy przez **5 minut**. W tym czasie stan aprowizacji subskrypcji zdarzenia to `AwaitingManualAction`. Jeśli weryfikacja ręczna nie zostanie zakończona w ciągu 5 minut, stan aprowizacji zostanie ustawiony na `Failed`. Przed rozpoczęciem walidacji ręcznej należy utworzyć subskrypcję zdarzeń.

   Ten mechanizm uwierzytelniania wymaga również, aby punkt końcowy elementu webhook zwracał kod stanu HTTP 200, aby wiedział, że wpis dla zdarzenia sprawdzania poprawności został zaakceptowany, zanim będzie można go umieścić w trybie walidacji ręcznej. Innymi słowy, jeśli punkt końcowy zwraca 200, ale nie zwraca odpowiedzi walidacji synchronicznie, tryb jest przenoszony do trybu walidacji ręcznej. Jeśli w ciągu 5 minut znajduje się adres URL sprawdzania poprawności, uzgadnianie weryfikacji jest uznawane za pomyślne.

> [!NOTE]
> Sprawdzanie poprawności przy użyciu certyfikatów z podpisem własnym nie jest obsługiwane. Zamiast tego użyj podpisanego certyfikatu z urzędu certyfikacji (CA).

### <a name="validation-details"></a>Szczegóły walidacji

- W momencie tworzenia/aktualizowania subskrypcji zdarzeń Event Grid ogłasza zdarzenie weryfikacji subskrypcji w docelowym punkcie końcowym.
- Zdarzenie zawiera wartość nagłówka "AEG-Event-Type: SubscriptionValidation".
- Treść zdarzenia ma ten sam schemat co inne zdarzenia Event Grid.
- Właściwość eventType zdarzenia ma `Microsoft.EventGrid.SubscriptionValidationEvent`wartość.
- Właściwość Data zdarzenia zawiera `validationCode` właściwość z losowo generowanym ciągiem. Na przykład "validationCode: acb13...".
- Dane zdarzenia zawierają również `validationUrl` właściwość z adresem URL służącą do ręcznego weryfikowania subskrypcji.
- Tablica zawiera tylko zdarzenie walidacji. Inne zdarzenia są wysyłane w oddzielnym żądaniu po ECHA kodu weryfikacyjnego.
- Zestawy SDK EventGrid dataplanes mają klasy odpowiadające danym zdarzenia weryfikacji subskrypcji i odpowiedzi na weryfikację subskrypcji.

Przykład SubscriptionValidationEvent pokazano w następującym przykładzie:

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

Aby udowodnić własność punktu końcowego, echo kod weryfikacyjny we właściwości validationResponse, jak pokazano w następującym przykładzie:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Należy zwrócić kod stanu odpowiedzi HTTP 200. Akceptowany protokół HTTP 202 nie został rozpoznany jako prawidłowa odpowiedź na weryfikację subskrypcji Event Grid. Żądanie HTTP musi zakończyć się w ciągu 30 sekund. Jeśli operacja nie zakończy się w ciągu 30 sekund, operacja zostanie anulowana i może zostać ponowiona po 5 sekundach. Jeśli wszystkie próby zakończą się niepowodzeniem, będzie ono traktowane jako błąd uzgadniania walidacji.

Można też ręcznie zweryfikować subskrypcję, wysyłając żądanie GET do adresu URL walidacji. Subskrypcja zdarzeń pozostaje w stanie oczekiwania do momentu zweryfikowania. Adres URL walidacji używa portu 553. Jeśli reguły zapory blokują port 553, reguły mogą wymagać aktualizacji w celu pomyślnego uzgodnienia ręcznego.

Aby zapoznać się z przykładem obsługi uzgadniania subskrypcji, zobacz [przykład w języku C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Rozwiązywanie problemów z walidacją EventSubsciption

Podczas tworzenia subskrypcji zdarzeń, jeśli widzisz komunikat o błędzie, taki jak "próba zweryfikowania podanego punktu końcowego HTTPS\/:/Your-Endpoint-here nie powiodła się. Aby uzyskać więcej informacji, odwiedź stronę\/https:/aka.MS/esvalidation ", co oznacza, że wystąpił błąd uzgadniania walidacji. Aby rozwiązać ten problem, sprawdź następujące aspekty:

- Wykonaj wpis HTTP w adresie URL elementu webhook za pomocą [przykładowej](#validation-details) treści żądania SubscriptionValidationEvent przy użyciu elementu Poster lub zwinięcie lub podobnego narzędzia.
- Jeśli element webhook implementuje mechanizm uzgadniania synchronicznego walidacji, należy sprawdzić, czy ValidationCode jest zwracany jako część odpowiedzi.
- Jeśli element webhook implementuje mechanizm uzgadniania asynchronicznego walidacji, sprawdź, czy wpis HTTP jest zwracany 200 OK.
- Jeśli element webhook zwraca 403 (zabroniony) w odpowiedzi, sprawdź, czy element webhook znajduje się za usługą Azure Application Gateway lub zaporą aplikacji sieci Web. W przeciwnym razie należy wyłączyć te reguły zapory i ponownie wykonać POST protokołu HTTP:

  920300 (żądanie nie zawiera nagłówka Accept, możemy to naprawić)

  942430 (ograniczone wykrycie anomalii znaku SQL (args): Przekroczono liczbę znaków specjalnych (12))

  920230 (wykryto wielokrotne kodowanie adresu URL)

  942130 (atak wstrzykiwania kodu SQL: wykryto tautology SQL).

  931130 (możliwe, że ataku zdalnego dołączania plików (RFI) = odwołuje się do domeny/linku

### <a name="event-delivery-security"></a>Zabezpieczenia dostarczania zdarzeń

#### <a name="azure-ad"></a>Azure AD

Punkt końcowy elementu webhook można zabezpieczyć za pomocą Azure Active Directory do uwierzytelniania i autoryzowania Event Grid do publikowania zdarzeń w punktach końcowych. Konieczne będzie utworzenie aplikacji Azure Active Directory, utworzenie zasady roli i usługi w aplikacji, która autoryzuje Event Grid, i skonfigurowanie subskrypcji zdarzeń do korzystania z aplikacji usługi Azure AD. [Dowiedz się, jak skonfigurować usługi AAD za pomocą Event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Parametry zapytania

Punkt końcowy elementu webhook można zabezpieczyć przez dodanie parametrów zapytania do adresu URL elementu webhook podczas tworzenia subskrypcji zdarzeń. Ustaw jeden z tych parametrów zapytania jako wpis tajny, taki jak [token dostępu](https://en.wikipedia.org/wiki/Access_token). Element webhook może użyć klucza tajnego do rozpoznawania zdarzenia z Event Grid z prawidłowymi uprawnieniami. Event Grid będą zawierać te parametry zapytania w każdym dostarczeniu zdarzenia do elementu webhook.

Podczas edytowania subskrypcji zdarzeń parametry zapytania nie są wyświetlane ani zwracane, chyba że w [interfejsie wiersza polecenia](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)platformy Azure jest używany parametr [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) .

Na koniec należy pamiętać, że Azure Event Grid obsługuje tylko punkty końcowe elementu webhook protokołu HTTPS.

## <a name="event-subscription"></a>Subskrypcja zdarzeń

Aby subskrybować zdarzenie, musisz udowodnić, że masz dostęp do źródła i obsługi zdarzeń. Dowód, że jesteś członkiem elementu webhook, został objęty poprzednią sekcją. Jeśli używasz procedury obsługi zdarzeń, która nie jest elementem webhook (takim jak centrum zdarzeń lub magazyn kolejki), musisz mieć dostęp do zapisu do tego zasobu. To sprawdzenie uprawnień uniemożliwia wysyłanie zdarzeń do zasobu przez nieautoryzowanego użytkownika.

Użytkownik musi mieć uprawnienie **Microsoft. EventGrid/EventSubscriptions/Write** do zasobu, który jest źródłem zdarzenia. To uprawnienie jest wymagane, ponieważ piszesz nową subskrypcję w zakresie zasobu. Wymagany zasób różni się w zależności od tego, czy subskrybujesz temat systemowy czy temat niestandardowy. Oba typy zostały opisane w tej sekcji.

### <a name="system-topics-azure-service-publishers"></a>Tematy systemowe (wydawcy usług platformy Azure)

W przypadku tematów systemowych należy mieć uprawnienia do napisania nowej subskrypcji zdarzeń w zakresie zasobu, który publikuje zdarzenie. Format zasobu:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Na przykład w celu subskrybowania zdarzenia na koncie magazynu o nazwie Moja **ACCT**wymagane jest uprawnienie Microsoft. EventGrid/EventSubscriptions/Write dotyczące:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tematy niestandardowe

W przypadku niestandardowych tematów należy mieć uprawnienia do zapisywania nowej subskrypcji zdarzeń w zakresie tematu usługi Event Grid. Format zasobu:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Na przykład w celu subskrybowania niestandardowego tematu o nazwie Moja **tematu**wymagane jest uprawnienie Microsoft. EventGrid/EventSubscriptions/Write dotyczące:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikowanie tematów niestandardowych

Tematy niestandardowe wykorzystują sygnaturę dostępu współdzielonego (SAS) lub uwierzytelnianie klucza. Zalecamy używanie sygnatury dostępu współdzielonego, ale uwierzytelnianie klucza zapewnia proste programowanie i jest zgodne z wieloma istniejącymi wydawcami elementów webhook.

Wartość uwierzytelniania należy uwzględnić w nagłówku HTTP. Dla sygnatury dostępu współdzielonego Użyj **AEG-SAS-token** dla wartości nagłówka. W przypadku uwierzytelniania za pomocą klucza Użyj wartości nagłówka **AEG-SAS-Key** .

### <a name="key-authentication"></a>Uwierzytelnianie klucza

Uwierzytelnianie klucza jest najprostszą formą uwierzytelniania. Użyj formatu:`aeg-sas-key: <your key>`

Na przykład przekazujesz klucz z:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Tokeny sygnatury dostępu współdzielonego dla Event Grid obejmują zasób, czas wygaśnięcia i sygnaturę. Format tokenu sygnatury dostępu współdzielonego `r={resource}&e={expiration}&s={signature}`to:.

Zasób jest ścieżką do tematu usługi Event Grid, do którego są wysyłane zdarzenia. Na przykład prawidłowa ścieżka zasobu to: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Aby wyświetlić wszystkie obsługiwane wersje interfejsu API, zobacz [typy zasobów Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Sygnatura jest generowana z klucza.

Na przykład prawidłowa wartość **AEG-SAS-token** to:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Poniższy przykład tworzy token sygnatury dostępu współdzielonego do użycia z Event Grid:

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

Wszystkie zdarzenia lub dane zapisywane na dysku przez usługę Event Grid są szyfrowane przez klucz zarządzany przez firmę Microsoft, dzięki czemu są szyfrowane w stanie spoczynku. Ponadto maksymalny okres czasu, przez jaki zdarzenia lub dane są przechowywane, wynosi 24 godziny zgodnie z [zasadami ponowienia Event Grid](delivery-and-retry.md). Event Grid automatycznie usunie wszystkie zdarzenia lub dane po 24 godzinach lub czas wygaśnięcia zdarzenia na żywo, w zależności od tego, która wartość jest mniejsza.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Walidacja punktu końcowego z CloudEvents v 1.0
Jeśli znasz już Event Grid, możesz mieć świadomość, Event Grid uzgadniania sprawdzania poprawności punktu końcowego w celu zapobiegania nadużyciom. CloudEvents v 1.0 implementuje własną [semantykę ochrony przed nadużyciami](security-authentication.md#webhook-event-delivery) przy użyciu metody http Options. Więcej informacji na ten temat można znaleźć [tutaj](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). W przypadku korzystania ze schematu CloudEvents dla danych wyjściowych Event Grid jest używany z ochroną nadużycia CloudEvents v 1.0 zamiast mechanizmu zdarzenia walidacji Event Grid.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do Event Grid, zobacz [Informacje o Event Grid](overview.md)
