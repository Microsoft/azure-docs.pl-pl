---
title: Szybki Start — Inicjowanie obsługi symulowanego urządzenia X. 509 na platformie Azure IoT Hub przy użyciu języka C
description: W tym przewodniku Szybki start używane są rejestracje indywidualne. W tym przewodniku szybki start utworzysz i zainicjujesz symulowane urządzenie X. 509 za pomocą zestawu SDK języka C dla usługi Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/26/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 8f4c35a61edc970f5d4d3beb0c4255ccfe4e336a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968149"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Szybki start: aprowizowanie symulowanego urządzenia X.509 za pomocą zestawu SDK języka C usługi Azure IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Z tego przewodnika Szybki start dowiesz się, jak utworzyć i uruchomić symulator urządzenia X.509 na maszynie deweloperskiej z systemem Windows. To symulowane urządzenie skonfigurujesz tak, aby przypisać je do centrum IoT Hub za pomocą rejestracji w wystąpieniu usługi Device Provisioning Service. W celu symulowania sekwencji uruchamiania dla tego urządzenia będzie używany przykładowy kod z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Urządzenie zostanie rozpoznane na podstawie rejestracji w usłudze Provisioning Service i przypisane do centrum IoT Hub.

Jeśli nie znasz procesu autozastrzegania, zapoznaj się z omówieniem [aprowizacji](about-iot-dps.md#provisioning-process) . Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](quick-setup-auto-provision.md). 

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:

* [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
* [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służą do rejestrowania pojedynczych urządzeń.

W tym artykule przedstawiono rejestracje indywidualne.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne dotyczą środowiska projektowego systemu Windows. W systemie Linux lub macOS zapoznaj się z odpowiednią sekcją w sekcji [Przygotowywanie środowiska deweloperskiego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji zestawu SDK.

* [Program Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Programowanie aplikacji klasycznych w języku C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) . Obsługiwane są również programy Visual Studio 2015 i Visual Studio 2017.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji zostanie przygotowane środowisko programistyczne służące do tworzenia [zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c), który zawiera przykładowy kod dla sekwencji rozruchu X. 509.

1. Pobierz [system kompilacji CMAKE](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK.

3. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium [usługi Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Użyj znacznika znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

4. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. Uruchom następujące polecenia z `azure-iot-sdk-c` katalogu:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Przykładowy kod używa certyfikatu X.509 w celu dostarczenia poświadczeń za pośrednictwem uwierzytelniania X.509. Uruchom następujące polecenie, aby skompilować wersję zestawu SDK specyficzną dla platformy deweloperskiej, która zawiera klienta aprowizacji urządzeń. W katalogu zostanie wygenerowane rozwiązanie programu Visual Studio dla symulowanego urządzenia `cmake` .

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Po pomyślnym zakończeniu kompilacji, Ostatnia część wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym

W tej sekcji zostanie użyty certyfikat z podpisem własnym X.509. Ważne jest, aby pamiętać o następujących kwestiach:

* Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowania i nie powinny być używane w środowisku produkcyjnym.
* Domyślny termin wygaśnięcia certyfikatu z podpisem własnym to jeden rok.

Przykładowy kod z zestawu SDK języka C platformy usługi Azure IoT zostanie użyty do utworzenia certyfikatu, który będzie używany z indywidualnym wpisem rejestracji dla urządzenia symulowanego.

1. Uruchom program Visual Studio i otwórz nowy plik rozwiązania o nazwie `azure_iot_sdks.sln`. Ten plik rozwiązania znajduje się w folderze `cmake` utworzonym wcześniej w katalogu głównym repozytorium Git azure-iot-sdk-c.

2. W menu programu Visual Studio wybierz pozycję **Kompiluj**  >  **kompilację rozwiązania** , aby skompilować wszystkie projekty w rozwiązaniu.

3. W oknie programu Visual Studio *Eksplorator rozwiązań* przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **dice\_device\_enrollment** i wybierz pozycję **Ustaw jako projekt startowy**.

4. W menu programu Visual Studio wybierz kolejno opcje **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić rozwiązanie. W oknie danych wyjściowych po wyświetleniu monitu wprowadź wartość **i**, aby przeprowadzić rejestrację indywidualną.

    W oknie danych wyjściowych zostanie wyświetlony lokalnie wygenerowany certyfikat X.509 z podpisem własnym dla symulowanego urządzenia. Skopiuj do schowka dane wyjściowe od wiersza **-----BEGIN CERTIFICATE-----** do pierwszego wiersza **-----END CERTIFICATE-----**, włącznie z tymi wierszami. Będziesz potrzebować tylko pierwszego certyfikatu z okna danych wyjściowych.

5. Za pomocą edytora tekstów zapisz certyfikat w nowym pliku o nazwie **_X509testcert.pem_**.

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Tworzenie wpisu rejestracji urządzenia w portalu

1. Zaloguj się do Azure Portal, wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz swoją usługę Device Provisioning.

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry.

3. W panelu **Dodaj rejestrację** Wprowadź poniższe informacje, a następnie naciśnij przycisk **Zapisz** .

    * **Mechanizm:** wybierz **X.509** jako *Mechanizm* poświadczania tożsamości.
    * **Plik PEM lub CER certyfikatu podstawowego:** Wybierz **pozycję Wybierz plik** , aby wybrać plik certyfikatu X509testcert. pem utworzony wcześniej.
    * **Identyfikator urządzenia usługi IoT Hub:** wprowadź ciąg **test-docs-cert-device**, aby nadać urządzeniu identyfikator.

      [![Dodawanie rejestracji indywidualnej dla zaświadczania X. 509 w portalu](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Po pomyślnej rejestracji urządzenie X.509 jest wyświetlane jako **riot-device-cert** w kolumnie *Identyfikator rejestracji* na karcie *Indywidualne rejestracje*. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Symulowanie sekwencji pierwszego uruchamiania dla urządzenia

W tej sekcji zaktualizujesz kod przykładowy w celu wysłania sekwencji uruchamiania urządzenia do wystąpienia usługi Device Provisioning Service. Ta sekwencja uruchamiania spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT Hub połączonego z wystąpieniem usługi Device Provisioning Service.

1. W Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning i zanotuj wartość **_Identyfikator zakresu_** .

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W oknie *Eksplorator rozwiązań* programu Visual Studio przejdź do folderu **Provision\_Samples**. Rozwiń przykładowy projekt o nazwie **prov\_dev\_client\_sample**. Rozwiń pozycję **Pliki źródłowe**, a następnie otwórz plik **prov\_dev\_client\_sample.c**.

3. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że zmienna `hsm_type` jest ustawiona na `SECURE_DEVICE_TYPE_X509`, a nie `SECURE_DEVICE_TYPE_TPM`, jak pokazano poniżej.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

6. W menu programu Visual Studio wybierz kolejno opcje **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić rozwiązanie. W wierszu polecenia, aby ponownie skompilować projekt, wybierz opcję **tak** , aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład pomyślnego uruchomienia aprowizowanego urządzenia klienta i połączenia z wystąpieniem usługi Provisioning Service w celu uzyskania informacji na temat centrum IoT Hub oraz rejestracji:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. W portalu przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie wybierz kartę **urządzenia IoT** . Po pomyślnej aprowizacji symulowanego urządzenia X. 509 w centrum identyfikator urządzenia jest wyświetlany w bloku **urządzenia IoT** z opcją *stan* jako **włączone**. Może być konieczne naciśnięcie przycisku **Odśwież** w górnej części ekranu. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-x509/hub-registration.png) 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz pozycję **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz **urządzenia IoT** dla swojego centrum, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono symulowane urządzenie X. 509 na komputerze z systemem Windows i udostępniono je Centrum IoT Hub przy użyciu IoT Hub Device Provisioning Service platformy Azure w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie X. 509 programowo, przejdź do przewodnika Szybki Start dotyczącego rejestrowania na urządzeniach X. 509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — rejestrowanie urządzeń X. 509 w usłudze Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)