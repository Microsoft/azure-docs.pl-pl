---
title: Aktualizacje platformy Azure Notification Hubs iOS 13 | Microsoft Docs
description: Dowiedz się więcej o wprowadzaniu zmian w systemie iOS 13 na platformie Azure Notification Hubs
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998054"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Aktualizacje usługi Azure Notification Hubs dla systemu iOS 13

Firma Apple wprowadziła niedawno pewne zmiany do ich publicznej usługi wypychania. zmiany są w większości wyrównane z wersjami systemu iOS 13 i Xcode. W tym artykule opisano wpływ tych zmian na platformie Azure Notification Hubs.

## <a name="apns-push-payload-changes"></a>Zmiany ładunku wypychania APNS

### <a name="apns-push-type"></a>Typ wypychania APNS

Firma Apple wymaga teraz, aby deweloperzy identyfikli powiadomienia jako alerty lub powiadomienia w tle za pomocą nowego `apns-push-type` nagłówka w interfejsie API usługi APNs. Zgodnie z [dokumentacją firmy Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "wartość tego nagłówka musi dokładnie odzwierciedlać zawartość ładunku powiadomienia. W przypadku niezgodności lub braku nagłówka w wymaganych systemach usługi APNs mogą zwrócić błąd, opóźnić dostarczenie powiadomienia lub usunąć je całkowicie. "

Deweloperzy muszą teraz ustawić ten nagłówek w aplikacjach wysyłających powiadomienia za pomocą usługi Azure Notification Hubs. Ze względu na ograniczenie techniczne klienci muszą używać uwierzytelniania opartego na tokenach dla poświadczeń usługi APNS z żądaniami, które zawierają ten atrybut. Jeśli używasz uwierzytelniania opartego na certyfikatach dla poświadczeń usługi APNS, musisz przełączyć się do korzystania z uwierzytelniania opartego na tokenach.

Poniższy przykład kodu pokazuje, jak ustawić ten atrybut nagłówka w żądaniach powiadomień wysyłanych za pomocą usługi Azure Notification Hubs.

#### <a name="template-notifications---net-sdk"></a>Powiadomienia dotyczące szablonów — zestaw SDK platformy .NET

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Powiadomienia natywne — zestaw SDK dla platformy .NET

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Bezpośrednie wywołania REST

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Aby pomóc w tym przejściu, gdy usługa Azure Notification Hubs wykryje powiadomienie, które nie ma `apns-push-type` ustawionego zestawu, usługi wnioskuje typ wypychania z żądania powiadomienia i ustawi wartość automatycznie. Należy pamiętać, że usługi Azure Notification Hubs należy skonfigurować do używania uwierzytelniania opartego na tokenach w celu ustawienia wymaganego nagłówka. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie oparte na tokenach (http/2) dla usługi APNs](./notification-hubs-push-notification-http2-token-authentication.md).

## <a name="apns-priority"></a>Priorytet usługi APNS

Inna niewielka zmiana, ale taka, która wymaga zmiany w aplikacji zaplecza, która wysyła powiadomienia, jest wymagana dla powiadomień w tle, a `apns-priority` nagłówek musi teraz mieć wartość 5. Wiele aplikacji ustawia `apns-priority` nagłówek na 10 (wskazujący natychmiastowe dostarczanie) lub nie ustawia go i pobiera wartość domyślną (10).

Ustawienie tej wartości na 10 nie jest już dozwolone w przypadku powiadomień w tle i należy ustawić wartość dla każdego żądania. Jeśli ta wartość nie zostanie podana, firma Apple nie będzie dostarczać powiadomień w tle. Na przykład:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Zmiany zestawu SDK

W latach, deweloperzy systemu iOS używały `description` atrybutu `deviceToken` danych wysyłanych do delegata tokenu wypychania, aby wyodrębnić token wypychania używany przez aplikację zaplecza do wysyłania powiadomień do urządzenia. W Xcode 11 ten `description` atrybut został zmieniony na inny format. Istniejący kod, który deweloperzy użył dla tego atrybutu, został teraz przerwany. Zaktualizowaliśmy zestaw SDK platformy Azure Notification Hubs w celu uwzględnienia tej zmiany. Zaktualizuj zestaw SDK używany przez aplikacje do wersji 2.0.4 lub nowszej [zestawu SDK platformy azure Notification Hubs iOS](https://github.com/Azure/azure-notificationhubs-ios).
