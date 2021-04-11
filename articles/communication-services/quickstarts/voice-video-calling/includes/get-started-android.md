---
title: Szybki Start — dodawanie telefonu VOIP do aplikacji systemu Android przy użyciu usług Azure Communications Services
description: W tym samouczku dowiesz się, jak używać zestawu SDK wywołań usługi Azure Communication Services dla systemu Android
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b68d3a92015a8add2dd97313f9971cf896f8e77e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108295"
---
W tym przewodniku szybki start dowiesz się, jak rozpocząć wywoływanie przy użyciu zestawu SDK wywołań usługi Azure Communication Services dla systemu Android.

> [!NOTE]
> Ten dokument używa wersji 1.0.0-beta. 8 zestawu SDK wywoływania.

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

Wybierz opcję minimalny zestaw SDK "API 26: Android 8,0 (OREO)" lub nowszy.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Zrzut ekranu przedstawiający opcję &quot;puste działanie&quot; wybraną na ekranie szablonu projektu 2.":::


### <a name="install-the-package"></a>Zainstaluj pakiet

<!-- TODO: update with instructions on how to download, install and add package to project -->
Znajdź na poziomie projektu Build. Gradle i upewnij się, że dodano `mavenCentral()` do listy repozytoriów w ramach `buildscript` i `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Następnie w kompilacji na poziomie modułu. Gradle Dodaj następujące wiersze do sekcji zależności i Android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>Dodawanie uprawnień do manifestu aplikacji

Aby zażądać uprawnień wymaganych do wykonania wywołania, należy najpierw je zadeklarować w manifeście aplikacji ( `app/src/main/AndroidManifest.xml` ). Zastąp zawartość pliku następującym:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our Calling SDK depends on the Apache HTTP SDK.
When targeting Android SDK 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>Konfigurowanie układu aplikacji

Są potrzeby dwa dane wejściowe: tekst wejściowy dla identyfikatora wywoływanego i przycisk służący do umieszczania wywołania. Mogą one być dodawane przez projektanta lub przez edycję kodu XML układu. Utwórz przycisk o IDENTYFIKATORze `call_button` i tekście wejściowym `callee_id` . Przejdź do ( `app/src/main/res/layout/activity_main.xml` ) i Zastąp zawartość pliku następującym:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Tworzenie szkieletu i powiązań głównego działania

Po utworzeniu układu można dodać powiązania oraz podstawowe szkielety działania. Działanie będzie obsługiwać żądania uprawnień środowiska uruchomieniowego, tworzenie agenta wywołań i umieszczanie wywołania po naciśnięciu przycisku. Każdy z nich zostanie objęty własną sekcją. `onCreate`Metoda zostanie przesłonięta na wartość Invoke `getAllPermissions` , a `createAgent` także Dodaj powiązania dla przycisku wywołania. To nastąpi tylko raz podczas tworzenia działania. Aby uzyskać więcej informacji na temat `onCreate` , zobacz Przewodnik dotyczący [cyklu życia działania](https://developer.android.com/guide/components/activities/activity-lifecycle).

Przejdź do **MAINS. Java** i Zastąp zawartość następującym kodem:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
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
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Podczas projektowania aplikacji należy wziąć pod uwagę, kiedy te uprawnienia powinny być wymagane. Wymagane są uprawnienia, które są wymagane, a nie czasowo. Aby uzyskać więcej informacji, zobacz [Przewodnik po uprawnieniach systemu Android.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK wywołującego usługi Azure Communications Services:

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient jest głównym punktem wejścia do wywołującego zestawu SDK.|
| CallAgent | CallAgent jest używany do uruchamiania wywołań i zarządzania nimi. |
| CommunicationUserCredential | CommunicationUserCredential jest używany jako poświadczenia tokenu do tworzenia wystąpienia CallAgent.|
| CommunicationIdentifier | CommunicationIdentifier jest używany jako inny typ uczestnika, który mógłby być częścią wywołania.|

## <a name="create-an-agent-from-the-user-access-token"></a>Tworzenie agenta na podstawie tokenu dostępu użytkownika

Za pomocą tokenu użytkownika można utworzyć wystąpienie agenta wywołania uwierzytelnionego. Zazwyczaj ten token zostanie wygenerowany z usługi z uwierzytelnianiem specyficznym dla aplikacji. Aby uzyskać więcej informacji o tokenach dostępu użytkowników, sprawdź Przewodnik po [tokenach dostępu użytkownika](../../access-tokens.md) . Na potrzeby przewodnika Szybki Start Zastąp `<User_Access_Token>` token dostępu użytkownika wygenerowany dla zasobu usługi Azure Communications Service.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Rozpocznij wywołanie przy użyciu agenta wywołań

Umieszczenie wywołania może odbywać się za pośrednictwem agenta wywołań i wystarczy podać listę identyfikatorów wywoływanych i opcji wywołań. Dla przewodnika Szybki Start zostanie użyta opcja domyślnego wywołania bez wideo i pojedynczego wywoływanego identyfikatora z tekstu wejściowego.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.startCall(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>Uruchom aplikację i Wywołaj echo bot

Aplikację można teraz uruchomić przy użyciu przycisku "Uruchom aplikację" na pasku narzędzi (Shift + F10). Sprawdź, czy masz możliwość nawiązywania wywołań przez wywołanie metody `8:echo123` . Zostanie odtworzony wstępnie zarejestrowany komunikat, a następnie powtórzony komunikat z powrotem.

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="Zrzut ekranu przedstawiający ukończoną aplikację.":::

## <a name="sample-code"></a>Przykładowy kod

Możesz pobrać przykładową aplikację z usługi [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling)
