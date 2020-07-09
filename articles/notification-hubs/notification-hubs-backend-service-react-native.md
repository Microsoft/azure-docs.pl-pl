---
title: Wysyłanie powiadomień wypychanych w celu reagowania na aplikacje natywne przy użyciu usługi Azure Notification Hubs za pośrednictwem usług zaplecza | Microsoft Docs
description: Dowiedz się, jak wypychanie powiadomień w celu reagowania na aplikacje natywne korzystające z usługi Azure Notification Hubs w ramach usługi zaplecza.
author: alexeystrakh
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alstrakh
ms.openlocfilehash: 007be386b7b64fd3461fa508d35a4ef9be377c1f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170915"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Samouczek: wysyłanie powiadomień wypychanych w celu reagowania na aplikacje natywne przy użyciu usługi Azure Notification Hubs za pośrednictwem usług zaplecza  

[![Pobierz przykład ](./media/notification-hubs-backend-service-react-native/download.png) Pobierz przykład](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

W tym samouczku użyjesz [usługi Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) , aby wypchnąć powiadomienia wypychane do aplikacji [natywnej reagującej](https://reactnative.dev/) na system **Android** i **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Ten samouczek przeprowadzi Cię przez następujące kroki:

> [!div class="checklist"]
>
> * [Skonfiguruj usługi powiadomień wypychanych i Notification Hubs platformy Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Utwórz aplikację ASP.NET Core Web API zaplecza.](#create-an-aspnet-core-web-api-backend-application)
> * [Utwórz międzyplatformową aplikację w aplikacji natywnej.](#create-a-cross-platform-react-native-application)
> * [Skonfiguruj natywny projekt systemu Android na potrzeby powiadomień wypychanych.](#configure-the-native-android-project-for-push-notifications)
> * [Skonfiguruj natywny projekt systemu iOS na potrzeby powiadomień wypychanych.](#configure-the-native-ios-project-for-push-notifications)
> * [Przetestuj rozwiązanie.](#test-the-solution)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

* [Subskrypcja platformy Azure](https://portal.azure.com) , w której można tworzyć zasoby i zarządzać nimi.
* Komputer Mac z zainstalowanym [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) (lub komputerem z programem [Visual Studio 2019](https://visualstudio.microsoft.com/vs) z **programowaniem mobilności przy użyciu obciążenia .NET** ).
* Możliwość uruchamiania aplikacji na urządzeniach z **systemem Android** (fizycznym lub emulatorem) lub **iOS** (tylko w przypadku urządzeń fizycznych).

W przypadku systemu Android należy dysponować:

* Deweloper odblokował urządzenie fizyczne lub emulator *(z zainstalowanym interfejsem API 26 lub nowszym i usługi Google Play)*.

W przypadku systemu iOS wymagane są:

* Aktywne [konto dewelopera firmy Apple](https://developer.apple.com).
* Fizyczne urządzenie z systemem iOS [zarejestrowane na koncie dewelopera](https://help.apple.com/developer-account/#/dev40df0d9fa) *(z systemem iOS 13,0 lub nowszym)*.
* [Certyfikat deweloperski](https://help.apple.com/developer-account/#/dev04fd06d56) **. p12** zainstalowany w **łańcuchu kluczy** umożliwia [Uruchamianie aplikacji na urządzeniu fizycznym](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).

> [!NOTE]
> Symulator systemu iOS nie obsługuje powiadomień zdalnych, a więc urządzenie fizyczne jest wymagane podczas eksplorowania tego przykładu w systemie iOS. Nie trzeba jednak uruchamiać aplikacji w systemach **Android** i **iOS** , aby można było ukończyć ten samouczek.

Możesz wykonać kroki opisane w tym przykładzie pierwszej zasady bez wcześniejszego środowiska. Można jednak skorzystać z następujących aspektów.

* [Portal dla deweloperów firmy Apple](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Konsola usługi Google Firebase](https://console.firebase.google.com/u/0/)
* [React Native](https://reactnative.dev/docs/getting-started)

Podane kroki dotyczą [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i [Visual Studio Code](https://code.visualstudio.com/download) ale można je wykonać przy użyciu [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Konfigurowanie usług powiadomień wypychanych i centrum powiadomień platformy Azure

W tej sekcji skonfigurujesz usługę **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** i **[Apple Push Notification Services (APNs)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)**. Następnie można utworzyć i skonfigurować centrum powiadomień, aby współpracowało z tymi usługami.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Tworzenie aplikacji zaplecza interfejsu Web API ASP.NET Core

W tej sekcji utworzysz ASP.NET Core zaplecza [interfejsu API sieci Web](https://dotnet.microsoft.com/apps/aspnet/apis) w celu obsługi [rejestracji urządzeń](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) i wysyłania powiadomień do natywnej aplikacji mobilnej.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Tworzenie aplikacji natywnej reagującej na wiele platform

W tej sekcji utworzysz natywną aplikację mobilną [reagującą](https://reactnative.dev/) na wdrożenie powiadomień wypychanych w sposób Międzyplatformowy.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](../../includes/notification-hubs-backend-service-sample-app-reactnative.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Konfigurowanie natywnego projektu systemu Android na potrzeby powiadomień wypychanych

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-reactnative-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Konfigurowanie natywnego projektu systemu iOS na potrzeby powiadomień wypychanych

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-reactnative-ios.md)]

## <a name="test-the-solution"></a>Testowanie rozwiązania

Teraz można testować wysyłanie powiadomień za pośrednictwem usługi zaplecza.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Następne kroki

Teraz powinna być dostępna podstawowa aplikacja do reagowania na reakcję, która jest połączona z centrum powiadomień za pośrednictwem usługi zaplecza i może wysyłać i odbierać powiadomienia.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Linki pokrewne

* [Omówienie usługi Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Instalowanie programu Visual Studio dla komputerów Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Instalowanie Visual Studio Code](https://code.visualstudio.com/download)
* [Konfigurowanie natywnego środowiska programistycznego do reagowania](https://reactnative.dev/docs/environment-setup)
* [Notification Hubs SDK dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Notification Hubs SDK w witrynie GitHub](https://github.com/Azure/azure-notificationhubs)
* [Rejestrowanie za pomocą zaplecza aplikacji](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
* [Praca ze znacznikami](notification-hubs-tags-segment-push-message.md)
* [Praca z szablonami niestandardowymi](notification-hubs-templates-cross-platform-push-messages.md)
