---
title: Wprowadzenie do Azure Maps Android SDK
description: Zapoznaj się z Android SDK Microsoft Azure Maps. Zobacz jak utworzyć projekt w Android Studio, zainstalować zestaw SDK i utworzyć mapę interaktywną.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531265"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Wprowadzenie do Azure Maps Android SDK

Android SDK Azure Maps jest biblioteką mapy wektorowej dla systemu Android. Ten artykuł przeprowadzi Cię przez proces instalowania Android SDK Azure Maps i ładowania mapy.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.
Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).
3. [Pobierz i zainstaluj Android Studio firmy Google](https://developer.android.com/studio/).

## <a name="create-a-project-in-android-studio"></a>Tworzenie projektu w Android Studio

Wykonaj następujące kroki, aby utworzyć projekt Android Studio:

1. Uruchom program Android Studio.
2. Kliknij pozycję **+ Utwórz nowy projekt**.
3. Na karcie **telefon i tablet** kliknij pozycję **puste działanie**. Kliknij przycisk **Dalej**.
4. W obszarze **Konfigurowanie projektu** wybierz `API 21: Android 5.0.0 (Lollipop)` jako minimalny zestaw SDK.
5. Wybierz `Java` jako język.
6. Zaakceptuj wartość domyślną `Name` dla projektu. Kliknij przycisk **Finish** (Zakończ).

Zapoznaj się z [dokumentacją Android Studio](https://developer.android.com/studio/intro/) , aby uzyskać pomoc dotyczącą instalowania Android Studio i tworzenia nowego projektu.

![Tworzenie projektu w programie Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Konfigurowanie urządzenia

Aby przetestować aplikację podczas opracowywania, można użyć telefonu z systemem Android lub emulatora systemu Android.

Aby dowiedzieć się więcej o konfigurowaniu AVD (urządzenie wirtualne z systemem Android), zobacz [dokumentację Android Studio](https://developer.android.com/studio/run/managing-avds).

## <a name="install-the-azure-maps-android-sdk"></a>Zainstaluj Android SDK Azure Maps

Następnym krokiem tworzenia aplikacji jest zainstalowanie Android SDK Azure Maps.

Wykonaj następujące kroki, aby zainstalować zestaw SDK:

1. Na karcie projekt rozwiń węzeł **skrypty Gradle**. Otwórz program **Build. Gradle (Project: My_Application)** i Dodaj następujący kod do sekcji **wszystkie projekty** `repositories`  :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Otwórz **Build. Gradle (moduł: My_Application)**.

3. Upewnij się, że **minSdkVersion** w `defaultConfig` sekcji jest w interfejsie API w wersji 21 lub nowszej.

4. Dodaj następujący kod do sekcji systemu Android:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Dodaj następujący kod do `dependencies` sekcji:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Kliknij pozycję **plik** na głównym pasku narzędzi, a następnie wybierz pozycję **Synchronizuj projekt z plikami Gradle**.

7. Otwórz plik `res > layout > activity_main.xml`. Kliknij przycisk `Code` Wyświetl w prawym górnym rogu. Dodaj następujący kod XML wewnątrz `<androidx.constraintlayout.widget.ConstraintLayout>` elementu.
    
    ```XML
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

8. W `java > com.example.myapplication > MainActivity.java` pliku należy:

    * Dodaj Importy dla zestawu SDK Azure Maps.
    * Ustaw Azure Maps informacje o uwierzytelnianiu.
    * Pobierz wystąpienie kontrolki mapy w metodzie **OnCreate** .

    Aby uniknąć konieczności dodawania informacji o uwierzytelnianiu dla każdego widoku aplikacji, skonfigurujemy informacje uwierzytelniania globalnie przez wywołanie `AzureMaps.setSubscriptionKey` . Możesz również wywołać `AzureMaps.setAadProperties` , jeśli chcesz uwierzytelnić się przy użyciu Azure Active Directory.

    Kontrolka mapy zastępuje następujące metody cyklu życia klasy MAINS. Te metody są odpowiedzialne za zarządzanie cyklem życia OpenGL dla systemu Android.

    * OnCreate (pakiet)
    * OnStart ()
    * onresume ()
    * OnPause ()
    * onStop ()
    * OnDestroy ()
    * onSaveInstanceState (pakiet)
    * onLowMemory()

    Edytuj `MainActivity.java` plik w następujący sposób:

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {

        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.

            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

>[!WARNING]
>Nie zaimportowano wymaganych klas Android Studio.  W związku z tym kod będzie zawierał pewne odwołania nierozpoznawalne. Aby zaimportować wymagane klasy, po prostu umieść kursor nad każdym nierozwiązanym odwołaniem i naciśnij klawisz `Alt + Enter` (Option + Return na komputerze Mac).

Kompilowanie aplikacji Android Studio potrwa kilka sekund. Po zakończeniu kompilacji można przetestować aplikację na emulowanym urządzeniu z systemem Android. Powinna zostać wyświetlona mapa przypominająca:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps w aplikacji systemu Android":::

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Azure Maps Android SDK oferuje trzy różne sposoby ustawiania języka i ustawień regionalnych mapy.

1. Ustawianie języka i ustawień regionalnych poprzez wywoływanie metod statycznych w klasie AzureMaps.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Zdefiniuj język i ustawienia regionalne w kodzie XML formantu mapy.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Ustawianie języka i ustawień regionalnych przez wywoływanie metod w formancie mapy. Ta opcja umożliwia zmianę ustawień w czasie wykonywania.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Oto przykład Azure Maps z językiem ustawionym na `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, obraz mapy przedstawiający etykiety w języku francuskim":::

Pełna lista obsługiwanych języków i widoków regionalnych została udokumentowana [tutaj](supported-languages.md).

## <a name="navigating-the-map"></a>Nawigowanie po mapie

Istnieje kilka różnych sposobów, w których mapowanie może być powiększane, przesuwane, obracane i mierzone. Poniżej przedstawiono szczegółowe informacje na temat różnych sposobów nawigowania po mapie.

**Powiększ mapę**

- Dotknij mapy dwoma palcami i szczypania razem, aby powiększyć lub rozłożyć palcami z zewnątrz, aby powiększyć.
- Naciśnij dwukrotnie mapę, aby powiększyć na jednym poziomie.
- Naciśnij dwukrotnie dwa palcami, aby powiększyć mapę o jeden poziom.
- Naciśnij dwukrotnie; po naciśnięciu klawisza, przytrzymaj palec na mapie i przeciągnij w górę, aby powiększyć, lub w dół, aby pomniejszyć.

**Panoramowanie mapy**

- Dotknij mapy i przeciągnij w dowolnym kierunku.

**Obróć mapę**

- Dotknij mapy dwoma palcami i Obróć.

**Wysokość mapy**

- Dotknij mapy dwoma palcami i przeciągnij je w górę lub w dół.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dane nakładki na mapie:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli do mapy systemu Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Zmień style mapy w usłudze mapy systemu Android](./set-android-map-styles.md)