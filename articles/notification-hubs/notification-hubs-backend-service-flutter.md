---
title: Wysyłanie powiadomień wypychanych do aplikacji Flutter przy użyciu usługi Azure Notification Hubs za pośrednictwem usług zaplecza | Microsoft Docs
description: Dowiedz się, jak wysyłać powiadomienia wypychane do aplikacji Flutter, które używają platformy Azure Notification Hubs za pośrednictwem usługi zaplecza.
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: miparker
ms.openlocfilehash: 5fa753a6b8b1284c4f8fcd046f74fabcbae3f8fb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171010"
---
# <a name="tutorial-send-push-notifications-to-flutter-apps-using-azure-notification-hubs-via-a-backend-service"></a>Samouczek: wysyłanie powiadomień wypychanych do aplikacji Flutter przy użyciu usługi Azure Notification Hubs za pośrednictwem usług zaplecza  

[![Pobierz przykład ](./media/notification-hubs-backend-service-flutter/download.png) Pobierz przykład](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

W tym samouczku użyjesz [usługi Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) do wypychania powiadomień do aplikacji [Flutter](https://flutter.dev) dla systemów **Android** i **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Ten samouczek przeprowadzi Cię przez następujące kroki:

> [!div class="checklist"]
>
> * [Skonfiguruj usługi powiadomień wypychanych i Notification Hubs platformy Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Utwórz aplikację ASP.NET Core Web API zaplecza.](#create-an-aspnet-core-web-api-backend-application)
> * [Utwórz międzyplatformową aplikację Flutter.](#create-a-cross-platform-flutter-application)
> * [Skonfiguruj natywny projekt systemu Android na potrzeby powiadomień wypychanych.](#configure-the-native-android-project-for-push-notifications)
> * [Skonfiguruj natywny projekt systemu iOS na potrzeby powiadomień wypychanych.](#configure-the-native-ios-project-for-push-notifications)
> * [Przetestuj rozwiązanie.](#test-the-solution)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

* [Subskrypcja platformy Azure](https://portal.azure.com) , w której można tworzyć zasoby i zarządzać nimi.
* Zestaw narzędzi [Flutter](https://flutter.dev/docs/get-started/install) (wraz z wymaganiami wstępnymi).
* [Visual Studio Code](https://code.visualstudio.com) z zainstalowanymi [wtyczkami Flutter i dart](https://flutter.dev/docs/get-started/editor?tab=vscode) .
* Możliwość uruchamiania aplikacji na urządzeniach z **systemem Android** (fizycznym lub emulatorem) lub **iOS** (tylko w przypadku urządzeń fizycznych).

W przypadku systemu Android należy dysponować:

* Deweloper odblokował urządzenie fizyczne lub emulator *(z zainstalowanym interfejsem API 26 lub nowszym i usługi Google Play)*.

W przypadku systemu iOS wymagane są:

* Aktywne [konto dewelopera firmy Apple](https://developer.apple.com).
* Fizyczne urządzenie z systemem iOS [zarejestrowane na koncie dewelopera](https://help.apple.com/developer-account/#/dev40df0d9fa) *(z systemem iOS 13,0 lub nowszym)*.
* [Certyfikat deweloperski](https://help.apple.com/developer-account/#/dev04fd06d56) **. p12** zainstalowany w **łańcuchu kluczy** umożliwia [Uruchamianie aplikacji na urządzeniu fizycznym](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).
* [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation) zainstalowano do zarządzania zależnościami biblioteki.

> [!NOTE]
> Symulator systemu iOS nie obsługuje powiadomień zdalnych, a więc urządzenie fizyczne jest wymagane podczas eksplorowania tego przykładu w systemie iOS. Nie trzeba jednak uruchamiać aplikacji w systemach **Android** i **iOS** , aby można było ukończyć ten samouczek.

Możesz wykonać kroki opisane w tym przykładzie pierwszej zasady bez wcześniejszego środowiska. Można jednak skorzystać z następujących aspektów.

* [Portal dla deweloperów firmy Apple](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Konsola usługi Google Firebase](https://console.firebase.google.com/u/0/)
* [Flutter](https://flutter.dev) i [dart](https://dart.dev) dla tworzenia aplikacji na wiele platform
* [Kotlin](https://kotlinlang.org) i [SWIFT](https://developer.apple.com/swift) dla programowania aplikacji natywnych dla systemów Android i iOS

Podane kroki są specyficzne dla [macOS](https://developer.apple.com/macos). W [systemie Windows](https://www.microsoft.com/windows) można postępować zgodnie z pominięciem aspektów systemu **iOS** .

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Konfigurowanie usług powiadomień wypychanych i centrum powiadomień platformy Azure

W tej sekcji skonfigurujesz usługę **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** i **[Apple Push Notification Services (APNs)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)**. Następnie można utworzyć i skonfigurować centrum powiadomień, aby współpracowało z tymi usługami.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Tworzenie aplikacji zaplecza interfejsu Web API ASP.NET Core

W tej sekcji utworzysz ASP.NET Core zaplecza internetowego [interfejsu API](https://dotnet.microsoft.com/apps/aspnet/apis) w celu obsługi [rejestracji urządzeń](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) i wysyłania powiadomień do aplikacji mobilnej Flutter.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-flutter-application"></a>Tworzenie aplikacji Flutter dla wielu platform

W tej sekcji utworzysz aplikację mobilną [Flutter](https://flutter.dev) implementującą powiadomienia wypychane w sposób Międzyplatformowy.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Flutter application](../../includes/notification-hubs-backend-service-sample-app-flutter.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Konfigurowanie natywnego projektu systemu Android na potrzeby powiadomień wypychanych

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-flutter-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Konfigurowanie natywnego projektu systemu iOS na potrzeby powiadomień wypychanych

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-flutter-ios.md)]

## <a name="test-the-solution"></a>Testowanie rozwiązania

Teraz można testować wysyłanie powiadomień za pośrednictwem usługi zaplecza.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Następne kroki

Teraz powinna być dostępna podstawowa aplikacja Flutter, która jest połączona z centrum powiadomień za pośrednictwem usługi zaplecza i może wysyłać i odbierać powiadomienia.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Linki pokrewne

* [Omówienie usługi Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Instalowanie Flutter na macOS](https://flutter.dev/docs/get-started/install/macos)
* [Instalowanie Flutter w systemie Windows](https://flutter.dev/docs/get-started/install/windows)
* [Notification Hubs SDK dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Notification Hubs SDK w witrynie GitHub](https://github.com/Azure/azure-notificationhubs)
* [Rejestrowanie za pomocą zaplecza aplikacji](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
* [Praca ze znacznikami](notification-hubs-tags-segment-push-message.md)
* [Praca z szablonami niestandardowymi](notification-hubs-templates-cross-platform-push-messages.md)
