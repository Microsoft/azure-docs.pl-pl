---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: fc479522b3fd436ff02ff6b8985bdeddb66da90a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448780"
---
### <a name="create-the-xamarinforms-solution"></a>Tworzenie rozwiązania Xamarin. Forms

1. W programie **Visual Studio**Utwórz nowe rozwiązanie **Xamarin. Forms** za pomocą **pustej aplikacji formularzy** jako szablonu i wprowadź *PushDemo* dla **nazwy projektu**.

    > [!NOTE]
    > W oknie dialogowym **Konfigurowanie pustej aplikacji formularzy** upewnij się, że **identyfikator organizacji** pasuje do używanej wcześniej wartości i że są sprawdzane elementy docelowe dla systemów **Android** i **iOS** .

1. **Kontrolka**  +  **Kliknij** rozwiązanie *PushDemo* , a następnie wybierz polecenie **Aktualizuj pakiety NuGet**.
1. **Kontrolka**  +  **Kliknij** rozwiązanie *PushDemo* , a następnie wybierz pozycję **Zarządzaj pakietami NuGet..**.
1. Wyszukaj **Newtonsoft.Js** i upewnij się, że jest zaznaczone.
1. Kliknij przycisk **Dodaj pakiety**, a następnie kliknij przycisk **Akceptuj** po wyświetleniu monitu, aby zaakceptować postanowienia licencyjne.
1. Kompiluj i uruchamiaj aplikację na każdej platformie docelowej (**Command**  +  **wprowadź**polecenie), aby przetestować uruchomioną aplikację na urządzeniach.

### <a name="implement-the-cross-platform-components"></a>Zaimplementuj składniki dla wielu platform

1. **Kontrolka**  +  **Kliknij** projekt **PushDemo** , wybierz pozycję **Nowy folder** z menu **Dodaj** , a następnie kliknij pozycję **Dodaj** przy użyciu *modeli* jako **nazwę folderu**.

1. **Kontrolka**  +  **Kliknij** folder **modele** , a następnie wybierz pozycję **nowy plik...** z menu **Dodaj** .

1. Wybierz pozycję **Ogólne**  >  **pusta Klasa**, wpisz *DeviceInstallation.cs*, a następnie Dodaj następującą implementację.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Dodaj **puste Wyliczenie** do folderu **models** o nazwie *PushDemoAction.cs* z następującą implementacją.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Dodaj nowy folder do projektu **PushDemo** o nazwie *Services* , a następnie Dodaj **pustą klasę** do tego folderu o nazwie *ServiceContainer.cs* z następującą implementacją.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Jest to przycięta w dół wersja klasy [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) z repozytorium [XamCAT](https://github.com/xamcat/mobcat-library) . Będzie on używany jako IoC lekki (Inversion of Control).

1. Dodaj **pusty interfejs** do folderu **Services** o nazwie *IDeviceInstallationService.cs*, a następnie Dodaj następujący kod.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Ten interfejs zostanie wdrożony i wykonany przez każdy element docelowy później, aby zapewnić funkcjonalność specyficzną dla platformy i informacje **DeviceInstallation** wymagane przez usługę zaplecza.

1. Dodaj inny **pusty interfejs** do folderu **Services** o nazwie *INotificationRegistrationService.cs*, a następnie Dodaj następujący kod.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Będzie to obsługiwać interakcję między klientem a usługą zaplecza.

1. Dodaj inny **pusty interfejs** do folderu **Services** o nazwie *INotificationActionService.cs*, a następnie Dodaj następujący kod.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Jest on używany jako prosty mechanizm scentralizowania obsługi akcji powiadamiania.

1. Dodaj **pusty interfejs** do folderu **Services** o nazwie *IPushDemoNotificationActionService.cs* , który pochodzi z *INotificationActionService*, z następującą implementacją.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Ten typ jest specyficzny dla aplikacji **PushDemo** i używa wyliczenia **PushDemoAction** do identyfikowania akcji, która jest wyzwalana w sposób silnie wpisywany.

1. Dodaj **pustą klasę** do folderu **Services** o nazwie *NotificationRegistrationService.cs* implementującego *INotificationRegistrationService* z poniższym kodem.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > Argument **apiKey** jest wymagany tylko w przypadku wybrania opcji ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) .

1. Dodaj **pustą klasę** do folderu **Services** o nazwie *PushDemoNotificationActionService.cs* implementującego *IPushDemoNotificationActionService* z poniższym kodem.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Dodaj **pustą klasę** do projektu **PushDemo** o nazwie *config.cs* z następującą implementacją.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Jest to prosty sposób, aby zachować wpisy tajne z kontroli źródła. Można zastąpić te wartości jako część zautomatyzowanej kompilacji lub zastąpić je za pomocą lokalnej klasy częściowej. Należy to zrobić w następnym kroku.
    >
    > Pole **ApiKey** jest wymagane tylko w przypadku wybrania opcji ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) .

1. Dodaj kolejną **pustą klasę** do projektu **PushDemo** tego czasu o nazwie *config. local_secrets. cs* z następującą implementacją.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Zastąp wartości zastępcze własnymi. Należy zanotować te zmiany podczas tworzenia usługi zaplecza. Adres URL **aplikacji interfejsu API** powinien mieć wartość ``https://<api_app_name>.azurewebsites.net/`` . Pamiętaj, aby dodać ``*.local_secrets.*`` do pliku GITIGNORE, aby uniknąć zatwierdzania tego pliku.
    >
    > Pole **ApiKey** jest wymagane tylko w przypadku wybrania opcji ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) .

1. Dodaj **pustą klasę** do projektu **PushDemo** o nazwie *Bootstrap.cs* z następującą implementacją.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > Metoda **BEGIN** zostanie wywołana przez każdą platformę, gdy aplikacja zostanie uruchomiona w ramach implementacji dla platformy **IDeviceInstallationService**.
    >
    > Argument konstruktora **ApiKey** **NotificationRegistrationService** jest wymagany tylko w przypadku wybrania opcji ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) .

### <a name="implement-the-cross-platform-ui"></a>Implementowanie międzyplatformowego interfejsu użytkownika

1. W projekcie **PushDemo** Otwórz **MainPage. XAML** i Zastąp kontrolkę **StackLayout** następującym.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. Teraz w **MainPage.XAML.cs**Dodaj pole zapasowe **tylko do odczytu** w celu zapisania odwołania do implementacji **INotificationRegistrationService** .

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. W konstruktorze **MainPage** należy rozwiązać implementację **INotificationRegistrationService** przy użyciu **servicecontainerer** i przypisać ją do pola zapasowego *_notificationRegistrationService_* .

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Zaimplementuj procedury **obsługi zdarzeń dla** przycisków **RegisterButton** i **DeregisterButton** , które wywołują odpowiednie metody **wyrejestrowywania rejestru** / **Deregister** .

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Teraz w **App.XAML.cs**upewnij się, że istnieją następujące przestrzenie nazw.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Zaimplementuj procedurę obsługi zdarzeń dla zdarzenia **IPushDemoNotificationActionService** **ActionTriggered** .

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. W konstruktorze **aplikacji** należy rozwiązać implementację **IPushNotificationActionService** przy użyciu **servicecontainerer** i subskrybować zdarzenie **IPushDemoNotificationActionService** **ActionTriggered** .

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Jest to po prostu zademonstrowanie przyjęcia i propagacji akcji powiadomień wypychanych. Zwykle te byłyby obsługiwane dyskretnie, na przykład przechodzenie do określonego widoku lub odświeżanie niektórych danych zamiast wyświetlania alertu za pośrednictwem **strony**głównej, **MainPage** w tym przypadku.
