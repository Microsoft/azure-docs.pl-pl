---
title: Wysyłanie zabezpieczonych powiadomień wypychanych za pomocą usługi Azure Notification Hubs
description: Dowiedz się, jak wysyłać bezpieczne powiadomienia wypychane do aplikacji systemu Android z platformy Azure. Przykłady kodu zapisywane w języku Java i C#.
documentationcenter: android
keywords: powiadomienia wypychane, powiadomienia wypychane, komunikaty wypychane, powiadomienia wypychane systemu Android
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f2d5d618fabbe7400ce825f984ace1622a524f05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88004024"
---
# <a name="send-secure-push-notifications-with-azure-notification-hubs"></a>Wysyłanie zabezpieczonych powiadomień wypychanych za pomocą usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Obsługa powiadomień wypychanych w programie Microsoft Azure pozwala uzyskać dostęp do łatwej w skalowaniu infrastruktury komunikatów wypychanych z obsługą wielu platform, która znacznie upraszcza implementację powiadomień wypychanych zarówno dla aplikacji dla klientów, jak i dla przedsiębiorstw dla platform mobilnych.

Ze względu na ograniczenia prawne lub zabezpieczenia czasami aplikacja może chcieć uwzględnić coś w powiadomieniu, którego nie można przesłać za pomocą standardowej infrastruktury powiadomień wypychanych. W tym samouczku opisano sposób osiągnięcia tego samego środowiska przez wysyłanie poufnych informacji za pomocą bezpiecznego, uwierzytelnionego połączenia między klientem a urządzeniem z systemem Android i zapleczem aplikacji.

Na wysokim poziomie przepływ jest następujący:

- Zaplecze aplikacji:
  * Przechowuje bezpieczny ładunek w bazie danych zaplecza.
  * Wysyła identyfikator tego powiadomienia do urządzenia z systemem Android (żadne zabezpieczone informacje nie są wysyłane).
- Aplikacja na urządzeniu podczas otrzymywania powiadomienia:
  * Urządzenie z systemem Android nawiązuje połączenie z zapleczem bezpiecznym.
  * Aplikacja może wyświetlać ładunek jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływie (i w tym samouczku) zakłada się, że urządzenie przechowuje token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Takie podejście gwarantuje bezproblemowe działanie, ponieważ urządzenie może pobrać bezpieczny ładunek powiadomienia przy użyciu tego tokenu. Jeśli aplikacja nie przechowuje tokenów uwierzytelniania na urządzeniu lub jeśli te tokeny mogą wygasnąć, aplikacja urządzenia po odebraniu powiadomienia wypychanego powinna wyświetlić ogólne powiadomienie z monitem użytkownika o uruchomienie aplikacji. Następnie aplikacja uwierzytelnia użytkownika i wyświetla ładunek powiadomienia.

W tym samouczku pokazano, jak wysyłać bezpieczne powiadomienia wypychane. Jest ona oparta na samouczku [Powiadamianie użytkowników](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , dlatego należy najpierw wykonać kroki opisane w tym samouczku.

> [!NOTE]
> W tym samouczku przyjęto założenie, że utworzono i skonfigurowano centrum powiadomień zgodnie z opisem w artykule wprowadzenie [do Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modyfikuj projekt systemu Android

Po zmodyfikowaniu zaplecza aplikacji w celu wysłania tylko identyfikatora powiadomienia wypychanego należy zmienić aplikację dla systemu Android w taki sposób, aby obsługiwała to powiadomienie, i wywoływać ją ponownie w celu pobrania bezpiecznej wiadomości do wyświetlenia.
Aby osiągnąć ten cel, należy się upewnić, że aplikacja dla systemu Android wie, jak uwierzytelnić się w zapleczu po odebraniu powiadomień wypychanych.

Teraz zmodyfikuj przepływ logowania w celu zapisania wartości nagłówka uwierzytelniania w preferencjach udostępnionych aplikacji. Analogiczne mechanizmy mogą służyć do przechowywania dowolnego tokenu uwierzytelniania (na przykład tokenów OAuth), którego aplikacja musi używać, bez poświadczeń użytkownika.

1. W projekcie aplikacji systemu Android Dodaj następujące stałe w górnej części `MainActivity` klasy:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```

2. W `MainActivity` klasie należy zaktualizować metodę tak, `getAuthorizationHeader()` aby zawierała następujący kod:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```

3. Dodaj następujące `import` instrukcje w górnej części `MainActivity` pliku:

    ```java
    import android.content.SharedPreferences;
    ```

Teraz zmień procedurę obsługi, która jest wywoływana po odebraniu powiadomienia.

1. W `MyHandler` klasie Zmień metodę, `OnReceive()` aby zawierała:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```

2. Następnie Dodaj `retrieveNotification()` metodę, zastępując symbol zastępczy `{back-end endpoint}` punktem końcowym, który został uzyskany podczas wdrażania zaplecza:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Ta metoda wywołuje zaplecze aplikacji, aby pobrać zawartość powiadomienia przy użyciu poświadczeń przechowywanych w preferencjach udostępnionych i wyświetlać ją jako zwykłe powiadomienie. Powiadomienie będzie wyglądało użytkownikowi aplikacji tak samo jak inne powiadomienia wypychane.

Zalecane jest, aby obsłużyć przypadki brakującej właściwości nagłówka uwierzytelniania lub odrzucenia przez zaplecze. Konkretna obsługa tych przypadków w większości zależy od środowiska użytkownika docelowego. Jedną z opcji jest wyświetlenie powiadomienia z ogólnym monitem użytkownika o uwierzytelnienie w celu pobrania rzeczywistego powiadomienia.

## <a name="run-the-application"></a>Uruchom aplikację

Aby uruchomić aplikację, wykonaj następujące czynności:

1. Upewnij się, że **AppBackend** jest wdrożony na platformie Azure. Jeśli używasz programu Visual Studio, uruchom aplikację internetowego interfejsu API **AppBackend** . Zostanie wyświetlona strona sieci Web ASP.NET.
2. W programie zaćmienie Uruchom aplikację na fizycznym urządzeniu z systemem Android lub w emulatorze.
3. W interfejsie użytkownika aplikacji systemu Android wprowadź nazwę użytkownika i hasło. Mogą to być dowolne ciągi, ale muszą mieć tę samą wartość.
4. W interfejsie użytkownika aplikacji systemu Android kliknij przycisk **Zaloguj**. Następnie kliknij pozycję **Wyślij wypychanie**.
