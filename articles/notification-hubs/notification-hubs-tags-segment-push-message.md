---
title: Routing i wyrażenia tagów na platformie Azure Notification Hubs
description: Dowiedz się, jak kierować i oznakować wyrażenia dla Notification Hubs platformy Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ff8225522e79e2be40682fb5e4823777dde2aa0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998307"
---
# <a name="routing-and-tag-expressions"></a>Routing i wyrażenia tagów

## <a name="overview"></a>Omówienie

Wyrażenia tagów umożliwiają kierowanie określonych zestawów urządzeń lub bardziej szczegółowych rejestracji podczas wysyłania powiadomień wypychanych za pomocą Notification Hubs.

## <a name="targeting-specific-registrations"></a>Kierowanie określonych rejestracji

Jedynym sposobem na kierowanie określonych rejestracji powiadomień jest skojarzenie z nimi tagów, a następnie określenie ich jako docelowej. Zgodnie z opisem w temacie [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)w celu otrzymywania powiadomień wypychanych, aplikacja musi zarejestrować dojście urządzenia w centrum powiadomień. Gdy aplikacja utworzy rejestrację w centrum powiadomień, zaplecze aplikacji może wysyłać do niej powiadomienia wypychane. Zaplecze aplikacji może wybrać rejestracje docelowe dla określonego powiadomienia w następujący sposób:

1. **Broadcast**: Wszystkie rejestracje w centrum powiadomień otrzymują powiadomienie.
2. **Tag**: Wszystkie rejestracje zawierające określony tag otrzymują powiadomienie.
3. **Wyrażenie tagu**: Wszystkie rejestracje, których zestaw tagów odpowiada określonemu wyrażeniu, otrzymuje powiadomienie.

## <a name="tags"></a>Tagi

Tag może być dowolnym ciągiem, do 120 znaków, zawierający alfanumeryczne i następujące znaki inne niż alfanumeryczne: " `_` ", "", " `@` `#` ", " `.` `:` ", `-` "", "". W poniższym przykładzie przedstawiono aplikację, z której można otrzymywać wyskakujące powiadomienia dotyczące określonych grup utworów muzycznych. W tym scenariuszu prostym sposobem na kierowanie powiadomień jest etykietowanie rejestracji ze znacznikami reprezentującymi różne grupy, jak na poniższej ilustracji:

![Przegląd tagów](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Na rysunku komunikat oznaczony za pomocą **Beatles** osiągnie tylko tablet zarejestrowany z tagiem **Beatles**.

Aby uzyskać więcej informacji o tworzeniu rejestracji dla tagów, zobacz [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md).

Powiadomienia można wysyłać do tagów przy użyciu metod wysyłania powiadomień `Microsoft.Azure.NotificationHubs.NotificationHubClient` klasy w zestawie SDK [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . Można również użyć Node.js lub interfejsów API REST powiadomień wypychanych.  Oto przykład użycia zestawu SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Nie można wstępnie zainicjować obsługi tagów i można odwoływać się do wielu koncepcji specyficznych dla aplikacji. Na przykład użytkownicy tej przykładowej aplikacji mogą komentować paski i chcą otrzymywać wyskakujące okienka, nie tylko w przypadku komentarzy do ich ulubionych grup, ale także dla wszystkich komentarzy od ich znajomych, niezależnie od pasma, do którego są komentowane. Na poniższej ilustracji przedstawiono przykład tego scenariusza:

![Znajomi z tagami](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

W tym przykładzie Alicja jest zainteresowana aktualizacjami Beatles, a Robert interesuje aktualizacje dla Wailers. Robert jest również zainteresowany komentarzami Charlie, a Charlie interesuje Wailers. Po wysłaniu powiadomienia dla komentarza Charlie na Beatles, Notification Hubs wysyła je do Alicja i Roberta.

Chociaż można kodować wiele obaw w tagach (na przykład `band_Beatles` lub `follows_Charlie` ), Tagi są prostymi ciągami i nie są właściwościami z wartościami. Rejestracja pasuje tylko do obecności lub braku określonego tagu.

Aby uzyskać pełny samouczek krok po kroku dotyczący sposobu używania tagów do wysyłania do grup zainteresowań, zobacz artykuł dotyczący [najświeższych wiadomości](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Usługa Azure Notification Hubs obsługuje maksymalnie 60 tagów na rejestrację.

## <a name="using-tags-to-target-users"></a>Używanie tagów do użytkowników docelowych

Innym sposobem używania tagów jest zidentyfikowanie wszystkich urządzeń skojarzonych z określonym użytkownikiem. Można oznaczyć rejestrację za pomocą tagu, który zawiera identyfikator użytkownika, tak jak na poniższym rysunku:

![Użytkownicy tagów](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Na rysunku komunikat otagowany `user_Alice` osiągnie wszystkie urządzenia oznaczone przy użyciu `user_Alice` .

## <a name="tag-expressions"></a>Wyrażenia tagów

Istnieją przypadki, w których powiadomienia muszą kierować zestawem rejestracji identyfikowanych nie przez pojedynczy tag, ale przez wyrażenie logiczne używające tagów.

Rozważmy aplikację sportową, która wysyła do wszystkich użytkowników w Boston informacje o grze między czerwoną SOX a Kardynalnami. Jeśli aplikacja kliencka rejestruje znaczniki dotyczące zainteresowania w zespołach i w lokalizacji, to powiadomienie powinno być skierowane do wszystkich osób w Boston, które interesują się czerwoną SOX lub Kardynalnami. Ten stan można wyrazić za pomocą następującego wyrażenia logicznego:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Wyrażenia tagów](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Wyrażenia tagów obsługują Typowe operatory logiczne, takie jak `AND` ( `&&` ), `OR` ( `||` ) i `NOT` ( `!` ); mogą również zawierać nawiasy. Wyrażenia tagów używające tylko `OR` operatorów mogą odwoływać się do 20 tagów; wyrażenia z `AND` operatorami, ale nie `OR` mogą odwoływać się do 10 tagów; w przeciwnym razie wyrażenia tagów są ograniczone do 6 tagów.

Oto przykład wysyłania powiadomień z wyrażeniami tagów przy użyciu zestawu SDK:

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
