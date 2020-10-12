---
title: Korzystanie z programu Twilio do obsługi wiadomości głosowych, VoIP i SMS na platformie Azure
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS z usługą interfejsu API Twilio na platformie Azure. Przykłady kodu zapisywane w Node.js.
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: c4b1c506ff0e78e5e304ceb1682c73f9cd784c95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080545"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Korzystanie z programu Twilio do obsługi wiadomości głosowych, VoIP i SMS na platformie Azure
W tym przewodniku pokazano, jak tworzyć aplikacje, które komunikują się z Twilio i node.js na platformie Azure.

<a name="whatis"></a>

## <a name="what-is-twilio"></a>Co to jest Twilio?
Twilio to platforma interfejsu API, która ułatwia deweloperom tworzenie i odbieranie połączeń telefonicznych, wysyłanie i odbieranie wiadomości SMS oraz osadzanie połączeń VoIP w aplikacjach mobilnych opartych na przeglądarce i natywnych. Zacznijmy od tego, jak to działa przed nakazaniem.

### <a name="receiving-calls-and-text-messages"></a>Otrzymywanie wywołań i wiadomości SMS
Twilio umożliwia deweloperom [kupowanie programowalnych numerów telefonów][purchase_phone] , które mogą być używane do wysyłania i odbierania połączeń oraz wiadomości tekstowych. Gdy numer Twilio odbiera wywołanie przychodzące lub tekst, Twilio wyśle aplikację sieci Web, wysyłając żądanie HTTP POST lub GET, z prośbą o instrukcje dotyczące obsługi wywołania lub tekstu. Serwer odpowie na żądanie HTTP Twilio z [TwiML][twiml], prostym zestawem tagów XML zawierającym instrukcje dotyczące obsługi wywołania lub tekstu. W tej chwili zobaczymy przykłady TwiML.

### <a name="making-calls-and-sending-text-messages"></a>Wykonywanie wywołań i wysyłanie wiadomości SMS
Wykonując żądania HTTP do interfejsu API usługi sieci Web Twilio, deweloperzy mogą wysyłać wiadomości SMS lub inicjować wychodzące rozmowy telefoniczne. Dla połączeń wychodzących Deweloper musi także określić adres URL, który zwraca instrukcje TwiML, jak obsłużyć połączenie wychodzące po powiązaniu.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Osadzanie możliwości VoIP w kodzie interfejsu użytkownika (JavaScript, iOS lub Android)
Twilio udostępnia zestaw SDK po stronie klienta, który umożliwia włączenie dowolnej przeglądarki sieci Web na komputerze stacjonarnym, aplikacji dla systemu iOS lub aplikacji systemu Android do telefonu VoIP. W tym artykule opisano, jak używać technologii VoIP wywołującej w przeglądarce. Oprócz *zestawu SDK Twilio języka JavaScript* działającego w przeglądarce, aplikacja po stronie serwera (aplikacja node.js) musi być używana do wydawania "tokenu możliwości" klientowi JavaScript. Więcej informacji na temat korzystania z usługi VoIP z usługą node.js można znaleźć [na blogu dotyczącym deweloperów Twilio][voipnode].

<a name="signup"></a>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Rejestracja w usłudze Twilio (rabat firmy Microsoft)
Przed rozpoczęciem korzystania z usług Twilio należy najpierw [zarejestrować się w celu utworzenia konta][signup]. Microsoft Azure klienci otrzymują specjalne zniżki — [Pamiętaj o tym, aby zarejestrować się w tym miejscu][signup].

<a name="azuresite"></a>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Tworzenie i wdrażanie witryny sieci Web node.js Azure
Następnie musisz utworzyć node.js witrynę sieci Web działającą na platformie Azure. [Oficjalna dokumentacja tego elementu][azure_new_site]znajduje się tutaj. Na wysokim poziomie można wykonać następujące czynności:

* Zarejestrowanie się w celu korzystania z konta platformy Azure, jeśli go nie masz
* Tworzenie nowej witryny sieci Web za pomocą konsoli administracyjnej platformy Azure
* Dodawanie obsługi kontroli źródła (przyjęto założenie, że korzystasz z narzędzia Git)
* Tworzenie pliku `server.js` z prostą aplikacją sieci web node.js
* Wdrażanie tej prostej aplikacji na platformie Azure

<a name="twiliomodule"></a>

