---
title: Szybki Start — rejestrowanie urządzenia TPM w usłudze Azure Device Provisioning przy użyciu języka Java
description: Szybki Start — rejestrowanie urządzenia TPM w usłudze Azure IoT Hub Device Provisioning Service (DPS) przy użyciu zestawu SDK usługi Java. W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0a1f4ed46ab9e467a19cfa722a2d345284fdc94a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96463058"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Szybki Start: rejestrowanie urządzenia TPM w IoT Hub Device Provisioning Service przy użyciu zestawu SDK usługi Java

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

W tym przewodniku szybki start utworzysz samodzielną rejestrację dla symulowanego urządzenia TPM w usłudze Azure IoT Hub Device Provisioning Service przy użyciu zestawu SDK usługi Java z pomocą przykładowej aplikacji Java.

## <a name="prerequisites"></a>Wymagania wstępne

- Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md).
- Kończenie [odczytywania kluczy kryptograficznych z urządzenia TPM](quick-create-simulated-device.md#simulatetpm).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). Ten przewodnik Szybki Start instaluje [zestaw SDK usługi Java](https://azure.github.io/azure-iot-sdk-java/master/service/) poniżej. Działa on zarówno w systemie Windows, jak i Linux. Ten przewodnik Szybki Start używa systemu Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- Usługi [git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego 

1. Upewnij się, że na maszynie zainstalowano środowisko [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). 

2. Skonfiguruj zmienne środowiskowe instalacji języka Java. Zmienna `PATH` powinna zawierać pełną ścieżkę do katalogu *jdk1.8.x\bin*. Jeśli jest to pierwsza instalacja języka Java na komputerze, utwórz nową zmienną środowiskową o nazwie `JAVA_HOME` i ustaw ją na pełną ścieżkę do katalogu *jdk1.8.x*. Na komputerze z systemem Windows ten katalog znajduje się w folderze *C:\\Program Files\\Java\\*, a zmienne środowiskowe można tworzyć lub edytować po wyszukaniu frazy **Edytuj zmienne środowiskowe systemu** w **Panelu sterowania** komputera z systemem Windows. 

   Aby sprawdzić poprawność skonfigurowania języka Java na komputerze, możesz uruchomić następujące polecenie w oknie polecenia:

    ```cmd\sh
    java -version
    ```

3. Pobierz pakiet [Maven 3](https://maven.apache.org/download.cgi) i wyodrębnij go na swoim komputerze. 

4. Zmodyfikuj zmienną środowiskową `PATH`, aby wskazywała na folder *apache-maven-3.x.x\\bin* znajdujący się w folderze, do którego wyodrębniono pakiet Maven. Poprawność zainstalowania pakietu Maven można potwierdzić, uruchamiając to polecenie w oknie polecenia:

    ```cmd\sh
    mvn --version
    ```

5. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie [git](https://git-scm.com/download/) i że jest ono dodane do zmiennej środowiskowej `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Pobieranie i modyfikowanie przykładowego kodu Java

W tej sekcji przedstawiono sposób dodawania szczegółów aprowizacji urządzenia TPM do przykładowego kodu. 

1. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub na potrzeby przykładowego kodu rejestracji urządzenia przy użyciu [zestawu SDK usługi Java](https://azure.github.io/azure-iot-sdk-java/master/service/):
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. W pobranym kodzie źródłowym przejdź do folderu przykładu **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Otwórz plik **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** w dowolnym edytorze i dodaj następujące szczegółowe informacje:

   1. Dodaj ciąg `[Provisioning Connection String]` dla usługi aprowizacji z portalu, jako pokazano poniżej:
       1. Przejdź do usługi aprowizacji w [Azure Portal](https://portal.azure.com). 
       2. Otwórz **Zasady dostępu współużytkowanego** i wybierz zasadę, która ma uprawnienie *EnrollmentWrite*.
       3. Skopiuj **Parametry połączenia klucza podstawowego**. 

           ![Pobieranie parametrów połączenia aprowizacji z portalu](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. W przykładowym pliku kodu **_ServiceEnrollmentSample.java_** zastąp ciąg `[Provisioning Connection String]`**parametrami połączenia klucza podstawowego**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Dodaj szczegóły urządzenia TPM:
       1. Pobierz *identyfikator rejestracji* i *klucz poręczenia modułu TPM* na potrzeby symulacji urządzenia TPM, wykonując kroki prowadzące do sekcji [Symulowanie urządzenia TPM](quick-create-simulated-device.md#simulatetpm).
       2. Użyj **_identyfikatora rejestracji_** i **_klucza poręczenia_** z danych wyjściowych poprzedniego kroku, aby zastąpić ciągi `[RegistrationId]` i `[TPM Endorsement Key]` w przykładowym pliku kodu **_ServiceEnrollmentSample.java_**:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Opcjonalnie możesz skonfigurować usługę aprowizacji za pośrednictwem przykładowego kodu:
      - Aby dodać tę konfigurację do przykładu, wykonaj następujące kroki:
        1. Przejdź do centrum IoT połączonego z usługą aprowizacji w witrynie [Azure Portal](https://portal.azure.com). Otwórz kartę **Przegląd** centrum i skopiuj wartość pola **Nazwa hosta**. Przypisz tę **nazwę hosta** do parametru *IOTHUB_HOST_NAME*.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Przypisz przyjazną nazwę do parametru *DEVICE_ID* i zachowaj dla parametru *PROVISIONING_STATUS* wartość domyślną *ENABLED*. 
    
      - LUB, jeśli nie chcesz konfigurować usługi aprowizacji, oznacz jako komentarze bądź usuń następujące instrukcje w pliku _ServiceEnrollmentSample.java_:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Przejrzyj przykładowy kod. Tworzy on, aktualizuje i usuwa rejestrację indywidualną urządzenia TPM oraz wykonuje względem niej zapytanie. Aby sprawdzić poprawność rejestracji w portalu, tymczasowo oznacz jako komentarz następujące wiersze kodu na końcu pliku _ServiceEnrollmentSample.java_:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Zapisz plik _ServiceEnrollmentSample.java_.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Kompilowanie i uruchamianie przykładowego kodu Java

1. Otwórz okno polecenia i przejdź do folderu **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**.

2. Skompiluj przykładowy kod za pomocą tego polecenia:

    ```cmd\sh
    mvn install -DskipTests
    ```

   To polecenie pobiera pakiet Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) na maszynę. Ten pakiet zawiera pliki binarne dla [zestawu SDK usługi Java](https://azure.github.io/azure-iot-sdk-java/master/service/), których przykładowy kod musi skompilować. 

3. Uruchom przykładowy kod przy użyciu tych poleceń w oknie polecenia:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Sprawdź poprawność rejestracji w oknie danych wyjściowych. 

5. Przejdź do usługi aprowizacji w witrynie Azure Portal. Wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **rejestracje indywidualne** . Zwróć uwagę, że teraz zostanie wyświetlony *Identyfikator rejestracji* symulowanego urządzenia TPM. 

    ![Potwierdzanie poprawności rejestracji urządzenia TPM w portalu](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli planujesz Eksplorowanie przykładu usługi Java, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij okno danych wyjściowych przykładowej usługi Java na swojej maszynie.
1. Zamknij okno symulatora modułu TPM, które mogło zostać utworzone do symulacji urządzenia TPM.
1. Przejdź do usługi Device Provisioning w Azure Portal wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* wpisu rejestracji utworzonego w ramach tego przewodnika Szybki Start i naciśnij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start zarejestrowano symulowane urządzenie TPM w usłudze Device Provisioning. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal. 

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)