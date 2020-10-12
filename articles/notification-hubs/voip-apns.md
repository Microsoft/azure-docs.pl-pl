---
title: Wysyłanie powiadomień VOIP APNS za pomocą usługi Azure Notification Hubs
description: Dowiedz się, jak wysyłać powiadomienia VOIP APN za pomocą usługi Azure Notification Hubs (nie jest to oficjalnie obsługiwane).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80146890"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Korzystanie z VOIP usługi APNS za pomocą Notification Hubs (nie jest oficjalnie obsługiwane)

Można używać powiadomień VOIP usługi APNS za pomocą platformy Azure Notification Hubs; nie ma jednak oficjalnego wsparcia dla tego scenariusza.

## <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli nadal chcesz wysyłać powiadomienia VOIP usługi APNS za pomocą Notification Hubs, pamiętaj o następujących ograniczeniach:

- Wysyłanie powiadomienia VOIP wymaga, `apns-topic` Aby nagłówek miał ustawioną identyfikator pakietu aplikacji + `.voip` sufiks. Przykładowo dla przykładowej aplikacji z IDENTYFIKATORem pakietu `com.microsoft.nhubsample` `apns-topic` nagłówek powinien być ustawiony na `com.microsoft.nhubsample.voip.`

   Ta metoda nie działa poprawnie z usługą Azure Notification Hubs, ponieważ identyfikator pakietu aplikacji musi być skonfigurowany jako część poświadczeń APNS centrum i nie można zmienić wartości. Ponadto Notification Hubs nie zezwala na `apns-topic` przesłanianie wartości nagłówka w czasie wykonywania.

   Aby wysyłać powiadomienia VOIP, należy skonfigurować osobne centrum powiadomień z `.voip` identyfikatorem pakietu aplikacji.

- Wysyłanie powiadomienia VOIP wymaga, `apns-push-type` Aby nagłówek miał ustawioną wartość `voip` .

   Aby ułatwić klientom przejście do systemu iOS 13, Notification Hubs próbuje wnioskować o poprawność wartości `apns-push-type` nagłówka. Logika wnioskowania jest celowo prosta, co pozwala uniknąć przerywania standardowych powiadomień. Niestety, ta metoda powoduje problemy z powiadomieniami VOIP, ponieważ firma Apple traktuje powiadomienia VOIP jako przypadek specjalny, który nie jest zgodny z tymi samymi regułami co standardowe powiadomienia.

   Aby wysyłać powiadomienia VOIP, należy określić wartość jawną dla `apns-push-type` nagłówka.

- Notification Hubs ogranicza liczbę ładunków APN do 4 KB, zgodnie z opisem w firmie Apple. W przypadku powiadomień VOIP firma Apple zezwala na ładunki do 5 KB. Notification Hubs nie odróżnia się między powiadomieniami standardowymi i VOIP; w związku z tym wszystkie powiadomienia są ograniczone do 4 KB.

   Aby wysyłać powiadomienia VOIP, nie można przekroczyć limitu rozmiaru ładunku o rozmiarze 4 KB.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, skorzystaj z następujących linków:

- [Dokumentacja dla `apns-topic` i `apns-push-type` nagłówków oraz wartości, w tym specjalne przypadki powiadomień VoIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Dokumentacja limitu rozmiaru ładunku](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Aktualizacje Notification Hubs dla systemu iOS 13](push-notification-updates-ios-13.md#apns-push-type).
