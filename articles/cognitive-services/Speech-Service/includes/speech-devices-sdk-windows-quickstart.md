---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: df29ecf0a4ddb30aea1c1ab9ceed44a93fc61eb8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400134"
---
W tym przewodniku Szybki start dowiesz się, jak używać zestawu SDK urządzeń mowy dla systemu Windows do tworzenia produktu obsługującego mowę lub używania go jako urządzenia [do transkrypcji konwersacji.](../conversation-transcription-service.md) Transkrypcji konwersacji obsługiwany jest tylko [plik DK usługi Azure Kinect.](https://azure.microsoft.com/services/kinect-dk/) W przypadku innych zastosowań mowy obsługiwane są liniowe tablice mikrofonowe, które zapewniają geometrię tablicy mikrofonu.

Aplikacja jest zbudowana z pakietu Speech SDK i Eclipse Java IDE (v4) w 64-bitowym systemie Windows. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Ten przewodnik wymaga konta [usługi Azure Cognitive Services](../get-started.md) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy [dla przykładowej aplikacji](https://aka.ms/sdsdk-download-JRE) jest dołączony do zestawu SDK urządzeń mowy. Jest również [dostępny na GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bitowy system Windows
* Macierz mikrofonów, taka jak [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Tylko Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Pakiet Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](../get-started.md).
* Pobierz najnowszą wersję [sdk urządzeń mowy](https://aka.ms/sdsdk-download-JRE) dla języka Java i wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > Ten przewodnik Szybki start zakłada, że aplikacja jest wyodrębniona do wersji C:\SDSDK\JRE-Sample-Release

Transkrypcja konwersacji jest obecnie dostępna tylko dla "en-US" i "zh-CN", w regionach "centralus" i "eastasia". Aby użyć transkrypcji konwersacji, w jednym z tych regionów musi być używany klucz mowy.

Jeśli planujesz użyć intencji, będziesz potrzebować subskrypcji [usługi rozumienia języka (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Aby dowiedzieć się więcej o usłudze LUIS i rozpoznawaniu intencji, zobacz [Rozpoznawanie intencji mowy za pomocą usługi LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). [Przykładowy model usługi LUIS](https://aka.ms/sdsdk-luis) jest dostępny dla tej aplikacji.

## <a name="create-and-configure-the-project"></a>Tworzenie i konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. W programie **Eclipse IDE Launcher**w polu **Obszar roboczy** wprowadź nazwę nowego katalogu obszaru roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](../media/speech-devices-sdk/eclipse-launcher.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu Eclipse utwórz nowy projekt, wybierając **pozycję Plik** > **nowego** > **projektu Java**. Jeśli nie jest dostępny, wybierz pozycję **Project,** a następnie **Program Java Project**.

1. Zostanie uruchomiony kreator **nowego projektu Java.** **Wyszukaj** lokalizację przykładowego projektu. Wybierz **pozycję Zakończ**.

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. W **Eksploratorze pakietów**kliknij prawym przyciskiem myszy projekt. Z menu kontekstowego **wybierz polecenie Konfiguruj** > **konwertowanie do projektu Maven.** Wybierz **pozycję Zakończ**.

   ![Zrzut ekranu narzędzia Package Explorer](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Otwórz plik pom.xml i edytuj go.

    Na końcu pliku, przed tagiem `</project>`zamknięcia `repositories` `dependencies` , tworzenie i elementy, `version` jak pokazano tutaj, i upewnij się, że pasuje do bieżącej wersji:
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
             <version>1.11.0</version>
        </dependency>
    </dependencies>
   ```

1. Kopiowanie zawartości **systemu Windows x64** do lokalizacji projektu Java, **np.**

1. `kws.table`Kopiowanie `participants.properties` `Microsoft.CognitiveServices.Speech.extension.pma.dll` i do folderu projektu **docelowego\klas**

## <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Dodaj klucz subskrypcji mowy do kodu źródłowego. Jeśli chcesz spróbować rozpoznawania intencji, należy również dodać klucz subskrypcji [usługi language understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) i identyfikator aplikacji.

   W przypadku mowy i usługi `FunctionsList.java`LUIS informacje są dostępne w:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Jeśli używasz transkrypcji konwersacji, klucz mowy `Cts.java`i informacje o regionie są również potrzebne w :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Domyślnym słowem kluczowym (słowo kluczowe) jest "Komputer". Możesz też wypróbować jedno z innych słów kluczowych, takich jak "Maszyna" lub "Asystent". Pliki zasobów dla tych alternatywnych słów kluczowych znajdują się w SDK urządzeń mowy w folderze słów kluczowych. Na przykład `C:\SDSDK\JRE-Sample-Release\keyword\Computer` zawiera pliki używane dla słowa kluczowego "Komputer".

    > [!TIP]
    > Można również [utworzyć niestandardowe słowo kluczowe](../speech-devices-sdk-create-kws.md).

    Aby użyć nowego słowa kluczowego, `FunctionsList.java`zaktualizuj następujący wiersz w pliku , i skopiuj je do aplikacji. Na przykład, aby użyć słowa kluczowego `machine.zip`"Machine" z pakietu słów kluczowych:

   * Skopiuj `kws.table` plik z pakietu zip do **obiektu docelowego/klas**folderu projektu .
   * Zaktualizuj `FunctionsList.java` nazwę słowa kluczowego:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Uruchamianie przykładowej aplikacji z programu Eclipse

1. Na pasku menu Eclipse **uruchom** > **jako** > **aplikację Java**. Następnie wybierz **pozycję FunctionsList** i **OK**.

   ![Zrzut ekranu przedstawiający pozycję Wybierz aplikację Java](../media/speech-devices-sdk/eclipse-run-sample.png)

1. Przykładowa aplikacja SDK urządzeń mowy uruchamia i wyświetla następujące opcje:

   ![Przykładowa przykładowa aplikacja i opcje zestawów SDK urządzeń mowy](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Wypróbuj nowe demo **transkrypcji konwersacji.** Rozpocznij transkrypcję za pomocą **sesji** > **Start**. Domyślnie każdy jest gościem. Jeśli jednak masz podpisy głosowe uczestnika, można `participants.properties` je umieścić w pliku w folderze projektu **docelowym/klasami**. Aby wygenerować podpis głosowy, spójrz na [Transkrypcji konwersacji (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demo](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Tworzenie i uruchamianie aplikacji autonomicznej

1. W **Eksploratorze pakietów**kliknij prawym przyciskiem myszy projekt. Wybierz **pozycję Eksportuj**.

1. Zostanie wyświetlone okno **Eksportuj.** Rozwiń pozycję **Java** i wybierz **plik JAR z możliwością uruchomienia,** a następnie wybierz pozycję **Dalej**.

   ![Zrzut ekranu przedstawiający okno Eksportuj](../media/speech-devices-sdk/eclipse-export-windows.png)

1. Zostanie wyświetlone okno **Eksportu pliku JAR.** Wybierz **miejsce docelowe eksportu** dla aplikacji, a następnie wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający eksport pliku JAR do uruchomienia](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Proszę `kws.table`umieścić `participants.properties` `unimic_runtime.dll`, `pma.dll` `Microsoft.CognitiveServices.Speech.extension.pma.dll` , i w folderze docelowym wybranym powyżej, ponieważ pliki te są potrzebne przez aplikację.

1. Aby uruchomić aplikację autonomiczną

   ```powershell
   java -jar SpeechDemo.jar
   ```
