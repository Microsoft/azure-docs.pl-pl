---
title: Jak używać Twilio do komunikacji głosowej i SMS (Python) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS z usługą interfejsu API Twilio na platformie Azure. Przykłady kodu zapisywane w języku Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: b4b9cd0db2a3a99aca80f42b6d69485a542bbadb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580957"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Jak używać Twilio do obsługi głosu i SMS w języku Python
W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne za pomocą usługi interfejsu API Twilio na platformie Azure. Omówione scenariusze obejmują wykonywanie połączeń telefonicznych i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat Twilio i używania programów Voice i SMS w aplikacjach, zobacz sekcję [następne kroki](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Twilio to zaawansowana komunikacja biznesowa, dzięki czemu deweloperzy mogą osadzać głos, VoIP i komunikaty w aplikacjach. Umożliwiają one wirtualizację całej infrastruktury wymaganej w środowisku globalnym opartym na chmurze, dzięki czemu można ją uwidocznić za pośrednictwem platformy interfejsu API komunikacji Twilio. Aplikacje są proste do kompilowania i skalowalności. Korzystaj z elastyczności, aby korzystać z cen z płatność zgodnie z rzeczywistym użyciem, a także skorzystać z niezawodności chmury.

**Twilio Voice** umożliwia aplikacjom tworzenie i odbieranie połączeń telefonicznych.
**TWILIO SMS** umożliwia aplikacji wysyłanie i odbieranie wiadomości SMS.
**Klient Twilio** umożliwia wykonywanie wywołań VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje protokołu WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio i oferty specjalne
Klienci korzystający z platformy Azure otrzymują [specjalną ofertę][special_offer] $10 kredyt Twilio w przypadku uaktualniania konta Twilio. Ten kredyt Twilio można zastosować do dowolnego Twilioego użycia ($10 kredytu równoważnego do 1 000 wysłania wiadomości SMS lub odebrania do 1000 w ciągu kilku minut, w zależności od lokalizacji numeru telefonu i wiadomości lub miejsca docelowego wywołania). Zrealizuj ten [Twilio kredyt][special_offer] i zacznij pracę.

Twilio to usługa płatność zgodnie z rzeczywistym użyciem. Nie ma żadnych opłat skonfigurowanych i możesz zamknąć konto w dowolnym momencie. Więcej szczegółów można znaleźć w [cenniku Twilio][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API Twilio jest interfejsem API RESTful, który zapewnia funkcje głosu i programu SMS dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [biblioteki interfejsu API Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API Twilio są czasownikami Twilio i Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Zlecenia Twilio
Interfejs API korzysta z czasowników Twilio; na przykład czasownik **&lt; powiedzie &gt;** nakazuje Twilio audibly dostarczenie komunikatu w wywołaniu.

Poniżej znajduje się lista zleceń Twilio. Zapoznaj się z innymi zleceniami i możliwościami za pośrednictwem [dokumentacji języka Twilio Markup][twiml]Language.

* **&lt; Wybierz &gt;**: łączy obiekt wywołujący z innym telefonem.
* **&lt; Zbieranie &gt;**: zbiera cyfry liczbowe wprowadzone na klawiaturze telefonicznej.
* Rozłączenie: przerywa wywołanie. **&lt; &gt;**
* **&lt; Wstrzymaj &gt;**: czeka w trybie cichym przez określoną liczbę sekund.
* **&lt; Odtwórz &gt;**: odtwarza plik audio.
* **&lt; Kolejka &gt;**: Dodaj do kolejki obiektów wywołujących.
* **&lt; Rekord &gt;**: rejestruje głos obiektu wywołującego i zwraca adres URL pliku, który zawiera nagranie.
* **&lt; Przekierowanie &gt;**: przenosi kontrolę nad wywołaniem lub wiadomością SMS do TwiML pod innym adresem URL.
* **&lt; Odrzuć &gt;**: odrzuca wywołanie przychodzące do numeru Twilio bez rozliczania.
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

Gdy aplikacja wywołuje interfejs API Twilio, jeden z parametrów interfejsu API jest adresem URL, który zwraca odpowiedź TwiML. W celach programistycznych można użyć adresów URL dostarczonych przez Twilio w celu zapewnienia odpowiedzi TwiML używanych przez aplikacje. Możesz również hostować własne adresy URL, aby generować odpowiedzi TwiML, a inna opcja to użycie `TwiMLResponse` obiektu.

Aby uzyskać więcej informacji na temat czasowników Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje o interfejsie API Twilio, zobacz [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Utwórz konto Twilio
Gdy wszystko będzie gotowe do uzyskania konta Twilio, zarejestruj się na [Wypróbie Twilio][try_twilio]. Możesz zacząć korzystać z bezpłatnego konta i później uaktualnić swoje konto.

Gdy zarejestrujesz się w celu korzystania z konta usługi Twilio, otrzymasz identyfikator SID konta i token uwierzytelniania. Oba te elementy będą konieczne do wykonywania wywołań interfejsu API Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, Zachowaj bezpieczny token uwierzytelniania. Identyfikator SID konta i token uwierzytelniania są widoczne w [konsoli programu Twilio][twilio_console], odpowiednio w polach **identyfikatory SID konta** i **token uwierzytelniania**.

## <a name="create-a-python-application"></a><a id="create_app"></a>Tworzenie aplikacji w języku Python
Aplikacja języka Python korzystająca z usługi Twilio i działająca na platformie Azure nie jest inna niż jakakolwiek inna aplikacja w języku Python, która korzysta z usługi Twilio. Chociaż usługi Twilio są oparte na architekturze REST i mogą być wywoływane z poziomu języka Python na kilka sposobów, ten artykuł koncentruje się na sposobach używania usług Twilio Services z [biblioteką Twilio dla języka Python z usługi GitHub][twilio_python]. Aby uzyskać więcej informacji o korzystaniu z biblioteki Twilio dla języka Python, zobacz [https://www.twilio.com/docs/libraries/python][twilio_lib_docs] .

Najpierw [Skonfiguruj nową maszynę wirtualną platformy Azure z systemem Linux] [azure_vm_setup], aby pełnić rolę hosta dla nowej aplikacji sieci Web w języku Python. Po uruchomieniu maszyny wirtualnej należy uwidocznić aplikację na porcie publicznym, zgodnie z poniższym opisem.

### <a name="add-an-incoming-rule"></a>Dodawanie reguły przychodzącej
  1. Przejdź do strony [Network Security Group] [azure_nsg].
  2. Wybierz grupę zabezpieczeń sieci zgodną z maszyną wirtualną.
  3. Dodaj **regułę wychodzącą** dla **portu 80**. Pamiętaj, aby zezwolić na ruch przychodzący z dowolnego adresu.

### <a name="set-the-dns-name-label"></a>Ustawianie etykiety nazwy DNS
  1. Przejdź do strony [Publiczne adresy IP] [azure_ips].
  2. Wybierz publiczny adres IP, który odnosi się do Twojej maszyny wirtualnej.
  3. W sekcji **Konfiguracja** Ustaw **etykietę nazwa DNS** . W przypadku tego przykładu będzie wyglądać podobnie do tej *etykiety*. centralus.cloudapp.Azure.com

Po nawiązaniu połączenia za pośrednictwem protokołu SSH z maszyną wirtualną można zainstalować dowolnie wybraną platformę sieci Web (dwie najbardziej dobrze znane w języku [Python i](http://flask.pocoo.org/) [Django](https://www.djangoproject.com)). Każdą z nich można zainstalować tylko przez uruchomienie `pip install` polecenia.

Należy pamiętać, że skonfigurowano maszynę wirtualną tak, aby zezwalała na ruch tylko na porcie 80. Upewnij się, że aplikacja ma być skonfigurowana do używania tego portu.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurowanie aplikacji do korzystania z bibliotek Twilio
Aplikację można skonfigurować tak, aby korzystała z biblioteki Twilio dla języka Python na dwa sposoby:

* Zainstaluj bibliotekę Twilio dla języka Python jako pakiet PIP. Można go zainstalować za pomocą następujących poleceń:
   
  `$ pip install twilio`

    — Lub —

* Pobierz bibliotekę Twilio dla języka Python z usługi GitHub ( [https://github.com/twilio/twilio-python][twilio_python] ) i zainstaluj ją w następujący sposób:

  `$ python setup.py install`

Po zainstalowaniu biblioteki Twilio dla języka Python można `import` ją następnie w plikach w języku Python:

  `import twilio`

Aby uzyskać więcej informacji, zobacz [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Instrukcje: wykonywanie wywołania wychodzącego
Poniżej pokazano, jak wykonać połączenie wychodzące. Ten kod używa również witryny dostarczonej przez Twilio do zwrócenia odpowiedzi Twilio Markup Language (TwiML). Przed uruchomieniem kodu Zastąp wartości **from_number** i **to_number** numerów telefonów i upewnij się, że zostałeś zweryfikowany **from_number** numer telefonu dla konta usługi Twilio.

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> Numery telefonów powinny być sformatowane przy użyciu znak "+" i kodu kraju. Na przykład + 16175551212 (format E. 164). Twilio również akceptuje niesformatowane numery US. Na przykład: (415) 555-1212 lub 415-555-1212.

Jak wspomniano, ten kod używa witryny dostarczonej przez Twilio do zwrócenia odpowiedzi TwiML. Zamiast tego możesz użyć własnej lokacji, aby zapewnić odpowiedź TwiML; Aby uzyskać więcej informacji, zobacz artykuł [jak zapewnić odpowiedzi TwiML z własnej witryny sieci Web](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Instrukcje: wysyłanie wiadomości SMS
Poniżej przedstawiono sposób wysyłania wiadomości SMS przy użyciu `TwilioRestClient` klasy. Numer **from_number** jest dostarczany przez Twilio dla kont próbnych do wysyłania wiadomości SMS. Przed uruchomieniem kodu należy zweryfikować numer **to_number** konta usługi Twilio.

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Instrukcje: udostępnianie odpowiedzi TwiML z własnej witryny sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API Twilio, Twilio wyśle żądanie do adresu URL, który powinien zwrócić odpowiedź TwiML. Powyższy przykład używa adresu URL podanego przez Twilio [https://twimlets.com/message][twimlet_message_url] . (Podczas gdy TwiML jest przeznaczony do użytku przez Twilio, można wyświetlić go w przeglądarce. Na przykład kliknij, [https://twimlets.com/message][twimlet_message_url] Aby wyświetlić pusty `<Response>` element; w innym przykładzie kliknij, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] Aby wyświetlić `<Response>` element, który zawiera `<Say>` element.)

Zamiast polegać na adresie URL podanego przez Twilio, można utworzyć własną lokację, która zwraca odpowiedzi HTTP. Lokację można utworzyć w dowolnym języku, który zwraca odpowiedzi XML; w tym temacie założono, że przy użyciu języka Python utworzysz TwiML.

W poniższych przykładach zostanie wyświetlona odpowiedź TwiML z informacją o tym, **Hello World** w wywołaniu.

Z kolbą:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Z Django:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Jak widać w powyższym przykładzie, odpowiedź TwiML jest po prostu dokumentem XML. Biblioteka Twilio dla języka Python zawiera klasy, które wygenerują TwiML. Poniższy przykład tworzy równoważną odpowiedź, jak pokazano powyżej, ale używa `twiml` modułu w bibliotece Twilio dla języka Python:

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

Aby uzyskać więcej informacji na temat TwiML, zobacz [https://www.twilio.com/docs/api/twiml][twiml_reference] .

Po skonfigurowaniu aplikacji w języku Python w celu zapewnienia odpowiedzi TwiML Użyj adresu URL aplikacji jako adresu URL przekazaną do `client.calls.create`  metody. Na przykład jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożoną w usłudze hostowanej na platformie Azure, możesz użyć jej adresu URL jako elementu webhook, jak pokazano w następującym przykładzie:

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Instrukcje: korzystanie z dodatkowych usług Twilio Services
Oprócz przykładów przedstawionych tutaj Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do korzystania z dodatkowych funkcji Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API Twilio][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Następne kroki
Teraz, gdy znasz już podstawy usługi Twilio, Skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń Twilio][twilio_security_guidelines]
* [Twilio prowadnice porady i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu Twilio][twilio_quickstarts]
* [Twilio w serwisie GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną Twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