## <a name="configure-the-twilio-module"></a>Konfigurowanie modułu Twilio
Następnie zaczniemy pisać prostą node.js aplikację, która korzysta z interfejsu API Twilio. Przed rozpoczęciem należy skonfigurować nasze poświadczenia konta Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurowanie poświadczeń Twilio w zmiennych środowiskowych systemu
Aby żądania były uwierzytelniane na zapleczu Twilio, potrzebujemy identyfikatora SID konta i tokenu uwierzytelniania, który działa jako nazwa użytkownika i hasło ustawione dla konta Twilio. Najbezpieczniejszym sposobem konfigurowania tych funkcji do użycia z modułem węzła na platformie Azure jest systemowe zmienne środowiskowe, które można ustawić bezpośrednio w konsoli administracyjnej platformy Azure.

Wybierz witrynę sieci Web node.js i kliknij link "Konfiguruj".  Jeśli przewiniesz bit w dół, zobaczysz obszar, w którym można ustawić właściwości konfiguracji dla aplikacji.  Wprowadź swoje poświadczenia konta Twilio ([znajdujące się w konsoli Twilio][twilio_console]), jak pokazano na ekranie — upewnij się, że nazwy są odpowiednie `TWILIO_ACCOUNT_SID` i `TWILIO_AUTH_TOKEN` odpowiednio:

![Konsola administracyjna platformy Azure][azure-admin-console]

Po skonfigurowaniu tych zmiennych należy ponownie uruchomić aplikację w konsoli platformy Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarowanie modułu Twilio w package.jsna
Następnie należy utworzyć package.jsna potrzeby zarządzania naszymi zależnościami modułów węzła za pośrednictwem [npm]. Na tym samym poziomie, co `server.js` plik utworzony w samouczku *platformy Azure/node.js* , Utwórz plik o nazwie `package.json` .  W tym pliku Umieść następujące elementy:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

To deklaruje moduł Twilio jako zależność, a także popularne [środowisko sieci Web Express][express] i aparat szablonów EJS.  Teraz wszystko jest gotowe. Napiszmy jakiś kod!

<a name="makecall"></a>

## <a name="make-an-outbound-call"></a>Utwórz wywołanie wychodzące
Utwórzmy prosty formularz, który będzie nawiązać połączenie z wybranym przez nas liczbą. Otwórz `server.js` program i wprowadź następujący kod. Zwróć uwagę na to, gdzie mówi "CHANGE_ME" — Wprowadź nazwę witryny sieci Web platformy Azure:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Następnie Utwórz katalog o nazwie w `views` tym katalogu, Utwórz plik o nazwie `index.ejs` z następującą zawartością:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Teraz Wdróż swoją witrynę sieci Web na platformie Azure i Otwórz stronę główną. W polu tekstowym powinno być możliwe wprowadzanie numeru telefonu i otrzymywanie wywołania z numeru Twilio!

<a name="sendmessage"></a>

## <a name="send-an-sms-message"></a>Wyślij wiadomość SMS
Teraz Skonfigurujmy interfejs użytkownika i logikę obsługi formularzy w celu wysłania wiadomości SMS. Otwórz `server.js` i Dodaj następujący kod po ostatnim wywołaniu `app.post` :

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

W programie `views/index.ejs` Dodaj kolejną formę poniżej pierwszej, aby przesłać numer i wiadomość tekstową:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Wdróż ponownie aplikację na platformie Azure, a teraz powinna być w stanie przesłać ten formularz i wysłać do siebie wiadomość SMS.

<a name="nextsteps"></a>

## <a name="next-steps"></a>Następne kroki
Znasz już podstawy używania node.js i Twilio do tworzenia aplikacji, które komunikują się. Jednak te przykłady stanowią jedynie ułameką powierzchnię możliwości z Twilio i node.js. Aby uzyskać więcej informacji na temat korzystania z programu Twilio z node.js, zapoznaj się z następującymi zasobami:

* [Dokumenty oficjalnych modułów][docs]
* [Samouczek dotyczący technologii VoIP z aplikacjami node.js][voipnode]
* [Votr — aplikacja do głosowania SMS w czasie rzeczywistym z node.js i CouchDB (trzy części)][votr]
* [Parowanie programowania w przeglądarce za pomocą node.js][pair]

Mamy nadzieję, że lubisz działanie hakerskie node.js i Twilio na platformie Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/quickstart-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
