---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 4a2d9f382045db2aeab80d9ecf5a05b031bcbc9e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400102"
---
W tym przewodniku Szybki start dowiesz się, jak używać SDK urządzeń mowy dla systemu Android do tworzenia produktu obsługującego mowę lub używania go jako urządzenia [do transkrypcji konwersacji.](../conversation-transcription-service.md)

Ten przewodnik wymaga konta [usługi Azure Cognitive Services](../get-started.md) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy dla przykładowej aplikacji jest dołączony do zestawu SDK urządzeń mowy. Jest również [dostępny na GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z sdk urządzeń mowy należy:

- Postępuj zgodnie z instrukcjami dostarczonymi wraz z [zestawem programistycznym,](../get-speech-devices-sdk.md) aby włączyć urządzenie.

- Pobierz najnowszą wersję [sdk urządzeń mowy](https://aka.ms/sdsdk-download)i wyodrębnij plik zip do katalogu roboczego.

  > [!NOTE]
  > Ten przewodnik Szybki start zakłada, że aplikacja jest wyodrębniona do C:\SDSDK\Android-Sample-Release

- Aby uzyskać [klucz subskrypcji platformy Azure dla usługi Mowy](../get-started.md)

- Jeśli planujesz używać transkrypcji konwersacji, musisz użyć [okrągłego urządzenia mikrofonowego,](../get-speech-devices-sdk.md) a ta funkcja jest obecnie dostępna tylko dla "en-US" i "zh-CN" w regionach "centralus" i "eastasia". Aby użyć transkrypcji konwersacji, w jednym z tych regionów musi być używany klucz mowy.

- Jeśli planujesz używać usługi Mowy do identyfikowania intencji (lub akcji) z wypowiedzi użytkowników, musisz subskrypcji [usługi rozumienia języka (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Aby dowiedzieć się więcej o usłudze LUIS i rozpoznawaniu intencji, zobacz [Rozpoznawanie intencji mowy za pomocą usługi LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Można [utworzyć prosty model usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) lub użyć przykładowego modelu usługi LUIS, LUIS-example.json. Przykładowy model usługi LUIS jest dostępny w [witrynie pobierania SDK SDK urządzeń mowy.](https://aka.ms/sdsdk-luis) Aby przekazać plik JSON modelu do [portalu usługi LUIS,](https://www.luis.ai/home)wybierz pozycję **Importuj nową aplikację**, a następnie wybierz plik JSON.

- Zainstaluj [Android Studio](https://developer.android.com/studio/) i [Vysor](https://vysor.io/download/) na swoim komputerze.

## <a name="set-up-the-device"></a>Konfigurowanie urządzenia

1. Uruchom Vysor na komputerze.

   ![Vysor (Wysor)](../media/speech-devices-sdk/qsg-3.png)

1. Urządzenie powinno być wyświetlane w obszarze **Wybierz urządzenie**. Wybierz przycisk **Widok** obok urządzenia.

1. Połącz się z siecią bezprzewodową, wybierając ikonę folderu, a następnie wybierz pozycję **Ustawienia** > **sieci WLAN**.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Jeśli twoja firma ma zasady dotyczące łączenia urządzeń z systemem Wi-Fi, musisz uzyskać adres MAC i skontaktować się z działem IT, aby dowiedzieć się, jak połączyć go z firmową siecią Wi-Fi.
   >
   > Aby znaleźć adres MAC zestawu deweloperskiego, wybierz ikonę folderu plików na pulpicie zestawu deweloperskiego.
   >
   > ![Folder plików Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Wybierz **pozycję Ustawienia**. Wyszukaj "adres mac", a następnie wybierz **adres** > Mac**Advanced WLAN**. Zapisz adres MAC, który pojawia się u dołu okna dialogowego.
   >
   > ![Adres MAC Vysor](../media/speech-devices-sdk/qsg-11.png)
   >
   > Niektóre firmy mogą mieć limit czasu na czas, przez jaki urządzenie może być podłączone do systemu Wi-Fi. Może być konieczne przedłużenie rejestracji zestawu deweloperskiego w systemie Wi-Fi po określonej liczbie dni.

## <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

Aby sprawdzić poprawność konfiguracji zestawu deweloperskiego, skompiluj i zainstaluj przykładową aplikację:

1. Uruchom Android Studio.

1. Wybierz pozycję **Open an existing Android Studio project** (Otwórz istniejący projekt Android Studio).

   ![Android Studio — otwieranie istniejącego projektu](../media/speech-devices-sdk/qsg-5.png)

1. Przejdź do folderu C:\SDSDK\Android-Sample-Release\example. Wybierz **przycisk OK,** aby otworzyć przykładowy projekt.

1. Skonfiguruj gradle, aby odwoływać się do SDK mowy. Poniższe pliki można znaleźć pod **Gradle Scripts** w Android Studio.

    Zaktualizuj **build.gradle(Project:example)**, blok allprojects powinien być zgodny z poniższymi, dodając wiersze maven.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Zaktualizuj **build.gradle(Module:app),** dodając ten wiersz do sekcji zależności. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
    ```
    
1. Dodaj klucz subskrypcji mowy do kodu źródłowego. Jeśli chcesz spróbować rozpoznawania intencji, należy również dodać klucz subskrypcji [usługi language understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) i identyfikator aplikacji.

   W przypadku mowy i usługi LUIS informacje są dostępne w pliku MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Jeśli używasz transkrypcji konwersacji, klucz mowy i informacje o regionie są również potrzebne w conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Domyślnym słowem kluczowym jest "Komputer". Możesz też wypróbować jedno z innych słów kluczowych, takich jak "Maszyna" lub "Asystent". Pliki zasobów dla tych alternatywnych słów kluczowych znajdują się w SDK urządzeń mowy w folderze słów kluczowych. Na przykład C:\SDSDK\Android-Sample-Release\keyword\Computer zawiera pliki używane dla słowa kluczowego "Komputer".

   > [!TIP]
   > Można również [utworzyć niestandardowe słowo kluczowe](../speech-devices-sdk-create-kws.md).

   Aby użyć nowego słowa kluczowego, `MainActivity.java`zaktualizuj następujące dwa wiersze w pliku programu i skopiuj pakiet słów kluczowych do aplikacji. Na przykład, aby użyć słowa kluczowego "Machine" z pakietu słów kluczowych kws-machine.zip:

   - Skopiuj pakiet słów kluczowych do folderu "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Zaktualizuj `MainActivity.java` słowo kluczowe i nazwę pakietu:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Zaktualizuj następujące linie, które zawierają ustawienia geometrii tablicy mikrofonu:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   W tej tabeli wymieniono obsługiwane wartości:

   | Zmienna | Znaczenie | Dostępne wartości |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Konfiguracja mikrofonu fizycznego | W przypadku okrągłego zestawu deweloperów:`Circular6+1` |
   |          |         | W przypadku zestawu deweloperów liniowych:`Linear4` |
   | `SelectedGeometry` | Konfiguracja mikrofonu oprogramowania | W przypadku okrągłego zestawu deweloperskiego, który używa wszystkich mikrofonów:`Circular6+1` |
   |          |         | W przypadku okrągłego zestawu deweloperskiego, który wykorzystuje cztery mikrofony:`Circular3+1` |
   |          |         | W przypadku zestawu deweloperskiego liniowego, który używa wszystkich mikrofonów:`Linear4` |
   |          |         | W przypadku zestawu deweloperskiego liniowego, który używa dwóch mikrofonów:`Linear2` |

1. Aby utworzyć aplikację, w menu **Uruchom** wybierz polecenie **Uruchom "app"**. Zostanie wyświetlone okno dialogowe **Wybieranie celu wdrożenia.**

1. Wybierz urządzenie, a następnie wybierz **przycisk OK,** aby wdrożyć aplikację na urządzeniu.

   ![Okno dialogowe Wybieranie obiektu docelowego wdrożenia](../media/speech-devices-sdk/qsg-7.png)

1. Przykładowa aplikacja SDK urządzeń mowy uruchamia i wyświetla następujące opcje:

   ![Przykładowa przykładowa aplikacja i opcje zestawów SDK urządzeń mowy](../media/speech-devices-sdk/qsg-8.png)

1. Wypróbuj nowe demo transkrypcji konwersacji. Rozpocznij transkrypcję za pomocą "Rozpocznij sesję". Domyślnie każdy jest gościem. Jeśli jednak masz podpisy głosowe uczestnika, można `/video/participants.properties` je umieścić w pliku na urządzeniu. Aby wygenerować podpis głosowy, spójrz na [Transkrypcji konwersacji (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demo](../media/speech-devices-sdk/qsg-15.png)

1. Eksperyment!

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie można połączyć się z urządzeniem mowy. Wpisz następujące polecenie w oknie wiersza polecenia. Zwróci listę urządzeń:

```powershell
 adb devices
```

> [!NOTE]
> To polecenie używa mostka debugowania systemu Android, `adb.exe`który jest częścią instalacji Programu Android Studio. To narzędzie znajduje się w\[języku C:\Użytkownicy nazwa użytkownika]\AppData\Local\Android\Sdk\platform-tools. Możesz dodać ten katalog do ścieżki, aby ułatwić `adb`wywoływanie . W przeciwnym razie należy określić pełną ścieżkę do instalacji adb.exe w każdym wywołaniu `adb`polecenia .
>
> Jeśli widzisz błąd, `no devices/emulators found` sprawdź, czy kabel USB jest podłączony i upewnij się, że używany jest kabel wysokiej jakości.
