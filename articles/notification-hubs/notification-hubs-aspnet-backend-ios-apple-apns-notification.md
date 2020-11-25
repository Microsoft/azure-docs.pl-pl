---
title: Wysyłanie powiadomień wypychanych do określonych użytkowników przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak wysyłać powiadomienia wypychane do określonych użytkowników systemu iOS przy użyciu usługi Azure Notification Hubs.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016957"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień wypychanych do określonych użytkowników przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

W tym samouczku pokazano, jak wysyłać powiadomienia push do użytkownika konkretnej aplikacji na konkretnym urządzeniu za pomocą usługi Azure Notification Hubs. Zaplecze ASP.NET WebAPI jest używany do uwierzytelniania klientów i generowania powiadomień, jak pokazano w temacie Wskazówki dotyczące [rejestrowania się z zaplecza aplikacji](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu interfejsu WebAPI
> * Uwierzytelnianie klientów w zapleczu interfejsu WebAPI
> * Rejestrowanie na potrzeby powiadomień za pomocą zaplecza interfejsu WebAPI
> * Wysyłanie powiadomień z zaplecza interfejsu WebAPI
> * Publikowanie nowego zaplecza interfejsu WebAPI
> * Modyfikowanie aplikacji systemu iOS
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że utworzono i skonfigurowano centrum powiadomień zgodnie z opisem w temacie [wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md). Ten samouczek jest również warunkiem wstępnym samouczka [bezpiecznego wypychania (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
Jeśli chcesz użyć Mobile Apps jako usługi wewnętrznej bazy danych, zapoznaj się z tematem [Mobile Apps wprowadzenie do wypychania](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modyfikowanie aplikacji systemu iOS

1. Otwórz aplikację widok jednej strony utworzoną w samouczku [wysyłanie powiadomień wypychanych do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md) .

   > [!NOTE]
   > W tej sekcji założono, że projekt jest skonfigurowany z pustą nazwą organizacji. W przeciwnym razie poprzedź nazwę organizacji do wszystkich nazw klas.

2. W `Main.storyboard` pliku Dodaj składniki widoczne na zrzucie ekranu z biblioteki obiektów.

    ![Edytuj scenorys w konstruktorze interfejsu Xcode][1]

   * **Nazwa użytkownika**: UITextField z tekstem zastępczym, *Wprowadź nazwę użytkownika*, bezpośrednio poniżej etykiety Wyślij wyniki i ograniczenie do lewego i prawego marginesu i poniżej etykiety Wyślij wyniki.
   * **Hasło**: UITextField z tekstem zastępczym, *Wprowadź hasło*, bezpośrednio poniżej pola tekstowego nazwy użytkownika i ograniczone do lewego i prawego marginesu i poniżej pola tekstowego username. Zaznacz opcję **bezpiecznego wprowadzania tekstu** w Inspektorze atrybutów w obszarze *klucz powrotu*.
   * **Logowanie**: UIButton oznaczone bezpośrednio poniżej pola tekstowego hasło i usuń zaznaczenie opcji **włączone** w Inspektorze atrybutów, w obszarze *kontrolka-zawartość*
   * **WNS**: Oznacz i Przełącz, aby włączyć wysyłanie powiadomień usługi powiadomień systemu Windows, jeśli została skonfigurowana w centrum. Zapoznaj się z samouczkiem [wprowadzenie systemu Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
   * **GCM**: Etykieta i przełącznik umożliwiające wysłanie powiadomienia do Google Cloud Messaging, jeśli został on skonfigurowany w centrum. Zobacz samouczek [wprowadzenie systemu Android](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APNs**: Etykieta i przełącznik umożliwiające wysłanie powiadomienia do usługi powiadomień platformy firmy Apple.
   * **Nazwa użytkownika adresata: element** UITextField z tekstem zastępczym, *tag nazwy użytkownika odbiorcy*, bezpośrednio poniżej etykiety GCM i ograniczony do lewego i prawego marginesu i poniżej etykiety GCM.

     Niektóre składniki zostały dodane w samouczku [wysyłanie powiadomień wypychanych do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md) .

3. **Naciśnij klawisz Ctrl** i przeciągnij ze składników w widoku, aby `ViewController.h` i dodać te nowe możliwości:

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. W programie `ViewController.h` Dodaj następujące elementy `#define` po instrukcjach importu. Zastąp `<Your backend endpoint>` symbol zastępczy docelowym adresem URL użytym do wdrożenia zaplecza aplikacji w poprzedniej sekcji. Na przykład `http://your_backend.azurewebsites.net` :

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. W projekcie Utwórz nową klasę dotyku kakao o nazwie `RegisterClient` do interfejsu z utworzonym zapleczem ASP.NET. Utwórz klasę, która dziedziczy z `NSObject` . Następnie Dodaj następujący kod w `RegisterClient.h` :

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. W programie `RegisterClient.m` zaktualizuj `@interface` sekcję:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Zastąp `@implementation` sekcję w RegisterClient. m następującym kodem:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Ten kod implementuje logikę wyjaśnioną w artykule ze wskazówkami dotyczącymi [rejestrowania z zaplecza aplikacji](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) przy użyciu usługi NSURLSession w celu wykonywania wywołań REST do zaplecza aplikacji oraz NSUserDefaults do lokalnego przechowywania Identyfikator rejestracji zwróconego przez centrum powiadomień.

    Aby `authorizationHeader` można było prawidłowo działać, ta klasa wymaga ustawienia właściwości. Ta właściwość jest ustawiana przez `ViewController` klasę po nazwie logowania.

8. W programie `ViewController.h` Dodaj `#import` instrukcję dla elementu `RegisterClient.h` . Następnie Dodaj deklarację dla tokenu urządzenia i odwołanie do `RegisterClient` wystąpienia w `@interface` sekcji:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. W plik viewcontroller. m Dodaj prywatną deklarację metody do `@interface` sekcji:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Poniższy fragment kodu nie jest bezpiecznym schematem uwierzytelniania, należy zastąpić implementację    `createAndSetAuthenticationHeaderWithUsername:AndPassword:` z określonym mechanizmem uwierzytelniania, który generuje token uwierzytelniania, który ma być używany przez klasę klienta rejestru, np. OAuth, Active Directory.

10. Następnie w `@implementation` sekcji `ViewController.m` , Dodaj następujący kod, który dodaje implementację dla ustawienia token urządzenia i nagłówek uwierzytelniania.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Zwróć uwagę, jak ustawienie tokenu urządzenia włącza przycisk **Zaloguj** . Jest to spowodowane tym, że jako część akcji logowania kontroler widoku rejestruje powiadomienia wypychane przy użyciu zaplecza aplikacji. Nie chcesz, aby Akcja **logowania** była dostępna do momentu poprawnego skonfigurowania tokenu urządzenia. Możesz oddzielić nazwę logowania od rejestracji wypychanej, o ile była ona wcześniejsza niż ta ostatnia.

11. W plik viewcontroller. m Użyj następujących fragmentów kodu, aby zaimplementować metodę akcji dla przycisku **Zaloguj** i metodę wysyłania komunikatu powiadomienia przy użyciu zaplecza ASP.NET.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Zaktualizuj akcję dla przycisku **Wyślij powiadomienie** , aby użyć zaplecza ASP.NET i wysłać do dowolnego PNS włączonego przez przełącznik.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. W `ViewDidLoad` funkcji Dodaj następujące polecenie, aby utworzyć `RegisterClient` wystąpienie wystąpienia i ustawić delegata dla pól tekstowych.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Teraz w programie `AppDelegate.m` Usuń całą zawartość metody `application:didRegisterForPushNotificationWithDeviceToken:` i Zastąp ją następującym (aby upewnić się, że kontroler widoku zawiera najnowszy token urządzenia pobrany z usługi APNS):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Na koniec upewnij `AppDelegate.m` się, że masz następującą metodę:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testowanie aplikacji

1. W programie XCode Uruchom aplikację na fizycznym urządzeniu z systemem iOS (powiadomienia wypychane nie działają w symulatorze).
2. W interfejsie użytkownika aplikacji systemu iOS wprowadź tę samą wartość dla nazwy użytkownika i hasła. Następnie kliknij przycisk **Zaloguj**.

    ![aplikacja testowa systemu iOS][2]

3. Powinno zostać wyświetlone okno podręczne z informacją o powodzeniu rejestracji. Kliknij przycisk **OK**.

    ![wyświetlone powiadomienie testowe dla systemu iOS][3]

4. W polu tekstowym **tag nazwy użytkownika odbiorcy* wprowadź tag nazwy użytkownika używany podczas rejestracji z innego urządzenia.
5. Wprowadź komunikat z powiadomieniem, a następnie kliknij pozycję **Wyślij powiadomienie**. Komunikat powiadomienia są odbierane tylko w przypadku urządzeń, które mają rejestrację z tagiem nazwy użytkownika odbiorcy. Jest on wysyłany tylko do tych użytkowników.

    ![powiadomienie dotyczące testu dla systemu iOS][4]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wysyłania powiadomień push do konkretnych użytkowników, którzy mają tagi skojarzone ze swoimi rejestracjami. Aby dowiedzieć się, jak wypychać powiadomienia oparte na lokalizacji, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień na podstawie lokalizacji](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
