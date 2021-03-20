---
title: Rejestrowanie bieżącego użytkownika na potrzeby powiadomień wypychanych przy użyciu interfejsu API sieci Web | Microsoft Docs
description: Dowiedz się, jak zażądać rejestracji powiadomień wypychanych w aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs, gdy rejestracja odbywa się za pomocą interfejsu API sieci Web ASP.NET.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0819f5196fffca25a840dc16d1df04cdd0a55029
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86223316"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Rejestrowanie bieżącego użytkownika na potrzeby powiadomień wypychanych przy użyciu ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Omówienie

W tym temacie pokazano, jak zażądać rejestracji powiadomień wypychanych za pomocą usługi Azure Notification Hubs, gdy rejestracja odbywa się za pomocą interfejsu API sieci Web ASP.NET. Ten temat rozszerza samouczek o [poinformowanie użytkowników o Notification Hubs]. Aby utworzyć uwierzytelnioną usługę mobilną, należy wcześniej wykonać czynności opisane w tym samouczku. Aby uzyskać więcej informacji na temat scenariusza powiadamiania użytkowników, zobacz [Powiadamianie użytkowników za pomocą Notification Hubs].

## <a name="update-your-app"></a>Aktualizowanie aplikacji

1. W MainStoryboard_iPhone. scenorys Dodaj następujące składniki z biblioteki obiektów:

   * **Label**: "push to User with Notification Hubs"
   * **Etykieta**: "Identyfikator InstallationID"
   * **Etykieta**: "użytkownik"
   * **Pole tekstowe**: "użytkownik"
   * **Etykieta**: "hasło"
   * **Pole tekstowe**: "hasło"
   * **Przycisk**: "login"

     W tym momencie scenorys wygląda następująco:

     ![Zrzut ekranu aplikacji MainStoryboard_iPhone. scenorys ze składnikami, które zostały dodane.][0]

2. W edytorze asystenta Utwórz opcje dla wszystkich przełączników przełączanych i Wywołaj je, Połącz pola tekstowe z kontrolerem widoku (delegat) i Utwórz **akcję** dla przycisku **Zaloguj** .

    ![Zrzut ekranu edytora asystenta w aplikacji MainStoryboard_iPhone. scenorys][1]

    Plik BreakingNewsViewController. h powinien teraz zawierać następujący kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Utwórz klasę o nazwie `DeviceInfo` i skopiuj następujący kod do sekcji Interface pliku DEVICEINFO. h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Skopiuj następujący kod w sekcji implementacji pliku DeviceInfo. m:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. W PushToUserAppDelegate. h Dodaj następującą właściwość pojedynczą:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. W `didFinishLaunchingWithOptions` metodzie w PushToUserAppDelegate. m Dodaj następujący kod:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Pierwszy wiersz inicjuje `DeviceInfo` pojedynczą. Drugi wiersz rozpoczyna rejestrację powiadomień wypychanych, która już istnieje, jeśli już ukończono samouczek wprowadzenie do [Notification Hubs] .
7. W PushToUserAppDelegate. m Zaimplementuj metodę `didRegisterForRemoteNotificationsWithDeviceToken` w AppDelegate i Dodaj następujący kod:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Spowoduje to ustawienie tokenu urządzenia dla żądania.

   > [!NOTE]
   > W tym momencie nie powinno być żadnych innych kodów w tej metodzie. Jeśli masz już wywołanie `registerNativeWithDeviceToken` metody, która została dodana po zakończeniu wykonywania [powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md) , musisz dodać komentarz lub usunąć to wywołanie.

8. W `PushToUserAppDelegate.m` pliku Dodaj następującą metodę obsługi:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Ta metoda wyświetla alert w interfejsie użytkownika, gdy aplikacja otrzymuje powiadomienia, gdy jest uruchomiona.

9. Otwórz `PushToUserViewController.m` plik i Przywróć klawiaturę w następującej implementacji:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. W `viewDidLoad` metodzie w `PushToUserViewController.m` pliku, zainicjuj `installationId` etykietę w następujący sposób:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Dodaj następujące właściwości w interfejsie w `PushToUserViewController.m` :

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Następnie Dodaj następującą implementację:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Skopiuj następujący kod do `login` metody obsługi utworzonej przez Xcode:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Ta metoda pobiera zarówno identyfikator instalacji, jak i kanał dla powiadomień wypychanych i wysyła je wraz z typem urządzenia do metody uwierzytelnionego interfejsu API sieci Web, która tworzy rejestrację w Notification Hubs. Ten internetowy interfejs API został zdefiniowany w obszarze [Powiadamianie użytkowników o Notification Hubs].

Teraz, gdy aplikacja kliencka została zaktualizowana, Wróć do okna [Powiadamianie użytkowników o Notification Hubs] i aktualizowanie usługi mobilnej w celu wysyłania powiadomień przy użyciu Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Powiadamianie użytkowników za pomocą Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Wprowadzenie do Notification Hubs]: ios-sdk-get-started.md
