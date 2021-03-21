---
title: Jak używać programu Twilio for Voice i SMS (PHP) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS z usługą interfejsu API Twilio na platformie Azure. Przykłady kodu zapisywane w języku PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: bf1ab01b39d594002bc5e677ffe6c3049fbb91ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95521023"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Jak używać funkcji Twilio do obsługi głosu i SMS w języku PHP
W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne za pomocą usługi interfejsu API Twilio na platformie Azure. Omówione scenariusze obejmują wykonywanie połączeń telefonicznych i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat Twilio i używania programów Voice i SMS w aplikacjach, zobacz sekcję [następne kroki](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Twilio to zaawansowana komunikacja biznesowa, dzięki czemu deweloperzy mogą osadzać głos, VoIP i komunikaty w aplikacjach. Umożliwiają one wirtualizację całej infrastruktury wymaganej w środowisku globalnym opartym na chmurze, dzięki czemu można ją uwidocznić za pośrednictwem platformy interfejsu API komunikacji Twilio. Aplikacje są proste do kompilowania i skalowalności. Korzystaj z elastyczności, aby korzystać z cen z płatność zgodnie z rzeczywistym użyciem, a także skorzystać z niezawodności chmury.

**Twilio Voice** umożliwia aplikacjom tworzenie i odbieranie połączeń telefonicznych. **TWILIO SMS** umożliwia aplikacji wysyłanie i odbieranie wiadomości SMS. **Klient Twilio** umożliwia wykonywanie wywołań VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje protokołu WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio i oferty specjalne
Klienci korzystający z platformy Azure otrzymują [specjalną ofertę](https://www.twilio.com/azure): bezpłatny $10 of Twilio kredyt w przypadku uaktualnienia konta Twilio. Ten kredyt Twilio można zastosować do dowolnego Twilioego użycia ($10 kredytu równoważnego do 1 000 wysłania wiadomości SMS lub odebrania do 1000 w ciągu kilku minut, w zależności od lokalizacji numeru telefonu i wiadomości lub miejsca docelowego wywołania). Zrealizuj ten Twilio kredyt i zacznij od: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure) .

Twilio to usługa płatność zgodnie z rzeczywistym użyciem. Nie ma żadnych opłat skonfigurowanych i możesz zamknąć konto w dowolnym momencie. Więcej szczegółów można znaleźć w [cenniku Twilio][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API Twilio jest interfejsem API RESTful, który zapewnia funkcje głosu i programu SMS dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [biblioteki interfejsu API Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API Twilio są czasownikami Twilio i Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Zlecenia Twilio
Interfejs API korzysta z czasowników Twilio; na przykład czasownik **&lt; powiedzie &gt;** nakazuje Twilio audibly dostarczenie komunikatu w wywołaniu.

Poniżej znajduje się lista zleceń Twilio. Zapoznaj się z innymi zleceniami i możliwościami za pośrednictwem [dokumentacji języka Twilio Markup](https://www.twilio.com/docs/api/twiml)Language.

* **&lt; Wybierz &gt;**: łączy obiekt wywołujący z innym telefonem.
* **&lt; Zbieranie &gt;**: zbiera cyfry liczbowe wprowadzone na klawiaturze telefonicznej.
* Rozłączenie: przerywa wywołanie. **&lt; &gt;**
* **&lt; Odtwórz &gt;**: odtwarza plik audio.
* **&lt; Wstrzymaj &gt;**: czeka w trybie cichym przez określoną liczbę sekund.
* **&lt; Rekord &gt;**: rejestruje głos obiektu wywołującego i zwraca adres URL pliku, który zawiera nagranie.
* **&lt; Przekierowanie &gt;**: przenosi kontrolę nad wywołaniem lub wiadomością SMS do TwiML pod innym adresem URL.
* **&lt; Odrzuć &gt;**: odrzuca wywołanie przychodzące do numeru Twilio bez rozliczeń
* **&lt; Załóżmy &gt;**: konwertuje tekst na mowę, która jest wykonywana w wywołaniu.
* **&lt; SMS &gt;**: wysyła wiadomość SMS.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
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

## <a name="create-a-php-application"></a><a id="create_app"></a>Tworzenie aplikacji PHP
Aplikacja PHP korzystająca z usługi Twilio i działająca na platformie Azure nie jest inna niż jakakolwiek inna aplikacja PHP, która korzysta z usługi Twilio. Mimo że usługi Twilio są oparte na architekturze REST i mogą być wywoływane z języka PHP na kilka sposobów, ten artykuł koncentruje się na sposobach używania usług Twilio Services z [biblioteką Twilio for PHP z usługi GitHub][twilio_php]. Aby uzyskać więcej informacji o korzystaniu z biblioteki Twilio dla języka PHP, zobacz [https://www.twilio.com/docs/libraries/php][twilio_lib_docs] .

Szczegółowe instrukcje dotyczące tworzenia i wdrażania aplikacji Twilio/PHP na platformie Azure są dostępne podczas naliczania [połączenia telefonicznego przy użyciu Twilio w aplikacji PHP na platformie Azure][howto_phonecall_php].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurowanie aplikacji do korzystania z bibliotek Twilio
Aplikację można skonfigurować tak, aby korzystała z biblioteki Twilio dla języka PHP na dwa sposoby:

1. Pobierz bibliotekę Twilio dla języka PHP z witryny GitHub ( [https://github.com/twilio/twilio-php][twilio_php] ) i Dodaj katalog **usług** do swojej aplikacji.
   
    — Lub —
2. Zainstaluj bibliotekę Twilio dla języka PHP jako pakiet GRUSZ. Można go zainstalować za pomocą następujących poleceń:

    ```bash
    $ pear channel-discover twilio.github.com/pear
    $ pear install twilio/Services_Twilio
    ```

Po zainstalowaniu biblioteki Twilio dla języka PHP można dodać instrukcję **require_once** w górnej części plików php, aby odwołać się do biblioteki:

```php
require_once 'Services/Twilio.php';
```

Aby uzyskać więcej informacji, zobacz [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Instrukcje: wykonywanie wywołania wychodzącego
Poniżej pokazano, jak wykonać połączenie wychodzące przy użyciu klasy **Services_Twilio** . Ten kod używa również witryny dostarczonej przez Twilio do zwrócenia odpowiedzi Twilio Markup Language (TwiML). Zastąp wartości parametrów **od** i **do** numeru telefonu, a następnie upewnij się, że przed uruchomieniem kodu sprawdzisz **numer telefonu dla** konta usługi Twilio.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";

// The number of the phone initiating the call.
$from_number = "NNNNNNNNNNN";

// The number of the phone receiving call.
$to_number = "NNNNNNNNNNN";

// Use the Twilio-provided site for the TwiML response.
$url = "https://twimlets.com/message";

// The phone message text.
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

//Make the call.
try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Jak wspomniano, ten kod używa witryny dostarczonej przez Twilio do zwrócenia odpowiedzi TwiML. Zamiast tego możesz użyć własnej lokacji, aby zapewnić odpowiedź TwiML; Aby uzyskać więcej informacji, zobacz artykuł [jak zapewnić odpowiedzi TwiML z własnej witryny sieci Web](#howto_provide_twiml_responses).

* **Uwaga**: Aby rozwiązać problemy z błędami walidacji certyfikatu TLS/SSL, zobacz [https://www.twilio.com/docs/api/errors][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Instrukcje: wysyłanie wiadomości SMS
Poniżej przedstawiono sposób wysyłania wiadomości SMS przy użyciu klasy **Services_Twilio** . Numer **od** jest dostarczany przez Twilio dla kont próbnych do wysyłania wiadomości SMS. Przed uruchomieniem **kodu należy zweryfikować numer konta** usługi Twilio.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";


$from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
$to_number = "NNNNNNNNNNN";
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

// Send the SMS message.
try
{
    $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Instrukcje: udostępnianie odpowiedzi TwiML z własnej witryny sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API Twilio, Twilio wyśle żądanie do adresu URL, który powinien zwrócić odpowiedź TwiML. Powyższy przykład używa adresu URL podanego przez Twilio [https://twimlets.com/message][twimlet_message_url] . (Podczas gdy TwiML jest przeznaczony do użytku przez Twilio, można wyświetlić je w przeglądarce. Na przykład kliknij, [https://twimlets.com/message][twimlet_message_url] Aby wyświetlić pusty `<Response>` element; w innym przykładzie kliknij, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] Aby wyświetlić `<Response>` element, który zawiera `<Say>` element.)

Zamiast polegać na adresie URL podanego przez Twilio, można utworzyć własną lokację, która zwraca odpowiedzi HTTP. Lokację można utworzyć w dowolnym języku, który zwraca odpowiedzi XML; w tym temacie założono, że używasz języka PHP do tworzenia TwiML.

Na poniższej stronie PHP zostanie wyświetlona odpowiedź TwiML, która informuje o **Hello World** w wywołaniu.

```xml
<?php    
    header("content-type: text/xml");    
    echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
?>
<Response>    
    <Say>Hello world.</Say>
</Response>
```

Jak widać w powyższym przykładzie, odpowiedź TwiML jest po prostu dokumentem XML. Biblioteka Twilio dla języka PHP zawiera klasy, które wygenerują TwiML. Poniższy przykład tworzy równoważną odpowiedź, jak pokazano powyżej, ale używa klasy **Services \_ Twilio \_ Twiml** w bibliotece Twilio dla języka PHP:

```php
require_once('Services/Twilio.php');

$response = new Services_Twilio_Twiml();
$response->say("Hello world.");
print $response;
```

Aby uzyskać więcej informacji na temat TwiML, zobacz [https://www.twilio.com/docs/api/twiml][twiml_reference] . 

Po skonfigurowaniu strony PHP do udostępniania odpowiedzi TwiML Użyj adresu URL strony PHP jako adresu URL przekazaną w  `Services_Twilio->account->calls->create`  metodzie. Na przykład jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożoną dla usługi hostowanej na platformie Azure, a nazwa strony PHP to **MyTwiML. php**, adres URL można przesłać do  **konta Services_Twilio->Account->Calls->Create**  , jak pokazano w następującym przykładzie:

```php
require_once 'Services/Twilio.php';

$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";
$from_number = "NNNNNNNNNNN";
$to_number = "NNNNNNNNNNN";
$url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

// The phone message text.
$message = "Hello world.";

$client = new Services_Twilio($sid, $token, "2010-04-01");

try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Aby uzyskać dodatkowe informacje na temat używania Twilio na platformie Azure z językiem PHP, zobacz Jak nawiązać [połączenie telefoniczne przy użyciu Twilio w aplikacji PHP na platformie Azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Instrukcje: korzystanie z dodatkowych usług Twilio Services
Oprócz przykładów przedstawionych tutaj Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do korzystania z dodatkowych funkcji Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API Twilio][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Następne kroki
Teraz, gdy znasz już podstawy usługi Twilio, Skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń Twilio][twilio_security_guidelines]
* [Twilio porady i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu Twilio][twilio_quickstarts] 
* [Twilio w serwisie GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart