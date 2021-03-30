---
title: Monitoruj interfejsy API przy użyciu usługi Azure API Management, Event Hubs i Moesif
titleSuffix: Azure API Management
description: Przykładowa aplikacja pokazująca zasady logowania do usługi Azure API Management, Azure Event Hubs i Moesif na potrzeby rejestrowania i monitorowania HTTP
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: abb9cbb73f8957cec2cb3240bbf186623b9b2ef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88205502"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitoruj interfejsy API przy użyciu usługi Azure API Management, Event Hubs i Moesif
[Usługa API Management](api-management-key-concepts.md) udostępnia wiele funkcji, które rozszerzają przetwarzanie żądań HTTP wysyłanych do interfejsu API protokołu HTTP. Jednak istnienie żądań i odpowiedzi jest przejściowe. Żądanie zostało zrealizowane i przepływa przez usługę API Management do interfejsu API zaplecza. Interfejs API przetwarza żądanie i odpowiedź z powrotem do klienta interfejsu API. Usługa API Management zachowuje pewne ważne statystyki dotyczące interfejsów API do wyświetlania na pulpicie nawigacyjnym Azure Portal, ale poza tym, szczegóły zostały utracone.

Korzystając z zasad log-to-eventhub w usłudze API Management, można wysłać wszelkie szczegóły żądania i odpowiedzi do [centrum zdarzeń platformy Azure](../event-hubs/event-hubs-about.md). Istnieje wiele powodów, dla których może być konieczne generowanie zdarzeń z komunikatów HTTP wysyłanych do interfejsów API. Niektóre przykłady obejmują historię śledzenia aktualizacji, analizy użycia, alertów o wyjątkach i integracji innych firm.

W tym artykule pokazano, jak przechwycić całe żądanie HTTP i komunikat odpowiedzi, wysłać go do centrum zdarzeń, a następnie przekazać ten komunikat do usługi innej firmy, która udostępnia usługi rejestrowania i monitorowania HTTP.

## <a name="why-send-from-api-management-service"></a>Dlaczego warto wysyłać z usługi API Management?
Istnieje możliwość napisania oprogramowania pośredniczącego HTTP, które może być przyłączane do struktur interfejsu API protokołu HTTP w celu przechwytywania żądań i odpowiedzi HTTP oraz strumieniowego umieszczania ich w systemach rejestrowania i monitorowania. Minusem tego podejścia to oprogramowanie pośredniczące HTTP, które musi zostać zintegrowane z interfejsem API zaplecza i musi być zgodne z platformą interfejsu API. Jeśli istnieje wiele interfejsów API, każdy z nich musi wdrożyć oprogramowanie pośredniczące. Często istnieją powody, dla których nie można zaktualizować interfejsów API zaplecza.

Korzystanie z usługi Azure API Management do integracji z infrastrukturą rejestrowania zapewnia scentralizowane i niezależne od platformy rozwiązanie. Jest on również skalowalny w części ze względu na możliwości [replikacji geograficznej](api-management-howto-deploy-multi-region.md) platformy Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Dlaczego warto wysyłać do centrum zdarzeń platformy Azure?
Warto zadawać pytania, dlaczego należy utworzyć zasady specyficzne dla platformy Azure Event Hubs? Istnieje wiele różnych miejsc, w których można rejestrować moje żądania. Dlaczego nie tylko wysyłać żądania bezpośrednio do ostatecznego miejsca docelowego?  Jest to opcja. Jednak podczas wykonywania żądań rejestrowania z usługi API Management należy rozważyć sposób, w jaki komunikaty rejestrowania wpływają na wydajność interfejsu API. Stopniowe zwiększanie obciążenia może być obsługiwane przez zwiększenie dostępnych wystąpień składników systemowych lub wykorzystanie replikacji geograficznej. Jednak krótkie skoki ruchu mogą spowodować opóźnienia żądań, jeśli żądania rejestracji infrastruktury zaczynają działać wolniej pod obciążeniem.

