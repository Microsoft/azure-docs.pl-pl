---
title: Samouczek — Inicjowanie obsługi symulowanego urządzenia X. 509 na platformie Azure IoT Hub przy użyciu języka Java i grup rejestracji
description: W tym samouczku Utwórz i zainicjuj symulowane urządzenie X. 509 za pomocą zestawu SDK urządzeń i usług Java oraz grup rejestracji dla IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4cfbfe3e3e3ba620d8292767012c9bb866d8a878
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968098"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Samouczek: Tworzenie i Inicjowanie symulowanego urządzenia X. 509 za pomocą zestawu SDK urządzeń i usług Java oraz rejestracji grup dla IoT Hub Device Provisioning Service

Te kroki pokazują, jak zasymulować urządzenie X.509 na maszynie deweloperskiej z systemem operacyjnym Windows OS i użyć przykładowego kodu do połączenia tego symulowanego urządzenia z usługą Device Provisioning Service i Twoim centrum IoT Hub przy użyciu grup rejestracji. 

Pamiętaj, aby wcześniej wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md).


## <a name="prerequisites"></a>Wymagania wstępne

1. Upewnij się, że na maszynie zainstalowano środowisko [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

1. Pobierz i zainstaluj pakiet [Maven](https://maven.apache.org/install.html).

1. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie `git` i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

1. Użyj następującego [omówienia certyfikatów](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), aby utworzyć certyfikaty testowe.

    > [!NOTE]
    > Ten krok wymaga biblioteki [OpenSSL](https://www.openssl.org/), która może zostać skompilowana i zainstalowana ze źródła lub pobrana i zainstalowana od [innego podmiotu](https://wiki.openssl.org/index.php/Binaries), takiego jak [ten](https://sourceforge.net/projects/openssl/). Jeśli certyfikaty: _główny_, _pośredni_ i _urządzenia_ zostały już utworzone, możesz pominąć ten krok.
    >

    1. Wykonaj pierwsze dwa kroki, aby utworzyć certyfikat _główny_ i _pośredni_.

    1. Zaloguj się do witryny Azure Portal, kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie, a następnie otwórz używaną usługę aprowizacji.

        1. W bloku podsumowania usługi Device Provisioning wybierz pozycję **Certyfikaty** i kliknij przycisk **Dodaj** na górze.

        1. W obszarze **Dodawanie certyfikatu** wprowadź następujące informacje:
            - Wprowadź unikatową nazwę certyfikatu.
            - Wybierz utworzony wcześniej plik **_RootCA.pem_**.
            - Gdy skończysz, kliknij przycisk **Zapisz**.

           ![Dodawanie certyfikatu](./media/tutorial-group-enrollments/add-certificate.png)

        1. Wybierz nowo utworzony certyfikat:
            - Kliknij pozycję **Generuj kod weryfikacyjny**. Skopiuj wygenerowany kod.
            - Uruchom krok weryfikacji. Wprowadź _kod weryfikacyjny_ lub kliknij prawym przyciskiem myszy, aby wkleić go w uruchomionym oknie programu PowerShell.   Naciśnij klawisz **Enter**.
            - Wybierz nowo utworzony plik **_verifyCert4.pem_** w witrynie Azure Portal. Kliknij pozycję **Zweryfikuj**.

              ![Sprawdzanie poprawności certyfikatu](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Zakończ, wykonując kroki umożliwiające utworzenie certyfikatów urządzeń i oczyszczenie zasobów.

       > [!NOTE]
       > Podczas tworzenia certyfikatów urządzeń w nazwach urządzeń używaj tylko małych znaków alfanumerycznych i łączników.
       >


## <a name="create-a-device-enrollment-entry"></a>Tworzenie wpisu rejestracji urządzenia

1. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub, aby uzyskać przykłady kodu Java SDK:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. W pobranym kodzie źródłowym przejdź do folderu przykładu **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Otwórz plik **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** w dowolnym edytorze i dodaj następujące szczegółowe informacje:

    1. Dodaj ciąg `[Provisioning Connection String]` dla usługi aprowizacji z portalu, jako pokazano poniżej:

        1. Przejdź do usługi aprowizacji w [Azure Portal](https://portal.azure.com).

        1. Otwórz **Zasady dostępu współużytkowanego** i wybierz zasadę, która ma uprawnienie *EnrollmentWrite*.

        1. Skopiuj **Parametry połączenia klucza podstawowego**.

            ![Pobieranie parametrów połączenia aprowizacji z portalu](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. W przykładowym pliku kodu **_ServiceEnrollmentGroupSample.java_** zastąp ciąg `[Provisioning Connection String]`**parametrami połączenia klucza podstawowego**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Otwórz plik pośredniczącego certyfikatu podpisywania w edytorze tekstów. Zaktualizuj wartość `PUBLIC_KEY_CERTIFICATE_STRING` wartością pośredniczącego certyfikatu podpisywania.

        Jeśli certyfikaty urządzeń wygenerowano za pomocą powłoki Bash, plik *./certs/azure-iot-test-only.intermediate.cert.pem* zawiera klucz certyfikatu pośredniczącego. Jeśli Twoje certyfikaty zostały wygenerowane za pomocą programu PowerShell, plik *./Intermediate1.pem* będzie plikiem certyfikatu pośredniczącego.

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. Przejdź do centrum IoT połączonego z usługą aprowizacji w witrynie [Azure Portal](https://portal.azure.com). Otwórz kartę **Przegląd** centrum i skopiuj wartość pola **Nazwa hosta**. Przypisz tę **nazwę hosta** do parametru *IOTHUB_HOST_NAME*.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Przejrzyj przykładowy kod. Tworzy on, aktualizuje i usuwa rejestrację grupową urządzeń X.509 oraz tworzy do niej zapytanie. Aby sprawdzić poprawność rejestracji w portalu, tymczasowo oznacz jako komentarz następujące wiersze kodu na końcu pliku _ServiceEnrollmentGroupSample.java_:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Zapisz plik _ServiceEnrollmentGroupSample.java_.

1. Otwórz okno polecenia i przejdź do folderu **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

1. Skompiluj przykładowy kod za pomocą tego polecenia:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Uruchom przykładowy kod przy użyciu tych poleceń w oknie polecenia:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Sprawdź poprawność rejestracji w oknie danych wyjściowych.

    ![Pomyślna rejestracja](./media/tutorial-group-enrollments/enrollment.png) 

1. Przejdź do usługi aprowizacji w witrynie Azure Portal. Kliknij pozycję **Zarządzanie rejestracjami**. Należy zauważyć, że grupa urządzeń X. 509 jest wyświetlana na karcie **grupy rejestracji** z automatycznie wygenerowaną *nazwą grupy*.

## <a name="simulate-the-device"></a>Symulowanie urządzenia

1. W bloku podsumowania usługi Device Provisioning wybierz pozycję **Przegląd** i zanotuj _zakres identyfikatorów_ i _globalny punkt końcowy usługi aprowizacji_.

    ![Informacje o usłudze](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Otwórz wiersz polecenia. Przejdź do folderu przykładowego projektu.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Edytuj plik `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, aby uwzględnić w nim zanotowane wcześniej wartości _Identyfikator zakresu_ i _Globalny punkt końcowy usługi aprowizacji_.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. Zaktualizuj zmienne `leafPublicPem` i `leafPrivateKey` publicznym i prywatnym certyfikatem urządzenia.

    Jeśli certyfikaty urządzeń wygenerowano przy użyciu programu PowerShell, pliki mydevice* zawierają klucz publiczny, klucz prywatny i plik PFX dla urządzenia.

    Jeśli certyfikaty urządzeń wygenerowano za pomocą powłoki Bash, plik ./certs/new-device.cert.pem zawiera klucz publiczny. Klucz prywatny urządzenia będzie się znajdować w pliku ./private/new-device.key.pem.

    Otwórz plik klucza publicznego i zaktualizuj zmienną `leafPublicPem` tą wartością. Skopiuj tekst od ciągu _-----BEGIN PRIVATE KEY-----_ do _-----END PRIVATE KEY-----_.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Otwórz plik klucza prywatnego i zaktualizuj zmienną `leafPrivatePem` tą wartością. Skopiuj tekst od ciągu _-----BEGIN RSA PRIVATE KEY-----_ do _-----END RSA PRIVATE KEY-----_.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Dodaj nową zmienną tuż pod zmienną `leafPrivateKey` na potrzeby certyfikatu pośredniczącego. Nadaj tej nowej zmiennej nazwę `intermediateKey`. Zaktualizuj ją wartością pośredniczącego certyfikatu podpisywania.

    Jeśli certyfikaty urządzeń wygenerowano za pomocą powłoki Bash, plik *./certs/azure-iot-test-only.intermediate.cert.pem* zawiera klucz certyfikatu pośredniczącego. Jeśli Twoje certyfikaty zostały wygenerowane za pomocą programu PowerShell, plik *./Intermediate1.pem* będzie plikiem certyfikatu pośredniczącego.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. W funkcji `main` dodaj zmienną `intermediateKey` do kolekcji `signerCertificates` przed zainicjowaniem obiektu `securityProviderX509`.

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Zapisz zmiany i skompiluj przykład. Przejdź do folderu docelowego i wykonaj utworzony plik jar.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Rejestracja zakończona powodzeniem](./media/tutorial-group-enrollments/registration.png)

1. W portalu przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie otwórz blok **Device Explorer**. Po pomyślnej aprowizacji symulowanego urządzenia X.509 w centrum identyfikator urządzenia jest wyświetlany w bloku **Device Explorer** z pozycją *STATUS* (stan) ustawioną na wartość **enabled** (włączone). Należy pamiętać, że konieczne może być kliknięcie przycisku **Odśwież** znajdującego się u góry, jeśli blok został otwarty przed uruchomieniem przykładowej aplikacji urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz dalszą pracę z przykładem klienta urządzenia i eksplorowanie go, nie czyść zasobów utworzonych w ramach tego przewodnika Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoją usługę Device Provisioning Service. Otwórz blok **Zarządzanie rejestracjami** dla usługi, a następnie kliknij kartę **rejestracje indywidualne** . Wybierz *Identyfikator rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie kliknij przycisk **Usuń** u góry. 
1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. Otwórz blok **Urządzenia IoT** Twojego centrum, wybierz *IDENTYFIKATOR URZĄDZENIA* zarejestrowanego w ramach tego przewodnika Szybki start, a następnie kliknij przycisk **Usuń** u góry.


## <a name="next-steps"></a>Następne kroki

W tym samouczku na Twojej maszynie z systemem Windows utworzono symulowane urządzenie X.509, które zostało następnie aprowizowane do Twojego centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning i grup rejestracji. Aby dowiedzieć się więcej o urządzeniu X.509, przejdź do pojęć dotyczących urządzenia. 

> [!div class="nextstepaction"]
> [Koncepcje IoT Hub Device Provisioning Service](concepts-service.md)