---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: aee6e6d8ca505bfdcfd4a51e4693779f44b2b0c0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88226182"
---
W tym przewodniku szybki start dowiesz się, jak za pomocą zestawu Speech Devices SDK dla systemu Linux utworzyć produkt z obsługą mowy lub użyć go jako urządzenia [transkrypcji konwersacji](../conversation-transcription-service.md) . Obecnie jest obsługiwana tylko [usługa Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/) .

Aplikacja została skompilowana przy użyciu pakietu SDK mowy, a środowisko IDE języka Java (v4) w systemie 64-bitowym Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Ten przewodnik wymaga konta [Cognitive Services platformy Azure](../get-started.md) z zasobem usługi mowy. 

Kod źródłowy [przykładowej aplikacji](https://aka.ms/sdsdk-download-JRE) jest dołączony do zestawu Speech Devices SDK. Jest ona również [dostępna w witrynie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* Tylko [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](../get-started.md).
* Pobierz najnowszą wersję [zestawu Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) dla języka Java i wyodrębnij plik zip do katalogu roboczego.
   > [!NOTE]
   > W tym przewodniku szybki start przyjęto założenie, że aplikacja została wyodrębniona do/home/wcaltest/JRE-Sample-Release

Przed rozpoczęciem przezaćmienia upewnij się, że te zależności są zainstalowane.

* W systemie Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* W programie Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

* Na RHEL/CentOS:
  
  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

  > [!NOTE]
  > - W systemie RHEL/CentOS 7 postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA RHEL/CentOS 7 dla zestawu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

Transkrypcja konwersacji jest obecnie dostępna tylko dla "en-US" i "zh-CN" w regionach "środkowe" i "eastasia". Aby można było używać transkrypcji konwersacji, w jednym z tych regionów musi znajdować się klucz mowy.

Jeśli planujesz korzystać z intencji, musisz mieć subskrypcję [usługi Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Aby dowiedzieć się więcej na temat LUIS i rozpoznawania intencji, zobacz [rozpoznawanie mowy w języku Luis, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Dla tej aplikacji jest dostępny [przykładowy model Luis](https://aka.ms/sdsdk-luis) .

## <a name="create-and-configure-the-project"></a>Tworzenie i Konfigurowanie projektu

1. Uruchom środowisko Eclipse.

1. W obszarze **roboczym** **zaćmienie**w środowisku IDE wprowadź nazwę nowego katalogu obszaru roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran powitalny, jeśli się pojawi.

1. Na pasku menu przezaćmienie Utwórz nowy projekt, wybierając kolejno pozycje **plik**  >  **Nowy**  >  **projekt Java**. W przypadku niedostępności wybierz **projekt** , a następnie **projekt Java**.

1. Zostanie uruchomiony Kreator **nowego projektu Java** . **Przeglądaj** w poszukiwaniu lokalizacji przykładowego projektu. Wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. W **Eksploratorze pakietów**kliknij projekt prawym przyciskiem myszy. Wybierz pozycję **Konfiguruj**  >  **Konwertuj do projektu Maven** z menu kontekstowego. Wybierz pozycję **Zakończ**.

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
             <version>1.13.0</version>
        </dependency>
    </dependencies>
   ```

1. W **Eksploratorze pakietów**kliknij projekt prawym przyciskiem myszy. Wybierz **Właściwości**, a następnie **Uruchom/Debuguj ustawienia**  >  **nowe...** > **Aplikacja Java**. 

1. Zostanie wyświetlone okno **Edytuj konfigurację** . W polu **Nazwa** wprowadź wartość **Main**, a następnie **Search** Użyj opcji Wyszukaj **klasy głównej** , aby znaleźć i wybrać obiekt **com. Microsoft. cognitiveservices. Speech. Samples. FunctionsList**.

   ![Zrzut ekranu przedstawiający konfigurację uruchamiania edycji](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Skopiuj pliki binarne audio dla architektury docelowej z **systemu Linux-ARM** lub **linux-x64**do lokalizacji projektu Java, np. **/Home/wcaltest/JRE-Sample-Release**

1. W oknie **Edycja konfiguracji** wybierz stronę **środowisko** i **nową**. Zostanie wyświetlone okno **Nowa zmienna środowiskowa** . W polu **Nazwa** wprowadź **LD_LIBRARY_PATH** i w polu **wartość** wprowadź folder zawierający pliki *. so, na przykład **/Home/wcaltest/JRE-Sample-Release**

1. Kopiuj `kws.table` i `participants.properties` do folderu projektu **Target/Classs**


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

1. Słowo kluczowe default (słowo kluczowe) to "Computer". Możesz również wypróbować jeden z innych podanych słów kluczowych, takich jak "Machine" lub "Assistant". Pliki zasobów dla tych alternatywnych słów kluczowych znajdują się w zestawie SDK urządzeń mowy w folderze słów kluczowych. Na przykład `/home/wcaltest/JRE-Sample-Release/keyword/Computer` zawiera pliki używane dla słowa kluczowego "Computer".

   > [!TIP]
   > Możesz również [utworzyć niestandardowe słowo kluczowe](../speech-devices-sdk-create-kws.md).

    Aby użyć nowego słowa kluczowego, zaktualizuj Poniższy wiersz w `FunctionsList.java` i skopiuj słowo kluczowe do aplikacji. Na przykład, aby użyć słowa kluczowego "Machine" z pakietu słów kluczowych `machine.zip` :

   * Skopiuj `kws.table` plik z pakietu zip do folderu **docelowego/klas**projektu.

   * Zaktualizuj `FunctionsList.java` przy użyciu nazwy słowa kluczowego:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Uruchamianie przykładowej aplikacji z poziomu programu zaćmienie

1. Na pasku menu przezaćmienia **Uruchom**polecenie  >  **Uruchom** 

1. Aplikacja Przykładowa zestawu SDK urządzeń mowy uruchamia się i wyświetla następujące opcje:

   ![Przykładowa aplikacja i opcje zestawu SDK urządzeń rozpoznawania mowy](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Wypróbuj nową wersję demonstracyjną **transkrypcji konwersacji** . Rozpocznij jego przepisywania z **Session**  >  **rozpoczęciem**sesji. Domyślnie wszyscy są gośćmi. Jeśli jednak masz podpisy głosowe uczestnika, można je umieścić `participants.properties` w **obiekcie docelowym/Klasa**folderu projektu. Aby wygenerować podpis głosowy, spójrz na [transkrypcja konwersacje (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikacja do transkrypcji konwersacji demonstracyjnej](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Tworzenie i uruchamianie autonomicznej aplikacji

1. W **Eksploratorze pakietów**kliknij projekt prawym przyciskiem myszy. Wybierz pozycję **Eksportuj**. 
1. Zostanie wyświetlone okno **eksport** . Rozwiń węzeł **Java** i wybierz pozycję **możliwy do uruchomienia plik JAR** , a następnie wybierz pozycję **Next (dalej**).

   ![Zrzut ekranu okna eksportowania](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Zostanie wyświetlone okno **Eksportuj plik możliwy do uruchomienia jar** . Wybierz **lokalizację docelową eksportu** aplikacji, a następnie wybierz pozycję **Zakończ**.
 
   ![Zrzut ekranu przedstawiający Eksport pliku JAR możliwy do uruchomienia](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Umieść `kws.table` i `participants.properties` w folderze docelowym wybranym powyżej, ponieważ te pliki są zbędne przez aplikację.

1. Ustaw LD_LIBRARY_LIB w folderze zawierającym pliki *. so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Aby uruchomić aplikację autonomiczną

     ```bash
     java -jar SpeechDemo.jar
     ```
