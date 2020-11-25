---
title: Plik dyrektywy include
description: Plik dyrektywy include, który zawiera kod służący tworzenia projektu zaplecza interfejsu WebAPI platformy ASP.NET.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 6f490b6f25112ed8a10bbd865070bd07ea3ee84f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016958"
---
## <a name="create-the-webapi-project"></a>Tworzenie projektu interfejsu WebAPI

W poniższych sekcjach omówiono proces tworzenia nowego zaplecza interfejsu WebAPI platformy ASP.NET. Ten proces ma trzy główne cele:

- **Uwierzytelnianie klientów**: procedura obsługi komunikatów jest dodawana w celu uwierzytelnienia żądań klientów i kojarzenia użytkownika z żądaniem.
- **Rejestrowanie do używania powiadomień za pomocą zaplecza interfejsu WebAPI**: kontroler jest dodawany w celu obsługi nowych rejestracji, aby urządzenie klienckie mogło otrzymywać powiadomienia. Nazwa uwierzytelnionego użytkownika jest automatycznie dodawana do rejestracji jako [tag](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md).
- **Wysyłanie powiadomień do klientów**: jest dodawany kontroler udostępniający użytkownikom możliwość wyzwalania bezpiecznej operacji wypychania do urządzeń i klientów skojarzonych z tagiem.

Utwórz nowe zaplecze interfejsu WebAPI na platformie ASP.NET, wykonując następujące akcje:

> [!IMPORTANT]
> Jeśli używasz programu Visual Studio 2015 lub starszego, przed rozpoczęciem tego samouczka upewnij się, że masz zainstalowaną najnowszą wersję Menedżera pakietów NuGet dla programu Visual Studio.
>
>Aby to sprawdzić, uruchom program Visual Studio. W menu **Narzędzia** wybierz pozycję **rozszerzenia i aktualizacje**. Wyszukaj pozycję **Menedżer pakietów NuGet** w swojej wersji programu Visual Studio i sprawdź, czy masz najnowszą wersję. Jeśli nie używasz najnowszej wersji, odinstaluj ją i ponownie zainstaluj Menedżera pakietów NuGet.

![Zrzut ekranu przedstawiający okno dialogowe rozszerzenia i aktualizacje z wyróżnionym pakietem NuGet Manage for Visual Studios.][B4]

