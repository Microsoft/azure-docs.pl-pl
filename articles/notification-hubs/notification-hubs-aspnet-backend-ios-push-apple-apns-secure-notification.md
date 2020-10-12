---
title: Azure Notification Hubs bezpieczne wypychanie dla systemu iOS
description: Dowiedz się, jak wysyłać bezpieczne powiadomienia wypychane do aplikacji systemu iOS z platformy Azure. Przykłady kodu w języku C i C#.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a6c85ba017656bd312ddfe3d5f6d98014a3dc89a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090350"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs bezpieczne wypychanie

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Omówienie

Obsługa powiadomień wypychanych w programie Microsoft Azure pozwala uzyskać dostęp do łatwej w skalowaniu infrastruktury wypychanej z obsługą wielu platform, która znacznie upraszcza implementację powiadomień wypychanych zarówno dla aplikacji dla klientów, jak i dla przedsiębiorstw dla platform mobilnych.

Ze względu na ograniczenia prawne lub zabezpieczenia czasami aplikacja może chcieć uwzględnić coś w powiadomieniu, którego nie można przesłać za pomocą standardowej infrastruktury powiadomień wypychanych. W tym samouczku opisano sposób osiągnięcia tego samego środowiska przez wysyłanie poufnych informacji za pomocą bezpiecznego, uwierzytelnionego połączenia między urządzeniem klienckim a zaplecze aplikacji.

Na wysokim poziomie przepływ jest następujący:

1. Zaplecze aplikacji:
   * Przechowuje bezpieczny ładunek w bazie danych zaplecza.
   * Wysyła identyfikator tego powiadomienia do urządzenia (żadne zabezpieczone informacje nie są wysyłane).
2. Aplikacja na urządzeniu podczas otrzymywania powiadomienia:
   * Urządzenie kontaktuje się z wewnętrzną prośbą o bezpieczny ładunek.
   * Aplikacja może wyświetlać ładunek jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływie (i w tym samouczku) przyjęto założenie, że urządzenie przechowuje token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Gwarantuje to bezproblemowe działanie, ponieważ urządzenie może pobrać bezpieczny ładunek powiadomienia przy użyciu tego tokenu. Jeśli aplikacja nie przechowuje tokenów uwierzytelniania na urządzeniu lub jeśli te tokeny mogą wygasnąć, aplikacja urządzenia po odebraniu powiadomienia powinien wyświetlić ogólne powiadomienie z monitem użytkownika o uruchomienie aplikacji. Następnie aplikacja uwierzytelnia użytkownika i wyświetla ładunek powiadomienia.

Ten samouczek bezpiecznego wypychania pokazuje, jak bezpiecznie wysyłać powiadomienia wypychane. Samouczek jest oparty na samouczku [Powiadamianie użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , dlatego należy najpierw wykonać kroki opisane w tym samouczku.

> [!NOTE]
> W tym samouczku przyjęto założenie, że utworzono i skonfigurowano centrum powiadomień zgodnie z opisem w temacie [wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modyfikowanie projektu systemu iOS

Po zmodyfikowaniu zaplecza aplikacji w celu wysłania tylko *identyfikatora* powiadomienia należy zmienić aplikację dla systemu iOS, aby obsługiwała to powiadomienie, i wywoływać zaplecze w celu pobrania bezpiecznej wiadomości do wyświetlenia.

Aby osiągnąć ten cel, należy napisać logikę, aby pobrać bezpieczną zawartość z zaplecza aplikacji.

1. W programie upewnij `AppDelegate.m` się, że aplikacja jest zarejestrowana na potrzeby powiadomień dyskretnych, aby przetworzyć identyfikator powiadomienia, który został wysłany z zaplecza. Dodaj `UIRemoteNotificationTypeNewsstandContentAvailability` opcję w didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. W `AppDelegate.m` sekcji Dodaj implementację u góry z następującą deklaracją:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Następnie Dodaj do sekcji implementacji następujący kod, zastępując symbol zastępczy `{back-end endpoint}` punktem końcowym, który został uzyskany wcześniej:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Ta metoda wywołuje zaplecze aplikacji, aby pobrać zawartość powiadomienia przy użyciu poświadczeń przechowywanych w preferencjach udostępnionych.

4. Teraz obsłużmy przychodzące powiadomienie i użyj powyższej metody, aby pobrać zawartość do wyświetlenia. Najpierw włącz uruchamianie aplikacji systemu iOS w tle podczas otrzymywania powiadomień wypychanych. W **Xcode**wybierz projekt aplikacji w panelu po lewej stronie, a następnie kliknij swój główny obiekt docelowy aplikacji w sekcji **obiekty docelowe** w środkowym okienku.
5. Następnie kliknij kartę **możliwości** w górnej części okienka środkowe i sprawdź pole **powiadomienia zdalne** .

    ![Zrzut ekranu przedstawiający XCode z wybranym projektem aplikacji i na karcie możliwości Otwórz. Zaznaczone jest pole wyboru powiadomienia zdalne.][IOS1]

6. W obszarze `AppDelegate.m` Dodaj następującą metodę do obsługi powiadomień wypychanych:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Należy pamiętać, że zaleca się obsługę przypadków braku właściwości nagłówka uwierzytelniania lub odrzucenia przez zaplecze. Konkretna obsługa tych przypadków zależy głównie od środowiska użytkownika docelowego. Jedną z opcji jest wyświetlenie powiadomienia z ogólnym monitem użytkownika o uwierzytelnienie w celu pobrania rzeczywistego powiadomienia.

## <a name="run-the-application"></a>Uruchom aplikację

Aby uruchomić aplikację, wykonaj następujące czynności:

1. W programie XCode Uruchom aplikację na fizycznym urządzeniu z systemem iOS (powiadomienia wypychane nie będą działać w symulatorze).
2. W interfejsie użytkownika aplikacji systemu iOS wprowadź nazwę użytkownika i hasło. Mogą to być dowolne ciągi, ale muszą mieć tę samą wartość.
3. W interfejsie użytkownika aplikacji systemu iOS kliknij pozycję **Zaloguj**. Następnie kliknij pozycję **Wyślij wypychanie**. Powinno zostać wyświetlone bezpieczne powiadomienie wyświetlane w centrum powiadomień.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
