---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: d45986dcd8b846015abfef9cb3719d0107c6b8d6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400109"
---
W tym przewodniku Szybki start dowiesz się, jak używać SDK urządzeń mowy dla systemu Linux do tworzenia produktu obsługującego mowę lub używać go jako urządzenia [do transkrypcji konwersacji.](../conversation-transcription-service.md) Obecnie obsługiwane są tylko [usługi Azure Kinect DK.](https://azure.microsoft.com/services/kinect-dk/)

Aplikacja jest zbudowana z pakietu Speech SDK i Eclipse Java IDE (v4) na 64-bitowym systemie Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Ten przewodnik wymaga konta [usługi Azure Cognitive Services](../get-started.md) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

Kod źródłowy [dla przykładowej aplikacji](https://aka.ms/sdsdk-download-JRE) jest dołączony do zestawu SDK urządzeń mowy. Jest również [dostępny na GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bitowy Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Tylko Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](../get-started.md).
* Pobierz najnowszą wersję [sdk urządzeń mowy](https://aka.ms/sdsdk-download-JRE) dla języka Java i wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > Ten przewodnik Szybki start zakłada, że aplikacja jest wyodrębniona do /home/wcaltest/JRE-Sample-Release

Upewnij się, że te zależności są zainstalowane przed rozpoczęciem programu Eclipse.

* W systemie Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Na Debianie 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Na RHEL/CentOS 8:

  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

> [!NOTE]
> Na RHEL/CentOS 8 postępuj zgodnie z instrukcjami [dotyczącymi konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

Transkrypcja konwersacji jest obecnie dostępna tylko dla "en-US" i "zh-CN", w regionach "centralus" i "eastasia". Aby użyć transkrypcji konwersacji, w jednym z tych regionów musi być używany klucz mowy.

Jeśli planujesz użyć intencji, będziesz potrzebować subskrypcji [usługi rozumienia języka (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Aby dowiedzieć się więcej o usłudze LUIS i rozpoznawaniu intencji, zobacz [Rozpoznawanie intencji mowy za pomocą usługi LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). [Przykładowy model usługi LUIS](https://aka.ms/sdsdk-luis) jest dostępny dla tej aplikacji.

## <a name="create-and-configure-the-project"></a>Tworzenie i konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. W programie **Eclipse IDE Launcher**w polu **Obszar roboczy** wprowadź nazwę nowego katalogu obszaru roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu Eclipse utwórz nowy projekt, wybierając **pozycję Plik** > **nowego** > **projektu Java**. Jeśli nie jest dostępny, wybierz pozycję **Project,** a następnie **Program Java Project**.

1. Zostanie uruchomiony kreator **nowego projektu Java.** **Wyszukaj** lokalizację przykładowego projektu. Wybierz **pozycję Zakończ**.

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

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

1. W **Eksploratorze pakietów**kliknij prawym przyciskiem myszy projekt. Wybierz **polecenie Właściwości**, a następnie **Uruchom/Debugowanie ustawień** > **nowych...** > **Aplikacja Java**. 

1. Zostanie wyświetlone okno **Edytuj konfigurację.** W polu **Nazwa** wprowadź **pozycję Główny**i użyj **funkcji Wyszukaj** **klasę główną,** aby znaleźć i wybrać **pozycję com.microsoft.cognitiveservices.speech.samples.FunctionsList**.

   ![Zrzut ekranu przedstawiający konfigurację uruchamiania edycji](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Skopiuj pliki binarne audio dla architektury docelowej, z **linux-arm** lub **Linux-x64**, do lokalizacji projektu Java, np **/home/wcaltest/JRE-Sample-Release**

1. Również w oknie **Edytuj konfigurację** wybierz stronę **Środowisko** i **Nowy**. Zostanie wyświetlene okno **Nowa zmienna środowiskowa.** W polu **Nazwa** wprowadź **LD_LIBRARY_PATH** i w polu **wartości** wprowadź folder zawierający pliki *.so, na przykład **/home/wcaltest/JRE-Sample-Release**

1. `kws.table` Kopiowanie `participants.properties` i do **obiektu docelowego/klas** folderu projektu


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

1. Domyślnym słowem kluczowym (słowo kluczowe) jest "Komputer". Możesz też wypróbować jedno z innych słów kluczowych, takich jak "Maszyna" lub "Asystent". Pliki zasobów dla tych alternatywnych słów kluczowych znajdują się w SDK urządzeń mowy w folderze słów kluczowych. Na przykład `/home/wcaltest/JRE-Sample-Release/keyword/Computer` zawiera pliki używane dla słowa kluczowego "Komputer".

   > [!TIP]
   > Można również [utworzyć niestandardowe słowo kluczowe](../speech-devices-sdk-create-kws.md).

    Aby użyć nowego słowa kluczowego, `FunctionsList.java`zaktualizuj następujący wiersz w pliku , i skopiuj je do aplikacji. Na przykład, aby użyć słowa kluczowego `machine.zip`"Machine" z pakietu słów kluczowych:

   * Skopiuj `kws.table` plik z pakietu zip do **obiektu docelowego/klas**folderu projektu .

   * Zaktualizuj `FunctionsList.java` nazwę słowa kluczowego:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Uruchamianie przykładowej aplikacji z programu Eclipse

1. Na pasku menu Eclipse **uruchom** > **Run** 

1. Przykładowa aplikacja SDK urządzeń mowy uruchamia i wyświetla następujące opcje:

   ![Przykładowa przykładowa aplikacja i opcje zestawów SDK urządzeń mowy](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Wypróbuj nowe demo **transkrypcji konwersacji.** Rozpocznij transkrypcję za pomocą **sesji** > **Start**. Domyślnie każdy jest gościem. Jeśli jednak masz podpisy głosowe uczestnika, `participants.properties` można je umieścić w folderze projektu **docelowym/klasami**. Aby wygenerować podpis głosowy, spójrz na [Transkrypcji konwersacji (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demo](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Tworzenie i uruchamianie autonomicznej aplikacji

1. W **Eksploratorze pakietów**kliknij prawym przyciskiem myszy projekt. Wybierz **pozycję Eksportuj**. 
1. Zostanie wyświetlone okno **Eksportuj.** Rozwiń pozycję **Java** i wybierz **plik JAR z możliwością uruchomienia,** a następnie wybierz pozycję **Dalej**.

   ![Zrzut ekranu przedstawiający okno Eksportuj](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Zostanie wyświetlone okno **Eksportu pliku JAR.** Wybierz **miejsce docelowe eksportu** dla aplikacji, a następnie wybierz pozycję **Zakończ**.
 
   ![Zrzut ekranu przedstawiający eksport pliku JAR do uruchomienia](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Proszę `kws.table` umieścić `participants.properties` i w folderze docelowym wybranym powyżej, ponieważ pliki te są potrzebne przez aplikację.

1. Ustaw LD_LIBRARY_LIB do folderu zawierającego pliki *.so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Aby uruchomić aplikację autonomiczną

     ```bash
     java -jar SpeechDemo.jar
     ```
