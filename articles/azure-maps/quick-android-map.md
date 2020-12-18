---
title: 'Szybki Start: Tworzenie aplikacji dla systemu Android za pomocą Azure Maps | Microsoft Azure '
description: 'Szybki Start: informacje na temat tworzenia aplikacji dla systemu Android przy użyciu Android SDK Azure Maps.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: mvc
ms.openlocfilehash: 874da5ffcd2777648bb06c6e702932d64a32e59c
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682002"
---
# <a name="quickstart-create-an-android-app-with-azure-maps"></a>Szybki Start: Tworzenie aplikacji dla systemu Android za pomocą Azure Maps

W tym artykule opisano sposób dodawania Azure Maps do aplikacji systemu Android. Przeprowadza on użytkownika przez następujące podstawowe kroki:

* Skonfiguruj środowisko deweloperskie.
* Tworzenie konta usługi Azure Maps.
* Pobierz klucz podstawowy Azure Maps, który będzie używany w aplikacji.
* Odwołuje się do bibliotek Azure Maps z projektu.
* Dodaj kontrolkę Azure Maps do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

1. Utwórz konto Azure Maps, logując się do [Azure Portal](https://portal.azure.com). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).
4. [Pobierz Android Studio](https://developer.android.com/studio/) bezpłatnie z usługi Google.

## <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Utwórz nowe konto Azure Maps, wykonując następujące czynności:

1. W lewym górnym rogu witryny [Azure Portal](https://portal.azure.com) kliknij przycisk **Utwórz zasób**.
2. W polu *Wyszukaj w witrynie Marketplace* wpisz **Azure Maps**.
3. Z *wyników* wybierz pozycję **Azure Maps**. Kliknij przycisk **Utwórz** znajdujący się poniżej mapy.
4. Na stronie **Tworzenie konta usługi Maps** wprowadź następujące wartości:
    * *Subskrypcja*, która ma być używana dla tego konta.
    * Nazwa *grupy zasobów* dla tego konta. Można wybrać pozycję *Utwórz nowe* lub *Użyj istniejącego* dla grupy zasobów.
    * *Nazwa* nowego konta.
    * *Warstwa cenowa* dla tego konta.
    * Zapoznaj się z *Licencją* oraz *Zasadami zachowania poufności informacji* i zaznacz pole wyboru, aby zaakceptować warunki.
    * Kliknij przycisk **Utwórz**.

    ![Tworzenie konta usługi Maps w portalu](media/quick-android-map/create-account.png)

## <a name="get-the-primary-key-for-your-account"></a>Pobieranie klucza podstawowego konta

Po pomyślnym utworzeniu konta Maps Pobierz klucz podstawowy, który umożliwia wykonywanie zapytań dotyczących interfejsów API map.

1. Otwórz konto usługi Maps w portalu.
2. W sekcji Ustawienia wybierz pozycję **uwierzytelnianie**.
3. Skopiuj **klucz podstawowy** do schowka. Zapisz go lokalnie — będzie używany w dalszej części tego samouczka.

>[!NOTE]
> Jeśli używasz klucza subskrypcji platformy Azure zamiast Azure Maps klucz podstawowy, mapa nie będzie renderowana prawidłowo. Ponadto ze względów bezpieczeństwa zaleca się obracanie między kluczami podstawowymi i pomocniczymi. Aby obrócić klucze, zaktualizuj aplikację tak, aby korzystała z klucza pomocniczego, wdróż, a następnie naciśnij przycisk Cykl/Odśwież obok klucza podstawowego, aby wygenerować nowy klucz podstawowy. Stary klucz podstawowy zostanie wyłączony. Aby uzyskać więcej informacji na temat rotacji kluczy, zobacz [konfigurowanie Azure Key Vault przy użyciu rotacji kluczy i inspekcji](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring)

![Pobierz klucz podstawowy Azure Maps w Azure Portal](media/quick-android-map/get-key.png)

## <a name="create-a-project-in-android-studio"></a>Tworzenie projektu w Android Studio

Najpierw utwórz nowy projekt z pustym działaniem. Wykonaj następujące kroki, aby utworzyć projekt Android Studio:

1. W obszarze **Wybierz projekt** wybierz pozycję **telefon i tablet**. Aplikacja będzie działać na tym formularzu.
2. Na karcie **telefon i tablet** wybierz pozycję **puste działanie**, a następnie wybierz przycisk **dalej**.
3. W obszarze **Konfigurowanie projektu** wybierz `API 21: Android 5.0.0 (Lollipop)` jako minimalny zestaw SDK. Jest to Najwcześniejsza wersja obsługiwana przez Android SDK Azure Maps.
4. Zaakceptuj wartość domyślną `Activity Name` i `Layout Name` Wybierz pozycję **Zakończ**.

Zapoznaj się z [dokumentacją Android Studio](https://developer.android.com/studio/intro/) , aby uzyskać pomoc dotyczącą instalowania Android Studio i tworzenia nowego projektu.

![Tworzenie projektu w programie Android Studio](media/quick-android-map/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Konfigurowanie urządzenia wirtualnego

Android Studio umożliwia skonfigurowanie wirtualnego urządzenia z systemem Android na komputerze. Wykonanie tej czynności może pomóc w testowaniu aplikacji podczas jej tworzenia. Aby skonfigurować urządzenie wirtualne, wybierz ikonę Menedżera urządzeń wirtualnych systemu Android (AVD) w prawym górnym rogu ekranu projektu, a następnie wybierz pozycję **Utwórz urządzenie wirtualne**. Możesz również uzyskać dostęp do Menedżera AVD, wybierając pozycję **Narzędzia**  >  **Android**  >  **AVD Manager** na pasku narzędzi. W kategorii **telefony** wybierz pozycję **Nexus pięciokrotną**, a następnie wybierz pozycję **Next (dalej**).

Więcej informacji na temat konfigurowania AVD można znaleźć w [dokumentacji Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulator systemu Android](media/quick-android-map/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Zainstaluj Android SDK Azure Maps

Następnym krokiem tworzenia aplikacji jest zainstalowanie Android SDK Azure Maps. Wykonaj następujące kroki, aby zainstalować zestaw SDK:

1. Otwórz plik **Build. Gradle** najwyższego poziomu i Dodaj następujący kod do sekcji **wszystkie projekty**, blok **repozytoriów** :

    ```java
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Zaktualizuj **aplikację/Build. Gradle** i Dodaj do niej następujący kod:

    1. Upewnij się, że **minSdkVersion** projektu ma interfejs API w wersji 21 lub nowszej.

    2. Dodaj następujący kod do sekcji systemu Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Zaktualizuj blok zależności i Dodaj nową linię zależności implementacji dla najnowszej Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
        ```

        > [!Note]
        > Numer wersji można ustawić na wartość "0 +", aby kod zawsze wskazywał najnowszą wersję.

    4. Przejdź do **pliku** na pasku narzędzi, a następnie kliknij pozycję **Synchronizuj projekt z plikami Gradle**.
3. Dodaj fragment mapy do działania głównego ( \> aktywność układu zasobu \> \_main.xml):

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
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

4. W pliku **MAINS. Java** należy:

    * Dodaj Importy dla zestawu SDK Azure Maps
    * Ustawianie informacji o uwierzytelnianiu Azure Maps
    * Pobieranie wystąpienia kontrolki mapy w metodzie **OnCreate**

    Ustawienie informacji o uwierzytelnianiu w `AzureMaps` klasie globalnie przy użyciu `setSubscriptionKey` `setAadProperties` metod lub powoduje, że nie trzeba dodawać informacji o uwierzytelnianiu w każdym widoku.

    Kontrolka mapy zawiera własne metody cyklu życia do zarządzania cyklem życia OpenGL dla systemu Android. Te metody cyklu życia muszą być wywoływane bezpośrednio z działania zawierającego. Aby aplikacja poprawnie wywołała metody cyklu życia kontrolki mapy, należy zastąpić następujące metody cyklu życia w działaniu, które zawiera formant mapy. I, należy wywołać odpowiednią metodę kontroli mapy.

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    Edytuj plik **MAINS. Java** w następujący sposób:

    ```Java
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

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
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
    }}
    ```

    > [!NOTE]
    > Po wykonaniu powyższych kroków prawdopodobnie otrzymasz ostrzeżenia z Android Studio o niektórym kodzie. Aby rozwiązać te ostrzeżenia, zaimportuj klasy, do których odwołuje się `MainActivity.java` .
    > Można automatycznie importować te klasy, wybierając pozycję `Alt`  +  `Enter` ( `Option`  +  `Return` na komputerze Mac).

5. Wybierz przycisk Run (Uruchom), jak pokazano na poniższej ilustracji (lub naciśnij klawisz `Control`  +  `R` na komputerze Mac), aby skompilować aplikację.

    ![Kliknij pozycję Uruchom.](media/quick-android-map/run-app.png)

Kompilowanie aplikacji Android Studio potrwa kilka sekund. Po zakończeniu kompilacji można przetestować aplikację na emulowanym urządzeniu z systemem Android. Powinna zostać wyświetlona mapa przypominająca:

![Azure Maps w aplikacji systemu Android](media/quick-android-map/quickstart-android-map.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

>[!WARNING]
> W samouczkach wymienionych w sekcji [następne kroki](#next-steps) szczegółowo opisano sposób używania i konfigurowania Azure Maps przy użyciu konta. Jeśli planujesz kontynuować pracę z samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start.

Jeśli nie planujesz kontynuować korzystania z samouczków, wykonaj następujące kroki, aby wyczyścić zasoby:

1. Zamknij Android Studio i Usuń utworzoną aplikację.
2. Jeśli aplikacja została przetestowana na urządzeniu zewnętrznym, odinstaluj ją z tego urządzenia.

Jeśli nie planujesz kontynuowania opracowywania przy użyciu Azure Maps Android SDK:

1. Przejdź do strony Azure Portal. Wybierz pozycję **wszystkie zasoby** na stronie portalu głównego. Lub kliknij ikonę menu w lewym górnym rogu. Wybierz pozycję **Wszystkie zasoby**.
2. Kliknij konto Azure Maps. W górnej części strony kliknij pozycję **Usuń**.
3. Opcjonalnie, jeśli nie planujesz kontynuować tworzenia aplikacji dla systemu Android, Odinstaluj Android Studio.

Aby uzyskać więcej przykładów kodu, zobacz następujące przewodniki:

* [Zarządzanie uwierzytelnianiem w Azure Maps](how-to-manage-authentication.md)
* [Zmień style mapy w usłudze mapy systemu Android](set-android-map-styles.md)
* [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)
* [Dodawanie warstwy linii](android-map-add-line-layer.md)
* [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono konto Azure Maps i utworzono aplikację demonstracyjną. Zapoznaj się z następującymi samouczkami, aby dowiedzieć się więcej na temat Azure Maps:

> [!div class="nextstepaction"]
> [Ładowanie danych GEOJSON do Azure Maps](tutorial-load-geojson-file-android.md)
