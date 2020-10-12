---
title: Wysyłanie zlokalizowanych powiadomień wypychanych do systemu iOS przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak przy użyciu usługi Azure Notification Hubs używać wypychanych powiadomień z systemem iOS.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4bc392cb6d499d967656358e920f46e261af2906
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87030338"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Samouczek: wysyłanie zlokalizowanych powiadomień wypychanych do systemu iOS przy użyciu usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Sklep Windows C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

W tym samouczku pokazano, jak za pomocą funkcji [szablonów](notification-hubs-templates-cross-platform-push-messages.md) w usłudze Azure Notification Hubs emitować powiadomienia o najświeższych wiadomościach, które zostały zlokalizowane przez język i urządzenie. W tym samouczku zaczniesz korzystać z aplikacji systemu iOS utworzonej w [Notification Hubs w celu wysyłania najświeższych wiadomości]. Po zakończeniu możesz zarejestrować kategorie, które interesują Cię, określić język, w którym mają zostać odebrane powiadomienia, i odbierać powiadomienia wypychane tylko dla wybranych kategorii w tym języku.

W tym scenariuszu istnieją dwie części:

* Aplikacja dla systemu iOS umożliwia urządzeniom klienckim Określanie języka oraz subskrybowanie różnych kategorii Aktualności.
* Zaplecza emituje powiadomienia przy użyciu funkcji **tagów** i **szablonów** usługi Azure Notification Hubs.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Aktualizowanie interfejsu użytkownika aplikacji
> * Tworzenie aplikacji dla systemu iOS
> * Wyślij zlokalizowane powiadomienia szablonu z aplikacji konsolowej platformy .NET
> * Wyślij zlokalizowane powiadomienia szablonu z urządzenia

## <a name="overview"></a>Omówienie

[Korzystając z Notification Hubs do wysyłania najświeższych wiadomości], utworzysz aplikację, która używa **tagów** , aby subskrybować powiadomienia dla różnych kategorii wiadomości. Wiele aplikacji jest jednak przeznaczonych dla wielu rynków i wymaga lokalizacji. Oznacza to, że zawartość powiadomień musi być lokalizowana i dostarczana do prawidłowego zestawu urządzeń. W tym samouczku pokazano, jak za pomocą funkcji **szablonu** Notification Hubs łatwo dostarczać zlokalizowane powiadomienia o najświeższych wiadomościach.

> [!NOTE]
> Jednym ze sposobów wysyłania zlokalizowanych powiadomień jest utworzenie wielu wersji poszczególnych tagów. Na przykład, aby obsługiwać język angielski, francuski i mandarynki, potrzebne są trzy różne Tagi dla wiadomości World News: "world_en", "world_fr" i "world_ch". Następnie należy wysłać zlokalizowaną wersję wiadomości na świecie do każdego z tych tagów. W tym temacie opisano użycie szablonów, aby uniknąć rozprzestrzeniania tagów i wymagania wysyłania wielu komunikatów.

Szablony umożliwiają określenie sposobu odbierania powiadomienia przez określone urządzenie. Szablon określa dokładny format ładunku, odwołując się do właściwości stanowiących część komunikatu wysyłanego przez zaplecze aplikacji. W Twoim przypadku wysyłasz wiadomość niezależny od z ustawieniami regionalnymi zawierającą wszystkie obsługiwane języki:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Następnie upewnij się, że urządzenia rejestrują się za pomocą szablonu, który odwołuje się do poprawnej właściwości. Na przykład aplikacja dla systemu iOS, która chce zarejestrować w przypadku rejestrów wiadomości w języku francuskim, przy użyciu następującej składni:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Aby uzyskać więcej informacji na temat szablonów, zobacz artykuł [Szablony](notification-hubs-templates-cross-platform-push-messages.md) .

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ pracę z [powiadomieniami wypychanymi na określonych urządzeniach z systemem iOS](notification-hubs-ios-xplat-segmented-apns-push-notification.md) i Udostępnij kod, ponieważ ten samouczek kompiluje się bezpośrednio po tym kodzie.
* Program Visual Studio 2019 jest opcjonalny.

## <a name="update-the-app-user-interface"></a>Aktualizowanie interfejsu użytkownika aplikacji

