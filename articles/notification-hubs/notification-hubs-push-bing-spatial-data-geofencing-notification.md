---
title: Wysyłanie powiadomień wypychanych za pomocą usługi Azure Notification Hubs i danych przestrzennych Bing | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak dostarczać powiadomienia push oparte na lokalizacji przy użyciu usługi Azure Notification Hubs i danych przestrzennych usługi Bing.
services: notification-hubs
documentationcenter: windows
keywords: powiadomienia wypychane, powiadomienia wypychane
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 51ad23e67a77c28d0ad8a147168a0094f5de1796
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578006"
---
# <a name="tutorial-send-location-based-push-notifications-with-notification-hubs-and-bing-spatial-data"></a>Samouczek: wysyłanie powiadomień wypychanych opartych na lokalizacji z danymi przestrzennymi Notification Hubs i Bing

Korzystając z tego samouczka, dowiesz się, jak dostarczać powiadomienia push oparte na lokalizacji przy użyciu usługi Azure Notification Hubs i danych przestrzennych usługi Bing.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Konfigurowanie źródła danych
> * Konfigurowanie aplikacji platformy uniwersalnej systemu Windows
> * Konfigurowanie zaplecza
> * Testowanie powiadomień push w aplikacji platformy uniwersalnej systemu Windows

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem  [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* Program [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) lub nowszy ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)).
* Najnowsza wersja zestawu [Azure SDK](https://azure.microsoft.com/downloads/).
* [Konto Centrum deweloperów Map Bing](https://www.bingmapsportal.com/) (można utworzyć je bezpłatnie i skojarzyć z kontem Microsoft).

## <a name="set-up-the-data-source"></a>Konfigurowanie źródła danych

1. Zaloguj się do [Centrum deweloperów Map Bing](https://www.bingmapsportal.com/).
2. Wybierz pozycję **Źródła danych** na górnym pasku nawigacyjnym, a następnie wybierz pozycję **Zarządzaj źródłami danych**.

    ![Zrzut ekranu przedstawiający Centrum deweloperów map Bing na stronie Zarządzanie źródłami danych za pomocą opcji Przekaż dane jako źródło danych pokreślonym kolorem czerwonym.](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Jeśli nie masz źródła danych, możesz je utworzyć za pomocą widocznego linku. Wybierz pozycję **Upload data as a data source** (Przekaż dane jako źródło danych). Możesz również użyć menu **Data sources**  >  **przekazywanie danych** źródła danych.

    ![Zrzut ekranu przedstawiający okno dialogowe przekazywanie źródła danych.](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. Utwórz `NotificationHubsGeofence.pipe` na dysku twardym plik z następującą zawartością: w tym samouczku użyjesz przykładowego pliku z potokiem, który tworzy ramkę obszaru Francisco Waterfront:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    Plik potoku reprezentuje następującą jednostkę:

    ![Zrzut ekranu przedstawiający mapę sieci San Francisco Waterfront z czerwonym wielokątem, który podkreśla obszar kopiarek.](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Na stronie **Upload a data source** (Przekazywanie źródła danych) wykonaj następujące czynności:
   1. Wybierz pozycję **Pipe** (Potok) w polu **Data format** (Format danych).
   2. Wyszukaj i wybierz plik `NotificationHubGeofence.pipe` utworzony w poprzednim kroku.
   3. Wybierz przycisk **Upload** (Przekaż).

      > [!NOTE]
      > Może zostać wyświetlony monit o określenie nowego klucza dla właściwości **Klucz główny**, który różni się od właściwości **Klucz zapytania**. Po prostu utwórz nowy klucz przy użyciu pulpitu nawigacyjnego i odśwież stronę przekazywania źródła danych.
6. Po przekazaniu pliku danych opublikuj źródło danych. Wybierz pozycję **źródła danych**  ->  **Zarządzaj źródłami danych** , takimi jak wcześniej.
7. Wybierz źródło danych na liście, a następnie wybierz pozycję **Publish** (Publikuj) w kolumnie **Actions** (Akcje).

    ![Zrzut ekranu przedstawiający Centrum deweloperów map Bing na stronie Zarządzanie źródłami danych z wybraną kartą dane geokodowane i opcją publikowania podaną na czerwono.](./media/notification-hubs-geofence/publish-button.png)
8. Otwórz kartę **Published Data Sources** (Opublikowane źródła danych) i upewnij się, że źródło danych jest widoczne na liście.

    ![Zrzut ekranu przedstawiający Centrum deweloperów map Bing na stronie Zarządzanie źródłami danych z wybraną kartą opublikowane źródła danych.](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. Kliknij pozycję **Edytuj**. Od razu zobaczysz lokalizacje wprowadzone w danych.

    ![Zrzut ekranu przedstawiający stronę Edytowanie danych jednostki z mapą zachodnich Stanów Zjednoczonych i karmazynową kropką w sieci San Francisco Waterfront.](./media/notification-hubs-geofence/bing-maps-data-details.png)

    W tym momencie w portalu nie są wyświetlane granice utworzonego wirtualnego ogrodzenia — potrzebne jest jedynie potwierdzenie, że określona lokalizacja znajduje się w odpowiedniej okolicy.
10. Teraz wszystkie wymagania dotyczące źródła danych zostały spełnione. Aby uzyskać szczegółowe informacje dotyczące adresu URL żądania dla wywołania interfejsu API, w Centrum deweloperów Map Bing wybierz pozycję **Źródła danych** i wybierz pozycję **Informacje o źródle danych**.

    ![Zrzut ekranu przedstawiający Centrum deweloperów map Bing na stronie informacje o źródle danych.](./media/notification-hubs-geofence/bing-maps-data-info.png)

    **Adres URL zapytania** to punkt końcowy, względem którego możemy wykonywać zapytania, aby sprawdzić, czy urządzenie znajduje się obecnie w granicach lokalizacji, czy nie. Aby to sprawdzić, wykonujemy wywołanie GET dla adresu URL zapytania z dołączonymi następującymi parametrami:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    Mapy Bing automatycznie wykonają obliczenia w celu sprawdzenia, czy urządzenie znajduje się w wirtualnym ogrodzeniu. Po wykonaniu żądania przy użyciu przeglądarki (lub programu cURL) uzyskasz standardową odpowiedź w formacie JSON:

    ![Zrzut ekranu przedstawiający standardową odpowiedź JSON.](./media/notification-hubs-geofence/bing-maps-json.png)

    Ta odpowiedź jest wysyłana tylko wtedy, gdy punkt rzeczywiście znajduje się w wyznaczonych granicach. W przeciwnym razie otrzymasz pusty zasobnik **results**:

    ![Zrzut ekranu przedstawiający odpowiedź JSON z pustym zasobnikem wyników.](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>Konfigurowanie aplikacji platformy uniwersalnej systemu Windows

1. W programie Visual Studio utwórz nowy projekt typu **Pusta aplikacja (uniwersalna aplikacja systemu Windows)**.

    ![Zrzut ekranu okna dialogowego Nowy projekt programu Visual Studio z wyróżnioną pustą aplikacją (uniwersalną opcją systemu Windows Visual C#).](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Po zakończeniu tworzenia projektu uzyskasz dostęp do aplikacji. Teraz skonfigurujemy wszystko dla infrastruktury z ogrodzeniem geograficznym. Ponieważ w tym celu użyjemy usług Bing, możemy skorzystać z publicznego punktu końcowego interfejsu API REST, umożliwiającego wykonywanie zapytań dotyczących określonego zakresu lokalizacji:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Aby przygotować go do pracy, określ następujące parametry:

   * **Identyfikator źródła danych** i **Nazwa źródła danych** — w interfejsie API Map Bing źródła danych zawierają różne metadane w zasobnikach, takie jak lokalizacje i godziny pracy.  
   * **Nazwa jednostki** — jednostka, która ma być używana jako punkt odniesienia dla powiadomienia.
   * **Klucz interfejsu API Map Bing** — klucz uzyskany wcześniej podczas tworzenia konta Centrum deweloperów Bing.

     Po przygotowaniu źródła danych możemy rozpocząć pracę nad aplikacją platformy uniwersalnej systemu Windows.
2. Włącz usługi lokalizacji dla aplikacji. Otwórz plik `Package.appxmanifest` w **Eksploratorze rozwiązań**.

    ![Zrzut ekranu przedstawiający Eksplorator rozwiązań z wyróżnionym plikiem Package. appxmanifest.](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Na karcie właściwości pakietu, która została otwarta, wybierz pozycję **Możliwości**, a następnie pozycję **Lokalizacja**.

    ![Zrzut ekranu przedstawiający okno dialogowe właściwości pakietu z wyróżnioną pozycją lokalizacja.](./media/notification-hubs-geofence/vs-package-location.png)
4. W rozwiązaniu utwórz nowy folder o nazwie `Core` i dodaj do niego nowy plik o nazwie `LocationHelper.cs`:

    ![Zrzut ekranu przedstawiający Eksplorator rozwiązań z wyróżnionym nowym folderem podstawowym.](./media/notification-hubs-geofence/vs-location-helper.png)

    Klasa `LocationHelper` zawiera kod umożliwiający uzyskiwanie lokalizacji użytkownika przy użyciu interfejsu API systemu:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    Aby dowiedzieć się więcej na temat pobierania lokalizacji użytkownika w aplikacjach platformy UWP, zobacz[Pobieranie lokalizacji użytkownika](/windows/uwp/maps-and-location/get-location).
5. Aby sprawdzić, czy uzyskiwanie lokalizacji rzeczywiście działa, otwórz stronę kodu strony głównej (`MainPage.xaml.cs`). Utwórz nowy program obsługi zdarzeń dla zdarzenia `Loaded` w konstruktorze `MainPage`.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    Implementacja programu obsługi zdarzeń wygląda następująco:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Uruchom aplikację i zezwól jej na dostęp do Twojej lokalizacji.

    ![Zrzut ekranu przedstawiający ogranicznik geograficzny Let Notification Hubs dostęp do lokalizacji okna dialogowego.](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Po uruchomieniu aplikacji współrzędne powinny być widoczne w oknie **Dane wyjściowe**:

    ![Zrzut ekranu przedstawiający okno dane wyjściowe ze współrzędnymi.](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Teraz wiesz, że usługa pozyskiwania lokalizacji działa, możesz usunąć załadowana procedurę obsługi zdarzeń, jeśli chcesz, ponieważ nie będzie ona już używana.
8. Następnym krokiem jest przechwytywanie zmian lokalizacji. W klasie `LocationHelper` dodaj program obsługi zdarzeń dla zdarzenia `PositionChanged`:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    Implementacja umożliwia wyświetlenie współrzędnych lokalizacji w oknie **Dane wyjściowe**:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>Konfigurowanie zaplecza

1. Pobierz [przykład zaplecza programu .NET z usługi GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).
2. Po zakończeniu pobierania otwórz folder `NotifyUsers`, a następnie otwórz plik `NotifyUsers.sln` w programie Visual Studio.
3. Ustaw projekt `AppBackend` jako **Projekt startowy**, a następnie uruchom go.

    ![Zrzut ekranu z menu prawym przyciskiem myszy z wyróżnioną opcją Ustaw jako projekt startowy.](./media/notification-hubs-geofence/vs-startup-project.png)

    Projekt jest już skonfigurowany do wysyłania powiadomień push do urządzeń docelowych, dlatego musimy wykonać tylko dwie czynności — podać odpowiednie parametry połączenia dla centrum powiadomień i dodać identyfikację granic, aby powiadomienie było wysyłane tylko wtedy, gdy użytkownik znajduje się w wirtualnym ogrodzeniu.

4. Aby skonfigurować parametry połączenia, w folderze `Models` otwórz plik `Notifications.cs`. Funkcja `NotificationHubClient.CreateClientFromConnectionString` powinna zawierać informacje o centrum powiadomień uzyskane w witrynie [Azure Portal](https://portal.azure.com) (na stronie **Zasady dostępu** w obszarze **Ustawienia**). Zapisz zaktualizowany plik konfiguracji.
5. Utwórz model dla wyniku interfejsu API Map Bing. Najprostszym sposobem na to otwarcie `Models` folderu i wybranie polecenia **Dodaj**  >  **klasę**. Nadaj jej nazwę `GeofenceBoundary.cs`. Następnie skopiuj kod JSON z odpowiedzi interfejsu API uzyskanej w pierwszej sekcji. W programie Visual Studio Użyj polecenie **Edytuj**  >  **Wklej specjalne**  >  **Wklej kod JSON jako klasy**.

    W ten sposób upewniamy się, że obiekt zostanie zdeserializowany dokładnie w zamierzony sposób. Wynikowy zestaw klas powinien wyglądać następująco:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Następnie otwórz plik `Controllers` > `NotificationsController.cs`. Zaktualizuj wywołanie metody POST, aby uwzględnić docelową długość i szerokość geograficzną. W tym celu po prostu dodaj dwa ciągi do sygnatury funkcji — `latitude` i `longitude`.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Utwórz nową klasę w projekcie o nazwie `ApiHelper.cs`. Zostanie ona użyta do połączenia z usługą Bing w celu sprawdzenia puntów przecięcia granic. Zaimplementuj funkcję `IsPointWithinBounds` zgodnie z poniższym kodem:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > Zastąp punkt końcowy interfejsu API adresem URL zapytania uzyskanym wcześniej z Centrum deweloperów Bing (to samo dotyczy klucza interfejsu API).

    Jeśli uzyskano wyniki zapytania, oznacza to, że określony punkt znajduje się w granicach wirtualnego ogrodzenia, dlatego funkcja zwraca wartość `true`. Jeśli nie ma wyników, usługa Bing informuje nas, że punkt znajduje się poza obszarem wyszukiwania, dlatego funkcja zwraca wartość `false`.
8. W pliku `NotificationsController.cs` utwórz operację sprawdzania przed instrukcją switch:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Testowanie powiadomień push w aplikacji platformy uniwersalnej systemu Windows

1. Teraz powinno być możliwe przetestowanie powiadomień w aplikacji platformy uniwersalnej systemu Windows. W klasie `LocationHelper` utwórz nową funkcję o nazwie `SendLocationToBackend`:

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > Ustaw zmienną `POST_URL` na wartość lokalizacji wdrożonej aplikacji internetowej. Teraz można uruchomić je lokalnie, ale podczas wdrażania wersji publicznej należy hostować ją z dostawcą zewnętrznym.
2. Zarejestruj aplikację platformy uniwersalnej systemu Windows dla usługi powiadomień push. W programie Visual Studio wybierz pozycję **Project**  >  **Store**  >  **Skojarz aplikację ze sklepem**.

    ![Zrzut ekranu z menu dostępnego po kliknięciu prawym przyciskiem myszy z wyróżnionymi opcjami Zapisz i skojarz aplikację ze sklepem.](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Po zalogowaniu się do konta dewelopera wybierz istniejącą aplikację lub utwórz nową i skojarz z nią pakiet.
4. Przejdź do Centrum deweloperów i otwórz utworzoną aplikację. Wybierz pozycję **usługi**  >  **powiadomienia wypychane**w  >  **witrynie usługi Live Services**.

    ![Zrzut ekranu Centrum deweloperów systemu Windows przedstawiający stronę powiadomienia wypychane z wyróżnioną lokacją usługi Live Services.](./media/notification-hubs-geofence/ms-live-services.png)
5. W witrynie zanotuj **Klucz tajny aplikacji** i **Identyfikator SID pakietu**. Musisz mieć oba te elementy w Azure Portal — Otwórz Centrum powiadomień, wybierz pozycję **Ustawienia**  >  **usługi powiadomień**  >  **Windows (WNS)** i wprowadź informacje w wymaganych polach.

    ![Zrzut ekranu przedstawiający stronę ustawień z wyróżnionymi opcjami usługi powiadomień i systemu Windows (WNS) oraz wypełnione wartości identyfikatora SID pakietu i klucza zabezpieczeń.](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Wybierz polecenie **Zapisz**.
7. Otwórz obszar **Odwołania** w **Eksploratorze rozwiązań** i wybierz pozycję **Zarządzaj pakietami NuGet**. Dodaj odwołanie do **biblioteki zarządzanej usługi Microsoft Azure Service Bus**. Wyszukaj pakiet `WindowsAzure.Messaging.Managed` i dodaj go do projektu.

    ![Zrzut ekranu przedstawiający okno dialogowe Zarządzanie pakietami NuGet z wyróżnionym pakietem WindowsAzure. Messaging. Managed.](./media/notification-hubs-geofence/vs-nuget.png)
8. W celach testowych możemy ponownie utworzyć program obsługi zdarzeń `MainPage_Loaded` i dodać do niego następujący fragment kodu:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    Powyższy kod rejestruje aplikację w centrum powiadomień. Gotowe!
9. W elemencie `LocationHelper` w programie obsługi `Geolocator_PositionChanged` możesz dodać fragment kodu, który będzie wymuszać umieszczanie lokalizacji w wirtualnym ogrodzeniu:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Ponieważ nie są używane rzeczywiste współrzędne (które mogą obecnie znajdować się poza granicami), lecz wstępnie zdefiniowane wartości testowe, po aktualizacji zostanie wyświetlone powiadomienie:

    ![Zrzut ekranu przedstawiający pulpit systemu Windows z wyświetlonym komunikatem TESTowym.](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Następne kroki

Jest kilka kroków, których wykonanie może być konieczne, aby mieć pewność, że rozwiązanie jest gotowe do zastosowania w środowisku produkcyjnym.

1. Najpierw musisz upewnić się, że wirtualne ogrodzenia są dynamiczne. To wymaga dodatkowej pracy z interfejsem API Bing w celu umożliwienia przekazywania nowych granic w ramach istniejącego źródła danych. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu API usług Bing Spatial Data Services](/bingmaps/spatial-data-services/).
2. Upewniając się, że powiadomienia są dostarczane do odpowiednich uczestników, warto adresować je przy użyciu [tagów](notification-hubs-tags-segment-push-message.md).

W tym samouczku opisano scenariusz, w którym może występować wiele różnych platform docelowych, dlatego nie ograniczono wirtualnego grodzenia do możliwości specyficznych dla określonego systemu. Jednak platforma uniwersalna systemu Windows oferuje wbudowane możliwości [wykrywania wirtualnych ogrodzeń](/windows/uwp/maps-and-location/set-up-a-geofence).