> [!NOTE]
> Upewnij się, że zainstalowano wdrożenie [zestawu Azure SDK](https://azure.microsoft.com/downloads/) dla programu Visual Studio dla witryny sieci Web.

1. Uruchom program Visual Studio lub Visual Studio Express.

2. Wybierz pozycję **Eksplorator serwera** i zaloguj się do konta platformy Azure. Aby tworzyć zasoby witryny internetowej na swoim koncie, musisz się zalogować.

3. W programie Visual Studio kliknij prawym przyciskiem myszy rozwiązanie programu Visual Studio, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy projekt**.
4. Rozwiń węzeł **Visual C#**, wybierz pozycję **Internet** i kliknij pozycję **Aplikacja internetowa ASP.NET**.

5. W polu **Nazwa** wpisz ciąg **AppBackend**, a następnie wybierz przycisk **OK**.

    ![Okno Nowy projekt][B1]

6. W oknie **Nowy projekt ASP.NET** zaznacz pole wyboru **Internetowy interfejs API**, a następnie wybierz przycisk **OK**.

    ![Okno Nowy projekt ASP.NET][B2]

7. W oknie **Konfigurowanie aplikacji internetowej platformy Microsoft Azure** wybierz subskrypcję, a następnie na liście **Plan usługi App Service** wykonaj jedną z następujących akcji:

    * Wybierz już utworzony plan usługi App Service.
    * Wybierz pozycję **Utwórz nowy plan usługi App Service**, a następnie utwórz plan.

   Ten samouczek nie wymaga bazy danych. Po wybraniu planu usługi App Service kliknij przycisk **OK**, aby utworzyć projekt.

    ![Okno Konfigurowanie aplikacji internetowej platformy Microsoft Azure][B5]

    Jeśli nie widzisz tej strony na potrzeby konfigurowania planu usługi App Service, przejdź do samouczka. Można go skonfigurować podczas późniejszego publikowania aplikacji. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>Uwierzytelnianie klientów w zapleczu interfejsu WebAPI

W tej sekcji tworzysz nową klasę procedury obsługi komunikatów o nazwie **AuthenticationTestHandler** dla nowego zaplecza. Ta klasa pochodzi od klasy [DelegatingHandler](/previous-versions/visualstudio/hh193679(v=vs.118)) i jest dodawana jako procedura obsługi komunikatów, aby umożliwić przetwarzanie wszystkich żądań przychodzących do zaplecza.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **AppBackend**, wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Klasa**.
2. Nadaj nowej klasie nazwę **AuthenticationTestHandler.cs**, a następnie wybierz pozycję **Dodaj**, aby wygenerować klasę. Ta klasa służy do uwierzytelniania użytkowników za pomocą *Uwierzytelniania podstawowego* dla uproszczenia. Twoja aplikacja może używać dowolnego schematu uwierzytelniania.
3. W klasie AuthenticationTestHandler.cs dodaj następujące instrukcje `using`:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. W klasie AuthenticationTestHandler.cs zastąp definicję klasy `AuthenticationTestHandler` następującym kodem:

    Ta procedura obsługi autoryzuje żądanie, gdy następujące trzy warunki zostaną spełnione:

   * Żądanie zawiera nagłówek *Authorization*.
   * Żądanie używa uwierzytelniania *basic*.
   * Ciąg nazwy użytkownika i ciąg hasła to ten sam ciąg.

   W przeciwnym razie żądanie jest odrzucane. To uwierzytelnianie nie reprezentuje rzeczywistego podejścia do uwierzytelniania i autoryzacji. Jest to wyłącznie prosty przykład na potrzeby tego samouczka.

   Jeśli komunikat żądania jest uwierzytelniany i autoryzowany przez klasę `AuthenticationTestHandler`, użytkownik uwierzytelniania podstawowego jest dołączany do bieżącego żądania w obiekcie [HttpContext](/dotnet/api/system.web.httpcontext.current). Informacje o użytkowniku w obiekcie HttpContext zostaną później użyte przez inny kontroler (RegisterController) w celu dodania [tagu](/previous-versions/azure/azure-services/dn530749(v=azure.100)) do żądania rejestracji powiadomienia.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (VerifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool VerifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Uwaga dotycząca zabezpieczeń: klasa `AuthenticationTestHandler` nie zapewnia rzeczywistego uwierzytelniania. Jest ona używana tylko do naśladowania uwierzytelniania podstawowego i nie jest bezpieczna. W swoich aplikacjach i usługach produkcyjnych musisz zaimplementować mechanizm bezpiecznego uwierzytelniania.
5. Aby zarejestrować procedurę obsługi komunikatów, dodaj następujący kod na końcu metody `Register` w klasie **App_Start/WebApiConfig.cs**:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Zapisz zmiany.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Rejestrowanie na potrzeby powiadomień za pomocą zaplecza interfejsu WebAPI

W tej sekcji dodajesz nowy kontroler do zaplecza interfejsu WebAPI w celu obsługi żądań rejestracji użytkownika i urządzenia do otrzymywania powiadomień przy użyciu biblioteki klienta dla centrów powiadomień. Kontroler dodaje tag użytkownika, który został uwierzytelniony i dołączony do obiektu HttpContext przez klasę `AuthenticationTestHandler`. Tag ma format ciągu `"username:<actual username>"`.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **AppBackend**, a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.

2. W okienku po lewej stronie wybierz pozycję **Online**, a następnie w polu **Wyszukaj** wpisz ciąg **Microsoft.Azure.NotificationHubs**.

3. Na liście wyników wybierz pozycję **Microsoft Azure Notification Hubs**, a następnie wybierz pozycję **Instaluj**. Zakończ instalację, a następnie zamknij okno Menedżera pakietów NuGet.

    Ta akcja spowoduje dodanie odwołania do zestawu SDK usługi Azure Notification Hubs z użyciem [pakietu NuGet Microsoft.Azure.Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

4. Utwórz plik nowej klasy, która reprezentuje połączenie z centrum powiadomień używane do wysyłania powiadomień. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **Modele**, wybierz polecenie **Dodaj**, a następnie kliknij pozycję **Klasa**. Nadaj nowej klasie nazwę **Notifications.cs**, a następnie wybierz pozycję **Dodaj**, aby wygenerować klasę.

    ![Okno Dodawanie nowego elementu][B6]

5. W klasie Notifications.cs dodaj następującą instrukcję `using` na początku pliku:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Zastąp definicję klasy `Notifications` poniższym kodem i zastąp dwa symbole zastępcze parametrami połączenia (z pełnym dostępem) dla Twojego centrum powiadomień i nazwą centrum (dostępną w witrynie [Azure Portal](https://portal.azure.com)):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > Aby kontynuować, wprowadź **nazwę** i **DefaultFullSharedAccessSignature** centrum. 
    
7. Następnie utwórz nowy kontroler o nazwie **RegisterController**. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **Kontrolery**, wybierz polecenie **Dodaj**, a następnie kliknij pozycję **Kontroler**.

8. Wybierz pozycję **Kontroler internetowego interfejsu API 2 — pusty**, a następnie wybierz pozycję **Dodaj**.

    ![Okno Dodawanie szkieletu][B7]

9. W polu **Nazwa kontrolera** wpisz ciąg **RegisterController**, aby nadać nazwę nowej klasie, a następnie wybierz pozycję **Dodaj**.

    ![Okno Dodawanie kontrolera][B8]

10. W pliku RegisterController.cs dodaj następujące instrukcje `using`:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Dodaj następujący kod wewnątrz definicji klasy `RegisterController`. W tym kodzie dodajesz tag użytkownika, który został dołączony do obiektu HttpContext. Użytkownik został uwierzytelniony i dołączony do obiektu HttpContext przez dodany przez Ciebie filtr komunikatów, `AuthenticationTestHandler`. Można również dodać opcjonalne sprawdzenia w celu weryfikacji, czy użytkownik ma uprawnienia do rejestrowania żądanych tagów.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Zapisz zmiany.

## <a name="send-notifications-from-the-webapi-backend"></a>Wysyłanie powiadomień z zaplecza interfejsu WebAPI

W tej sekcji dodasz nowego kontrolera, który opisuje sposób wysyłania powiadomień z urządzeń klienckich. Powiadomienie opiera się na tagu nazwy użytkownika, który używa biblioteki .NET usługi Azure Notification Hubs w zaplecze interfejsu WebAPI na platformie ASP.NET.

1. Utwórz innego nowego kontrolera o nazwie **NotificationsController** w taki sam sposób, jak utworzono kontrolera **RegisterController** w poprzedniej sekcji.

2. W pliku NotificationsController.cs dodaj następujące instrukcje `using`:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Dodaj następującą metodę do klasy **NotificationsController**:

    Ten kod wysyła typ powiadomienia na podstawie parametru `pns` usługi powiadomień platformy (PNS, Platform Notification Service). Wartość `to_tag` służy do ustawiania tagu *username* w komunikacie. Ten tag musi być zgodny z tagiem username aktywnej rejestracji centrum powiadomień. Komunikat powiadomienia jest pobierany z treści żądania POST i formatowany dla docelowej usługi PNS.

    W zależności od usługi PNS używanej do odbierania powiadomień przez obsługiwane urządzenia powiadomienia są obsługiwane przy użyciu różnych formatów. Na przykład w przypadku urządzeń z systemem Windows można użyć [wyskakujących powiadomień za pomocą usługi WNS](/uwp/schemas/tiles/toastschema/schema-root), które nie są bezpośrednio obsługiwane przez inną usługę PNS. W takim przypadku zaplecze musi sformatować powiadomienie jako obsługiwane powiadomienie w przypadku usługi PNS urządzeń, które planujesz obsługiwać. Następnie użyj odpowiedniego interfejsu API wysyłania w [klasie NotificationHubClient](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient).

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Aby uruchomić aplikację i sprawdzić dokładność pracy wykonanej do tej pory, naciśnij klawisz **F5**. Aplikacja otwiera przeglądarkę internetową z wyświetloną stroną główną platformy ASP.NET.

## <a name="publish-the-new-webapi-backend"></a>Publikowanie nowego zaplecza interfejsu WebAPI

Następnie wdrożysz tę aplikację w witrynie internetowej platformy Azure, aby udostępnić ją wszystkim urządzeniom.

1. Kliknij prawym przyciskiem myszy projekt **AppBackend**, a następnie wybierz polecenie **Publikuj**.

2. Wybierz pozycję **Microsoft Azure App Service** jako docelową lokalizację publikacji, a następnie wybierz pozycję \*\*Publikuj. Zostanie otwarte okno Tworzenie usługi App Service. W tym miejscu możesz tworzyć wszystkie niezbędne zasoby platformy Azure służące do uruchamiania aplikacji internetowej ASP.NET na platformie Azure.

    ![Kafelek usługi Microsoft Azure App Service][B15]

3. W oknie **Tworzenie usługi App Service** wybierz swoje konto platformy Azure. Wybierz pozycję **Zmień typ**  >  **aplikacji sieci Web**. Zachowaj wartość domyślną w polu **Nazwa aplikacji internetowej** i wybierz wartości w polach **Subskrypcja**, **Grupa zasobów** i **Plan usługi App Service**.

4. Wybierz pozycję **Utwórz**.

5. Zanotuj wartość właściwości **Adres URL witryny** w sekcji **Podsumowanie**. Ten adres URL jest Twoim *punktem końcowym zaplecza* w dalszej części samouczka.

6. Kliknij pozycję **Opublikuj**.

Po ukończeniu pracy z kreatorem aplikacja internetowa ASP.NET zostanie opublikowana na platformie Azure, a następnie otwarta w przeglądarce domyślnej.  Twoja aplikacja jest widoczna w usłudze Azure App Services.

Adres URL używa określonej wcześniej nazwy aplikacji internetowej w formacie http://<nazwa_aplikacji>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG