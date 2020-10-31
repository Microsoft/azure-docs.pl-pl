---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 5e83650bc9861f982c4905e26fbb674abbd4de97
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93136225"
---
W tym przewodniku szybki start dowiesz się, jak utworzyć produkt z obsługą mowy przy użyciu zestawu Speech Devices SDK dla systemu Android lub użyć go jako urządzenia [transkrypcji konwersacji](../conversation-transcription-service.md) .

Ten przewodnik wymaga konta [Cognitive Services platformy Azure](../get-started.md) z zasobem usługi mowy.

Kod źródłowy przykładowej aplikacji jest dołączony do zestawu Speech Devices SDK. Jest ona również [dostępna w witrynie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z zestawu SDK usługi Speech Devices należy:

- Aby włączyć urządzenie, postępuj zgodnie z instrukcjami dostarczonymi z Twoim [zestawem deweloperskim](../get-speech-devices-sdk.md) .

- Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download)i wyodrębnij plik zip do katalogu roboczego.

  > [!NOTE]
  > W tym przewodniku szybki start przyjęto założenie, że aplikacja została wyodrębniona do C:\SDSDK\Android-Sample-Release

- Aby uzyskać [klucz subskrypcji platformy Azure dla usługi mowy](../get-started.md)

- Jeśli planujesz korzystanie z transkrypcji konwersacji, musisz użyć [okrągłego urządzenia mikrofonu](../get-speech-devices-sdk.md) , a ta funkcja jest obecnie dostępna tylko dla "en-us" i "zh-CN" w regionach "środkowe" i "eastasia". Aby można było używać transkrypcji konwersacji, w jednym z tych regionów musi znajdować się klucz mowy.

- Jeśli planujesz używać usługi mowy do identyfikowania intencji (lub działań) z wyrażenia długości użytkownika, będziesz potrzebować subskrypcji [usługi Language Understanding Service (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Aby dowiedzieć się więcej na temat LUIS i rozpoznawania intencji, zobacz [rozpoznawanie mowy w języku Luis, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Można [utworzyć prosty model Luis](https://docs.microsoft.com/azure/cognitive-services/luis/) lub użyć przykładowego modelu LUIS, LUIS-example.json. Przykładowy model LUIS jest dostępny w [witrynie pobierania zestawu SDK urządzeń mowy](https://aka.ms/sdsdk-luis). Aby przekazać plik JSON modelu do [portalu Luis](https://www.luis.ai/home), wybierz pozycję **Importuj nową aplikację** , a następnie wybierz plik JSON.

- Zainstaluj [Android Studio](https://developer.android.com/studio/) i [Vysor](https://vysor.io/download/) na komputerze.

## <a name="set-up-the-device"></a>Konfigurowanie urządzenia

1. Uruchom Vysor na komputerze.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Urządzenie powinno być wyświetlane w obszarze **Wybierz urządzenie** . Wybierz przycisk **Widok** obok urządzenia.

1. Połącz się z siecią bezprzewodową, wybierając ikonę folderu, a następnie wybierz pozycję **Ustawienia**  >  **Sieć WLAN** .

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Jeśli firma ma zasady dotyczące łączenia urządzeń z systemem Wi-Fi, należy uzyskać adres MAC i skontaktować się z działem IT w celu połączenia go z siecią Wi-Fi firmy.
   >
   > Aby znaleźć adres MAC zestawu dev Kit, wybierz ikonę folderu plików na pulpicie zestawu deweloperskiego.
   >
   > ![Folder plików Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Wybierz pozycję **Ustawienia** . Wyszukaj ciąg "adres MAC", a następnie wybierz pozycję **adres MAC**  >  **zaawansowanej sieci WLAN** . Zapisz adres MAC, który pojawia się w dolnej części okna dialogowego.
   >
   > ![Vysor adres MAC](../media/speech-devices-sdk/qsg-11.png)
   >
   > Niektóre firmy mogą mieć limit czasu, w którym można podłączyć urządzenie do systemu Wi-Fi. Może zajść potrzeba przekroczenia rejestracji zestawu deweloperów w systemie Wi-Fi po upływie określonej liczby dni.

## <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

Aby sprawdzić poprawność instalacji zestawu deweloperskiego, skompiluj i zainstaluj aplikację przykładową:

1. Rozpocznij Android Studio.

1. Wybierz pozycję **Open an existing Android Studio project** (Otwórz istniejący projekt Android Studio).

   ![Android Studio otworzyć istniejącego projektu](../media/speech-devices-sdk/qsg-5.png)

1. Przejdź do C:\SDSDK\Android-Sample-Release\example. Wybierz **przycisk OK** , aby otworzyć przykładowy projekt.

1. Skonfiguruj Gradle, aby odwoływać się do zestawu Speech SDK. Następujące pliki można znaleźć w obszarze **skrypty Gradle** w Android Studio.

    Zaktualizuj element **Build. Gradle (Project: example)** , blok allprojects powinien odpowiadać poniżej, dodając linie Maven.

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

    Zaktualizuj program **Build. Gradle (module: App)** , dodając ten wiersz do sekcji zależności. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.14.0'
    ```
    
1. Dodaj swój klucz subskrypcji mowy do kodu źródłowego. Jeśli chcesz wypróbować funkcję rozpoznawania intencji, Dodaj również klucz subskrypcji [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) i identyfikator aplikacji.

   W przypadku mowy i LUIS informacje trafiają do MAINS. Java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Jeśli używasz transkrypcji konwersacji, informacje o kluczu mowy i regionie są również potrzebne w konwersacji. Java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Słowo kluczowe default ma wartość "Computer". Możesz również wypróbować jeden z innych podanych słów kluczowych, takich jak "Machine" lub "Assistant". Pliki zasobów dla tych alternatywnych słów kluczowych znajdują się w zestawie SDK urządzeń mowy w folderze słów kluczowych. Na przykład C:\SDSDK\Android-Sample-Release\keyword\Computer zawiera pliki używane dla słowa kluczowego "Computer".

   > [!TIP]
   > Możesz również [utworzyć niestandardowe słowo kluczowe](../speech-devices-sdk-create-kws.md).

   Aby użyć nowego słowa kluczowego, zaktualizuj następujące dwa wiersze w `MainActivity.java` i skopiuj pakiet słów kluczowych do aplikacji. Na przykład, aby użyć słowa kluczowego "Machine" z pakietu słowa kluczowego kws-machine.zip:

   - Skopiuj pakiet słowa kluczowego do folderu "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets \" .
   - Zaktualizuj `MainActivity.java` za pomocą słowa kluczowego i nazwy pakietu:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Zaktualizuj następujące wiersze, które zawierają ustawienia geometrii tablicy mikrofonu:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ta tabela zawiera listę obsługiwanych wartości:

   | Zmienna | Znaczenie | Dostępne wartości |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Konfiguracja fizycznego mikrofonu | Dla cyklicznego zestawu dev Kit: `Circular6+1` |
   |          |         | W przypadku zestawu liniowego dla deweloperów: `Linear4` |
   | `SelectedGeometry` | Konfiguracja mikrofonu oprogramowania | Dla cyklicznego zestawu deweloperskiego, który używa wszystkich Mics: `Circular6+1` |
   |          |         | Dla cyklicznego zestawu deweloperskiego, który używa czterech Mics: `Circular3+1` |
   |          |         | W przypadku zestawu liniowego dev, który używa wszystkich Mics: `Linear4` |
   |          |         | W przypadku zestawu liniowego dev, który używa dwóch Mics: `Linear2` |

1. Aby skompilować aplikację, w menu **Uruchom** wybierz polecenie **Uruchom aplikację** . Zostanie wyświetlone okno dialogowe **Wybieranie celu wdrożenia** .

1. Wybierz urządzenie, a następnie wybierz przycisk **OK** , aby wdrożyć aplikację na urządzeniu.

   ![Okno dialogowe Wybieranie celu wdrożenia](../media/speech-devices-sdk/qsg-7.png)

1. Aplikacja Przykładowa zestawu SDK urządzeń mowy uruchamia się i wyświetla następujące opcje:

   ![Przykładowa aplikacja i opcje zestawu SDK urządzeń rozpoznawania mowy](../media/speech-devices-sdk/qsg-8.png)

1. Wypróbuj nową wersję demonstracyjną transkrypcji konwersacji. Rozpocznij jego przepisywania z opcją "Rozpocznij sesję". Domyślnie wszyscy są gośćmi. Jeśli jednak masz podpisy głosu uczestników, można je umieścić w pliku `/video/participants.properties` na urządzeniu. Aby wygenerować podpis głosowy, spójrz na [transkrypcja konwersacje (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demonstracyjnej](../media/speech-devices-sdk/qsg-15.png)

1. Doświadczenia!

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie możesz nawiązać połączenia z urządzeniem mowy. Wpisz następujące polecenie w oknie wiersza polecenia. Zwróci listę urządzeń:

```powershell
 adb devices
```

> [!NOTE]
> To polecenie używa Android Debug Bridge, `adb.exe` , który jest częścią instalacji Android Studio. To narzędzie znajduje się w \[ nazwie użytkownika C:\Users] \AppData\Local\Android\Sdk\platform-Tools. Możesz dodać ten katalog do ścieżki, aby był wygodniejszy do wywoływania `adb` . W przeciwnym razie należy określić pełną ścieżkę do instalacji adb.exe w każdym poleceniu, które wywołuje `adb` .
>
> Jeśli zobaczysz błąd, `no devices/emulators found` Sprawdź, czy kabel USB jest podłączony, i upewnij się, że jest używany kabel wysokiej jakości.
