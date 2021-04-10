---
title: Jak używać programu Twilio for Voice i SMS (Java) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS z usługą interfejsu API Twilio na platformie Azure. Przykłady kodu zapisywane w języku Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: e7ff925534a9cab6523097f84a8ae199e9cb83eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548730"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Jak używać funkcji Twilio do obsługi głosu i programu SMS w języku Java
W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne za pomocą usługi interfejsu API Twilio na platformie Azure. Omówione scenariusze obejmują wykonywanie połączeń telefonicznych i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat Twilio i używania programów Voice i SMS w aplikacjach, zobacz sekcję [następne kroki](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Twilio to internetowy interfejs API usługi telefonii, który umożliwia używanie istniejących języków i umiejętności sieci Web do tworzenia aplikacji głosowych i SMS. Twilio to usługa innej firmy (nie jest to funkcja platformy Azure, a nie produkt firmy Microsoft).

**Twilio Voice** umożliwia aplikacjom tworzenie i odbieranie połączeń telefonicznych. **Program SMS Twilio** umożliwia aplikacjom wykonywanie i odbieranie wiadomości SMS. **Klient Twilio** umożliwia aplikacjom włączenie komunikacji głosowej przy użyciu istniejących połączeń internetowych, w tym połączeń mobilnych.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio i oferty specjalne
Informacje o cenach Twilio są dostępne w [cenniku Twilio][twilio_pricing]. Klienci korzystający z platformy Azure otrzymują [specjalną ofertę][special_offer]: bezpłatne środki w wysokości 1000 tekstów lub 1000 minut przychodzących. Aby zarejestrować się w tej ofercie lub uzyskać więcej informacji, odwiedź stronę [https://ahoy.twilio.com/azure][special_offer] .

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API Twilio jest interfejsem API RESTful, który zapewnia funkcje głosu i programu SMS dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [biblioteki interfejsu API Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API Twilio są czasownikami Twilio i Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Zlecenia Twilio
Interfejs API korzysta z czasowników Twilio; na przykład czasownik **&lt; powiedzie &gt;** nakazuje Twilio audibly dostarczenie komunikatu w wywołaniu.

Poniżej znajduje się lista zleceń Twilio.

* **&lt; Wybierz &gt;**: łączy obiekt wywołujący z innym telefonem.
* **&lt; Zbieranie &gt;**: zbiera cyfry liczbowe wprowadzone na klawiaturze telefonicznej.
* Rozłączenie: przerywa wywołanie. **&lt; &gt;**
* **&lt; Odtwórz &gt;**: odtwarza plik audio.
* **&lt; Kolejka &gt;**: Dodaj do kolejki obiektów wywołujących.
* **&lt; Wstrzymaj &gt;**: czeka w trybie cichym przez określoną liczbę sekund.
* **&lt; Rekord &gt;**: rejestruje głos obiektu wywołującego i zwraca adres URL pliku, który zawiera nagranie.
* **&lt; Przekierowanie &gt;**: przenosi kontrolę nad wywołaniem lub wiadomością SMS do TwiML pod innym adresem URL.
* **&lt; Odrzuć &gt;**: odrzuca wywołanie przychodzące do numeru Twilio bez rozliczania.
* **&lt; Załóżmy &gt;**: konwertuje tekst na mowę, która jest wykonywana w wywołaniu.
* **&lt; SMS &gt;**: wysyła wiadomość SMS.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML to zestaw instrukcji opartych na języku XML opartych na zleceniach Twilio, które informują Twilio o sposobie przetwarzania wywołania lub wiadomości SMS.

Na przykład następujące TwiML spowodują przekonwertowanie tekstu **Hello World!** na mowę.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Gdy aplikacja wywołuje interfejs API Twilio, jeden z parametrów interfejsu API jest adresem URL, który zwraca odpowiedź TwiML. W celach programistycznych można użyć adresów URL dostarczonych przez Twilio w celu zapewnienia odpowiedzi TwiML używanych przez aplikacje. Do tworzenia odpowiedzi TwiML można także hostować własne adresy URL, a inna opcja to użycie obiektu **TwiMLResponse** .

Aby uzyskać więcej informacji na temat czasowników Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje o interfejsie API Twilio, zobacz [TWILIO API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Utwórz konto Twilio
Gdy wszystko będzie gotowe do uzyskania konta Twilio, zarejestruj się na [Wypróbie Twilio][try_twilio]. Możesz zacząć korzystać z bezpłatnego konta i później uaktualnić swoje konto.

Po zarejestrowaniu się w celu skorzystania z konta usługi Twilio zostanie wyświetlony Identyfikator konta i token uwierzytelniania. Oba te elementy będą konieczne do wykonywania wywołań interfejsu API Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, Zachowaj bezpieczny token uwierzytelniania. Identyfikator konta i token uwierzytelniania są widoczne w konsoli programu [Twilio][twilio_console], odpowiednio w polach **identyfikatory SID konta** i **token uwierzytelniania**.

## <a name="create-a-java-application"></a><a id="create_app"></a>Tworzenie aplikacji Java
1. Uzyskaj Twilio JAR i dodaj go do swojej ścieżki kompilacji Java oraz zestawu wdrożeniowego WAR. Na stronie [https://github.com/twilio/twilio-java][twilio_java] można pobrać źródła usługi GitHub i utworzyć własny plik JAR lub pobrać wstępnie utworzony jar (z zależnościami lub bez).
2. Upewnij się, że magazyn kluczy **CACERTS** JDK zawiera certyfikat bezpiecznego urzędu certyfikacji z sygnaturą zabezpieczeń MD5 67: CB: 9D: C0:13:24:8A: 82:9B: B2:17:1E: D1:1b: ec: D4 (numer seryjny to 35: de: F4: CF i odcisk palca SHA1 to D2:32:09: AD: 23: D3:14:23:21:74: E4:0D: 7F: 9D: 62:13:97:86:63:3a). Jest to certyfikat urzędu certyfikacji dla [https://api.twilio.com][twilio_api_service] usługi, który jest wywoływany w przypadku korzystania z interfejsów API Twilio.

Szczegółowe instrukcje dotyczące korzystania z biblioteki klienta Twilio dla języka Java są dostępne podczas naliczania [połączenia telefonicznego przy użyciu Twilio w aplikacji Java na platformie Azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurowanie aplikacji do korzystania z bibliotek Twilio
W kodzie możesz dodać instrukcje **importu** u góry plików źródłowych dla pakietów Twilio lub klas, które mają być używane w aplikacji.

Dla plików źródłowych Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Dla plików źródłowych strony serwera Java (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
```

W zależności od tego, które pakiety lub klasy Twilio mają być używane, instrukcje **importu** mogą się różnić.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Instrukcje: wykonywanie wywołania wychodzącego
Poniżej pokazano, jak wykonać wywołanie wychodzące przy użyciu klasy **call** . Ten kod używa również witryny dostarczonej przez Twilio do zwrócenia odpowiedzi Twilio Markup Language (TwiML). Zastąp wartości parametrów **od** i **do** numeru telefonu, a następnie upewnij się, że przed uruchomieniem kodu sprawdzisz **numer telefonu dla** konta usługi Twilio.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Aby uzyskać więcej informacji o parametrach, które zostały przesłane do metody **call. Creator** , zobacz [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Jak wspomniano, ten kod używa witryny dostarczonej przez Twilio do zwrócenia odpowiedzi TwiML. Zamiast tego możesz użyć własnej lokacji, aby zapewnić odpowiedź TwiML; Aby uzyskać więcej informacji, zobacz [jak zapewnić odpowiedzi TwiML w aplikacji Java na platformie Azure](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Instrukcje: wysyłanie wiadomości SMS
Poniżej przedstawiono sposób wysyłania wiadomości SMS przy użyciu klasy **Message** . Numer **z** **4155992671** jest dostarczany przez Twilio dla kont próbnych do wysyłania wiadomości SMS. Przed uruchomieniem **kodu należy zweryfikować numer konta** usługi Twilio.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Aby uzyskać więcej informacji o parametrach przekazaną do metody **Message. Creator** , zobacz [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms] .

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Instrukcje: udostępnianie odpowiedzi TwiML z własnej witryny sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API Twilio, na przykład za pomocą metody **CallCreator. Create** , Twilio wyśle żądanie do adresu URL, który powinien zwrócić odpowiedź TwiML. Powyższy przykład używa adresu URL podanego przez Twilio [https://twimlets.com/message][twimlet_message_url] . (Podczas gdy TwiML jest przeznaczony do użycia przez usługi sieci Web, można wyświetlić TwiML w przeglądarce. Na przykład kliknij, [https://twimlets.com/message][twimlet_message_url] Aby wyświetlić pusty element **&lt; odpowiedzi &gt;** ; w innym przykładzie kliknij, [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] Aby zobaczyć element **&lt; odpowiedzi &gt;** , który zawiera element **&lt; powiedz &gt;** .).

Zamiast polegać na adresie URL podanej przez Twilio, można utworzyć własną witrynę adresu URL, która zwraca odpowiedzi HTTP. Lokację można utworzyć w dowolnym języku, który zwraca odpowiedzi HTTP; w tym temacie przyjęto założenie, że adres URL będzie przechowywany na stronie JSP.

Na poniższej stronie JSP zostanie wyświetlona **Hello World** odpowiedź TwiML. w wywołaniu.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Na poniższej stronie JSP zostanie wyświetlona odpowiedź TwiML, która wyświetla tekst, ma kilka pauz i zawiera informacje o wersji interfejsu API Twilio i nazwie roli platformy Azure.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

Parametr **ApiVersion** jest dostępny w żądaniach głosu Twilio (nie żądań SMS). Aby wyświetlić dostępne parametry żądania dla żądań Twilio Voice i SMS, zobacz <https://www.twilio.com/docs/api/twiml/twilio_request> odpowiednio i <https://www.twilio.com/docs/api/twiml/sms/twilio_request> . Zmienna środowiskowa **rolename** jest dostępna w ramach wdrożenia platformy Azure. (Jeśli chcesz dodać niestandardowe zmienne środowiskowe, aby mogły być pobierane z programu **System. getenv**, zobacz sekcję zmienne środowiskowe w temacie [różne ustawienia konfiguracji roli][misc_role_config_settings]).

Po skonfigurowaniu strony JSP w celu zapewnienia odpowiedzi TwiML Użyj adresu URL strony JSP jako adresu URL przesłanego do metody **call. Creator** . Na przykład jeśli masz aplikację sieci Web o nazwie MyTwiML wdrożoną w usłudze hostowanej na platformie Azure, a nazwa strony JSP jest mytwiml.jsp, adres URL można przesłać do **wywołania. Creator** , jak pokazano na poniższej liście:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Kolejną opcją reagowania na TwiML jest za pośrednictwem klasy **VoiceResponse** , która jest dostępna w pakiecie **com. Twilio. TwiML** .

Aby uzyskać dodatkowe informacje na temat używania programu Twilio na platformie Azure z językiem Java, zobacz Jak nawiązać [połączenie telefoniczne przy użyciu Twilio w aplikacji Java na platformie Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Instrukcje: korzystanie z dodatkowych usług Twilio Services
Oprócz przykładów przedstawionych tutaj Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do korzystania z dodatkowych funkcji Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API Twilio][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Następne kroki
Teraz, gdy znasz już podstawy usługi Twilio, Skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń Twilio][twilio_security_guidelines]
* [Twilio porady i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu Twilio][twilio_quickstarts]
* [Twilio w serwisie GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
