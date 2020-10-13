---
title: Jak korzystać z usługi poczty e-mail SendGrid (Java) | Microsoft Docs
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi poczty e-mail SendGrid na platformie Azure. Przykłady kodu zapisywane w języku Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.custom: devx-track-java
ms.openlocfilehash: 9ff006b74b6202b02a2767aee4d853b1206ce60d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87304483"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Jak wysyłać wiadomości E-mail przy użyciu programu SendGrid z języka Java
W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne przy użyciu usługi poczty e-mail SendGrid na platformie Azure. Przykłady są zapisywane w języku Java. Omówione scenariusze obejmują **konstruowanie poczty e-mail**, **wysyłanie wiadomości e-mail**, **Dodawanie załączników**, **Używanie filtrów**i **Aktualizowanie właściwości**. Aby uzyskać więcej informacji na temat SendGrid i wysyłania wiadomości e-mail, zobacz sekcję [następne kroki](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa poczty E-mail SendGrid?
SendGrid to [oparta na chmurze usługa poczty e-mail] , która zapewnia niezawodne [dostarczanie transakcyjnych wiadomości e-mail], skalowalność i analizę w czasie rzeczywistym oraz elastyczne interfejsy API, które ułatwiają integrację niestandardową. Typowe scenariusze użycia SendGrid obejmują:

* Automatyczne wysyłanie pokwitowań do klientów
* Administrowanie listami dystrybucyjnymi na potrzeby wysyłania klientów do comiesięcznych ofert e-Fliers i specjalnych
* Zbieranie metryk w czasie rzeczywistym dla elementów, takich jak zablokowane wiadomości e-mail i czas odpowiedzi klienta
* Generowanie raportów ułatwiających identyfikowanie trendów
* Przekazywanie zapytań klienta
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Instrukcje: korzystanie z bibliotek javax. mail
Uzyskaj biblioteki javax. mail, na przykład z <https://www.oracle.com/technetwork/java/javamail> i zaimportuj je do kodu. Na wysokim poziomie proces używania biblioteki javax. mail do wysyłania wiadomości e-mail przy użyciu protokołu SMTP:

1. Określ wartości SMTP, w tym serwer SMTP, który dla SendGrid jest smtp.sendgrid.net.

    ```java
    import java.util.Properties;
    import javax.mail.*;
    import javax.mail.internet.*;

    public class MyEmailer {
        private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
        private static final String SMTP_AUTH_USER = "your_sendgrid_username";
        private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

        public static void main(String[] args) throws Exception{
            new MyEmailer().SendMail();
        }

        public void SendMail() throws Exception
        {
            Properties properties = new Properties();
                properties.put("mail.transport.protocol", "smtp");
                properties.put("mail.smtp.host", SMTP_HOST_NAME);
                properties.put("mail.smtp.port", 587);
                properties.put("mail.smtp.auth", "true");
                // …
    ```

1. Rozwiń klasę *javax. mail. Authenticator* i w implementacji metody *getPasswordAuthentication* Zwróć nazwę użytkownika i hasło SendGrid.  

    ```java
    private class SMTPAuthenticator extends javax.mail.Authenticator {
    public PasswordAuthentication getPasswordAuthentication() {
        String username = SMTP_AUTH_USER;
        String password = SMTP_AUTH_PWD;
        return new PasswordAuthentication(username, password);
    }
    ```
2. Utwórz uwierzytelnioną sesję poczty e-mail za pośrednictwem obiektu *javax. mail. Session* .  

    ```java
    Authenticator auth = new SMTPAuthenticator();
    Session mailSession = Session.getDefaultInstance(properties, auth);
    ```
3. Utwórz wiadomość i przypisz **do**wartości, **z**, **temat** i zawartość. Jest to pokazane w sekcji [How to: Create a email](#how-to-create-an-email) .
4. Wyślij wiadomość za pomocą obiektu *javax. mail. Transport* . Jest to pokazane w sekcji [jak: wysyłanie wiadomości E-mail] [#how-to-Send-a-e-mail].

## <a name="how-to-create-an-email"></a>Instrukcje: tworzenie wiadomości e-mail
Poniżej pokazano, jak określić wartości dla wiadomości e-mail.

```java
MimeMessage message = new MimeMessage(mailSession);
Multipart multipart = new MimeMultipart("alternative");
BodyPart part1 = new MimeBodyPart();
part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
BodyPart part2 = new MimeBodyPart();
part2.setContent(
    "<p>Hello,</p>
    <p>Your Contoso order has <b>shipped</b>.</p>
    <p>Thank you,<br>John</br></p>",
    "text/html");
multipart.addBodyPart(part1);
multipart.addBodyPart(part2);
message.setFrom(new InternetAddress("john@contoso.com"));
message.addRecipient(Message.RecipientType.TO,
    new InternetAddress("someone@example.com"));
message.setSubject("Your recent order");
message.setContent(multipart);
```

## <a name="how-to-send-an-email"></a>Instrukcje: wysyłanie wiadomości e-mail
Poniżej przedstawiono sposób wysyłania wiadomości e-mail.

```java
Transport transport = mailSession.getTransport();
// Connect the transport object.
transport.connect();
// Send the message.
transport.sendMessage(message, message.getAllRecipients());
// Close the connection.
transport.close();
```

## <a name="how-to-add-an-attachment"></a>Instrukcje: Dodawanie załącznika
Poniższy kod pokazuje, jak dodać załącznik.

```java
// Local file name and path.
String attachmentName = "myfile.zip";
String attachmentPath = "c:\\myfiles\\";
MimeBodyPart attachmentPart = new MimeBodyPart();
// Specify the local file to attach.
DataSource source = new FileDataSource(attachmentPath + attachmentName);
attachmentPart.setDataHandler(new DataHandler(source));
// This example uses the local file name as the attachment name.
// They could be different if you prefer.
attachmentPart.setFileName(attachmentName);
multipart.addBodyPart(attachmentPart);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Instrukcje: Używanie filtrów do włączania stopek, śledzenia i analizy
Program SendGrid oferuje dodatkowe funkcje poczty e-mail za pośrednictwem *filtrów*. Są to ustawienia, które można dodać do wiadomości e-mail w celu włączenia określonych funkcji, takich jak włączenie śledzenia, Google Analytics, śledzenie subskrypcji itd. Aby uzyskać pełną listę filtrów, zobacz [Ustawienia filtru][Filter Settings].

* Poniżej pokazano, jak wstawić filtr stopki, który powoduje, że tekst HTML pojawia się u dołu wysyłanej wiadomości e-mail.

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"footer\":
        {\"settings\":
        {\"enable\":1,\"text/html\":
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
    ```
* Innym przykładem filtru jest kliknij śledzenie. Załóżmy, że tekst wiadomości e-mail zawiera hiperłącze, na przykład następujące i chcesz śledzić stawkę kliknięcia:

    ```java
    messagePart.setContent(
        "Hello,
        <p>This is the body of the message. Visit
        <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
        Thank you.",
        "text/html");
    ```
* Aby włączyć śledzenie kliknij, użyj następującego kodu:

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"clicktrack\":
        {\"settings\":
        {\"enable\":1}}}}");
    ```

## <a name="how-to-update-email-properties"></a>Instrukcje: aktualizowanie właściwości poczty e-mail
Niektóre właściwości wiadomości e-mail można zastąpić za pomocą **Właściwości Set** lub dołączyć przy użyciu **Właściwości Add**.

Na przykład, aby określić adresy **ReplyTo** , należy użyć następujących:

```java
InternetAddress addresses[] =
    { new InternetAddress("john@contoso.com"),
        new InternetAddress("wendy@contoso.com") };

message.setReplyTo(addresses);
```

Aby dodać adresata **DW** , użyj następujących czynności:

```java
message.addRecipient(Message.RecipientType.CC, new
InternetAddress("john@contoso.com"));
```

## <a name="how-to-use-additional-sendgrid-services"></a>Instrukcje: korzystanie z dodatkowych usług SendGrid Services
Usługa SendGrid oferuje interfejsy API oparte na sieci Web, których można użyć do korzystania z dodatkowych funkcji SendGrid z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy usługi poczty E-mail SendGrid, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Przykład demonstrujący korzystanie z SendGrid w ramach wdrożenia platformy Azure: [jak wysyłać wiadomości e-mail przy użyciu SendGrid z języka Java w ramach wdrożenia platformy Azure](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* Dokumentacja interfejsu API SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* Oferta Specjalna SendGrid dla klientów platformy Azure: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[usługa poczty e-mail oparta na chmurze]: https://sendgrid.com/email-solutions
[dostarczanie transakcyjnych wiadomości e-mail]: https://sendgrid.com/transactional-email
