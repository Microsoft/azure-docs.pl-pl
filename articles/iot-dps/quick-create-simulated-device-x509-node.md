---
title: Szybki Start — Inicjowanie obsługi symulowanego urządzenia X. 509 na platformie Azure IoT Hub przy użyciu Node.js
description: Utwórz i zainicjuj symulowane urządzenie X. 509 za pomocą zestawu SDK urządzeń Node.js dla usługi Azure IoT Hub Device Provisioning Service (DPS). Ten przewodnik Szybki Start używa indywidualnych rejestracji.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc, devx-track-js
ms.openlocfilehash: fe5279725a3d01001b44074eca8656e88d778cab
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968166"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Szybki Start: Tworzenie i Inicjowanie obsługi symulowanego urządzenia X. 509 za pomocą zestawu SDK urządzeń Node.js dla IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

W tym przewodniku szybki start utworzysz symulowane urządzenie X. 509 na komputerze z systemem Windows. Możesz użyć przykładowego kodu Node.js urządzenia do połączenia tego symulowanego urządzenia z usługą IoT Hub przy użyciu indywidualnej rejestracji w usłudze Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się z pojęciami [aprowizacji](about-iot-dps.md#provisioning-process) .
- Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org).
- Usługi [git](https://git-scm.com/download/).
- [OpenSSL](https://www.openssl.org/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Zanim przejdziesz dalej, wykonaj kroki przedstawione w artykule [Konfigurowanie usługi IoT Hub Device Provisioning w witrynie Azure Portal](./quick-setup-auto-provision.md).

2. Upewnij się, że na komputerze jest zainstalowane [środowisko Node.js w wersji 4.0 lub nowszej](https://nodejs.org).

3. Upewnij się, że na maszynie zainstalowano narzędzie [git](https://git-scm.com/download/) i dodano je do zmiennych środowiskowych dostępnych w oknie poleceń. 

4. Upewnij się, że na Twojej maszynie jest zainstalowana biblioteka [OpenSSL](https://www.openssl.org/) i że jest ona dodana do zmiennych środowiskowych dostępnych z okna poleceń. Ta biblioteka może zostać skompilowana i zainstalowana ze źródła lub pobrana i zainstalowana od [innego podmiotu](https://wiki.openssl.org/index.php/Binaries), takiego jak [ten](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Jeśli masz już utworzony certyfikat _główny_, _pośredni_ i/lub _liścia_ X.509, możesz pominąć ten krok i wszystkie następne czynności dotyczące generowania certyfikatów.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym i wpisu rejestracji indywidualnej

W tej sekcji zostanie użyty certyfikat z podpisem własnym X.509. Ważne jest, aby pamiętać o następujących kwestiach:

* Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowania i nie powinny być używane w środowisku produkcyjnym.
* Domyślny termin wygaśnięcia certyfikatu z podpisem własnym to jeden rok.

Przykładowy kod z [zestawu Azure IoT SDK dla środowiska Node.js](https://github.com/Azure/azure-iot-sdk-node.git) zostanie użyty do utworzenia certyfikatu, który będzie używany z indywidualnym wpisem rejestracji dla urządzenia symulowanego.

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:

- [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
- [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służy do rejestrowania jednego urządzenia.

W tym artykule przedstawiono rejestracje indywidualne.

1. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub, aby uzyskać przykłady kodu:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Przejdź do skryptu generatora certyfikatu i skompiluj projekt. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Utwórz certyfikat X.509 _liścia_, uruchamiając skrypt z użyciem Twojego parametru _certificate-name_. Nazwa pospolita certyfikatu liścia staje się [identyfikatorem rejestracji](./concepts-service.md#registration-id), dlatego używaj tylko małych znaków alfanumerycznych i łączników.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz wystąpienie usługi Device Provisioning Service.

5. W menu usługi Device Provisioning wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **indywidualne rejestracje** i wybierz u góry przycisk **Dodaj rejestrację indywidualną** . 

6. W panelu **Dodawanie rejestracji** wprowadź następujące informacje:
   - Wybierz opcję **X.509** jako *Mechanizm* poświadczania tożsamości.
   - W obszarze *plik PEM lub CER certyfikatu podstawowego* wybierz *pozycję Wybierz plik* , aby wybrać plik certyfikatu **{Certificate-Name} _cert. pem** utworzony w poprzednich krokach.  
   - Opcjonalnie można podać następujące informacje:
     - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
     - Wprowadź unikatowy identyfikator urządzenia. Nadając nazwę urządzeniu, unikaj korzystania z danych poufnych. 
     - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
     - Po zakończeniu naciśnij przycisk **Zapisz** . 

     [![Dodawanie rejestracji indywidualnej dla zaświadczania X. 509 w portalu](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     Po pomyślnej rejestracji urządzenie X. 509 jest wyświetlane jako **{certificateName}** w kolumnie *Identyfikator rejestracji* na karcie *indywidualne rejestracje* . Należy pamiętać, że ta wartość jest w przyszłości.

## <a name="simulate-the-device"></a>Symulowanie urządzenia

[Zestaw SDK urządzenia środowiska Node.js usługi Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-node) zapewnia prosty sposób symulowania urządzenia. Aby przeczytać więcej, zobacz [Device concepts (Pojęcia dotyczące urządzeń)](./concepts-service.md).

1. W Azure Portal wybierz blok **Przegląd** dla swojej usługi Device Provisioning i zanotuj wartości **_globalny punkt końcowy urządzenia_** oraz **_zakres identyfikatorów_** .

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Skopiuj _certyfikat_ i _klucz_ do przykładowego folderu.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Przejdź do skryptu testowego urządzenia i skompiluj projekt. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Przeprowadź edycję pliku **register\_x509.js**. Po wprowadzeniu poniższych zmian zapisz plik.
    - Zastąp wartość `provisioning host`**_globalnym punktem końcowym urządzenia_** zanotowanym w **kroku 1** powyżej.
    - Zamień `id scope` na **_zakres identyfikatorów_** zanotowany w **kroku 1** powyżej. 
    - Zamień na `registration id` **_Identyfikator rejestracji_** zanotowany w poprzedniej sekcji.
    - Zastąp wartości `cert filename` i `key filename` plikami skopiowanymi w **kroku 2** powyżej. 

5. Uruchom skrypt i sprawdź, czy urządzenie zostało pomyślnie aprowizowane.

    ```cmd/sh
    node register_x509.js
    ```   

6. W portalu przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie otwórz blok **urządzenia IoT** . Po pomyślnej aprowizacji symulowanego urządzenia X. 509 w centrum identyfikator urządzenia jest wyświetlany w bloku **urządzenia IoT** z opcją *stan* jako **włączone**. Może być konieczne naciśnięcie przycisku **Odśwież** w górnej części, jeśli blok został już otwarty przed uruchomieniem przykładowej aplikacji urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
2. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz blok **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
3. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz blok **urządzenia IoT** dla centrum, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono symulowane urządzenie X. 509, które zostało zainicjowane w usłudze IoT Hub przy użyciu IoT Hub Device Provisioning Service platformy Azure w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie X. 509 programowo, przejdź do przewodnika Szybki Start dotyczącego rejestrowania na urządzeniach X. 509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — rejestrowanie urządzeń X. 509 w usłudze Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-node.md)