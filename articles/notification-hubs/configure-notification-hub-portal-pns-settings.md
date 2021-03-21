---
title: Konfigurowanie powiadomień wypychanych na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować usługę Azure Notification Hubs w Azure Portal za pomocą ustawień systemu powiadomień platformy (PNS).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5dd1044895ba55d1fbc6be7f4f4a2d7f615daa16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94887267"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Szybki Start: Konfigurowanie powiadomień wypychanych w centrum powiadomień

Usługa Azure Notification Hubs udostępnia aparat wypychania, który jest łatwy w użyciu i skaluje się. Użyj Notification Hubs, aby wysyłać powiadomienia do dowolnej platformy (iOS, Android, Windows, Baidu) i z dowolnego zaplecza (w chmurze lub lokalnie). Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

W tym przewodniku szybki start użyjesz ustawień systemu powiadomień platformy (PNS) w Notification Hubs, aby skonfigurować powiadomienia wypychane na wielu platformach. W tym przewodniku szybki start przedstawiono kroki, które należy wykonać w Azure Portal. [Obsługa komunikatów w usłudze Google Firebase Cloud](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) zawiera instrukcje dotyczące korzystania z interfejsu wiersza polecenia platformy Azure.

Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md) lub [Tworzenie centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](create-notification-hub-azure-cli.md).

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Aby skonfigurować Apple Push Notification Service (APNS):

1. W Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Apple (APNs)** z menu po lewej stronie.

1. W obszarze **tryb uwierzytelniania** wybierz pozycję **certyfikat** lub **token**.

   a. W przypadku wybrania opcji **certyfikat**:
   * Wybierz ikonę pliku, a następnie wybierz plik *. p12* , który chcesz przekazać.
   * Wprowadź hasło.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu, wybierz pozycję Tryb **produkcyjny** .

     ![Zrzut ekranu przedstawiający konfigurację certyfikatu APNS w Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. W przypadku wybrania opcji **token**:

   * Wprowadź wartości dla **identyfikatora klucza**, **identyfikatora pakietu**, **identyfikatora zespołu** i **tokenu**.
   * Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu, wybierz pozycję Tryb **produkcyjny** .

     ![Zrzut ekranu przedstawiający konfigurację tokenu usługi APNS w Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby skonfigurować powiadomienia wypychane dla usługi Google FCM:

1. W Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Google (GCM/FCM)** z menu po lewej stronie.
2. Wklej **klucz interfejsu API** dla projektu Google FCM, który został wcześniej zapisany.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu pokazujący sposób konfigurowania Notification Hubs dla usługi Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po wykonaniu tych kroków alert wskazuje, że centrum powiadomień zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Potrzebny będzie **klucz interfejsu API** dla projektu Google Firebase Cloud Messaging (FCM).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Ten artykuł wymaga wersji 2.0.67 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

### <a name="set-up-push-notifications-for-google-fcm"></a>Konfigurowanie powiadomień wypychanych dla usługi Google FCM

1. Użyj polecenia [AZ Notification-Hub Credential GCM Update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) , aby dodać klucz usługi Google API do centrum powiadomień.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Aplikacja systemu Android wymaga parametrów połączenia w celu nawiązania połączenia z centrum powiadomień.  Użyj polecenia [AZ Notification-Hub Authorization-Rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) , aby wyświetlić listę dostępnych zasad dostępu.  Użyj polecenia [AZ Notification-Hub Authorization-Keys list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) , aby uzyskać parametry połączenia zasad dostępu.  Określ **primaryConnectionString** lub **secondaryConnectionString** w `--query` parametrze, aby uzyskać bezpośrednie parametry połączenia.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Za pomocą polecenia [AZ Notification-Hub test-Send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) przetestuj wysyłanie komunikatów do aplikacji systemu Android.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Pobierz odwołania interfejsu wiersza polecenia platformy Azure dla innych platform za pomocą poleceń [AZ Notification-Hub Credential](/cli/azure/ext/notification-hub/notification-hub/credential) .

Aby uzyskać więcej informacji na temat wysyłania powiadomień do aplikacji systemu Android, zobacz [wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu usługi Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

---

## <a name="windows-push-notification-service"></a>Usługa powiadomień wypychanych systemu Windows

Aby skonfigurować usługę powiadomień WNS (WNS):

1. W Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Windows (WNS)** z menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający pola identyfikatora SID pakietu i klucza zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień do aplikacji platformy UWP przy użyciu usługi Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Usługa powiadomień wypychanych firmy Microsoft dla Windows Phone

Aby skonfigurować usługę powiadomień wypychanych firmy Microsoft (usługi MPNS) dla Windows Phone:

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Windows Phone (usługi MPNS)** z menu po lewej stronie.
1. Włącz nieuwierzytelnione lub uwierzytelnione powiadomienia wypychane:

   a. Aby włączyć nieuwierzytelnione powiadomienia wypychane, zaznacz opcję **Włącz nieuwierzytelnione wypychanie**  >  .

      ![Zrzut ekranu pokazujący sposób włączania nieuwierzytelnionych powiadomień wypychanych](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Aby włączyć uwierzytelnione powiadomienia wypychane:
      * Na pasku narzędzi wybierz pozycję **Przekaż certyfikat**.
      * Wybierz ikonę pliku, a następnie wybierz plik certyfikatu.
      * Wprowadź hasło certyfikatu.
      * Wybierz przycisk **OK**.
      * Na stronie **Windows Phone (usługi MPNS)** wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych do Windows Phone aplikacji przy użyciu Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Aby skonfigurować powiadomienia wypychane dla Baidu:

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Baidu (Android China)** z menu po lewej stronie.
2. Wprowadź **klucz interfejsu API** uzyskany z konsoli Baidu w projekcie powiadomienia wypychane w chmurze Baidu.
3. Wprowadź **klucz tajny** uzyskany z konsoli Baidu w projekcie powiadomienia wypychane w chmurze Baidu.
4. Wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający Notification Hubs, w którym jest wyświetlana konfiguracja Baidu (Android China) dla powiadomień wypychanych](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Po wykonaniu tych kroków alert wskazuje, że centrum powiadomień zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony.

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z Notification Hubs przy użyciu Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób konfigurowania ustawień systemowych powiadomień platformy dla centrum powiadomień w Azure Portal.

Aby dowiedzieć się więcej o sposobie wypychania powiadomień do różnych platform, zobacz następujące samouczki:

* [Wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md)
* [Wysyłanie powiadomień do urządzeń z systemem Android przy użyciu usług Notification Hubs i Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Wysyłanie powiadomień do aplikacji platformy UWP działającej na urządzeniu z systemem Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Wysyłanie powiadomień do aplikacji Windows Phone 8 przy użyciu usługi MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Wysyłaj powiadomienia przy użyciu Notification Hubs i powiadomienia wypychane w chmurze Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
