---
title: Szybki Start — używanie klucza symetrycznego do aprowizacji symulowanego urządzenia na platformie Azure IoT Hub przy użyciu języka Java
description: W tym przewodniku szybki start użyjesz zestawu SDK urządzenia Java, aby utworzyć symulowane urządzenie korzystające z klucza symetrycznego w usłudze Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc, devx-track-java
ms.openlocfilehash: a32811b439a569a8f8f82fcc046e0f4b89e47a38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966704"
---
# <a name="quickstart-provision-a-simulated-device-to-iot-hub-with-symmetric-keys"></a>Szybki Start: udostępnianie symulowanego urządzenia do IoT Hub z użyciem kluczy symetrycznych

Z tego przewodnika Szybki start dowiesz się, jak utworzyć i uruchomić symulator urządzenia na maszynie deweloperskiej z systemem Windows. To symulowane urządzenie zostanie skonfigurowane do uwierzytelniania za pomocą wystąpienia usługi Device Provisioning Service (DPS) i przypisane do centrum IoT. Przykładowy kod z [Microsoft Azure zestaw SDK IoT dla języka Java](https://github.com/Azure/azure-iot-sdk-java) zostanie użyty do symulowania sekwencji rozruchu dla urządzenia inicjującego Inicjowanie obsługi administracyjnej. Urządzenie zostanie rozpoznane na podstawie rejestracji indywidualnej w wystąpieniu usługi DPS i przypisanej do centrum IoT.

Chociaż w tym artykule przedstawiono Inicjowanie obsługi przy użyciu rejestracji indywidualnej, można użyć grup rejestracji. W przypadku korzystania z grup rejestracji istnieją pewne różnice. Na przykład należy użyć pochodnego klucza urządzenia z unikatowym IDENTYFIKATORem rejestracji dla urządzenia. Mimo że grupy rejestracji klucza symetrycznego nie są ograniczone do starszych urządzeń, artykuł [Aprowizowanie starszych urządzeń za pomocą zaświadczenia klucza symetrycznego](how-to-legacy-device-symm-key.md) zawiera przykład grupy rejestracji. Aby uzyskać więcej informacji, zobacz [Rejestrowanie grupy dla zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md#group-enrollments).

Jeśli nie znasz procesu inicjowania obsługi administracyjnej, zapoznaj się z omówieniem [aprowizacji](about-iot-dps.md#provisioning-process) . 

Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). Ten przewodnik Szybki start wymaga utworzonego już wystąpienia usługi Device Provisioning Service.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby uzyskać przykład dla systemu Linux, zobacz [Aprowizowanie pod kątem wielu dzierżaw](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że na maszynie zainstalowano program [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) lub nowszy.

* Pobierz i zainstaluj pakiet [Maven](https://maven.apache.org/install.html).

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>Przygotowanie środowiska Java SDK 

1. Upewnij się, że na Twojej maszynie jest zainstalowane oprogramowanie Git i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

2. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub dla przykładu kodu symulacji urządzenia:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Przejdź do katalogu głównego `azure-iot-sdk-java` i skompiluj projekt, aby pobrać wszystkie zbędne pakiety.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz wystąpienie usługi Device Provisioning Service (DPS).

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 

3. W panelu **Dodaj rejestrację** Wprowadź poniższe informacje, a następnie naciśnij przycisk **Save (Zapisz** ).

   - **Mechanizm:** wybierz **Klucz symetryczny** jako *Mechanizm* poświadczania tożsamości.

   - **Automatycznie Generuj klucze**: zaznacz to pole.

   - **Identyfikator rejestracji**: wprowadź identyfikator rejestracji, aby zidentyfikować rejestrację. Użyj tylko małych znaków alfanumerycznych i kresek (-). Na przykład **Symm-Key-Java-Device-007**.

   - **Identyfikator urządzenia usługi IoT Hub:** wprowadź identyfikator urządzenia. Na przykład **Java-Device-007**.

     ![Dodawanie indywidualnej rejestracji dla zaświadczenia klucza symetrycznego w portalu](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Po zapisaniu rejestracji **klucz podstawowy** i **klucz pomocniczy** zostaną wygenerowane i dodane do wpisu rejestracji. Rejestracja urządzenia klucza symetrycznego jest wyświetlana jako **Symm-Key-Java-Device-007** w kolumnie *Identyfikator rejestracji* na karcie *indywidualne rejestracje* . 

    Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego**. Ta wartość klucza i **Identyfikator rejestracji** będą używane później podczas aktualizacji kodu Java dla urządzenia.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Symulowanie sekwencji rozruchu urządzenia

W tej sekcji zostanie zaktualizowany przykładowy kod urządzenia w celu wysłania sekwencji rozruchu urządzenia do wystąpienia DPS. Ta sekwencja rozruchu spowoduje, że urządzenie zostanie rozpoznane, uwierzytelnione i przypisane do centrum IoT połączonego z wystąpieniem DPS.

1. W menu usługi Device Provisioning wybierz pozycję **Przegląd** i zanotuj _zakres identyfikatorów_ i _globalny punkt końcowy usługi aprowizacji_.

    ![Informacje o usłudze](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Otwórz przykładowy kod urządzenia Java do edycji. Pełna ścieżka do przykładowego kodu urządzenia:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Dodaj _zakres identyfikatorów_ i _globalny punkt końcowy usługi aprowizacji_ wystąpienia DPS. Dołącz także podstawowy klucz symetryczny i Identyfikator rejestracji wybrany dla indywidualnej rejestracji. Zapisz zmiany. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Otwórz wiersz polecenia do kompilowania. Przejdź do folderu projektu przykładowego udostępniania w repozytorium zestawu SDK języka Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Utwórz przykład, a następnie przejdź do `target` folderu, aby wykonać utworzony plik JAR.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. Oczekiwane dane wyjściowe powinny wyglądać podobnie do następujących:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. W witrynie Azure Portal przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie otwórz blok **Device Explorer**. Po pomyślnej aprowizacji symulowanego urządzenia z kluczem symetrycznego w centrum, jego identyfikator urządzenia jest wyświetlany w bloku **Device Explorer** , z *stanem* **włączony**.  Może być konieczne naciśnięcie przycisku **Odśwież** w górnej części, jeśli blok został już otwarty przed uruchomieniem przykładowej aplikacji urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz pozycję **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz **urządzenia IoT** dla swojego centrum, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono symulowane urządzenie na komputerze z systemem Windows i zostało ono udostępnione w usłudze IoT Hub przy użyciu klucza symetrycznego za pomocą usługi Azure IoT Hub Device Provisioning Service w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie programowo, przejdź do przewodnika Szybki Start dotyczącego rejestrowania na urządzeniach X. 509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — rejestrowanie urządzeń X. 509 w usłudze Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)