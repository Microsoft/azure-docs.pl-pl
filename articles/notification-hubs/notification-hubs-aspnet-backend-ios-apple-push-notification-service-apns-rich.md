---
title: Azure Notification Hubs rozbudowane wypychanie
description: Dowiedz się, jak wysyłać rozbudowane powiadomienia wypychane do aplikacji systemu iOS z platformy Azure. Przykłady kodu w języku C i C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8ae4b984d0ce66dec2a32b3431301ed97fe2c3fd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530138"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs rozbudowane wypychanie

## <a name="overview"></a>Omówienie

Aby można było zaangażować użytkowników z błyskawiczną zawartością, aplikacja może chcieć przeprowadzić wypychanie poza zwykły tekst. Te powiadomienia promują interakcje użytkowników oraz zawartość taką jak adresy URL, dźwięki, obrazy/współdziałania i inne. Ten samouczek kompiluje się w temacie [Powiadamianie użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) i pokazuje, jak wysyłać powiadomienia wypychane, które zawierają ładunki (na przykład obraz).

Ten samouczek jest zgodny z systemem iOS 7 & 8.

  ![Trzy zrzuty ekranu: ekran aplikacji z przyciskiem Wyślij wypychanie, ekran startowy na urządzeniu i logo systemu Windows z przyciskiem wstecz.][IOS1]

Na wysokim poziomie:

1. Zaplecze aplikacji:
   * Przechowuje rozbudowany ładunek (w tym przypadku obraz) w bazie danych zaplecza/magazynu lokalnego
   * Wysyła identyfikator tego rozbudowanego powiadomienia do urządzenia
2. Aplikacja na urządzeniu:
   * Kontaktuje się z zaplecem żądającym obszernego ładunku z IDENTYFIKATORem, który odbiera
   * Wysyła do użytkowników powiadomienia na urządzeniu po zakończeniu pobierania danych, a następnie wyświetla ładunek natychmiast po naciśnięciu przez użytkowników, aby dowiedzieć się więcej

## <a name="webapi-project"></a>Projekt WebAPI

