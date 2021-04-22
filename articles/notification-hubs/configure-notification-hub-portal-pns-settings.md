---
title: Konfigurowanie powiadomień wypychanych w usłudze Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować usługę Azure Notification Hubs w Azure Portal przy użyciu ustawień systemu powiadomień platformy (PNS).
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
ms.openlocfilehash: 31d915cd44bcf60f3515eb1a84309980f45d40b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868302"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Szybki start: konfigurowanie powiadomień wypychanych w centrum powiadomień

Usługa Azure Notification Hubs udostępnia aparat wypychania, który jest łatwy w użyciu i skaluje się w zewnątrz. Użyj Notification Hubs do wysyłania powiadomień do dowolnej platformy (iOS, Android, Windows, Baidu) i z dowolnego zakańca (w chmurze lub lokalnie). Aby uzyskać więcej informacji, [zobacz Co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

W tym przewodniku Szybki start użyjesz ustawień systemu powiadomień platformy (PNS) w programie Notification Hubs do skonfigurowania powiadomień wypychanych na wielu platformach. W tym przewodniku Szybki start przedstawiono kroki do Azure Portal. [Usługa Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) zawiera instrukcje dotyczące korzystania z interfejsu wiersza polecenia platformy Azure.

Jeśli centrum powiadomień nie zostało jeszcze utworzone, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure](create-notification-hub-portal.md) w usłudze Azure Portal lub Tworzenie centrum powiadomień platformy Azure przy użyciu [interfejsu wiersza polecenia platformy Azure.](create-notification-hub-azure-cli.md)

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Aby skonfigurować Apple Push Notification Service (APNS):

1. W okienku Azure Portal na **stronie Centrum powiadomień** wybierz pozycję **Apple (APNS)** z menu po lewej stronie.

