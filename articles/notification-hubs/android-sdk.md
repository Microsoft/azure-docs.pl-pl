---
title: Wysyłanie powiadomień wypychanych do systemu Android przy użyciu usługi Azure Notification Hubs i zestawu SDK Firebase w wersji 1.0.0-zestawu
description: W tym samouczku dowiesz się, jak wysyłać powiadomienia wypychane do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Google Firebase Cloud Messaging.
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 63841bd603373d0fb325bcf82511ce3fb07b4136
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315202"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Samouczek: wysyłanie powiadomień wypychanych do urządzeń z systemem Android przy użyciu zestawu SDK Firebase w wersji 1.0.0-zestawu

W tym samouczku pokazano, jak używać usługi Azure Notification Hubs i zaktualizowanej wersji zestawu SDK Firebase Cloud Messaging (FCM) (wersja 1.0.0-zestawu) do wysyłania powiadomień wypychanych do aplikacji systemu Android. W tym samouczku utworzysz pustą aplikację dla systemu Android, która odbiera powiadomienia wypychane przy użyciu usługi Firebase Cloud Messaging (FCM).

Kompletny kod dla tego samouczka można pobrać z witryny [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh).

Ten samouczek obejmuje następujące kroki:

- Tworzenie projektu programu Android Studio.
- Tworzenie projektu Firebase obsługującego usługę Firebase Cloud Messaging.
- Tworzenie centrum powiadomień.
- Połącz aplikację z centrum.
- Testowanie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/free/).

Potrzebne są również następujące elementy:

- Zalecana jest Najnowsza wersja [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) .
- Minimalna obsługa to poziom interfejsu API 16.

## <a name="create-an-android-studio-project"></a>Tworzenie projektu Android Studio

Pierwszym krokiem jest utworzenie projektu w Android Studio:

1. Uruchom program Android Studio.

2. Wybierz pozycję **plik**, a następnie wybierz pozycję **Nowy**, a następnie **Nowy projekt**.

3. Na stronie **Wybierz projekt**   Wybierz pozycję **puste działanie**, a następnie wybierz pozycję **dalej**.

4. Na stronie **Konfiguruj projekt**   wykonaj następujące czynności:
   1. Wprowadź nazwę aplikacji.
   2. Określ lokalizację, w której mają zostać zapisane pliki projektu.
   3. Wybierz pozycję **Zakończ**.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Konfigurowanie projektu":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>Tworzenie projektu Firebase obsługującego usługę FCM

1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.

2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do aplikacji systemu Android**.

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Dodaj Firebase":::

3. Na stronie **Dodawanie Firebase do aplikacji systemu Android**   wykonaj następujące czynności:

   1. W polu **Nazwa pakietu systemu Android**skopiuj wartość identyfikator **aplikacji**   w pliku **Build. Gradle** aplikacji. W tym przykładzie jest to  `com.fabrikam.fcmtutorial1app` .

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Określ nazwę pakietu":::

   2. Wybierz pozycję **zarejestruj aplikację**.

4. Wybierz pozycję **pobierz google-services.jsna**, Zapisz plik w folderze **aplikacji**   projektu, a następnie wybierz przycisk **dalej**.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Pobierz usługę Google":::

5. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="Ustawienia projektu":::

6. Jeśli nie pobrano **google-services.js** pliku do folderu **aplikacji**   projektu Android Studio, można to zrobić na tej stronie.

7. Przejdź do karty usługi **Cloud Messaging**   .

8. Skopiuj i Zapisz **klucz serwera**   do późniejszego użycia. Ta wartość służy do konfigurowania centrum.

## <a name="configure-a-notification-hub"></a>Konfigurowanie centrum powiadomień

