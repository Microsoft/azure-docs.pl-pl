---
title: Jak nawiązać połączenie telefoniczne z Twilio (PHP) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS z usługą interfejsu API Twilio na platformie Azure. Przykłady dla aplikacji PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: ef0642b26898249d78f5103e8a8f80198887bed9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542528"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Jak nawiązać połączenie telefoniczne za pomocą Twilio w aplikacji PHP na platformie Azure
Poniższy przykład pokazuje, jak można użyć Twilio, aby wykonać wywołanie ze strony sieci Web w języku PHP hostowanej na platformie Azure. Aplikacja wynikowa wyświetli monit dla użytkownika o wartości połączeń telefonicznych, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure z użyciem Twilio i PHP][twilio_php]

Aby użyć kodu w tym temacie, należy wykonać następujące czynności:

1. Uzyskaj konto Twilio i token uwierzytelniania z [konsoli Twilio][twilio_console]. Aby rozpocząć pracę z usługą Twilio, Oceń ceny pod adresem [https://www.twilio.com/pricing][twilio_pricing] . Możesz zarejestrować się w celu uzyskania konta w wersji próbnej pod adresem [https://www.twilio.com/try-twilio][try_twilio] .
2. Uzyskaj [bibliotekę Twilio dla języka PHP](https://github.com/twilio/twilio-php) lub zainstaluj ją jako pakiet języka gruszkowego. Aby uzyskać więcej informacji, zobacz [plik Readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Zainstaluj zestaw Azure SDK dla języka PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](./app-service/quickstart-php.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Utwórz formularz sieci Web na potrzeby wywołania
Poniższy kod HTML pokazuje, jak utworzyć stronę sieci Web (**callform.html**), która pobiera dane użytkownika w celu utworzenia wywołania:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Utwórz kod, aby wykonać wywołanie
Poniższy kod przedstawia sposób kompilowania **MakeCall. php**, który jest wywoływany, gdy użytkownik prześle formularz wyświetlany przez **callform.html**. Kod przedstawiony poniżej tworzy komunikat wywołania i generuje wywołanie. Upewnij się również, że używasz konta Twilio i tokenu uwierzytelniania z poziomu [konsoli Twilio][twilio_console] zamiast wartości zastępczych przypisanych do **$SID** i **$token** w kodzie poniżej.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Oprócz tworzenia wywołania **MakeCall. php** wyświetla niektóre metadane wywołań, jak pokazano na poniższej ilustracji. Aby uzyskać więcej informacji na temat metadanych wywołań, zobacz [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties] .

![Odpowiedź na wywołanie platformy Azure przy użyciu Twilio i języka PHP][twilio_php_response]

## <a name="run-the-application"></a>Uruchamianie aplikacji
Następnym krokiem jest [wdrożenie aplikacji na platformie Azure Web Apps przy użyciu usługi git](app-service/quickstart-php.md) (chociaż nie wszystkie informacje są istotne). 

## <a name="next-steps"></a>Następne kroki
Ten kod został dostarczony, aby pokazać podstawowe funkcje przy użyciu Twilio w języku PHP na platformie Azure. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym warto dodać więcej obsługi błędów lub innych funkcji. Na przykład:

* Zamiast korzystać z formularza sieci Web, można użyć obiektów BLOB usługi Azure Storage lub SQL Database do przechowywania numerów telefonów i wywoływać tekst. Aby uzyskać informacje o korzystaniu z obiektów BLOB usługi Azure Storage w języku PHP, zobacz [Korzystanie z usługi Azure Storage z aplikacjami php][howto_blob_storage_php]. Aby uzyskać informacje o używaniu SQL Database w języku PHP, zobacz [używanie SQL Database z aplikacjami php][howto_sql_azure_php].
* Kod **MakeCall. php** używa adresu URL podanego przez Twilio ( [https://twimlets.com/message][twimlet_message_url] ) w celu udostępnienia odpowiedzi Twilio Markup Language (TwiML), która informuje, jak kontynuować wywołanie. Na przykład zwracana TwiML może zawierać `<Say>` czasownik, który powoduje wypowiadanie tekstu do odbiorcy wywołania. Zamiast używać adresu URL podanego przez Twilio, można utworzyć własną usługę, aby odpowiedzieć na żądanie Twilio; Aby uzyskać więcej informacji, zobacz [jak używać Twilio do obsługi głosu i programu SMS w języku PHP][howto_twilio_voice_sms_php]. Więcej informacji na temat TwiML można znaleźć pod adresem [https://www.twilio.com/docs/api/twiml][twiml] i więcej informacji `<Say>` na temat i innych zleceń Twilio można znaleźć pod adresem [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Zapoznaj się ze wskazówkami dotyczącymi zabezpieczeń Twilio na stronie [https://www.twilio.com/docs/security][twilio_docs_security] .

Aby uzyskać dodatkowe informacje na temat Twilio, zobacz [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Zobacz też
* [Jak używać funkcji Twilio do obsługi głosu i SMS w języku PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: ./storage/blobs/storage-quickstart-blobs-php.md
[howto_sql_azure_php]: ./azure-sql/database/connect-query-content-reference-guide.md
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php