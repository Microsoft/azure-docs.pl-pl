---
title: Store-SendGrid-Java-How-to-Send-email — przykład
description: Wyślij wiadomość e-mail przy użyciu programu SendGrid z języka Java w ramach wdrożenia platformy Azure. Aplikacja będąca wynikiem będzie monitował użytkownika o wartości poczty e-mail.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.custom: devx-track-java
ms.openlocfilehash: 7a857b887b53ccb2288507742ca52d72d90ff548
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214358"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Jak wysyłać wiadomości E-mail przy użyciu programu SendGrid z języka Java w ramach wdrożenia platformy Azure
W poniższym przykładzie pokazano, jak za pomocą SendGrid wysyłać wiadomości e-mail ze strony sieci Web hostowanej na platformie Azure. Aplikacja wynikowa wyświetli monit o podanie wartości poczty e-mail, jak pokazano na poniższym zrzucie ekranu.

![Formularz wiadomości e-mail][emailform]

Uzyskana wiadomość e-mail będzie wyglądać podobnie do poniższego zrzutu ekranu.

![Wiadomość e-mail][emailsent]

Aby użyć kodu w tym temacie, należy wykonać następujące czynności:

1. Pobierz javax. mail JARs, na przykład z <https://www.oracle.com/technetwork/java/javamail/index.html> .
2. Dodaj JARs do ścieżki kompilacji Java.
3. Jeśli używasz dyrektywy zaćmienie do utworzenia tej aplikacji Java, możesz dołączyć biblioteki SendGrid w pliku wdrożenia aplikacji (WAR) przy użyciu funkcji z zestawu wdrożeniowego z przeznaczeniem. Jeśli nie korzystasz z programu zaćmienie w celu utworzenia tej aplikacji Java, upewnij się, że biblioteki są zawarte w tej samej roli platformy Azure jako aplikacja Java, a następnie dodane do ścieżki klasy aplikacji.

Musisz również mieć własną nazwę użytkownika SendGrid i hasło, aby móc wysyłać wiadomości e-mail. Aby rozpocząć pracę z usługą SendGrid, zobacz [jak wysyłać wiadomości e-mail przy użyciu usługi SendGrid w języku Java](store-sendgrid-java-how-to-send-email.md).

Ponadto znajomość informacji na temat [tworzenia Hello World aplikacji dla platformy Azure w ramach Przezaćmienia](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)lub z innymi technikami do hostowania aplikacji Java na platformie Azure, jeśli nie używasz programu zaćmienie, jest zdecydowanie zalecane.

## <a name="create-a-web-form-for-sending-email"></a>Utwórz formularz sieci Web na potrzeby wysyłania wiadomości e-mail
Poniższy kod pokazuje, jak utworzyć formularz sieci Web, aby pobrać dane użytkownika na potrzeby wysyłania wiadomości e-mail. Do celów tej zawartości plik JSP ma nazwę **emailform.jsp**.

```html
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Email form</title>
</head>
<body>
  <p>Fill in all fields and click <b>Send this email</b>.</p>
  <br/>
  <form action="sendemail.jsp" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input type="text" size=50 name="emailTo">
        </td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input type="text" size=50 name="emailFrom">
        </td>
      </tr>
      <tr>
        <td>Subject:</td>
        <td><input type="text" size=100 name="emailSubject" value="My email subject">
        </td>
      </tr>
      <tr>
        <td>Text:</td>
        <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
        </td>
      </tr>
      <tr>
        <td>SendGrid user name:</td>
        <td><input type="text" name="sendGridUser">
        </td>
      </tr>
      <tr>
        <td>SendGrid password:</td>
        <td><input type="password" name="sendGridPassword">
        </td>
      </tr>
      <tr>
        <td colspan=2><input type="submit" value="Send this email">
        </td>
      </tr>
    </table>
  </form>
  <br/>
</body>
</html>
```

