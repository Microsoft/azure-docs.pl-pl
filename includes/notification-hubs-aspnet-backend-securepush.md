---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74260828"
---
## <a name="webapi-project"></a>Projekt WebAPI

1. W programie Visual Studio Otwórz projekt **AppBackend** , który został utworzony w samouczku **Powiadamianie użytkowników** .
2. W Notifications.cs Zastąp całą klasę **powiadomień** następującym kodem. Pamiętaj, aby zastąpić symbole zastępcze parametrami połączenia (z pełnymi dostępem) dla centrum powiadomień i nazwą centrum. Te wartości można uzyskać z [Azure Portal](https://portal.azure.com). Ten moduł reprezentuje teraz inne bezpieczne powiadomienia, które będą wysyłane. W przypadku pełnej implementacji powiadomienia będą przechowywane w bazie danych programu. dla uproszczenia w tym przypadku przechowujemy je w pamięci.
   
   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

1. W NotificationsController.cs Zastąp kod wewnątrz definicji klasy **NotificationsController** następującym kodem. Ten składnik implementuje sposób, w jaki urządzenie może bezpiecznie pobrać powiadomienie, a także zapewnia metodę (na potrzeby tego samouczka), aby wyzwolić bezpieczną wypychanie do urządzeń. Należy pamiętać, że podczas wysyłania powiadomienia do centrum powiadomień wysyłamy tylko nieprzetworzone powiadomienie z IDENTYFIKATORem powiadomienia (i nie jest to rzeczywisty komunikat):
   
   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

Należy pamiętać, `Post` że metoda teraz nie wysyła wyskakujących powiadomień. Wysyła ono pierwotne powiadomienie, które zawiera tylko identyfikator powiadomienia, a nie poufną zawartość. Pamiętaj również, aby dodać komentarz do operacji wysyłania dla platform, dla których nie skonfigurowano poświadczeń w centrum powiadomień, ponieważ spowodują one błędy.

1. Teraz będziemy ponownie wdrażać tę aplikację w witrynie sieci Web platformy Azure w celu udostępnienia jej ze wszystkich urządzeń. Kliknij prawym przyciskiem myszy projekt **AppBackend** i wybierz polecenie **Publikuj**.
2. Wybierz witrynę sieci Web platformy Azure jako element docelowy publikowania. Zaloguj się przy użyciu konta platformy Azure i wybierz istniejącą lub nową witrynę sieci Web i zanotuj Właściwość **docelowa adresu URL** na karcie **połączenie** . W dalszej części tego samouczka będziemy odwoływać się do tego adresu URL jako *punktu końcowego zaplecza* . Kliknij przycisk **Opublikuj**.