1. Zaloguj się do witryny  [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **wszystkie usługi**   , a następnie wybierz pozycję **Notification Hubs**   w sekcji **mobilnej**   . Wybierz ikonę gwiazdki obok nazwy usługi, aby dodać usługę do sekcji **Ulubione**   w menu po lewej stronie. Po dodaniu **Notification Hubs**   do **ulubionych**wybierz je w menu po lewej stronie.

3. Na stronie **Notification Hubs**   Wybierz pozycję **Dodaj**   na pasku narzędzi.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Dodawanie centrum":::

4. Na stronie **Notification Hubs**   wykonaj następujące czynności:

   1. Wprowadź nazwę w **centrum powiadomień**.

   2. Wprowadź nazwę w polu **Utwórz nową przestrzeń nazw**. Przestrzeń nazw zawiera co najmniej jedno centrum.

   3. Wybierz wartość z **Location**   listy rozwijanej lokalizacja. Ta wartość określa lokalizację, w której ma zostać utworzone centrum.

   4. Wybierz istniejącą grupę zasobów w **grupie zasobów**lub Utwórz nową.

   5. Wybierz pozycję  **Utwórz**.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Utwórz centrum":::

5. Wybierz pozycję **powiadomienia**   (ikona dzwonka), a następnie wybierz pozycję **Przejdź do zasobu**. Możesz również odświeżyć listę na stronie **Notification Hubs**   i wybrać centrum.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Wybierz centrum":::

6. Wybierz z listy pozycję **zasady dostępu**   . Należy zauważyć, że są dostępne dwa parametry połączenia. Będą one potrzebne później do obsługi powiadomień wypychanych.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Zasady dostępu":::

   > [!IMPORTANT]
   > Nie należy używać zasad **DefaultFullSharedAccessSignatureymi**   w aplikacji. Ta zasada ma być używana tylko w zapleczu aplikacji.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurowanie ustawień usługi Firebase Cloud Messaging na potrzeby centrum

1. W lewym okienku w obszarze **Ustawienia**   Wybierz pozycję **Google (GCM/FCM)**.

2. Wprowadź **klucz serwera**   dla projektu FCM, który został wcześniej zapisany.

3. Na pasku narzędzi wybierz pozycję **Zapisz**.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Klucz serwera":::

4. Azure Portal wyświetla komunikat informujący o tym, że centrum zostało pomyślnie zaktualizowane. Przycisk **Zapisz**   jest wyłączony.

Twoje centrum powiadomień jest teraz skonfigurowane do pracy z usługą Firebase Cloud Messaging. Istnieją również parametry połączenia, które są niezbędne do wysyłania powiadomień do urządzenia i rejestrowania aplikacji w celu otrzymywania powiadomień.

## <a name="connect-your-app-to-the-notification-hub"></a>Łączenie aplikacji z centrum powiadomień

### <a name="add-google-play-services-to-the-project"></a>Dodawanie usług Google Play do projektu

1. W Android Studio wybierz pozycję **Narzędzia**   z menu, a następnie wybierz pozycję **Menedżer zestawów SDK**.

2. Wybierz docelową wersję Android SDK, która jest używana w projekcie. Następnie wybierz pozycję **Pokaż szczegóły pakietu**.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="Menedżer zestawów SDK":::

3. Wybierz pozycję **Google API**, jeśli nie została jeszcze zainstalowana.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="Interfejsy API":::

4. Przejdź do karty **SDK Tools**   . Jeśli nie zainstalowano jeszcze Usługi Google Play, wybierz pozycję **usługi Google Play**,   jak pokazano na poniższej ilustracji. Następnie wybierz pozycję **Zastosuj**   do instalacji. Zanotuj ścieżkę zestawu SDK, która będzie potrzebna w kolejnym kroku.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Usługi odtwarzania":::

5. Jeśli zobaczysz okno dialogowe **Potwierdzanie zmiany**   , wybierz **przycisk OK**. Instalator składnika instaluje żądane składniki. Po zainstalowaniu składników wybierz pozycję **Zakończ**   .

6. Wybierz **przycisk OK**,   Aby zamknąć okno dialogowe **Ustawienia nowych projektów**   .

### <a name="add-azure-notification-hubs-libraries"></a>Dodawanie bibliotek Notification Hubs platformy Azure

1. W pliku **Build. Gradle** dla aplikacji Dodaj następujące wiersze w sekcji zależności:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk:1.0.0-preview1@aar'
   implementation 'androidx.appcompat:appcompat:1.0.0'

   implementation 'com.google.firebase:firebase-messaging:20.1.5'

   implementation 'com.android.volley:volley:1.1.1'
   ```

2. Dodaj następujące repozytorium po sekcji zależności:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Dodawanie obsługi usługi Google Firebase

1. Dodaj następującą wtyczkę na końcu pliku, jeśli jeszcze jej nie ma.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. Na pasku narzędzi wybierz pozycję **Synchronizuj teraz**   .

### <a name="add-code"></a>Dodaj kod

1. Utwórz obiekt **NotificationHubListener** , który obsługuje przechwytywanie komunikatów z platformy Azure Notification Hubs.

   ```csharp
   public class CustomNotificationListener implements NotificationHubListener {

      @override

      public void onNotificationReceived(Context context, NotificationMessage message) {

      /* The following notification properties are available. */

      String title = message.getTitle();
      String message = message.getMessage();
      Map<String, String> data = message.getData();

      if (message != null) {
         Log.d(TAG, "Message Notification Title: " + title);
         Log.d(TAG, "Message Notification Body: " + message);
         }
      }
   }
   ```

2. W  `OnCreate`   metodzie  `MainActivity`   klasy Dodaj następujący kod, aby uruchomić proces inicjowania Notification Hubs podczas tworzenia działania:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.initialize(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. W Android Studio na pasku menu wybierz pozycję **kompilacja**, a następnie wybierz pozycję **Skompiluj projekt**,   Aby upewnić się, że w kodzie nie ma błędów. Jeśli zostanie wyświetlony komunikat o błędzie dotyczący ikony **ic_launcher**   , Usuń następujące instrukcje z pliku AndroidManifest.xml:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Upewnij się, że masz urządzenie wirtualne do uruchamiania aplikacji. Jeśli go nie masz, Dodaj go w następujący sposób:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Menedżer urządzeń":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Urządzenia wirtualne":::
   3. Uruchom aplikację na wybranym urządzeniu i sprawdź, czy została ona pomyślnie zarejestrowana w centrum.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Rejestracja urządzenia":::

      > [!NOTE]
      > Rejestracja może zakończyć się niepowodzeniem podczas początkowego uruchamiania, dopóki nie `onTokenRefresh()` zostanie wywołana metoda usługi identyfikatora wystąpienia. Odświeżanie powinno inicjować pomyślne rejestrację w centrum powiadomień.

## <a name="send-a-test-notification"></a>Wysyłanie powiadomienia testowego

Powiadomienia wypychane można wysyłać do centrum powiadomień z [Azure Portal](https://portal.azure.com/)w następujący sposób:

1. W Azure Portal na stronie Centrum powiadomień centrum, wybierz pozycję **Testuj Wyślij**   w sekcji **Rozwiązywanie problemów**   .

2. Na **platformie**wybierz pozycję **Android**.

3. Wybierz pozycję **Wyślij**. Nie zobaczysz jeszcze powiadomienia na urządzeniu z systemem Android, ponieważ na nim nie uruchomiono aplikacji mobilnej. Po uruchomieniu aplikacji mobilnej ponownie wybierz przycisk **Wyślij**,   Aby zobaczyć komunikat powiadomienia.

4. Zapoznaj się z wynikami operacji na liście w dolnej części strony portalu.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Wyślij testowe powiadomienie":::

5. Zobaczysz komunikat powiadomienia na urządzeniu.

Powiadomienia wypychane są zwykle wysyłane w usłudze zaplecza, takiej jak Mobile Apps lub ASP.NET, przy użyciu zgodnej biblioteki. Jeśli biblioteka nie jest dostępna dla zaplecza, można także użyć interfejsu API REST bezpośrednio do wysyłania komunikatów powiadomień.

## <a name="run-the-mobile-app-on-emulator"></a>Uruchamianie aplikacji mobilnej na emulatorze

Przed przetestowaniem powiadomień wypychanych wewnątrz emulatora upewnij się, że obraz emulatora obsługuje poziom interfejsu API Google wybrany dla aplikacji. Jeśli obraz nie obsługuje natywnych interfejsów API Google, może wystąpić wyjątek **SERVICE_NOT_AVAILABLE**   .

Upewnij się również, że konto Google zostało dodane do działającego emulatora w obszarze **Ustawienia**   >  **konta**. W przeciwnym razie próby zarejestrowania się w usłudze FCM mogą spowodować wyjątek **AUTHENTICATION_FAILED**   .

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto usługi Firebase Cloud Messaging do rozgłaszania powiadomień do wszystkich urządzeń z systemem Android, które zostały zarejestrowane w usłudze. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Samouczek: wysyłanie powiadomień do określonych użytkowników](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Poniżej znajduje się lista innych samouczków dotyczących wysyłania powiadomień:

- Azure Mobile Apps: Aby zapoznać się z przykładem wysyłania powiadomień z Mobile Apps zaplecza zintegrowanego z usługą Notification Hubs, zobacz [Dodawanie powiadomień wypychanych do aplikacji systemu iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

- ASP.NET: [użyj Notification Hubs, aby wysyłać powiadomienia wypychane do użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- Azure Notification Hubs Java SDK: zobacz [, jak używać Notification Hubs języka Java](notification-hubs-java-push-notification-tutorial.md)   do wysyłania powiadomień z języka Java. To rozwiązanie przetestowano w programie Eclipse pod kątem tworzenia aplikacji dla systemu Android.

- PHP: [jak używać Notification Hubs w języku PHP](notification-hubs-php-push-notification-tutorial.md).