---
title: Zarządzanie rejestracją
description: W tym temacie wyjaśniono, jak zarejestrować urządzenia w centrach powiadomień w celu otrzymywania powiadomień wypychanych.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7807d28da459656938acb399eb8c621e4c292372
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89001573"
---
# <a name="registration-management"></a>Zarządzanie rejestracją

W tym temacie wyjaśniono, jak zarejestrować urządzenia w centrach powiadomień w celu otrzymywania powiadomień wypychanych. W temacie opisano rejestracje na wysokim poziomie, a następnie wprowadzono dwa główne wzorce rejestrowania urządzeń: rejestrowanie z urządzenia bezpośrednio do centrum powiadomień i rejestrowanie się w zapleczu aplikacji.

## <a name="what-is-device-registration"></a>Co to jest rejestracja urządzeń

Rejestracja urządzenia w centrum powiadomień odbywa się przy użyciu **rejestracji** lub **instalacji**.

### <a name="registrations"></a>Rejestracje

Rejestracja kojarzy obsługę usługi powiadomień platformy (PNS) dla urządzenia ze znacznikami i prawdopodobnie szablonem. Dojściem PNS może być identyfikator channeluri, token urządzenia lub Identyfikator rejestracji FCM. Tagi służą do kierowania powiadomień do poprawnego zestawu dojścia do urządzeń. Aby uzyskać więcej informacji, zobacz [Routing i wyrażenia tagów](notification-hubs-tags-segment-push-message.md). Szablony służą do implementowania transformacji na rejestrację. Aby uzyskać więcej informacji, zobacz [Szablony](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Usługa Azure Notification Hubs obsługuje maksymalnie 60 tagów na urządzenie.

### <a name="installations"></a>Instalacje

Instalacja to ulepszona Rejestracja obejmująca zbiór właściwości związanych z wypychaniem. Jest to najnowsze i najlepsze podejście do rejestracji urządzeń. Nie jest to jednak obsługiwane przez program .NET SDK po stronie klienta ([SDK Centrum powiadomień dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)).  Oznacza to, że jeśli rejestrujesz się z urządzenia klienckiego, należy użyć podejścia [interfejsu API REST Notification Hubs](/rest/api/notificationhubs/create-overwrite-installation) do obsługi instalacji. Jeśli używasz usługi wewnętrznej bazy danych, powinno być możliwe użycie [zestawu SDK Centrum powiadomień dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Poniżej przedstawiono kilka najważniejszych zalet używania instalacji:

- Tworzenie lub aktualizowanie instalacji jest w pełni idempotentne. Dzięki temu można ponowić próbę bez obaw o zduplikowane rejestracje.
- Model instalacji obsługuje specjalny format znacznika ( `$InstallationId:{INSTALLATION_ID}` ), który umożliwia wysyłanie powiadomień bezpośrednio do określonego urządzenia. Na przykład jeśli kod aplikacji ustawia identyfikator instalacji `joe93developer` dla tego konkretnego urządzenia, deweloper może kierować to urządzenie do tego urządzenia podczas wysyłania powiadomienia do `$InstallationId:{joe93developer}` znacznika. Pozwala to na określenie konkretnego urządzenia, bez konieczności wykonywania dodatkowych czynności programistycznych.
- Korzystanie z instalacji umożliwia również przeprowadzanie aktualizacji rejestracji częściowej. Częściowa aktualizacja instalacji jest wymagana z metodą PATCH przy użyciu [standardu JSON-patch](https://tools.ietf.org/html/rfc6902). Jest to przydatne, gdy chcesz zaktualizować Tagi rejestracji. Nie trzeba ściągać całej rejestracji, a następnie ponownie wysłać wszystkich powyższych tagów.

Instalacja może zawierać następujące właściwości. Aby zapoznać się z pełną listą właściwości instalacji, zobacz [Tworzenie lub zastępowanie instalacji przy użyciu interfejsu API REST](/rest/api/notificationhubs/create-overwrite-installation) lub [właściwości instalacji](/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Domyślnie rejestracje i instalacje nie wygasają.

Rejestracje i instalacje muszą zawierać prawidłowy uchwyt PNS dla każdego urządzenia/kanału. Ponieważ dojścia PNS można uzyskać tylko w aplikacji klienckiej na urządzeniu, jeden wzorzec jest rejestrowany bezpośrednio na tym urządzeniu przy użyciu aplikacji klienckiej. Z drugiej strony zagadnienia dotyczące zabezpieczeń i logika biznesowa związane ze znacznikami mogą wymagać zarządzania rejestracją urządzeń w zapleczu aplikacji.

> [!NOTE]
> Interfejs API instalacji nie obsługuje usługi Baidu (mimo że interfejs API rejestracji działa). 

### <a name="templates"></a>Szablony

Jeśli chcesz używać [szablonów](notification-hubs-templates-cross-platform-push-messages.md), instalacja urządzenia również zawiera wszystkie szablony skojarzone z danym urządzeniem w formacie JSON (Zobacz przykład powyżej). Nazwy szablonów ułatwiają Określanie różnych szablonów dla tego samego urządzenia.

Każda nazwa szablonu mapuje do treści szablonu i opcjonalnego zestawu tagów. Ponadto każda platforma może mieć dodatkowe właściwości szablonu. W przypadku sklepu Windows (przy użyciu WNS) i Windows Phone 8 (przy użyciu usługi MPNS) dodatkowy zestaw nagłówków może być częścią szablonu. W przypadku usługi APNs można ustawić właściwość wygaśnięcia na stałą lub wyrażenie szablonu. Aby zapoznać się z pełną listą właściwości instalacji, zobacz temat [Tworzenie lub zastępowanie instalacji przy użyciu REST](/rest/api/notificationhubs/create-overwrite-installation) .

### <a name="secondary-tiles-for-windows-store-apps"></a>Pomocnicze kafelki dla aplikacji ze sklepu Windows

W przypadku aplikacji klienckich do sklepu Windows wysyłanie powiadomień do kafelków pomocniczych jest takie samo, jak wysyłanie ich do podstawowego. Jest to również obsługiwane w instalacjach. Pomocnicze kafelki mają różne identyfikator channeluri, które zestaw SDK w aplikacji klienckiej obsługuje w sposób niewidoczny.

Słownik SecondaryTiles używa tego samego TileId, który jest używany do tworzenia obiektu SecondaryTiles w aplikacji ze sklepu Windows. Podobnie jak w przypadku podstawowego identyfikator channeluri, ChannelUris kafelków pomocniczych mogą ulec zmianie w dowolnym momencie. Aby można było zaktualizować instalacje w centrum powiadomień, urządzenie musi je odświeżyć przy użyciu bieżącego ChannelUris kafelków pomocniczych.

## <a name="registration-management-from-the-device"></a>Zarządzanie rejestracją z urządzenia

Podczas zarządzania rejestracją urządzenia z poziomu aplikacji klienckich zaplecze jest odpowiedzialna tylko za wysyłanie powiadomień. Aplikacje klienckie przechowują PNSą Aktualności i rejestrują Tagi. Poniższy rysunek ilustruje ten wzorzec.

![Rejestracja z urządzenia](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Urządzenie najpierw pobiera dojście PNS z PNS, a następnie rejestruje je bezpośrednio w centrum powiadomień. Po pomyślnym zarejestrowaniu zaplecze aplikacji może wysłać powiadomienie dotyczące tej rejestracji. Aby uzyskać więcej informacji o sposobach wysyłania powiadomień, zobacz [Routing i wyrażenia tagów](notification-hubs-tags-segment-push-message.md).

W takim przypadku użytkownik może uzyskać dostęp do centrów powiadomień z urządzenia tylko przy użyciu praw nasłuchiwania. Aby uzyskać więcej informacji, zobacz [zabezpieczenia](notification-hubs-push-notification-security.md).

Rejestrowanie z urządzenia jest najprostszą metodą, ale ma pewne wady:

- Aplikacja kliencka może aktualizować swoje Tagi tylko wtedy, gdy aplikacja jest aktywna. Na przykład jeśli użytkownik ma dwa urządzenia, które rejestrują Tagi związane z zespołami sportowymi, gdy pierwsze urządzenie rejestruje dla dodatkowego tagu (na przykład Seahawks), drugie urządzenie nie otrzyma powiadomień dotyczących Seahawks do momentu wykonania aplikacji na drugim urządzeniu. Bardziej ogólnie rzecz biorąc, gdy w przypadku tagów ma wpływ wiele urządzeń, zarządzanie tagami z zaplecza jest pożądaną opcją.
- Ponieważ aplikacje mogą być zaatakowana, zabezpieczenie rejestracji w określonych tagach wymaga dodatkowej opieki, jak wyjaśniono w artykule [zabezpieczenia](notification-hubs-push-notification-security.md)artykułu.

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Przykładowy kod do zarejestrowania z centrum powiadomień na urządzeniu przy użyciu instalacji

W tej chwili jest to obsługiwane tylko przy użyciu [interfejsu API REST Notification Hubs](/rest/api/notificationhubs/create-overwrite-installation).

Można również użyć metody PATCH przy użyciu [standardu JSON-patch](https://tools.ietf.org/html/rfc6902) do aktualizacji instalacji.

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored an installation ID in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Przykładowy kod do zarejestrowania się w centrum powiadomień na urządzeniu przy użyciu rejestracji

Te metody tworzą lub aktualizują rejestrację dla urządzenia, na którym są wywoływane. Oznacza to, że w celu zaktualizowania dojścia lub tagów należy zastąpić całą rejestrację. Należy pamiętać, że rejestracje są przejściowe, więc zawsze powinien istnieć niezawodny magazyn z bieżącymi tagami, które są określone dla konkretnego urządzenia.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
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

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Zarządzanie rejestracją z zaplecza

Zarządzanie rejestracjami z zaplecza wymaga napisania dodatkowego kodu. Aplikacja z urządzenia musi udostępnić zaktualizowane dojście PNS do zaplecza za każdym razem, gdy aplikacja zostanie uruchomiona (wraz ze znacznikami i szablonami), a zaplecze musi zaktualizować ten uchwyt w centrum powiadomień. Na poniższej ilustracji przedstawiono ten projekt.

![Zarządzanie rejestracją](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Zalety zarządzania rejestracjami z zaplecza obejmują możliwość modyfikowania tagów do rejestracji nawet wtedy, gdy odpowiednia aplikacja na urządzeniu jest nieaktywna i aby można było uwierzytelnić aplikację kliencką przed dodaniem znacznika do jego rejestracji.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Przykładowy kod do zarejestrowania się w centrum powiadomień z zaplecza przy użyciu instalacji

Urządzenie klienckie nadal pobiera uchwyt PNS oraz odpowiednie właściwości instalacji tak jak wcześniej i wywołuje niestandardowy interfejs API w zapleczu, który może przeprowadzić rejestrację i autoryzować Tagi itd. Zaplecze może korzystać z [zestawu SDK Centrum powiadomień dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Można również użyć metody PATCH przy użyciu [standardu JSON-patch](https://tools.ietf.org/html/rfc6902) do aktualizacji instalacji.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Przykładowy kod do zarejestrowania z centrum powiadomień na urządzeniu przy użyciu identyfikatora rejestracji

Z zaplecza aplikacji można wykonywać podstawowe operacje CRUDS na rejestracjach. Na przykład:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Zaplecze musi obsługiwać współbieżność między aktualizacjami rejestracji. Service Bus oferuje optymistyczną kontrolę współbieżności dla zarządzania rejestracją. Na poziomie protokołu HTTP jest to implementowane przy użyciu elementu ETag w operacjach zarządzania rejestracją. Ta funkcja jest w niewidoczny sposób używana przez zestawy SDK firmy Microsoft, które zgłasza wyjątek, jeśli aktualizacja zostanie odrzucona ze względów współbieżności. Zaplecze aplikacji jest odpowiedzialny za obsługę tych wyjątków i ponawianie próby aktualizacji, jeśli jest to konieczne.