W tej sekcji zmodyfikujesz aplikację z informacjami o aplikacji, która została utworzona w temacie [używanie Notification Hubs do wysyłania najświeższych] wiadomości w celu wysyłania zlokalizowanych najważniejszych wiadomości przy użyciu szablonów.

W programie `MainStoryboard_iPhone.storyboard` Dodaj Przesegmentowanie kontrolki w trzech językach: angielskim, francuskim i mandarynek.

![Tworzenie scenorysu interfejsu użytkownika systemu iOS][13]

Następnie upewnij się, że dodano element IBOutlet w plik viewcontroller. h, jak pokazano na poniższej ilustracji:

![Utwórz opcje dla przełączników][14]

## <a name="build-the-ios-app"></a>Tworzenie aplikacji dla systemu iOS

1. W programie `Notification.h` Dodaj `retrieveLocale` metodę i zmodyfikuj metody Store i Subskrybuj, jak pokazano w poniższym kodzie:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    W programie `Notification.m` zmodyfikuj `storeCategoriesAndSubscribe` metodę, dodając `locale` parametr i zapisując go w ustawieniach domyślnych użytkownika:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Następnie zmodyfikuj metodę *subskrybowania* w celu uwzględnienia ustawień regionalnych:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Używasz metody `registerTemplateWithDeviceToken` , a nie `registerNativeWithDeviceToken` . Podczas rejestrowania dla szablonu należy podać szablon JSON, a także nazwę szablonu (ponieważ aplikacja może chcieć zarejestrować różne szablony). Pamiętaj o zarejestrowaniu kategorii jako tagów, aby upewnić się, że chcesz otrzymywać powiadomienia o tych wiadomościach.

    Dodaj metodę pobierania ustawień regionalnych z ustawień domyślnych użytkownika:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Teraz, po zmodyfikowaniu `Notifications` klasy, należy upewnić się, że program `ViewController` korzysta z nowego `UISegmentControl` . Dodaj następujący wiersz w `viewDidLoad` metodzie, aby upewnić się, że wyświetlane są aktualnie wybrane ustawienia regionalne:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Następnie w `subscribe` metodzie Zmień wywołanie na `storeCategoriesAndSubscribe` następujący kod:

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Na koniec należy zaktualizować `didRegisterForRemoteNotificationsWithDeviceToken` metodę w AppDelegate. m, aby można było poprawnie odświeżać rejestrację po uruchomieniu aplikacji. Zmień wywołanie `subscribe` metody powiadomień z następującym kodem:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>obowiązkowe Wyślij zlokalizowane powiadomienia szablonu z aplikacji konsolowej platformy .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>obowiązkowe Wyślij zlokalizowane powiadomienia szablonu z urządzenia

Jeśli nie masz dostępu do programu Visual Studio lub chcesz tylko testować wysyłanie zlokalizowanych powiadomień szablonów bezpośrednio z aplikacji na urządzeniu. Można dodać zlokalizowane parametry szablonu do `SendNotificationRESTAPI` metody zdefiniowanej w poprzednim samouczku.

```objc
- (void)SendNotificationRESTAPI:(NSString*)categoryTag
{
    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                defaultSessionConfiguration] delegate:nil delegateQueue:nil];

    NSString *json;

    // Construct the messages REST endpoint
    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                        HUBNAME, API_VERSION]];

    // Generated the token to be used in the authorization header.
    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

    //Create the request to add the template notification message to the hub
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    [request setHTTPMethod:@"POST"];

    // Add the category as a tag
    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

    // Template notification
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

    // Signify template notification format
    [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

    // JSON Content-Type
    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

    //Authenticate the notification message POST request with the SaS token
    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

    //Add the notification message body
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

    // Send the REST request
    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
            }
            if (data != NULL)
            {
                //xmlParser = [[NSXMLParser alloc] initWithData:data];
                //[xmlParser setDelegate:self];
                //[xmlParser parse];
            }
        }];

    [dataTask resume];
}
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wysłane zlokalizowane powiadomienia do urządzeń z systemem iOS. Aby dowiedzieć się, jak wypychanie powiadomień do określonych użytkowników aplikacji systemu iOS, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/reference/dn223264(v=azure.100)