1. W **przypadku trybu uwierzytelniania** wybierz **pozycję Certyfikat lub** **Token.**

   a. W przypadku wybrania **opcji Certyfikat:**
   * Wybierz ikonę pliku, a następnie wybierz plik *p12,* który chcesz przekazać.
   * Wprowadź hasło.
   * Wybierz tryb **Piaskownica**. Aby wysłać powiadomienia wypychane do użytkowników, którzy kupili Twoją aplikację w sklepie, wybierz pozycję **Tryb** produkcyjny.

     ![Zrzut ekranu przedstawiający konfigurację certyfikatu usługi APNS w Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. W przypadku wybrania **opcji Token**:

   * Wprowadź wartości w **identyfikatorach klucza,** **identyfikatorze pakietu,** **identyfikatorze zespołu** i **tokenie**.
   * Wybierz tryb **Piaskownica**. Aby wysłać powiadomienia wypychane do użytkowników, którzy kupili Twoją aplikację w sklepie, wybierz pozycję **Tryb** produkcyjny.

     ![Zrzut ekranu przedstawiający konfigurację tokenu usługi APNS w Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Aby uzyskać więcej informacji, zobacz [Wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs.](ios-sdk-get-started.md)

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby skonfigurować powiadomienia wypychane dla usługi Google FCM:

1. W okienku Azure Portal na **stronie Centrum powiadomień** wybierz pozycję **Google (GCM/FCM)** z menu po lewej stronie.
2. Wklej klucz **interfejsu API** dla projektu Google FCM, który został zapisany wcześniej.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający sposób konfigurowania usługi Notification Hubs dla usługi Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po ukończeniu tych kroków alert wskazuje, że centrum powiadomień zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Będziesz potrzebować klucza **interfejsu API dla** projektu Google Firebase Cloud Messaging (FCM).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

### <a name="set-up-push-notifications-for-google-fcm"></a>Konfigurowanie powiadomień wypychanych dla usługi Google FCM

1. Użyj polecenia [az notification-hub credential gcm update,](/cli/azure/notification-hub/credential/gcm#az_notification_hub_credential_gcm_update) aby dodać klucz interfejsu API Google do centrum powiadomień.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Aplikacja systemu Android wymaga parametrów połączenia, aby nawiązać połączenie z centrum powiadomień.  Użyj polecenia [az notification-hub authorization-rule list,](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list) aby wyświetlić listę dostępnych zasad dostępu.  Użyj polecenia [az notification-hub authorization-rule list-keys,](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys) aby uzyskać parametry połączenia zasad dostępu.  Określ wartość **primaryConnectionString** lub **secondaryConnectionString** w parametrze , aby bezpośrednio pobrać `--query` podstawowe parametry połączenia.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Użyj polecenia [az notification-hub test-send,](/cli/azure/notification-hub#az_notification_hub_test_send) aby przetestować wysyłanie komunikatów do aplikacji systemu Android.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Pobierz odwołania interfejsu wiersza polecenia platformy Azure dla innych platform za pomocą [polecenia az notification-hub credential.](/cli/azure/notification-hub/credential)

Aby uzyskać więcej informacji na temat wysyłania powiadomień do aplikacji systemu Android, zobacz Wysyłanie powiadomień wypychanych do urządzeń [z systemem Android przy użyciu usługi Firebase.](notification-hubs-android-push-notification-google-fcm-get-started.md)

---

## <a name="windows-push-notification-service"></a>usługa powiadomień WNS

Aby skonfigurować usługa powiadomień WNS (WNS):

1. W okienku Azure Portal na **stronie Centrum powiadomień** wybierz pozycję **Windows (WNS)** z menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający pola Identyfikator SID pakietu i Klucz zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Aby uzyskać więcej informacji, zobacz [Wysyłanie powiadomień do aplikacji platformy UWP przy użyciu usługi Azure Notification Hubs.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Usługa powiadomień wypychanych firmy Microsoft dla Windows Phone

Aby skonfigurować Usługa powiadomień wypychanych firmy Microsoft (MPNS) dla Windows Phone:

1. W Azure Portal na stronie **Centrum** powiadomień wybierz pozycję **Windows Phone (MPNS)** z menu po lewej stronie.
1. Włącz nieuwierzytane lub uwierzytelnione powiadomienia wypychane:

   a. Aby włączyć nieuwierzytowane powiadomienia wypychane, wybierz **pozycję Włącz nieuwierzyskane wypychanie**  >  **Zapisz**.

      ![Zrzut ekranu przedstawiający sposób włączania nieuwierzytanych powiadomień wypychanych](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Aby włączyć uwierzytelnione powiadomienia wypychane:
      * Na pasku narzędzi wybierz pozycję **Przekaż certyfikat.**
      * Wybierz ikonę pliku, a następnie wybierz plik certyfikatu.
      * Wprowadź hasło certyfikatu.
      * Wybierz przycisk **OK**.
      * Na stronie **Windows Phone (MPNS)** wybierz pozycję **Zapisz.**

Aby uzyskać więcej informacji, zobacz [Push notifications to Windows Phone apps by using Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Aby skonfigurować powiadomienia wypychane dla aplikacji Baidu:

1. W okienku Azure Portal na **stronie Centrum powiadomień** wybierz pozycję **Baidu (Android China)** z menu po lewej stronie.
2. Wprowadź klucz **interfejsu API** uzyskany z konsoli Baidu w powiadomienia wypychane w chmurze Baidu projektu.
3. Wprowadź klucz **tajny uzyskany** z konsoli Baidu w powiadomienia wypychane w chmurze Baidu projektu.
4. Wybierz pozycję **Zapisz**.

    ![Zrzut ekranu Notification Hubs przedstawiający konfigurację usługi Baidu (Android China) dla powiadomień wypychanych](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Po ukończeniu tych kroków alert wskazuje, że centrum powiadomień zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony.

Aby uzyskać więcej informacji, zobacz Get started with Notification Hubs by using Baidu (Rozpoczynanie [pracy z Notification Hubs przy użyciu usługi Baidu).](notification-hubs-baidu-china-android-notifications-get-started.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób konfigurowania ustawień systemu powiadomień platformy dla centrum powiadomień w Azure Portal.

Aby dowiedzieć się więcej na temat sposobu wypychania powiadomień do różnych platform, zobacz następujące samouczki:

* [Wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md)
* [Wysyłanie powiadomień do urządzeń z systemem Android przy użyciu usługi Notification Hubs i usługi Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Wysyłanie powiadomień do aplikacji platformy UWP uruchomionej na urządzeniu z systemem Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Wysyłanie powiadomień do aplikacji Windows Phone 8 przy użyciu usługi MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Wysyłanie powiadomień przy użyciu Notification Hubs i powiadomienia wypychane w chmurze Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
