---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060504"
---
### <a name="configure-required-ios-packages"></a>Konfigurowanie wymaganych pakietów systemu iOS

Pakiet jest [automatycznie łączony](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) podczas kompilowania aplikacji. Wystarczy zainstalować natywne zasobniki:

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Skonfiguruj info. plist i uprawnienia. plist

1. Przejdź do folderu "PushDemo/iOS" i Otwórz obszar roboczy "PushDemo. xcworkspace", wybierz górny projekt "PushDemo" i wybierz kartę "możliwości podpisywania &".

1. Zaktualizuj identyfikator pakietu, aby odpowiadał wartości użytej w profilu aprowizacji.

1. Dodaj dwie nowe możliwości przy użyciu przycisku-"+":

    - Możliwość trybu w tle i zdalne powiadomienia o taktie.
    - Możliwość powiadomień wypychanych

### <a name="handle-push-notifications-for-ios"></a>Obsługa powiadomień wypychanych dla systemu iOS

1. Otwórz "AppDelegate. h" i Dodaj następujący import:

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. Aktualizowanie listy protokołów obsługiwanych przez "AppDelegate" przez dodanie `UNUserNotificationCenterDelegate` :

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Otwórz "AppDelegate. m" i skonfiguruj wszystkie wymagane wywołania zwrotne systemu iOS:

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
