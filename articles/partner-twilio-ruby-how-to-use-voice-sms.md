---
title: Jak używać Twilio do komunikacji głosowej i SMS (Ruby) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS z usługą interfejsu API Twilio na platformie Azure. Przykłady kodu zapisywane w języku Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 19372b30a5e56738230216777897c08b07a0a86a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86170704"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Jak używać funkcji Twilio do obsługi głosu i SMS w języku Ruby
W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne za pomocą usługi interfejsu API Twilio na platformie Azure. Omówione scenariusze obejmują wykonywanie połączeń telefonicznych i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat Twilio i używania programów Voice i SMS w aplikacjach, zobacz sekcję [następne kroki](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Twilio to internetowy interfejs API usługi telefonii, który umożliwia używanie istniejących języków i umiejętności sieci Web do tworzenia aplikacji głosowych i SMS. Twilio to usługa innej firmy (nie jest to funkcja platformy Azure, a nie produkt firmy Microsoft).

**Twilio Voice** umożliwia aplikacjom tworzenie i odbieranie połączeń telefonicznych. **Program SMS Twilio** umożliwia aplikacjom wykonywanie i odbieranie wiadomości SMS. **Klient Twilio** umożliwia aplikacjom włączenie komunikacji głosowej przy użyciu istniejących połączeń internetowych, w tym połączeń mobilnych.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio i oferty specjalne
Informacje o cenach Twilio są dostępne w [cenniku Twilio][twilio_pricing]. Klienci korzystający z platformy Azure otrzymują [specjalną ofertę][special_offer]: bezpłatne środki w wysokości 1000 tekstów lub 1000 minut przychodzących. Aby zarejestrować się w tej ofercie lub uzyskać więcej informacji, odwiedź stronę [https://ahoy.twilio.com/azure][special_offer] .  

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API Twilio jest interfejsem API RESTful, który zapewnia funkcje głosu i programu SMS dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [biblioteki interfejsu API Twilio][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML to zestaw instrukcji opartych na języku XML, które informują Twilio o sposobie przetwarzania wywołania lub wiadomości SMS.

Na przykład następujące TwiML spowodują przekonwertowanie tekstu **Hello World** na mowę.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Wszystkie dokumenty TwiML mają `<Response>` jako element główny. W tym miejscu możesz zdefiniować zachowanie aplikacji przy użyciu zleceń Twilio.

### <a name="twiml-verbs"></a><a id="Verbs"></a>Zlecenia TwiML
Zlecenia Twilio są tagami XML, które informują Twilio, co należy **zrobić**. Na przykład czasownik ** &lt; powiedzie &gt; ** nakazuje Twilio audibly dostarczenie komunikatu w wywołaniu. 

Poniżej znajduje się lista zleceń Twilio.

* ** &lt; Wybierz &gt; **: łączy obiekt wywołujący z innym telefonem.
* ** &lt; Zbieranie &gt; **: zbiera cyfry liczbowe wprowadzone na klawiaturze telefonicznej.
* Rozłączenie: przerywa wywołanie. ** &lt; &gt; **
* ** &lt; Odtwórz &gt; **: odtwarza plik audio.
* ** &lt; Wstrzymaj &gt; **: czeka w trybie cichym przez określoną liczbę sekund.
* ** &lt; Rekord &gt; **: rejestruje głos obiektu wywołującego i zwraca adres URL pliku, który zawiera nagranie.
* ** &lt; Przekierowanie &gt; **: przenosi kontrolę nad wywołaniem lub wiadomością SMS do TwiML pod innym adresem URL.
* ** &lt; Odrzuć &gt; **: odrzuca wywołanie przychodzące do numeru Twilio bez rozliczeń
* ** &lt; Załóżmy &gt; **: konwertuje tekst na mowę, która jest wykonywana w wywołaniu.
* ** &lt; SMS &gt; **: wysyła wiadomość SMS.

Aby uzyskać więcej informacji na temat czasowników Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje o interfejsie API Twilio, zobacz [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Utwórz konto Twilio
Gdy wszystko będzie gotowe do uzyskania konta Twilio, zarejestruj się na [Wypróbie Twilio][try_twilio]. Możesz zacząć korzystać z bezpłatnego konta i później uaktualnić swoje konto.

Po zarejestrowaniu się w celu skorzystania z konta usługi Twilio uzyskasz bezpłatny numer telefonu dla swojej aplikacji. Otrzymasz również identyfikator SID konta i token uwierzytelniania. Oba te elementy będą konieczne do wykonywania wywołań interfejsu API Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, Zachowaj bezpieczny token uwierzytelniania. Identyfikator SID konta i token uwierzytelniania są widoczne na [stronie konto Twilio][twilio_account], odpowiednio w polach **identyfikatory SID konta** i **token uwierzytelniania**.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Weryfikuj numery telefonów
Oprócz liczby podanych przez Twilio można także zweryfikować liczby, które kontrolujesz (czyli numer telefonu komórkowego lub domowy) do użycia w aplikacjach. 

Aby uzyskać informacje na temat weryfikowania numeru telefonu, zobacz [Zarządzanie numerami][verify_phone].

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Tworzenie aplikacji języka Ruby
Aplikacja języka Ruby korzystająca z usługi Twilio i działająca na platformie Azure nie jest różna od żadnej innej aplikacji Ruby, która korzysta z usługi Twilio. Chociaż usługi Twilio Services są RESTful i mogą być wywoływane z poziomu języka Ruby na kilka sposobów, ten artykuł koncentruje się na sposobach używania usług Twilio z [biblioteką pomocnika Twilio dla języka Ruby][twilio_ruby].

Najpierw [Skonfiguruj nową maszynę wirtualną z systemem Linux platformy Azure][azure_vm_setup] , która będzie pełnić rolę hosta dla nowej aplikacji sieci Web Ruby. Ignorowanie kroków związanych z tworzeniem aplikacji szyn, po prostu Skonfiguruj maszynę wirtualną. Upewnij się, że tworzysz punkt końcowy z portem zewnętrznym 80 i wewnętrznym portem 5000.

W poniższych przykładach będziemy używać [Sinatra][sinatra], a bardzo prostego środowiska sieci Web dla języka Ruby. Można jednak korzystać z biblioteki pomocnika Twilio dla języka Ruby z innymi platformami sieci Web, w tym z użyciem języka Ruby na szynach.

Użyj protokołu SSH do nowej maszyny wirtualnej i Utwórz katalog dla nowej aplikacji. W tym katalogu Utwórz plik o nazwie Gemfile i skopiuj do niego następujący kod:

```bash
source 'https://rubygems.org'
gem 'sinatra'
gem 'thin'
```

W wierszu polecenia Uruchom polecenie `bundle install` . Spowoduje to zainstalowanie powyższych zależności. Następnie utwórz plik o nazwie `web.rb` . Będzie to miejsce, w którym znajduje się kod aplikacji sieci Web. Wklej do niego następujący kod:

```ruby
require 'sinatra'

get '/' do
    "Hello Monkey!"
end
```

W tym momencie powinno być możliwe uruchomienie polecenia `ruby web.rb -p 5000` . Spowoduje to pomniejszenie poziomu serwera sieci Web na porcie 5000. Aby móc przejść do tej aplikacji w przeglądarce, należy odwiedzić adres URL skonfigurowany dla maszyny wirtualnej platformy Azure. Gdy będziesz mieć dostęp do aplikacji sieci Web w przeglądarce, możesz rozpocząć tworzenie aplikacji Twilio.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Konfigurowanie aplikacji do korzystania z Twilio
Aplikację sieci Web można skonfigurować tak, aby korzystała z biblioteki Twilio przez aktualizację `Gemfile` w celu uwzględnienia tego wiersza:

```bash
gem 'twilio-ruby'
```

W wierszu polecenia Uruchom polecenie `bundle install` . Teraz otwarty `web.rb` i obejmujący ten wiersz u góry:

```ruby
require 'twilio-ruby'
```

Teraz wszystko jest ustawione do korzystania z biblioteki pomocnika Twilio dla języka Ruby w aplikacji sieci Web.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Instrukcje: wykonywanie wywołania wychodzącego
Poniżej pokazano, jak wykonać połączenie wychodzące. Kluczowe koncepcje obejmują używanie biblioteki pomocnika Twilio dla języka Ruby do wykonywania wywołań interfejsu API REST i renderowania TwiML. Zastąp wartości parametrów **od** i **do** numeru telefonu, a następnie upewnij się, że przed uruchomieniem kodu sprawdzisz **numer telefonu dla** konta usługi Twilio.

Dodaj tę funkcję do `web.md` :

```ruby
# Set your account ID and authentication token.
sid = "your_twilio_account_sid";
token = "your_twilio_authentication_token";

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from = "NNNNNNNNNNN";

# The number of the phone receiving call.
to = "NNNNNNNNNNN";

# Use the Twilio-provided site for the TwiML response.
url = "http://yourdomain.cloudapp.net/voice_url";

get '/make_call' do
    # Create the call client.
    client = Twilio::REST::Client.new(sid, token);

    # Make the call
    client.account.calls.create(to: to, from: from, url: url)
end

post '/voice_url' do
    "<Response>
        <Say>Hello Monkey!</Say>
    </Response>"
end
```

W przypadku otwierania `http://yourdomain.cloudapp.net/make_call` w przeglądarce, która wywoła wywołanie interfejsu API Twilio w celu nawiązać połączenia telefonicznego. Pierwsze dwa parametry w `client.account.calls.create` są dość oczywistym objaśnieniem: numer wywołania jest `from` i numer telefonu `to` . 

Trzeci parametr ( `url` ) jest adresem URL, który Twilio żąda instrukcji, co należy zrobić po powiązaniu połączenia. W tym przypadku skonfigurujemy adres URL ( `http://yourdomain.cloudapp.net` ), który zwraca prosty dokument TwiML i używa `<Say>` zlecenia do wykonania niektórych zamiany tekstu na mowę i powiedzieć "Hello małp" osobie otrzymującej wywołanie.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Instrukcje: odbieranie wiadomości SMS
W poprzednim przykładzie zainicjowano **wychodzące** połączenie telefoniczne. Tym razem Użyjmy numeru telefonu, który Twilio nam podczas tworzenia konta, aby przetwarzać **przychodzące** wiadomości SMS.

Najpierw Zaloguj się do [pulpitu nawigacyjnego Twilio][twilio_account]. Kliknij pozycję "liczby" w górnej części nawigacyjnej, a następnie kliknij podany numer Twilio. Zobaczysz dwa adresy URL, które można skonfigurować. Adres URL żądania głosowego i adres URL żądania SMS. Są to adresy URL, które Twilioą wywołania za każdym razem, gdy nastąpi połączenie telefoniczne lub wiadomość SMS jest wysyłana do liczby. Adresy URL są również znane jako "elementy webhook".

Chcemy przetwarzać przychodzące wiadomości SMS, więc zaktualizujmy adres URL do `http://yourdomain.cloudapp.net/sms_url` . Przejdź dalej i kliknij pozycję Zapisz zmiany w dolnej części strony. Teraz, Wróć do `web.rb` programu, aby obsłużyć tę aplikację:

```ruby
post '/sms_url' do
    "<Response>
        <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
    </Response>"
end
```

Po wprowadzeniu zmiany upewnij się, że ponownie uruchomiono aplikację sieci Web. Teraz wykorzystaj swój telefon i Wyślij wiadomość SMS na numer Twilio. Należy natychmiast uzyskać odpowiedź SMS "Hej, Dziękujemy za polecenie ping! Twilio i Azure Rock! ".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Instrukcje: korzystanie z dodatkowych usług Twilio Services
Oprócz przykładów przedstawionych tutaj Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do korzystania z dodatkowych funkcji Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API Twilio][twilio_api_documentation].

### <a name="next-steps"></a><a id="NextSteps"></a>Następne kroki
Teraz, gdy znasz już podstawy usługi Twilio, Skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń Twilio][twilio_security_guidelines]
* [Twilio HowTos i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu Twilio][twilio_quickstarts] 
* [Twilio w serwisie GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