1. W programie Visual Studio Otwórz projekt **AppBackend** , który został utworzony w samouczku [Powiadamianie użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Uzyskaj obraz, z którym chcesz powiadomić użytkowników, i umieść go w folderze **IMG** w katalogu projektu.
3. Kliknij przycisk **Pokaż wszystkie pliki** w Eksplorator rozwiązań i kliknij prawym przyciskiem myszy folder do **uwzględnienia w projekcie**.
4. Po wybraniu obrazu zmień jego akcję kompilacji w okno Właściwości na **zasób osadzony**.

    ![Zrzut ekranu przedstawiający Eksplorator rozwiązań. Plik obrazu jest zaznaczony, a w okienku właściwości osadzony zasób jest wyświetlany jako akcja kompilacji.][IOS2]
5. W pliku `Notifications.cs` dodaj następującą instrukcję using:

    ```csharp
    using System.Reflection;
    ```
6. Zaktualizuj całą `Notifications` klasę przy użyciu następującego kodu. Pamiętaj, aby zastąpić symbole zastępcze poświadczeniami centrum powiadomień i nazwą pliku obrazu.

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

   > [!NOTE]
   > obowiązkowe Aby uzyskać więcej informacji na temat dodawania i uzyskiwania zasobów projektu, zobacz [temat jak osadzić i uzyskać dostęp do zasobów za pomocą języka Visual C#](https://support.microsoft.com/kb/319292) .

7. W programie `NotificationsController.cs` ponownie zdefiniuj polecenie "NotificationsController z poniższymi fragmentami kodu. Spowoduje to wysłanie początkowego, dyskretnego identyfikatora powiadomienia do urządzenia i umożliwia pobieranie obrazu po stronie klienta:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```
8. Teraz będziemy ponownie wdrażać tę aplikację w witrynie sieci Web platformy Azure w celu udostępnienia jej ze wszystkich urządzeń. Kliknij prawym przyciskiem myszy projekt **AppBackend** i wybierz polecenie **Publikuj**.
9. Wybierz witrynę sieci Web platformy Azure jako element docelowy publikowania. Zaloguj się przy użyciu konta platformy Azure i wybierz istniejącą lub nową witrynę sieci Web, a następnie zanotuj Właściwość **docelowa adresu URL** na karcie **połączenie** . W dalszej części tego samouczka będziemy odwoływać się do tego adresu URL jako *punktu końcowego zaplecza* . Kliknij przycisk **Opublikuj**.

## <a name="modify-the-ios-project"></a>Modyfikowanie projektu systemu iOS

Po zmodyfikowaniu zaplecza aplikacji w celu wysłania tylko *identyfikatora* powiadomienia należy zmienić aplikację dla systemu iOS, aby obsługiwała ten identyfikator, i pobrać zaawansowaną wiadomość z zaplecza.

1. Otwórz projekt systemu iOS i Włącz zdalne powiadomienia, przechodząc do głównego obiektu docelowego aplikacji w sekcji **targets** .
2. Kliknij pozycję **możliwości**, Włącz **tryb tła**i zaznacz pole wyboru **powiadomienia zdalne** .

    ![Zrzut ekranu przedstawiający projekt systemu iOS z ekranem możliwości. Tryby tła są włączone, a pole wyboru powiadomienia zdalne jest zaznaczone.][IOS3]
3. Otwórz `Main.storyboard` program i upewnij się, że masz kontroler widoku (nazywany kontrolerem widoku głównego w tym samouczku) z samouczka [Powiadamiaj użytkownika](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
4. Dodaj **kontroler nawigacyjny** do scenorysu i przeciągnij go do kontrolera widoku głównego, aby był **widokiem głównym** nawigacji. Upewnij się, że jest wybrana wartość **kontroler widoku początkowego** w Inspektorze atrybutów tylko dla kontrolera nawigacji.
5. Dodaj **kontroler widoku** do scenorysu i Dodaj **widok obrazu**. Ta strona zostanie wyświetlona, gdy zdecydują się, aby dowiedzieć się więcej, klikając powiadomienie. Scenorys powinien wyglądać następująco:

    ![Zrzut ekranu scenorysu. Widoczne są trzy ekrany aplikacji: Widok nawigacji, widok główny i widok obrazu.][IOS4]
6. Kliknij **kontroler widoku głównego** w scenorysie i upewnij się, że **HomeViewController** jako jego **Klasa niestandardowa** i **Identyfikator scenorysu** w Inspektorze tożsamości.
7. Wykonaj te same czynności dla kontrolera widoku obrazu jako **imageViewController**.
8. Następnie utwórz nową klasę kontrolera widoku zatytułowaną **imageViewController** , aby obsługiwać interfejs użytkownika, który właśnie został utworzony.
9. W **imageViewController. h**Dodaj następujący do deklaracji interfejsu kontrolera. Upewnij się, że kontrolka przeciągnie się z widoku obrazu scenorysu do tych właściwości, aby połączyć dwa:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. W programie `imageViewController.m` Dodaj następujące elementy na końcu `viewDidload` :

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. W programie `AppDelegate.m` Zaimportuj utworzony kontroler obrazu:

    ```objc
    #import "imageViewController.h"
    ```
12. Dodaj sekcję interfejsu z następującą deklaracją:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```
13. W programie upewnij `AppDelegate` się, że aplikacja rejestruje się w celu otrzymywania powiadomień dyskretnych w programie `application: didFinishLaunchingWithOptions` :

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Przed wprowadzeniem `application:didRegisterForRemoteNotificationsWithDeviceToken` zmian w interfejsie użytkownika scenorysu Zastąp w następującej implementacji:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Następnie Dodaj następujące metody do `AppDelegate.m` pobrania obrazu z punktu końcowego i Wyślij powiadomienie lokalne po zakończeniu pobierania. Upewnij się, że symbol zastępczy został zastąpiony przez `{backend endpoint}` punkt końcowy zaplecza:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```
16. Aby obsłużyć powiadomienie lokalne powyżej, otwierając kontroler widoku obrazu w `AppDelegate.m` następujących metodach:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>Uruchom aplikację

1. W programie XCode Uruchom aplikację na fizycznym urządzeniu z systemem iOS (powiadomienia wypychane nie będą działać w symulatorze).
2. W interfejsie użytkownika aplikacji systemu iOS wprowadź nazwę użytkownika i hasło o tej samej wartości na potrzeby uwierzytelniania, a następnie kliknij przycisk **Zaloguj**.
3. Kliknij przycisk **Wyślij wypychanie** , a powinien pojawić się alert w aplikacji. Jeśli klikniesz pozycję **więcej**, nastąpi przełączenie do obrazu wybranego do uwzględnienia w zapleczu aplikacji.
4. Możesz również kliknąć pozycję **Wyślij wypychanie** i natychmiast nacisnąć przycisk Home (Strona główna) urządzenia. Po kilku chwilach otrzymasz powiadomienie wypychane. Jeśli naciśniesz pozycję lub klikniesz przycisk więcej, nastąpi przełączenie do aplikacji i zawartości obrazu sformatowanego.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
