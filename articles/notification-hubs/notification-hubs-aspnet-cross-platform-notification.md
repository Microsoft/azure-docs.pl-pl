---
title: Wysyłanie powiadomień między platformami do użytkowników przy użyciu usługi Azure Notification Hubs (ASP.NET)
description: Dowiedz się, jak za pomocą szablonów Notification Hubs wysyłać powiadomienia w ramach pojedynczego żądania, czyli niezależny od na platformę, która jest przeznaczona dla wszystkich platform.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127042"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Wysyłanie powiadomień na wielu platformach za pomocą usługi Azure Notification Hubs

Ten samouczek jest oparty na poprzednim samouczku, [wysyłania powiadomień do określonych użytkowników przy użyciu usługi Azure Notification Hubs]. W tym samouczku opisano, jak wysyłać powiadomienia wypychane do wszystkich urządzeń zarejestrowanych dla określonego uwierzytelnionego użytkownika. To podejście wymagało wysłania powiadomienia do każdej obsługiwanej platformy klienckiej przez wiele żądań. Usługa Azure Notification Hubs obsługuje szablony, za pomocą których można określić, w jaki sposób konkretne urządzenie ma otrzymywać powiadomienia. Ta metoda upraszcza wysyłanie powiadomień między platformami.

W tym artykule pokazano, jak korzystać z szablonów do wysyłania powiadomień, które są przeznaczone dla wszystkich platform. W tym artykule jest używane pojedyncze żądanie do wysłania powiadomienia neutralnego od platformy. Aby uzyskać szczegółowe informacje na temat szablonów, zobacz [Notification Hubs Omówienie][Templates].

> [!IMPORTANT]
> Windows Phone projekty 8,1 i wcześniejsze nie są obsługiwane w programie Visual Studio 2019. Aby uzyskać więcej informacji, zobacz temat [określanie i zgodność platformy Visual Studio 2019](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> W przypadku Notification Hubs urządzenie może zarejestrować wiele szablonów przy użyciu tego samego tagu. W takim przypadku komunikat przychodzący, który jest przeznaczony dla tagu, powoduje przepełnienie wielu powiadomień do urządzenia, po jednym dla każdego szablonu. Ten proces umożliwia wyświetlenie tego samego komunikatu w wielu powiadomieniach wizualnych, takich jak identyfikator i wyskakujące powiadomienie w aplikacji ze sklepu Windows.

## <a name="send-cross-platform-notifications-using-templates"></a>Wysyłanie powiadomień na wielu platformach przy użyciu szablonów

W tej sekcji użyto przykładowego kodu skompilowanego w przypadku [wysyłania powiadomień do określonych użytkowników przy użyciu usługi Azure Notification Hubs] . Przykład można pobrać z witryny [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Aby wysyłać powiadomienia na wielu platformach za pomocą szablonów, wykonaj następujące czynności:

1. W programie Visual Studio w **Eksplorator rozwiązań**rozwiń folder **Kontrolery** , a następnie otwórz plik *RegisterController.cs* .

1. Znajdź blok kodu w `Put` metodzie, która tworzy nową rejestrację, a następnie zastąp `switch` zawartość następującym kodem:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Ten kod wywołuje metodę specyficzną dla platformy w celu utworzenia rejestracji szablonu zamiast natywnej rejestracji. Ponieważ rejestracje szablonów pochodzą z natywnych rejestracji, nie trzeba modyfikować istniejących rejestracji.

1. W **Eksplorator rozwiązań**w folderze **controllers (kontrolery** ) Otwórz plik *NotificationsController.cs* . Zastąp metodę `Post` poniższym kodem:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Ten kod wysyła powiadomienie do wszystkich platform w tym samym czasie. Nie określono natywnego ładunku. Notification Hubs kompiluje i dostarcza prawidłowy ładunek do każdego urządzenia z podaną wartością tagu, jak określono w zarejestrowanych szablonach.

1. Opublikuj ponownie projekt interfejsu API sieci Web.

1. Ponownie uruchom aplikację kliencką, aby sprawdzić, czy rejestracja zakończyła się pomyślnie.

1. Opcjonalnie Wdróż aplikację kliencką na drugim urządzeniu, a następnie uruchom aplikację. Na każdym urządzeniu zostanie wyświetlone powiadomienie.

## <a name="next-steps"></a>Następne kroki

Teraz, po ukończeniu tego samouczka, Dowiedz się więcej na temat Notification Hubs i szablonów w następujących artykułach:

* Aby zapoznać się z innym scenariuszem dotyczącym korzystania z szablonów, zobacz temat [powiadomienia wypychane do określonych urządzeń z systemem Windows, na których działają aplikacje platforma uniwersalna systemu Windows][Use Notification Hubs to send breaking news] .
* Aby uzyskać szczegółowe informacje na temat szablonów, zobacz [Notification Hubs Omówienie][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[wysyłanie powiadomień do konkretnych użytkowników przy użyciu usługi Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
