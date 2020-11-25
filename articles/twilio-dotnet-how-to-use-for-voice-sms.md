---
title: Jak używać programu Twilio for Voice i SMS (.NET) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS z usługą interfejsu API Twilio na platformie Azure. Przykłady kodu zapisywane w środowisku .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.custom: devx-track-dotnet
ms.openlocfilehash: 104f969f5e27ef36ad43eb10e19176a4bcfd6648
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003767"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Jak korzystać z funkcji Twilio na potrzeby obsługi głosu i programu SMS z platformy Azure
W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne za pomocą usługi interfejsu API Twilio na platformie Azure. Omówione scenariusze obejmują wykonywanie połączeń telefonicznych i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat Twilio i używania programów Voice i SMS w aplikacjach, zobacz sekcję [następne kroki](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Twilio to zaawansowana komunikacja biznesowa, dzięki czemu deweloperzy mogą osadzać głos, VoIP i komunikaty w aplikacjach. Umożliwiają one wirtualizację całej infrastruktury wymaganej w środowisku globalnym opartym na chmurze, dzięki czemu można ją uwidocznić za pośrednictwem platformy interfejsu API komunikacji Twilio. Aplikacje są proste do kompilowania i skalowalności. Korzystaj z elastyczności z cenami z płatność zgodnie z rzeczywistym użyciem, aby korzystać z niezawodności chmury.

**Twilio Voice** umożliwia aplikacjom tworzenie i odbieranie połączeń telefonicznych. **TWILIO SMS** umożliwia aplikacjom wysyłanie i odbieranie wiadomości SMS. **Klient Twilio** umożliwia wykonywanie wywołań VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje protokołu WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio i oferty specjalne
Klienci korzystający z platformy Azure otrzymują [specjalną ofertę](https://www.twilio.com/azure): bezpłatny $10 of Twilio kredyt w przypadku uaktualnienia konta Twilio. Ten kredyt Twilio można zastosować do dowolnego Twilioego użycia ($10 kredytu równoważnego do 1 000 wysłania wiadomości SMS lub odebrania do 1000 w ciągu kilku minut, w zależności od lokalizacji numeru telefonu i wiadomości lub miejsca docelowego wywołania). Zrealizuj ten Twilio kredyt i zacznij od [Twilio.com/Azure](https://twilio.com/azure).

Twilio to usługa płatność zgodnie z rzeczywistym użyciem. Nie ma opłat za konfigurację i można zamknąć konto w dowolnym momencie. Więcej szczegółów można znaleźć w [cenniku Twilio](https://www.twilio.com/voice/pricing).

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API Twilio jest interfejsem API RESTful, który zapewnia funkcje głosu i programu SMS dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [biblioteki interfejsu API Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API Twilio są czasownikami Twilio i Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Zlecenia Twilio
Interfejs API korzysta z czasowników Twilio; na przykład czasownik **&lt; powiedzie &gt;** nakazuje Twilio audibly dostarczenie komunikatu w wywołaniu.

Poniżej znajduje się lista zleceń Twilio.  Zapoznaj się z innymi zleceniami i możliwościami za pośrednictwem [dokumentacji języka Twilio Markup](https://www.twilio.com/docs/api/twiml)Language.

* `<Dial>`: Łączy obiekt wywołujący z innym telefonem.
* `<Gather>`: Zbiera cyfry numeryczne wprowadzone na klawiaturze telefonicznej.
* `<Hangup>`: Przerywa wywołanie.
* `<Play>`: Odtwarza plik audio.
* `<Pause>`: Czeka w trybie cichym przez określoną liczbę sekund.
* `<Record>`: Rejestruje głos wywołującego i zwraca adres URL pliku, który zawiera nagranie.
* `<Redirect>`: Przenosi kontrolę nad wywołaniem lub wiadomością SMS do TwiML pod innym adresem URL.
* `<Reject>`: Odrzuca przychodzące wywołanie numeru Twilio bez rozliczeń
* `<Say>`: Konwertuje tekst na mowę, która jest wykonywana w wywołaniu.
* `<Sms>`: Wysyła wiadomość SMS.

### <a name="twiml"></a>TwiML
TwiML to zestaw instrukcji opartych na języku XML opartych na zleceniach Twilio, które informują Twilio o sposobie przetwarzania wywołania lub wiadomości SMS.

Na przykład następujące TwiML spowodują przekonwertowanie tekstu **Hello World** na mowę.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Gdy aplikacja wywołuje interfejs API Twilio, jeden z parametrów interfejsu API jest adresem URL, który zwraca odpowiedź TwiML. W celach programistycznych można użyć adresów URL dostarczonych przez Twilio w celu zapewnienia odpowiedzi TwiML używanych przez aplikacje. Do tworzenia odpowiedzi TwiML można także hostować własne adresy URL, a inna opcja to użycie obiektu **TwiMLResponse** .

Aby uzyskać więcej informacji na temat czasowników Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje o interfejsie API Twilio, zobacz [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Utwórz konto Twilio
Gdy wszystko będzie gotowe do uzyskania konta Twilio, zarejestruj się na [Wypróbie Twilio][try_twilio]. Możesz zacząć korzystać z bezpłatnego konta i później uaktualnić swoje konto.

Po zarejestrowaniu się w celu skorzystania z konta usługi Twilio zostanie wyświetlony Identyfikator konta i token uwierzytelniania. Oba te elementy będą konieczne do wykonywania wywołań interfejsu API Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, Zachowaj bezpieczny token uwierzytelniania. Identyfikator konta i token uwierzytelniania są widoczne na [stronie konto Twilio][twilio_account], odpowiednio w polach **identyfikatory SID konta** i **token uwierzytelniania**.

## <a name="create-an-azure-application"></a><a id="create_app"></a>Tworzenie aplikacji platformy Azure
Aplikacja platformy Azure, która hostuje aplikację z obsługą Twilio, nie różni się od żadnej innej aplikacji platformy Azure. Należy dodać bibliotekę .NET Twilio i skonfigurować rolę do korzystania z bibliotek platformy .NET Twilio.
Aby uzyskać informacje na temat tworzenia początkowego projektu platformy Azure, zobacz [Tworzenie projektu platformy Azure za pomocą programu Visual Studio][vs_project].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurowanie aplikacji do korzystania z bibliotek Twilio
Twilio udostępnia zestaw bibliotek pomocników platformy .NET, które zawijają różne aspekty Twilio, aby zapewnić proste i łatwe w obsłudze interfejsy API REST Twilio i klienta Twilio do generowania odpowiedzi TwiML.

Twilio oferuje pięć bibliotek dla deweloperów platformy .NET:

| Biblioteka | Opis |
| --- | --- |
| Twilio. API | Podstawowa Biblioteka Twilio, która otacza interfejs API REST Twilio w przyjaznej bibliotece platformy .NET. Ta biblioteka jest dostępna dla platform .NET, Silverlight i Windows Phone 7. |
| Twilio. TwiML | Zapewnia przyjazny sposób generowania znaczników TwiML. |
| Twilio. MVC | W przypadku deweloperów korzystających z ASP.NET MVC Ta biblioteka zawiera atrybut TwilioController, TwiML ActionResult i walidacji żądania. |
| Twilio. WebMatrix | W przypadku deweloperów korzystających z bezpłatnego narzędzia deweloperskiego WebMatrix firmy Microsoft Ta biblioteka zawiera składnia Razor pomocników dla różnych akcji Twilio. |
| Twilio. Client. możliwości | Zawiera generator tokenów możliwości do użycia z zestawem SDK JavaScript klienta Twilio. |

> [!Important]
> Wszystkie biblioteki wymagają programu .NET 3,5, Silverlight 4 lub Windows Phone 7 lub nowszego.

Przykłady podane w tym przewodniku wykorzystują bibliotekę Twilio. API.

Biblioteki można [zainstalować przy użyciu rozszerzenia Menedżera pakietów NuGet](https://www.twilio.com/docs/csharp/install) dostępnego dla programu Visual Studio 2010 do 2015.  Kod źródłowy jest hostowany w witrynie [GitHub][twilio_github_repo], która obejmuje witrynę typu wiki, która zawiera kompletną dokumentację dotyczącą używania bibliotek.

Domyślnie Microsoft Visual Studio 2010 instaluje wersję 1,2 programu NuGet. Instalowanie bibliotek Twilio wymaga wersji 1,6 programu NuGet lub nowszej. Aby uzyskać informacje na temat instalowania lub aktualizowania programu NuGet, zobacz [https://nuget.org/][nuget] .

> [!NOTE]
> Aby zainstalować najnowszą wersję programu NuGet, należy najpierw odinstalować załadowanej wersji za pomocą Menedżera rozszerzeń programu Visual Studio. Aby to zrobić, musisz uruchomić program Visual Studio jako administrator. W przeciwnym razie przycisk Odinstaluj jest wyłączony.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Aby dodać biblioteki Twilio do projektu programu Visual Studio:
1. Otwórz rozwiązanie w programie Visual Studio.
2. Kliknij prawym przyciskiem myszy pozycję **odwołania**.
3. Kliknij pozycję **Zarządzaj pakietami NuGet...**
4. Kliknij pozycję **online**.
5. W polu Wyszukaj online wpisz *Twilio*.
6. Kliknij przycisk **Instaluj** w pakiecie Twilio.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Instrukcje: wykonywanie wywołania wychodzącego
Poniżej pokazano, jak wykonać połączenie wychodzące przy użyciu klasy **CallResource** . Ten kod używa również witryny dostarczonej przez Twilio do zwrócenia odpowiedzi Twilio Markup Language (TwiML). Zastąp wartości **do** i **z** numerów telefonów i upewnij się, że przed uruchomieniem kodu sprawdzisz **numer telefonu dla** konta usługi Twilio.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

Aby uzyskać więcej informacji o parametrach przekazaną do metody **CallResource. Create** , zobacz [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Jak wspomniano, ten kod używa witryny dostarczonej przez Twilio do zwrócenia odpowiedzi TwiML. Zamiast tego możesz użyć własnej lokacji, aby zapewnić odpowiedź TwiML. Aby uzyskać więcej informacji, zobacz [How to: dostarczanie odpowiedzi TwiML z własnej witryny sieci Web](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Instrukcje: wysyłanie wiadomości SMS
Poniższy zrzut ekranu przedstawia sposób wysyłania wiadomości SMS przy użyciu klasy **MessageResource**  . Numer **od** jest dostarczany przez Twilio dla kont próbnych do wysyłania wiadomości SMS. Przed uruchomieniem **kodu należy zweryfikować numer konta** usługi Twilio.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Instrukcje: udostępnianie odpowiedzi TwiML z własnej witryny sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API Twilio — na przykład za pomocą metody **CallResource. Create** -Twilio wysyła żądanie do adresu URL, który powinien zwrócić odpowiedź TwiML. Przykład w [instrukcje: nawiązać połączenie wychodzące](#howto_make_call) używa adresu URL podanego przez Twilio [https://twimlets.com/message][twimlet_message_url] do zwrócenia odpowiedzi.

> [!NOTE]
> Gdy TwiML jest przeznaczony do użycia przez usługi sieci Web, można wyświetlić TwiML w przeglądarce. Na przykład kliknij, [https://twimlets.com/message][twimlet_message_url] Aby wyświetlić pusty `<Response>` element; w innym przykładzie kliknij, [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) Aby wyświetlić `<Response>` element, który zawiera &lt; &gt; element powiedz.
>

Zamiast polegać na adresie URL podanej przez Twilio, można utworzyć własną witrynę adresu URL, która zwraca odpowiedzi HTTP. Lokację można utworzyć w dowolnym języku, który zwraca odpowiedzi HTTP. W tym temacie przyjęto założenie, że adres URL jest obsługiwany z procedury obsługi ogólnej ASP.NET.

Poniższy program obsługi ASP.NETa tworzy odpowiedź TwiML, która mówi, że **Hello World** w wywołaniu.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Jak widać w powyższym przykładzie, odpowiedź TwiML jest po prostu dokumentem XML. Biblioteka Twilio. TwiML zawiera klasy, które będą generować TwiML. Poniższy przykład tworzy równoważną odpowiedź, jak pokazano powyżej, ale używa klasy **VoiceResponse** .

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

Aby uzyskać więcej informacji na temat TwiML, zobacz [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml) .

Po skonfigurowaniu sposobu udostępniania odpowiedzi TwiML można przekazać ten adres URL do metody **CallResource. Create** . Na przykład jeśli masz aplikację sieci Web o nazwie MyTwiML wdrożoną w usłudze w chmurze platformy Azure, a nazwa programu obsługi ASP.NET to MyTwiML. ashx, adres URL można przesłać do **CallResource. Create** , jak pokazano w następującym przykładzie kodu:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Aby uzyskać dodatkowe informacje na temat używania programu Twilio na platformie Azure z usługą ASP.NET, zobacz Jak nawiązać [połączenie telefoniczne przy użyciu Twilio w roli sieci Web na platformie Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
