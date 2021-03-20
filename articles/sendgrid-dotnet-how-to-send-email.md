---
title: Jak korzystać z usługi poczty e-mail SendGrid (.NET) | Microsoft Docs
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi poczty e-mail SendGrid na platformie Azure. Przykłady kodu w języku C# i używają interfejsu API platformy .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.custom: devx-track-dotnet
ms.openlocfilehash: ae816d2be592ab774500d1cfe8f2f6a7b7905b91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98196558"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Jak wysyłać wiadomości E-mail przy użyciu programu SendGrid z platformą Azure
## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne przy użyciu usługi poczty e-mail SendGrid na platformie Azure. Przykłady są zapisywane w języku C \# i obsługują .NET Standard 1,3. Omówione scenariusze obejmują konstruowanie poczty e-mail, wysyłanie wiadomości e-mail, Dodawanie załączników i włączanie różnych ustawień poczty i śledzenia. Aby uzyskać więcej informacji na temat SendGrid i wysyłania wiadomości e-mail, zobacz sekcję [następne kroki][Next steps] .

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa poczty E-mail SendGrid?
SendGrid to [oparta na chmurze usługa poczty e-mail] , która zapewnia niezawodne [dostarczanie transakcyjnych wiadomości e-mail], skalowalność i analizę w czasie rzeczywistym oraz elastyczne interfejsy API, które ułatwiają integrację niestandardową. Typowe przypadki użycia SendGrid obejmują:

* Automatyczne wysyłanie potwierdzeń lub zakupionych potwierdzeń do klientów.
* Administrowanie listami dystrybucyjnymi służącymi do wysyłania klientom comiesięcznych Fliers i promocji.
* Zbieranie metryk w czasie rzeczywistym dla elementów, takich jak zablokowane wiadomości e-mail i zaangażowanie klientów.
* Przekazywanie zapytań klientów.
* Przetwarzanie przychodzących wiadomości e-mail.

