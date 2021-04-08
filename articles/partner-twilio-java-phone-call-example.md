---
title: Jak nawiązać połączenie telefoniczne z Twilio (Java) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne ze strony sieci Web przy użyciu Twilio w aplikacji Java na platformie Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: fdaa2eeef58599cbff8dbf7daa34f9a30f31b9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548764"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Jak nawiązać połączenie telefoniczne za pomocą Twilio w aplikacji Java na platformie Azure
Poniższy przykład pokazuje, jak można użyć Twilio do wywołania ze strony sieci Web hostowanej na platformie Azure. Aplikacja wynikowa wyświetli monit dla użytkownika o wartości połączeń telefonicznych, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure z użyciem Twilio i języka Java][twilio_java]

Aby użyć kodu w tym temacie, należy wykonać następujące czynności:

1. Uzyskaj konto Twilio i token uwierzytelniania. Aby rozpocząć pracę z usługą Twilio, Oceń ceny pod adresem [https://www.twilio.com/pricing][twilio_pricing] . Możesz zarejestrować się w usłudze [https://www.twilio.com/try-twilio][try_twilio] . Informacje o interfejsie API udostępnianym przez Twilio można znaleźć w temacie [https://www.twilio.com/api][twilio_api] .
2. Uzyskaj Twilio JAR. Na stronie [https://github.com/twilio/twilio-java][twilio_java_github] można pobrać źródła usługi GitHub i utworzyć własny plik JAR lub pobrać wstępnie utworzony jar (z zależnościami lub bez).
   Kod w tym temacie został zapisany przy użyciu wstępnie skompilowanego JAR TwilioJava-3.3.8-with-zależności.
3. Dodaj plik JAR do ścieżki kompilacji Java.
4. Jeśli używasz dyrektywy zaćmienie do utworzenia tej aplikacji Java, Dołącz plik JAR Twilio w pliku wdrożenia aplikacji (WAR) przy użyciu funkcji zestawu wdrożeniowego z przeznaczeniem. Jeśli nie korzystasz z programu Zastąp do tworzenia tej aplikacji Java, upewnij się, że Twilio JAR jest zawarty w tej samej roli platformy Azure co aplikacja Java i dodana do ścieżki klasy aplikacji.
5. Upewnij się, że magazyn kluczy cacerts zawiera certyfikat bezpiecznego urzędu certyfikacji Equifax z sygnaturą zabezpieczeń MD5 67: CB: 9D: C0:13:24:8A: 82:9B: B2:17:1E: D1:1B: EC: D4 (numer seryjny to 35: DE: F4: CF i odcisk palca SHA1 to D2:32:09: AD: 23: D3:14:23:21:74: E4:0D: 7F: 9D: 62:13:97:86:63:3A). Jest to certyfikat urzędu certyfikacji dla [https://api.twilio.com][twilio_api_service] usługi, który jest wywoływany w przypadku korzystania z interfejsów API Twilio.

Zapoznaj się z informacjami na temat [tworzenia aplikacji Hello World przy użyciu Azure Toolkit for Eclipse][azure_java_eclipse_hello_world]lub z innymi technikami do hostowania aplikacji Java na platformie Azure, jeśli nie używasz funkcji przeliczania.

## <a name="create-a-web-form-for-making-a-call"></a>Utwórz formularz sieci Web na potrzeby wywołania
Poniższy kod pokazuje, jak utworzyć formularz sieci Web, aby pobrać dane użytkownika w celu utworzenia wywołania. Na potrzeby tego przykładu utworzono nowy dynamiczny projekt sieci Web o nazwie **TwilioCloud**, a **callform.jsp** został dodany jako plik JSP.

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Automated call form</title>
    </head>
    <body>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <br/>
        <form action="makecall.jsp" method="post">
            <table>
                <tr>
                    <td>To:</td>
                    <td><input type="text" size=50 name="callTo" value="" />
                    </td>
                </tr>
                <tr>
                    <td>From:</td>
                    <td><input type="text" size=50 name="callFrom" value="" />
                    </td>
                </tr>
                <tr>
                    <td>Call message:</td>
                    <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
                    </td>
                </tr>
                <tr>
                    <td colspan=2><input type="submit" value="Make this call" />
                    </td>
                </tr>
            </table>
        </form>
        <br/>
    </body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Utwórz kod, aby wykonać wywołanie
Poniższy kod, który jest wywoływany, gdy użytkownik ukończy formularz wyświetlany przez callform.jsp, tworzy komunikat wywołania i generuje wywołanie. Na potrzeby tego przykładu plik JSP ma nazwę **makecall.jsp** i został dodany do projektu **TwilioCloud** . (Użyj konta Twilio i tokenu uwierzytelniania zamiast wartości zastępczych przypisanych do **accountSID** i **authToken** w poniższym kodzie).

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
import="java.util.*"
import="com.twilio.*"
import="com.twilio.sdk.*"
import="com.twilio.sdk.resource.factory.*"
import="com.twilio.sdk.resource.instance.*"
pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
<%
try 
{
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Instantiate an instance of the Twilio client.     
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to retrieve the CallFactory.
    Account account = client.getAccount();

    // Display the client endpoint. 
    out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

    // Display the API version.
    String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
    out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

    // Retrieve the values entered by the user.
    String callTo = request.getParameter("callTo");  
    // The Outgoing Caller ID, used for the From parameter,
    // must have previously been verified with Twilio.
    String callFrom = request.getParameter("callFrom");
    String userText = request.getParameter("callText");

    // Replace spaces in the user's text with '%20', 
    // to make the text suitable for a URL.
    userText = userText.replace(" ", "%20");

    // Create a URL using the Twilio message and the user-entered text.
    String Url="https://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=" + userText;

    // Display the message URL.
    out.println("<p>");
    out.println("The URL is " + Url);
    out.println("</p>");

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", callFrom);
    params.put("To", callTo);
    params.put("Url", Url);

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);
    out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
} 
catch (TwilioRestException e) 
{
    out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
catch (Exception e) 
{
    out.println("<p>Exception encountered: " + e.getMessage() + "");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
%>
    </body>
</html>
```

Oprócz wykonywania wywołania, makecall.jsp wyświetla punkt końcowy Twilio, wersję interfejsu API i stan wywołania. Przykładem jest następujący zrzut ekranu:

![Odpowiedź na wywołanie platformy Azure przy użyciu Twilio i języka Java][twilio_java_response]

## <a name="run-the-application"></a>Uruchamianie aplikacji
Poniżej przedstawiono ogólne kroki uruchamiania aplikacji; Szczegółowe informacje dotyczące tych kroków można znaleźć podczas [tworzenia aplikacji Hello World przy użyciu Azure Toolkit for Eclipse][azure_java_eclipse_hello_world].

1. Wyeksportuj WAR TwilioCloud do folderu **głównego aplikacji** platformy Azure. 
2. Zmodyfikuj **Start. cmd** , aby rozpakować wojny TwilioCloud.
3. Kompiluj aplikację dla emulatora obliczeń.
4. Rozpocznij wdrażanie w emulatorze obliczeniowym.
5. Otwórz przeglądarkę i uruchom polecenie `http://localhost:8080/TwilioCloud/callform.jsp` .
6. Wprowadź wartości w formularzu, kliknij pozycję **Utwórz to wywołanie**, a następnie Zobacz wyniki w makecall.jsp.

Gdy wszystko będzie gotowe do wdrożenia na platformie Azure, Skompiluj ponownie w celu wdrożenia w chmurze, wdróż ją na platformie Azure i uruchom http://*your_hosted_name*. cloudapp.NET/TwilioCloud/callform.jsp w przeglądarce (Zastąp wartość *your_hosted_name*).

## <a name="next-steps"></a>Następne kroki
Ten kod został dostarczony, aby pokazać podstawowe funkcje przy użyciu programu Twilio w języku Java na platformie Azure. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym warto dodać więcej obsługi błędów lub innych funkcji. Na przykład:

* Zamiast korzystać z formularza sieci Web, można użyć obiektów BLOB usługi Azure Storage lub SQL Database do przechowywania numerów telefonów i wywoływać tekst. Aby uzyskać informacje o korzystaniu z obiektów BLOB usługi Azure Storage w języku Java, zobacz [jak używać usługi BLOB Storage z poziomu języka Java][howto_blob_storage_java]. 
* Można użyć **RoleEnvironment. getConfigurationSettings** , aby pobrać identyfikator konta Twilio i token uwierzytelniania z ustawień konfiguracji wdrożenia, zamiast twardo kodowanie wartości w makecall.jsp. Aby uzyskać informacje na temat klasy **RoleEnvironment** , zobacz [Korzystanie z biblioteki środowiska uruchomieniowego usługi platformy Azure w JSP][azure_runtime_jsp].
* Kod w makecall.jsp przypisuje adres URL podany przez Twilio, [https://twimlets.com/message][twimlet_message_url] do zmiennej **URL** . Ten adres URL zawiera odpowiedź Twilio Markup Language (TwiML), która informuje Twilio, jak kontynuować wywoływanie. Na przykład zwracana TwiML może **&lt; &gt; zawierać czasownik,** który powoduje wypowiadanie tekstu do odbiorcy wywołania. Zamiast używać adresu URL podanego przez Twilio, można utworzyć własną usługę, aby odpowiedzieć na żądanie Twilio; Aby uzyskać więcej informacji, zobacz [jak używać funkcji Twilio na potrzeby obsługi głosu i programu SMS w języku Java][howto_twilio_voice_sms_java]. Więcej informacji na temat TwiML można znaleźć pod adresem [https://www.twilio.com/docs/api/twiml][twiml] i więcej informacji na temat **&lt; &gt; powiedzieć** i innych zleceń Twilio można znaleźć pod adresem [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Zapoznaj się ze wskazówkami dotyczącymi zabezpieczeń Twilio na stronie [https://www.twilio.com/docs/security][twilio_docs_security] .

Aby uzyskać dodatkowe informacje na temat Twilio, zobacz [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Zobacz też
* [Jak używać funkcji Twilio do obsługi głosu i programu SMS w języku Java][howto_twilio_voice_sms_java]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[azure_java_eclipse_hello_world]: /java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: /previous-versions/azure/hh690948(v=azure.100)
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