## <a name="create-the-code-to-send-the-email"></a>Utwórz kod, aby wysłać wiadomość e-mail
Poniższy kod, który jest wywoływany po zakończeniu formularza w emailform.jsp, tworzy wiadomość e-mail i wysyła ją. Do celów tej zawartości plik JSP ma nazwę **sendemail.jsp**.

```java
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Email processing happens here</title>
</head>
<body>
    <b>This is my send mail page.</b><p/>
  <%

  final String sendGridUser = request.getParameter("sendGridUser");
  final String sendGridPassword = request.getParameter("sendGridPassword");

  class SMTPAuthenticator extends Authenticator
  {
    public PasswordAuthentication getPasswordAuthentication()
    {
        String username = sendGridUser;
        String password = sendGridPassword;

        return new PasswordAuthentication(username, password);   
    }
  }
  try
  {

      // The SendGrid SMTP server.
      String SMTP_HOST_NAME = "smtp.sendgrid.net";

      Properties properties;

      properties = new Properties();

      // Specify SMTP values.
      properties.put("mail.transport.protocol", "smtp");
      properties.put("mail.smtp.host", SMTP_HOST_NAME);
      properties.put("mail.smtp.port", 587);
      properties.put("mail.smtp.auth", "true");

      // Display the email fields entered by the user. 
      out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
      out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
      out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
      out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

      // Create the authenticator object.
      Authenticator authenticator = new SMTPAuthenticator();

      // Create the mail session object.
      Session mailSession;
      mailSession = Session.getDefaultInstance(properties, authenticator);

      // Display debug information to stdout, useful when using the
      // compute emulator during development.
      mailSession.setDebug(true);

      // Create the message and message part objects.
      MimeMessage message;
      Multipart multipart;
      MimeBodyPart messagePart; 

      message = new MimeMessage(mailSession);

      multipart = new MimeMultipart("alternative");
      messagePart = new MimeBodyPart();
      messagePart.setContent(request.getParameter("emailText"), "text/html");
      multipart.addBodyPart(messagePart);            

      // Specify the email To, From, Subject and Content. 
      message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
      message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
      message.setSubject(request.getParameter("emailSubject")); 
      message.setContent(multipart);

      // Uncomment the following if you want to add a footer.
      // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

      // Uncomment the following if you want to enable click tracking.
      // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

      Transport transport;
      transport = mailSession.getTransport();
      // Connect the transport object.
      transport.connect();
      // Send the message.
      transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
      // Close the connection.
      transport.close();

    out.println("<p>Email processing completed.</p>");

  }
  catch (Exception e)
  {
      out.println("<p>Exception encountered: " + 
                        e.getMessage()     +
                        "</p>");   
  }
%>

</body>
</html>
```

Oprócz wysyłania wiadomości e-mail, emailform.jsp zawiera wynik dla użytkownika; Przykładem jest następujący zrzut ekranu:

![Wyślij wynik wiadomości][emailresult]

## <a name="next-steps"></a>Następne kroki
Wdróż aplikację w emulatorze obliczeń i w przeglądarce Uruchom emailform.jsp, wprowadź wartości w formularzu, kliknij pozycję **Wyślij tę wiadomość e-mail**, a następnie Zobacz wyniki w sendemail.jsp.

Ten kod został dostarczony, aby zobaczyć, jak używać SendGrid w języku Java na platformie Azure. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym warto dodać więcej obsługi błędów lub innych funkcji. Na przykład: 

* Możesz użyć obiektów BLOB usługi Azure Storage lub SQL Database do przechowywania adresów e-mail i wiadomości e-mail, zamiast korzystać z formularza sieci Web. Aby uzyskać informacje o korzystaniu z obiektów BLOB usługi Azure Storage w języku Java, zobacz [jak używać usługi BLOB Storage z poziomu języka Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Aby uzyskać informacje o używaniu SQL Database w języku Java, zobacz [używanie SQL Database w języku Java](./azure-sql/database/connect-query-java.md).
* Aby uzyskać więcej informacji o korzystaniu z SendGrid w języku Java, zobacz [jak wysyłać wiadomości e-mail przy użyciu usługi SendGrid w języku Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg