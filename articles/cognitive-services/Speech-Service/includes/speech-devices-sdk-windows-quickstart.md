---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 7ff53a700ab1b158d94af84907940f56afa74eb5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214345"
---
W tym przewodniku szybki start dowiesz się, jak za pomocą zestawu SDK usługi Speech dla systemu Windows utworzyć produkt z obsługą mowy lub użyć go jako urządzenia [transkrypcji konwersacji](../conversation-transcription.md) . W przypadku transkrypcji konwersacji jest obsługiwana tylko [usługa Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/) . W przypadku innych funkcji rozpoznawania mowy obsługiwane są tablice mikrofonu liniowego, które zapewniają geometrię macierzy mikrofonu.

Aplikacja została skompilowana przy użyciu pakietu zestawu Speech SDK, a środowisko IDE środowiska Java (v4) w systemie 64-bitowym. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Ten przewodnik wymaga konta [Cognitive Services platformy Azure](../overview.md#try-the-speech-service-for-free) z zasobem usługi mowy.

Kod źródłowy [przykładowej aplikacji](https://aka.ms/sdsdk-download-JRE) jest dołączony do zestawu Speech Devices SDK. Jest ona również [dostępna w witrynie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bitowy system Windows
* Tablica mikrofonu, taka jak [Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* Tylko [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* [Pakiet redystrybucyjny Microsoft Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](../overview.md#try-the-speech-service-for-free).
* Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) dla języka Java i wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > W tym przewodniku szybki start przyjęto założenie, że aplikacja została wyodrębniona do C:\SDSDK\JRE-Sample-Release

Transkrypcja konwersacji jest obecnie dostępna tylko dla "en-US" i "zh-CN" w regionach "środkowe" i "eastasia". Aby można było używać transkrypcji konwersacji, w jednym z tych regionów musi znajdować się klucz mowy.

Jeśli planujesz korzystać z intencji, musisz mieć subskrypcję [usługi Language Understanding (Luis)](../../luis/luis-how-to-azure-subscription.md) . Aby dowiedzieć się więcej na temat LUIS i rozpoznawania intencji, zobacz [rozpoznawanie mowy w języku Luis, C#](../how-to-recognize-intents-from-speech-csharp.md). Dla tej aplikacji jest dostępny [przykładowy model Luis](https://aka.ms/sdsdk-luis) .

## <a name="create-and-configure-the-project"></a>Tworzenie i Konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. W obszarze **roboczym** **zaćmienie** w środowisku IDE wprowadź nazwę nowego katalogu obszaru roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu, na którym jest wyświetlany moduł uruchamiający przezaćmienie, w którym wprowadzana jest nazwa katalogu obszaru roboczego.](../media/speech-devices-sdk/eclipse-launcher.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu przezaćmienie Utwórz nowy projekt, wybierając kolejno pozycje **plik**  >  **Nowy**  >  **projekt Java**. W przypadku niedostępności wybierz **projekt** , a następnie **projekt Java**.

1. Zostanie uruchomiony Kreator **nowego projektu Java** . **Przeglądaj** w poszukiwaniu lokalizacji przykładowego projektu. Wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający Kreatora nowego projektu Java.](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. W **Eksploratorze pakietów** kliknij projekt prawym przyciskiem myszy. Wybierz pozycję **Konfiguruj**  >  **Konwertuj do projektu Maven** z menu kontekstowego. Wybierz pozycję **Zakończ**.

   ![Zrzut ekranu narzędzia Package Explorer](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Otwórz plik pom.xml i edytuj go.

    Na końcu pliku, przed tagiem zamykającym `</project>` , Utwórz `repositories` i `dependencies` elementy, jak pokazano poniżej, i upewnij się, że są `version` zgodne z bieżącą wersją:
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.15.0</version>
        </dependency>
    </dependencies>
   ```

1. Skopiuj zawartość **systemu Windows — x64** do lokalizacji projektu Java, np. **C:\SDSDK\JRE-Sample-Release**

1. Kopiuj `kws.table` `participants.properties` i `Microsoft.CognitiveServices.Speech.extension.pma.dll` do folderu projektu **target\classes**

## <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Dodaj swój klucz subskrypcji mowy do kodu źródłowego. Jeśli chcesz wypróbować funkcję rozpoznawania intencji, Dodaj również klucz subskrypcji [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) i identyfikator aplikacji.

   W przypadku mowy i LUIS informacje są umieszczane w `FunctionsList.java` :

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   W przypadku korzystania z transkrypcji konwersacji informacje o kluczu mowy i regionie są również potrzebne w programie `Cts.java` :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Słowo kluczowe default (słowo kluczowe) to "Computer". Możesz również wypróbować jeden z innych podanych słów kluczowych, takich jak "Machine" lub "Assistant". Pliki zasobów dla tych alternatywnych słów kluczowych znajdują się w zestawie SDK urządzeń mowy w folderze słów kluczowych. Na przykład `C:\SDSDK\JRE-Sample-Release\keyword\Computer` zawiera pliki używane dla słowa kluczowego "Computer".

    > [!TIP]
    > Możesz również [utworzyć niestandardowe słowo kluczowe](../custom-keyword-basics.md).

    Aby użyć nowego słowa kluczowego, zaktualizuj Poniższy wiersz w `FunctionsList.java` i skopiuj słowo kluczowe do aplikacji. Na przykład, aby użyć słowa kluczowego "Machine" z pakietu słów kluczowych `machine.zip` :

   * Skopiuj `kws.table` plik z pakietu zip do folderu **docelowego/klas** projektu.
   * Zaktualizuj `FunctionsList.java` przy użyciu nazwy słowa kluczowego:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Uruchamianie przykładowej aplikacji z poziomu programu zaćmienie

1. Na pasku menu przezaćmienia **Uruchom** polecenie  >  **Uruchom jako**  >  **aplikację Java**. Następnie wybierz pozycję **FunctionsList** i **przycisk OK**.

   ![Zrzut ekranu przedstawiający wybraną aplikację Java](../media/speech-devices-sdk/eclipse-run-sample.png)

1. Aplikacja Przykładowa zestawu SDK urządzeń mowy uruchamia się i wyświetla następujące opcje:

   ![Zrzut ekranu przedstawiający przykładową aplikację i opcje zestawu SDK urządzeń rozpoznawania mowy.](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Wypróbuj nową wersję demonstracyjną **transkrypcji konwersacji** . Rozpocznij jego przepisywania z   >  **rozpoczęciem** sesji. Domyślnie wszyscy są gośćmi. Jeśli jednak masz podpisy głosu uczestników, można je umieścić w pliku `participants.properties` w **obiekcie docelowym/klasie** folderu projektu. Aby wygenerować podpis głosowy, spójrz na [transkrypcja konwersacje (SDK)](../how-to-use-conversation-transcription.md).

   ![Zrzut ekranu przedstawiający aplikację transkrypcji konwersacji.](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Tworzenie i uruchamianie aplikacji autonomicznej

1. W **Eksploratorze pakietów** kliknij projekt prawym przyciskiem myszy. Wybierz pozycję **Eksportuj**.

1. Zostanie wyświetlone okno **eksport** . Rozwiń węzeł **Java** i wybierz pozycję **możliwy do uruchomienia plik JAR** , a następnie wybierz pozycję **Next (dalej**).

   ![Zrzut ekranu pokazujący okno eksportu, w którym wybierasz plik JAR możliwy do uruchomienia.](../media/speech-devices-sdk/eclipse-export-windows.png)

1. Zostanie wyświetlone okno **Eksportuj plik możliwy do uruchomienia jar** . Wybierz **lokalizację docelową eksportu** aplikacji, a następnie wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający okno eksportowania pliku JAR możliwy do uruchomienia, w którym można wybrać miejsce docelowe eksportowania.](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Umieść `kws.table` ,, `participants.properties` `unimic_runtime.dll` , `pma.dll` i `Microsoft.CognitiveServices.Speech.extension.pma.dll` w folderze docelowym wybranym powyżej, ponieważ te pliki są wymagane przez aplikację.

1. Aby uruchomić aplikację autonomiczną

   ```powershell
   java -jar SpeechDemo.jar
   ```