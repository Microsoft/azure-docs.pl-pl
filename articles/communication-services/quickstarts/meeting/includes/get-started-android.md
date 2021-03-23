---
title: Szybki Start — Dodawanie dołączania do aplikacji dla zespołów z systemem Android przy użyciu usług Azure Communications Services
description: W tym przewodniku szybki start dowiesz się, jak używać biblioteki osadzania zespołów usługi Azure Communication Services dla systemu Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e9069b5d43044ef0d0341717a12fcce7c4a72dc7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104804006"
---
W tym przewodniku szybki start dowiesz się, jak przyłączyć się do spotkania zespołów przy użyciu biblioteki Osadź zespoły usługi Azure Communication Services dla systemu Android.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio), aby utworzyć aplikację dla systemu Android.
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- [Token dostępu użytkownika](../../access-tokens.md) dla usługi komunikacyjnej platformy Azure.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-android-app-with-an-empty-activity"></a>Tworzenie aplikacji dla systemu Android z pustym działaniem

W obszarze Android Studio wybierz pozycję Rozpocznij nowy Android Studio projekt.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Zrzut ekranu przedstawiający przycisk &quot;Rozpocznij nowy projekt Android Studio&quot; wybrany w Android Studio.":::

Wybierz szablon projektu "puste działanie" w obszarze "telefon i tablet".

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Zrzut ekranu przedstawiający opcję &quot;puste działanie&quot; wybraną na ekranie szablonu projektu.":::

Nazwij projekt `TeamsEmbedAndroidGettingStarted` , Ustaw język Java i wybierz minimalną bibliotekę klienta "API 21: Android 5,0 (lizak)" lub większą.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Zrzut ekranu przedstawiający opcję &quot;puste działanie&quot; wybraną na ekranie szablonu projektu 2.":::


### <a name="install-the-azure-package"></a>Zainstaluj pakiet platformy Azure

W kompilacji na poziomie aplikacji. Gradle Dodaj następujące wiersze do sekcji zależności i Android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Instalowanie pakietu osadzania zespołów

Pobierz `MicrosoftTeamsSDK` pakiet.

Następnie Rozpakuj folder MicrosoftTeamsSDK do folderu aplikacji Projects. Np. `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Dodaj zespoły Osadź pakiet do kompilacji. Gradle

Na poziomie aplikacji `build.gradle` Dodaj następujący wiersz na końcu pliku:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Synchronizuj projekt z plikami Gradle.

### <a name="create-application-class"></a>Utwórz klasę aplikacji

Utwórz nowy plik klasy języka Java o nazwie `TeamsEmbedAndroidGettingStarted` . Będzie to Klasa aplikacji, która musi zostać rozszerzona `TeamsSDKApplication` . [Dokumentacja systemu Android](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Zrzut ekranu przedstawiający miejsce tworzenia klasy aplikacji w Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Aktualizuj motywy

Ustaw nazwę stylu na `AppTheme` zarówno dla `theme.xml` plików, jak i `theme.xml (night)` .

:::image type="content" source="../media/android/theme-settings.png" alt-text="Zrzut ekranu przedstawiający pliki theme.xml w Android Studio":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Dodawanie uprawnień do manifestu aplikacji

Dodaj `RECORD_AUDIO` uprawnienie do manifestu aplikacji ( `app/src/main/AndroidManifest.xml` ):  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Dodawanie nazwy i motywu aplikacji do manifestu aplikacji

Dodaj element "xmlns: Tools =" http://schemas.android.com/tools "do manifestu.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Dodaj `.TeamsEmbedAndroidGettingStarted` do `android:name` , `android:name` do `tools:replace` i Zmień `android:theme` na `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Konfigurowanie układu aplikacji

Utwórz przycisk o IDENTYFIKATORze `join_meeting` . Przejdź do ( `app/src/main/res/layout/activity_main.xml` ) i Zastąp zawartość pliku następującym:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Tworzenie szkieletu i powiązań głównego działania

Po utworzeniu układu można dodać podstawowe szkielety działania wraz z wymaganymi powiązaniami. Działanie będzie obsługiwać żądania uprawnień środowiska uruchomieniowego, tworzenie klienta spotkania i dołączanie do spotkania po naciśnięciu przycisku. Każdy z nich zostanie objęty własną sekcją. 

