---
title: Jak korzystać z usługi poczty e-mail SendGrid (PHP) | Microsoft Docs
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi poczty e-mail SendGrid na platformie Azure. Przykłady kodu zapisywane w języku PHP.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: b3a9fee09d1eac6fb4d716af83c348cb2c21f7a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023792"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Jak używać usługi poczty E-mail SendGrid w języku PHP

W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne przy użyciu usługi poczty e-mail SendGrid na platformie Azure. Przykłady są zapisywane w języku PHP.
Omówione scenariusze obejmują **konstruowanie poczty e-mail**, **wysyłanie wiadomości e-mail** i **Dodawanie załączników**. Aby uzyskać więcej informacji na temat SendGrid i wysyłania wiadomości e-mail, zobacz sekcję [następne kroki](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa poczty E-mail SendGrid?
SendGrid to [oparta na chmurze usługa poczty e-mail] , która zapewnia niezawodne [dostarczanie transakcyjnych wiadomości e-mail], skalowalność i analizę w czasie rzeczywistym oraz elastyczne interfejsy API, które ułatwiają integrację niestandardową. Typowe scenariusze użycia SendGrid obejmują:

* Automatyczne wysyłanie pokwitowań do klientów
* Administrowanie listami dystrybucyjnymi na potrzeby wysyłania klientów do comiesięcznych ofert e-Fliers i specjalnych
* Zbieranie metryk w czasie rzeczywistym dla elementów, takich jak zablokowane wiadomości e-mail i czas odpowiedzi klienta
* Generowanie raportów ułatwiających identyfikowanie trendów
* Przekazywanie zapytań klienta
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Korzystanie z SendGrid z aplikacji PHP

Korzystanie z SendGrid w aplikacji PHP platformy Azure nie wymaga specjalnej konfiguracji ani kodowania. Ponieważ SendGrid jest usługą, można uzyskać do niej dostęp w taki sam sposób, jak w przypadku aplikacji w chmurze, co może być spowodowane przez aplikację lokalną.

## <a name="how-to-send-an-email"></a>Instrukcje: wysyłanie wiadomości E-mail

Możesz wysyłać wiadomości e-mail przy użyciu protokołu SMTP lub internetowego interfejsu API dostarczonego przez SendGrid.

### <a name="smtp-api"></a>INTERFEJS API SMTP

Aby wysłać wiadomość e-mail przy użyciu interfejsu API SendGrid SMTP, użyj *poczty SWIFT*, która jest biblioteką opartą na składniku do wysyłania wiadomości e-mail z aplikacji php. Możesz pobrać program [SWIFT 5.3.0 Library](https://swiftmailer.symfony.com/) v (Użyj programu [Composer] , aby zainstalować SWIFT poczty). Wysyłanie wiadomości e-mail z biblioteką obejmuje tworzenie wystąpień `Swift\_SmtpTransport` klas, `Swift\_Mailer` , i `Swift\_Message` , Ustawianie odpowiednich właściwości i wywoływanie `Swift\_Mailer::send` metody.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Interfejs API sieci Web
Użyj [funkcji zwinięcie][curl function] języka PHP do wysyłania wiadomości e-mail przy użyciu interfejsu API sieci Web SendGrid.

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

Interfejs API sieci Web SendGrid jest bardzo podobny do interfejsu API REST, ale nie jest on naprawdę interfejsem API RESTful, ponieważ w większości wywołań można używać zarówno zleceń GET i POST.

## <a name="how-to-add-an-attachment"></a>Instrukcje: Dodawanie załącznika

### <a name="smtp-api"></a>INTERFEJS API SMTP

Wysyłanie załącznika przy użyciu interfejsu API SMTP obejmuje jeden dodatkowy wiersz kodu do przykładowego skryptu służącego do wysyłania wiadomości e-mail przy użyciu poczty Swift.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

Dodatkowy wiersz kodu jest następujący:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Ten wiersz kodu wywołuje metodę Attach dla `Swift\_Message` obiektu i używa metody statycznej klasy w `fromPath` `Swift\_Attachment` celu pobrania i dołączenia pliku do wiadomości.

### <a name="web-api"></a>Interfejs API sieci Web

Wysyłanie załączników przy użyciu interfejsu API sieci Web jest bardzo podobne do wysyłania wiadomości e-mail przy użyciu internetowego interfejsu API. Należy jednak zauważyć, że w poniższym przykładzie tablica parametrów musi zawierać ten element:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Przykład

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Instrukcje: Używanie filtrów do włączania stopek, śledzenia i analizy

Program SendGrid oferuje dodatkowe funkcje poczty e-mail za pośrednictwem *filtrów*. Są to ustawienia, które można dodać do wiadomości e-mail w celu włączenia określonych funkcji, takich jak włączenie śledzenia, Google Analytics, śledzenie subskrypcji itd.

Filtry można zastosować do wiadomości za pomocą właściwości filters. Każdy filtr jest określony przez skrót zawierający ustawienia specyficzne dla filtru. Poniższy przykład włącza filtr stopki i określa wiadomość tekstową, która będzie dołączana do dolnej części wiadomości e-mail. Na potrzeby tego przykładu zostanie użyta [Biblioteka SendGrid-php].

Użyj programu [Composer] do instalacji biblioteki:

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Przykład  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy usługi poczty E-mail SendGrid, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Dokumentacja SendGrid: <https://sendgrid.com/docs>
* Biblioteka PHP SendGrid: <https://github.com/sendgrid/sendgrid-php>
* Oferta Specjalna SendGrid dla klientów platformy Azure: <https://sendgrid.com/windowsazure.html>

Aby uzyskać więcej informacji, zobacz również [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[usługa poczty e-mail oparta na chmurze]: https://sendgrid.com/email-solutions
[dostarczanie transakcyjnych wiadomości e-mail]: https://sendgrid.com/transactional-email
[SendGrid — Biblioteka php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
Program [Composer]: https://getcomposer.org/download/