Aby uzyskać więcej informacji, odwiedź stronę [https://sendgrid.com](https://sendgrid.com) repozytorium GitHub SendGrid lub [bibliotekę języka C#][sendgrid-csharp] .

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Odwołuje się do biblioteki klas SendGrid .NET
[Pakiet NuGet SendGrid](https://www.nuget.org/packages/Sendgrid) jest najprostszym sposobem uzyskania interfejsu API SendGrid i skonfigurowania aplikacji ze wszystkimi zależnościami. NuGet to rozszerzenie programu Visual Studio dołączone do Microsoft Visual Studio 2015 i nowszych, które ułatwia Instalowanie i aktualizowanie bibliotek i narzędzi.

> [!NOTE]
> Aby zainstalować pakiet NuGet, jeśli używasz wersji programu Visual Studio starszej niż Visual Studio 2015, odwiedź stronę [https://www.nuget.org](https://www.nuget.org) i kliknij przycisk **Zainstaluj pakiet NuGet** .
>
>

Aby zainstalować pakiet NuGet SendGrid w aplikacji, wykonaj następujące czynności:

1. Kliknij pozycję **Nowy projekt** i wybierz **szablon**.

   ![Tworzenie nowego projektu][create-new-project]
2. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **odwołania**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet**.

   ![Pakiet NuGet SendGrid][SendGrid-NuGet-package]
3. Wyszukaj pozycję **SendGrid** i wybierz element **SendGrid** na liście wyników.
4. Wybierz najnowszą stabilną wersję pakietu NuGet z listy rozwijanej wersja, aby móc współpracować z modelem obiektów i interfejsami API przedstawionymi w tym artykule.

   ![Pakiet SendGrid][sendgrid-package]
5. Kliknij przycisk **Zainstaluj** , aby ukończyć instalację, a następnie zamknij to okno dialogowe.

Biblioteka klas .NET SendGrid jest nazywana **SendGrid**. Zawiera następujące przestrzenie nazw:

* **SendGrid** do komunikacji z interfejsem API SendGrid.
* **SendGrid. pomocnicys. mail** for Helper Methods, aby łatwo tworzyć obiekty SendGridMessage, które określają sposób wysyłania wiadomości e-mail.

Dodaj następujące deklaracje przestrzeni nazw kodu na początku każdego pliku języka C#, w którym chcesz programowo uzyskać dostęp do usługi poczty e-mail SendGrid.

```csharp
using SendGrid;
using SendGrid.Helpers.Mail;
```

## <a name="how-to-create-an-email"></a>Instrukcje: tworzenie wiadomości E-mail
Użyj obiektu **SendGridMessage** , aby utworzyć wiadomość e-mail. Po utworzeniu obiektu wiadomości można ustawić właściwości i metody, w tym nadawcę wiadomości e-mail, adresata wiadomości e-mail oraz temat i treść wiadomości e-mail.

Poniższy przykład ilustruje sposób tworzenia w pełni wypełnionego obiektu poczty e-mail:

```csharp
var msg = new SendGridMessage();

msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

var recipients = new List<EmailAddress>
{
    new EmailAddress("jeff@example.com", "Jeff Smith"),
    new EmailAddress("anna@example.com", "Anna Lidman"),
    new EmailAddress("peter@example.com", "Peter Saddow")
};
msg.AddTos(recipients);

msg.SetSubject("Testing the SendGrid C# Library");

msg.AddContent(MimeType.Text, "Hello World plain text!");
msg.AddContent(MimeType.Html, "<p>Hello World!</p>");
```

Aby uzyskać więcej informacji na temat wszystkich właściwości i metod obsługiwanych przez typ **SendGrid** , zobacz [SendGrid-CSharp][sendgrid-csharp] w witrynie GitHub.

## <a name="how-to-send-an-email"></a>Instrukcje: wysyłanie wiadomości E-mail
Po utworzeniu wiadomości e-mail można wysłać ją przy użyciu interfejsu API SendGrid. Alternatywnie, możesz użyć [. Wbudowana Biblioteka sieci][NET-library].

Wysłanie wiadomości e-mail wymaga podania klucza interfejsu API usługi SendGrid. Jeśli potrzebujesz szczegółowych informacji o sposobie konfigurowania kluczy interfejsu API, odwiedź stronę z [dokumentacją dotyczącą][documentation]kluczy interfejsu API SendGrid.

Te poświadczenia mogą być przechowywane za pośrednictwem Azure Portal przez kliknięcie pozycji Ustawienia aplikacji i dodanie par klucz/wartość w obszarze Ustawienia aplikacji.

![Ustawienia aplikacji platformy Azure][azure_app_settings]

Następnie możesz uzyskać do nich dostęp w następujący sposób:

```csharp
var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
var client = new SendGridClient(apiKey);
```

W poniższych przykładach pokazano, jak wysłać wiadomość e-mail przy użyciu interfejsu API sieci Web SendGrid za pomocą aplikacji konsolowej.

```csharp
using System;
using System.Threading.Tasks;
using SendGrid;
using SendGrid.Helpers.Mail;

namespace Example
{
    internal class Example
    {
        private static void Main()
        {
            Execute().Wait();
        }

        static async Task Execute()
        {
            var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
            var client = new SendGridClient(apiKey);
            var msg = new SendGridMessage()
            {
                From = new EmailAddress("test@example.com", "DX Team"),
                Subject = "Hello World from the SendGrid CSharp SDK!",
                PlainTextContent = "Hello, Email!",
                HtmlContent = "<strong>Hello, Email!</strong>"
            };
            msg.AddTo(new EmailAddress("test@example.com", "Test User"));
            var response = await client.SendEmailAsync(msg);
        }
    }
}
```

## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Instrukcje: wysyłanie wiadomości e-mail z interfejsu API środowiska ASP .NET Core przy użyciu klasy MailHelper

Poniższy przykład może służyć do wysyłania pojedynczej wiadomości e-mail do wielu osób z interfejsu API platformy ASP .NET Core przy użyciu `MailHelper` klasy `SendGrid.Helpers.Mail` przestrzeni nazw. W tym przykładzie używamy środowiska ASP .NET Core 1,0. 

W tym przykładzie klucz interfejsu API został zapisany w `appsettings.json` pliku, który można przesłonić z Azure Portal, jak pokazano w powyższym przykładzie.

Zawartość `appsettings.json` pliku powinna wyglądać podobnie do:

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  },
  "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

Najpierw należy dodać poniższy kod w `Startup.cs` pliku projektu interfejsu API .NET Core. Jest to wymagane, aby można było uzyskać dostęp do `SENDGRID_API_KEY` `appsettings.json` pliku przy użyciu iniekcji zależności w kontrolerze interfejsu API. `IConfiguration`Interfejs można wstrzyknąć do konstruktora kontrolera po dodaniu go w `ConfigureServices` poniższej metodzie. Zawartość `Startup.cs` pliku wygląda następująco po dodaniu wymaganego kodu:

```csharp
    public IConfigurationRoot Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        // Add mvc here
        services.AddMvc();
        services.AddSingleton<IConfiguration>(Configuration);
    }
```

Po wprowadzeniu interfejsu przez kontroler, `IConfiguration` można użyć `CreateSingleEmailToMultipleRecipients` metody `MailHelper` klasy do wysłania pojedynczej wiadomości e-mail do wielu adresatów. Metoda akceptuje jeden dodatkowy parametr logiczny o nazwie `showAllRecipients` . Ten parametr może służyć do kontrolowania, czy odbiorcy poczty e-mail będą mogli zobaczyć każdy inny adres e-mail w sekcji do nagłówka wiadomości e-mail. Przykładowy kod dla kontrolera powinien wyglądać jak poniżej 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using SendGrid;
using SendGrid.Helpers.Mail;
using Microsoft.Extensions.Configuration;

namespace SendgridMailApp.Controllers
{
    [Route("api/[controller]")]
    public class NotificationController : Controller
    {
       private readonly IConfiguration _configuration;

       public NotificationController(IConfiguration configuration)
       {
         _configuration = configuration;
       }

       [Route("SendNotification")]
       public async Task PostMessage()
       {
          var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
          var client = new SendGridClient(apiKey);
          var from = new EmailAddress("test1@example.com", "Example User 1");
          List<EmailAddress> tos = new List<EmailAddress>
          {
              new EmailAddress("test2@example.com", "Example User 2"),
              new EmailAddress("test3@example.com", "Example User 3"),
              new EmailAddress("test4@example.com","Example User 4")
          };

          var subject = "Hello world email from Sendgrid ";
          var htmlContent = "<strong>Hello world with HTML content</strong>";
          var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
          var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
          var response = await client.SendEmailAsync(msg);
      }
   }
}
```

## <a name="how-to-add-an-attachment"></a>Instrukcje: Dodawanie załącznika
Załączniki można dodać do wiadomości, wywołując metodę **addattach** i minimalnie określając nazwę pliku oraz zawartość zakodowaną w formacie Base64, która ma zostać dołączona. Możesz dołączyć wiele załączników, wywołując tę metodę jednokrotnie dla każdego pliku, który ma zostać dołączony lub przy użyciu metody **Addattachs** . Poniższy przykład ilustruje Dodawanie załącznika do wiadomości:

```csharp
var banner2 = new Attachment()
{
    Content = Convert.ToBase64String(raw_content),
    Type = "image/png",
    Filename = "banner2.png",
    Disposition = "inline",
    ContentId = "Banner 2"
};
msg.AddAttachment(banner2);
```

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Instrukcje: Używanie ustawień poczty do włączania stopek, śledzenia i analizy
SendGrid oferuje dodatkowe funkcje poczty e-mail za pomocą ustawień poczty i ustawień śledzenia. Te ustawienia można dodać do wiadomości e-mail w celu włączenia określonych funkcji, takich jak śledzenie, Google Analytics, śledzenie subskrypcji itd. Aby uzyskać pełną listę aplikacji, zobacz [dokumentację ustawień][settings-documentation].

Aplikacje mogą być stosowane do **SendGrid** wiadomości e-mail przy użyciu metod zaimplementowanych jako część klasy **SendGridMessage** . W poniższych przykładach zademonstrowano stopkę i klikasz filtry śledzenia:

W poniższych przykładach zademonstrowano stopkę i klikasz filtry śledzenia:

### <a name="footer-settings"></a>Ustawienia stopki

```csharp
    msg.SetFooterSetting(
        true,
        "Some Footer HTML",
        "<strong>Some Footer Text</strong>");
```

### <a name="click-tracking"></a>Kliknij pozycję Śledzenie

```csharp
msg.SetClickTracking(true);
```

## <a name="how-to-use-additional-sendgrid-services"></a>Instrukcje: korzystanie z dodatkowych usług SendGrid Services
SendGrid oferuje kilka interfejsów API i elementów webhook, których można użyć do korzystania z dodatkowych funkcji w aplikacji platformy Azure. Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy usługi poczty E-mail SendGrid, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* \#Repozytorium biblioteki SendGrid C: [SendGrid-CSharp][sendgrid-csharp]
* Dokumentacja interfejsu API SendGrid: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/api-reference/
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[usługa poczty e-mail oparta na chmurze]: https://sendgrid.com/solutions
[dostarczanie transakcyjnych wiadomości e-mail]: https://sendgrid.com/use-cases/transactional-email