`onCreate`Metoda zostanie przesłonięta na wartość Invoke `getAllPermissions` , a `createAgent` także Dodaj powiązania dla `Join Meeting` przycisku. To nastąpi tylko raz podczas tworzenia działania. Aby uzyskać więcej informacji na temat `onCreate` , zobacz Przewodnik dotyczący [cyklu życia działania](https://developer.android.com/guide/components/activities/activity-lifecycle).

Przejdź do **MAINS. Java** i Zastąp zawartość następującym kodem:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Żądaj uprawnień w czasie wykonywania

W przypadku systemu Android 6,0 i nowszego (poziom interfejsu API 23) i `targetSdkVersion` 23 lub nowszego uprawnienia są przyznawane w czasie wykonywania, a nie podczas instalowania aplikacji. Aby to umożliwić, `getAllPermissions` można zaimplementować w celu wywołania `ActivityCompat.checkSelfPermission` i `ActivityCompat.requestPermissions` dla każdego wymaganego uprawnienia.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Podczas projektowania aplikacji należy wziąć pod uwagę, kiedy te uprawnienia powinny być wymagane. Wymagane są uprawnienia, które są wymagane, a nie czasowo. Aby uzyskać więcej informacji, zobacz [Przewodnik po uprawnieniach systemu Android.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje biblioteki osadzanej zespołów usługi Azure Communications Services:

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| MeetingUIClient jest głównym punktem wejścia do biblioteki osadzania zespołów. |
| MeetingJoinOptions | MeetingJoinOptions są używane na potrzeby konfigurowalnych opcji, takich jak nazwa wyświetlana. |
| CallState | CallState jest używany do raportowania zmian stanu wywołań. Dostępne są następujące opcje: `connecting` , `waitingInLobby` , `connected` , i `ended` . |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Tworzenie MeetingClient z tokenu dostępu użytkownika

Uwierzytelnionego klienta spotkania można utworzyć przy użyciu tokenu dostępu użytkownika. Token ten jest zwykle generowany przez usługę z uwierzytelnianiem specyficznym dla aplikacji. Aby dowiedzieć się więcej o tokenach dostępu użytkowników, zapoznaj się z podręcznikiem [tokeny dostępu użytkowników](../../access-tokens.md) . Na potrzeby przewodnika Szybki Start Zastąp `<USER_ACCESS_TOKEN>` token dostępu użytkownika wygenerowany dla zasobu usługi Azure Communications Service.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Odświeżanie tokenu Instalatora

Utwórz metodę, która umożliwia wywoływanie `tokenRefresher` . Następnie Utwórz `fetchToken` metodę, aby uzyskać token użytkownika. [Instrukcje na ten temat można znaleźć tutaj](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Pobierz link do spotkania dla zespołów

Link do spotkania zespołów można pobrać przy użyciu interfejsów API programu Graph. Jest to szczegółowo opisany w [dokumentacji programu Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Biblioteka klienta wywołującego usługę komunikacyjną akceptuje łącze do spotkania z pełnymi zespołami. Ten link jest zwracany jako część `onlineMeeting` zasobu, który jest dostępny w ramach [ `joinWebUrl` Właściwości](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) , można także uzyskać wymagane informacje o spotkaniu z adresu URL **spotkania dołączania** w przypadku, gdy spotkanie zespołu się zaprosiło.

## <a name="start-a-meeting-using-the-meeting-client"></a>Rozpocznij spotkanie przy użyciu klienta spotkania

Dołączenie do spotkania może odbywać się za pośrednictwem `MeetingClient` elementu, a tylko `meetingURL` i `JoinOptions` . Zamień na `<MEETING_URL>` adres URL spotkania zespołów.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Uruchom aplikację i Dołącz do spotkania

Aplikację można teraz uruchomić przy użyciu przycisku "Uruchom aplikację" na pasku narzędzi (Shift + F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Zrzut ekranu przedstawiający ukończoną aplikację.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Zrzut ekranu przedstawiający ukończoną aplikację po dołączeniu spotkania.":::

## <a name="sample-code"></a>Przykładowy kod

Możesz pobrać przykładową aplikację z usługi [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started)