Usługa Azure Event Hubs została zaprojektowana z myślą o przeniesieniu ogromnych ilości danych z możliwością przejmowania większej liczby zdarzeń niż liczba żądań HTTP większości procesów interfejsu API. Centrum zdarzeń działa jako rodzaj zaawansowanego buforu między usługą API Management a infrastrukturą, która przechowuje i przetwarza komunikaty. Gwarantuje to, że wydajność interfejsu API nie pogorszy się z powodu infrastruktury rejestrowania.

Po przekazaniu danych do centrum zdarzeń jest ono utrwalane i będzie oczekiwać na przetworzenie przez użytkowników centrum zdarzeń. Centrum zdarzeń nie dba o to, w jaki sposób jest przetwarzany, po prostu dba się, że komunikat zostanie pomyślnie dostarczony.

Event Hubs ma możliwość przesyłania strumieniowego zdarzeń do wielu grup odbiorców. Pozwala to na przetwarzanie zdarzeń przez różne systemy. Umożliwia to obsługę wielu scenariuszy integracji bez konieczności dodawania opóźnień w przetwarzaniu żądania interfejsu API w ramach usługi API Management, ponieważ tylko jedno zdarzenie musi zostać wygenerowane.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Zasady wysyłania komunikatów aplikacji/http
Centrum zdarzeń akceptuje dane zdarzeń jako proste ciągi. Zawartość tego ciągu jest do Ciebie. Aby móc spakować żądanie HTTP i wysyłać je do Event Hubs, musimy sformatować ciąg za pomocą informacji o żądaniu lub odpowiedzi. W takiej sytuacji, jeśli istnieje istniejący format, którego można użyć ponownie, może nie trzeba pisać kodu analizy. Początkowo uważam za użycie [Har](http://www.softwareishard.com/blog/har-12-spec/) do wysyłania żądań i odpowiedzi HTTP. Jednak ten format jest zoptymalizowany pod kątem przechowywania sekwencji żądań HTTP w formacie opartym na notacji JSON. Zawiera ona wiele obowiązkowych elementów, które Dodaliśmy niepotrzebną złożoność do scenariusza przekazywania wiadomości HTTP przez sieć.

Alternatywną opcją było użycie `application/http` typu nośnika zgodnie z opisem w specyfikacji http [RFC 7230](https://tools.ietf.org/html/rfc7230). Ten typ nośnika korzysta z tego samego formatu, który jest używany do rzeczywistego wysyłania komunikatów HTTP przez sieć, ale cały komunikat można umieścić w treści innego żądania HTTP. W naszym przypadku po prostu będziemy używać treści jako wiadomości wysyłanej do Event Hubs. Prawdopodobnie istnieje parser, który istnieje w bibliotekach [klienckich Microsoft ASP.NET Web API 2,2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) , które mogą przeanalizować ten format i przekonwertować go na natywne `HttpRequestMessage` i `HttpResponseMessage` obiekty.

Aby można było utworzyć ten komunikat, musimy skorzystać z [wyrażeń zasad](./api-management-policy-expressions.md) opartych na języku C# w usłudze Azure API Management. Poniżej przedstawiono zasady, które wysyłają komunikat żądania HTTP do usługi Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Deklaracja zasad
Istnieje kilka konkretnych informacji o tym wyrażeniu zasad. Zasady log-to-eventhub mają atrybut o nazwie Rejestrator-ID, który odwołuje się do nazwy rejestratora, który został utworzony w ramach usługi API Management. Szczegóły dotyczące sposobu konfigurowania rejestratora centrum zdarzeń w usłudze API Management można znaleźć w dokumencie [jak rejestrować zdarzenia w usłudze azure Event Hubs na platformie azure API Management](api-management-howto-log-event-hubs.md). Drugi atrybut jest opcjonalnym parametrem, który instruuje Event Hubs partycją, w której ma zostać zapisany komunikat. Event Hubs używa partycji w celu zapewnienia skalowalności i wymaga co najmniej dwóch. Uporządkowane dostarczanie komunikatów jest gwarantowane tylko w ramach partycji. Jeśli nie podasz centrum zdarzeń, w którym partycja ma umieścić komunikat, w celu rozesłania obciążenia jest używany algorytm okrężny. Jednak może to spowodować, że niektóre z naszych komunikatów mają być przetwarzane poza kolejnością.

### <a name="partitions"></a>Partycje
Aby zapewnić, że komunikaty są dostarczane do odbiorców w kolejności i wykorzystują możliwości dystrybucji obciążenia partycji, wybieram wysyłanie komunikatów żądań HTTP do jednej partycji i komunikatów odpowiedzi HTTP na drugą partycję. Zapewnia to równomierne rozłożenie obciążenia i gwarantuje, że wszystkie żądania będą używane w odpowiedniej kolejności, a wszystkie odpowiedzi są używane w odpowiedniej kolejności. Możliwe jest użycie odpowiedzi przed odpowiednim żądaniem, ale nie jest to problem, ponieważ mamy inny mechanizm skorelowania żądań odpowiedzi i wiemy, że żądania zawsze są dostępne przed odpowiedziami.

### <a name="http-payloads"></a>Ładunki HTTP
Po skompilowaniu programu `requestLine` sprawdzimy, czy treść żądania powinna zostać obcięta. Treść żądania została obcięta tylko do 1024. Można to zwiększyć, jednak poszczególne komunikaty centrum zdarzeń są ograniczone do 256 KB, dlatego niektóre treści komunikatów HTTP nie mieszczą się w pojedynczym komunikacie. Podczas rejestrowania i analizy znacząca ilość informacji może pochodzić tylko z wiersza żądania HTTP i nagłówków. Ponadto wiele żądań interfejsów API zwraca tylko małe treści i dlatego utrata wartości informacji przez obcinanie dużych treści jest stosunkowo minimalne w porównaniu z zmniejszeniem kosztów transferu, przetwarzania i magazynowania, aby zachować całą zawartość treści. Jedną z końcowych informacji o przetwarzaniu treści jest to, że musimy przekazać `true` do `As<string>()` metody, ponieważ odczytamy zawartość treści, ale był również chciał, aby interfejs API zaplecza mógł odczytać treść. Przekazując do tej metody wartość true, powoduje, że treść jest buforowana, aby można było odczytać ją po raz drugi. Jest to ważne, aby mieć świadomość, że masz interfejs API, który przekazują duże pliki lub korzysta z długiego sondowania. W takich przypadkach najlepszym rozwiązaniem jest unikanie odczytywania treści w ogóle.

### <a name="http-headers"></a>Nagłówki HTTP
Nagłówki HTTP można przenieść do formatu wiadomości w formacie prostego pary klucz/wartość. Wybrano opcję rozłożenia niektórych pól poufnych zabezpieczeń, aby uniknąć niepotrzebnych przecieków informacji o poświadczeniach. Jest mało prawdopodobne, że klucze interfejsu API i inne poświadczenia będą używane do celów analizy. Jeśli chcemy przeanalizować użytkownika i konkretny produkt, z którego korzystasz, możemy pobrać ten `context` obiekt z obiektu i dodać go do wiadomości.

### <a name="message-metadata"></a>Metadane komunikatów
Podczas kompilowania kompletnego komunikatu w celu wysłania do centrum zdarzeń pierwszy wiersz nie jest w rzeczywistości częścią `application/http` komunikatu. Pierwszy wiersz to dodatkowe metadane składające się z tego, czy komunikat jest żądaniem, czy komunikatem odpowiedzi oraz IDENTYFIKATORem komunikatu, który jest używany do skorelowania żądań odpowiedzi. Identyfikator wiadomości jest tworzony przy użyciu innych zasad, które wyglądają następująco:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Możemy utworzyć komunikat żądania przechowywany w zmiennej, dopóki odpowiedź nie została zwrócona, a następnie wysłano żądanie i odpowiedź jako pojedynczy komunikat. Jednak przez wysłanie żądania i odpowiedzi niezależnie oraz użycie identyfikatora komunikatu w celu skorelowania dwóch, firma Microsoft zapewnia nieco większą elastyczność w zakresie rozmiaru wiadomości, dzięki czemu można korzystać z wielu partycji przy jednoczesnym zachowaniu kolejności komunikatów, a żądanie pojawi się w naszym pulpicie nawigacyjnym logowania. Może istnieć również kilka scenariuszy, w których prawidłowa odpowiedź nigdy nie jest wysyłana do centrum zdarzeń, prawdopodobnie z powodu błędu krytycznego żądania w usłudze API Management, ale nadal mamy rekord żądania.

Zasady wysyłania komunikatu HTTP odpowiedzi wyglądają podobnie do żądania, a więc kompletna konfiguracja zasad wygląda następująco:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

`set-variable`Zasady te tworzą wartość dostępną zarówno dla `log-to-eventhub` zasad w `<inbound>` sekcji, jak i w `<outbound>` sekcji.

## <a name="receiving-events-from-event-hubs"></a>Otrzymywanie zdarzeń z Event Hubs
Zdarzenia z centrum zdarzeń platformy Azure są odbierane przy użyciu [protokołu AMQP](https://www.amqp.org/). Zespół Service Bus firmy Microsoft udostępnił biblioteki klienckie, aby ułatwić wykorzystywanie zdarzeń. Obsługiwane są dwa różne podejścia. jeden z nich jest *bezpośrednim odbiorcą* , a drugi używa `EventProcessorHost` klasy. Przykłady tych dwóch metod można znaleźć w [przewodniku programowania Event Hubs](../event-hubs/event-hubs-programming-guide.md). Krótka wersja różnic polega na tym, `Direct Consumer` że masz kompletną kontrolę i `EventProcessorHost` wykonuje kilka czynności związanych z działaniem, ale wprowadza pewne założenia dotyczące sposobu przetwarzania tych zdarzeń.

### <a name="eventprocessorhost"></a>EventProcessorHost
W tym przykładzie używamy `EventProcessorHost` dla prostoty, ale może to nie być najlepszym wyborem w tym konkretnym scenariuszu. `EventProcessorHost` Czy trudno jest upewnić się, że nie trzeba martwić się o problemy wątkowe w ramach określonej klasy procesora zdarzeń. Jednak w naszym scenariuszu wystarczy przekonwertować komunikat do innego formatu i przekazać go do innej usługi przy użyciu metody asynchronicznej. Nie ma potrzeby aktualizowania stanu udostępnionego i w związku z tym nie ma ryzyka związanego z wątkiem. W większości scenariuszy `EventProcessorHost` jest prawdopodobnie najlepszym wyborem i jest to prostsze dla opcji.

### <a name="ieventprocessor"></a>IEventProcessor
Koncepcja Centralna podczas korzystania z programu `EventProcessorHost` polega na utworzeniu implementacji `IEventProcessor` interfejsu, który zawiera metodę `ProcessEventAsync` . Istota tej metody jest pokazana tutaj:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

Lista obiektów EventData jest przenoszona do metody i wykonujemy iterację na tej liście. Bajty każdej metody są analizowane w obiekcie HttpMessage i ten obiekt jest przesyłany do wystąpienia elementu IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
`HttpMessage`Wystąpienie zawiera trzy fragmenty danych:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

`HttpMessage`Wystąpienie zawiera `MessageId` Identyfikator GUID, który umożliwia połączenie żądania HTTP z odpowiadającą odpowiedzią http i wartość logiczną określającą, czy obiekt zawiera wystąpienie elementu HttpRequestMessage i HttpResponseMessage. Korzystając z wbudowanych klas HTTP z `System.Net.Http` , mam możliwość skorzystania z `application/http` kodu analizowania zawartego w `System.Net.Http.Formatting` .  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
`HttpMessage`Następnie wystąpienie jest przekazywane do implementacji `IHttpMessageProcessor` , która jest interfejsem utworzonym w celu oddzielenia odbioru i interpretacji zdarzenia z usługi Azure Event Hub oraz rzeczywistego przetwarzania go.

## <a name="forwarding-the-http-message"></a>Przekazywanie wiadomości HTTP
Dla tego przykładu zdecyduję się na wypchnięcie żądania HTTP do [analizy interfejsu API Moesif](https://www.moesif.com). Moesif to usługa oparta na chmurze, która jest wyspecjalizowana w analizie i debugowaniu HTTP. Są one dostępne w warstwie Bezpłatna, dzięki czemu można łatwo ją wypróbować i umożliwić nam wyświetlanie żądań HTTP w przepływie w czasie rzeczywistym za pośrednictwem usługi API Management.

Implementacja wygląda następująco. `IHttpMessageProcessor`

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

Wykorzystuje `MoesifHttpMessageProcessor` [bibliotekę interfejsu API języka C# dla Moesif](https://www.moesif.com/docs/api?csharp#events) , która ułatwia wypychanie danych zdarzeń http do ich usługi. Aby można było wysyłać dane HTTP do interfejsu API modułu zbierającego Moesif, potrzebujesz konta i identyfikatora aplikacji. Możesz uzyskać identyfikator aplikacji Moesif, tworząc konto w [witrynie sieci Web Moesif](https://www.moesif.com) , a następnie przejdź do odpowiedniej   ->  _konfiguracji aplikacji_ menu w górnej części.

## <a name="complete-sample"></a>Ukończ przykład
[Kod źródłowy](https://github.com/dgilling/ApimEventProcessor) i testy dla przykładu znajdują się w serwisie GitHub. Potrzebujesz [usługi API Management](get-started-create-service-instance.md), [połączonego centrum zdarzeń](api-management-howto-log-event-hubs.md)i [konta magazynu](../storage/common/storage-account-create.md) , aby samodzielnie uruchomić przykład.   

Przykładem jest tylko prosta aplikacja konsolowa, która nasłuchuje zdarzeń pochodzących z centrum zdarzeń, konwertuje je do Moesif `EventRequestModel` i `EventResponseModel` obiektów, a następnie przekazuje je do interfejsu API modułu zbierającego Moesif.

Na poniższym obrazie animowanym można zobaczyć żądanie wykonywane do interfejsu API w portalu dla deweloperów, aplikację konsolową pokazującą odbierane, przetwarzane i przesyłane dalej komunikaty, a następnie żądanie i odpowiedź wyświetlane w strumieniu zdarzeń.

![Demonstracja żądania przekazywanego do Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Podsumowanie
Usługa Azure API Management zapewnia idealne miejsce do przechwytywania ruchu HTTP przesyłanego do i z interfejsów API. Platforma Azure Event Hubs to wysoce skalowalne, tańsze rozwiązanie do przechwytywania tego ruchu i żywienia je w systemach przetwarzania pomocniczego na potrzeby rejestrowania, monitorowania i innych zaawansowanych analiz. Łączenie się z systemami monitorowania ruchu innych firm, takimi jak Moesif, jest tak proste jak kilka dziesiątych wierszy kodu.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze Azure Event Hubs
  * [Wprowadzenie do usługi Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Przewodnik programowania Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej o integracji API Management i Event Hubs
  * [Jak rejestrować zdarzenia w usłudze Azure Event Hubs na platformie Azure API Management](api-management-howto-log-event-hubs.md)
  * [Odwołanie do jednostki rejestratora](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Dokumentacja zasad logowania do centrum eventhub](./api-management-advanced-policies.md#log-to-eventhub)
