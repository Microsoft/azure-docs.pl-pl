---
title: Szybki Start — Inicjowanie obsługi symulowanego urządzenia X. 509 na platformie Azure IoT Hub przy użyciu języka Python
description: Szybki Start — tworzenie i Inicjowanie obsługi symulowanego urządzenia X. 509 za pomocą zestawu SDK języka Python dla IoT Hub Device Provisioning Service (DPS). W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 01/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: c151f78c6164cc62aac618a141a26eb1da574e3c
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218392"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Szybki Start: Tworzenie i udostępnianie symulowanego urządzenia X. 509 za pomocą zestawu SDK języka Python dla IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

W tym przewodniku szybki start zainicjujesz komputer deweloperski jako urządzenie języka Python X. 509. Używasz przykładowego kodu urządzenia z [zestawu SDK usługi Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python) , aby podłączyć urządzenie do centrum IoT. Rejestracja indywidualna jest używana z usługą Device Provisioning (DPS) w tym przykładzie.

## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się z pojęciami [aprowizacji](about-iot-dps.md#provisioning-process) .
- Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.5.3 lub nowszy](https://www.python.org/downloads/)
- Usługi [git](https://git-scm.com/download/).


[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie `git` i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

2. Otwórz wiersz polecenia git bash. Sklonuj repozytorium GitHub dla [zestawu Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python).
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```


## <a name="create-a-self-signed-x509-device-certificate"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym 

W tej sekcji utworzysz certyfikat X. 509 z podpisem własnym. Należy pamiętać o następujących ważnych kwestiach:

* Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowania i nie powinny być używane w środowisku produkcyjnym.
* Domyślny termin wygaśnięcia certyfikatu z podpisem własnym to jeden rok.

Jeśli nie masz jeszcze certyfikatów urządzeń do uwierzytelniania urządzenia, możesz utworzyć certyfikat z podpisem własnym za pomocą OpenSSL do testowania w tym artykule.  OpenSSL jest dołączany do instalacji usługi git. 

1. Uruchom następujące polecenie w wierszu polecenia narzędzia Git bash.

    # <a name="windows"></a>[Windows](#tab/windows)
    
    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > Dodatkowy ukośnik w polu Nazwa podmiotu ( `//CN=Python-device-01` ) jest wymagany tylko w przypadku ucieczki ciągu przy użyciu usługi Git na platformach systemu Windows. 

    # <a name="linux"></a>[Linux](#tab/linux)
    
    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```
    
    ---
    
2. Gdy zostanie wyświetlony monit o **wprowadzenie frazy dostępu PEM:**, użyj frazy Przekaż `1234` do testowania w tym artykule.    

3. Po ponownym wyświetleniu monitu o ponowne **sprawdzenie frazy:** Użyj ponownie frazy `1234` .    

Plik certyfikatu testowego (*Python-Device. pem*) i plik klucza prywatnego (*Python-Device. Key. pem*) są generowane w katalogu, w którym uruchomiono `openssl` polecenie.


## <a name="create-an-individual-enrollment-entry-in-dps"></a>Tworzenie indywidualnego wpisu rejestracji w usłudze DPS


Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:

- [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
- [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służy do rejestrowania jednego urządzenia.

W tym artykule przedstawiono rejestrację indywidualną dla jednego urządzenia, która ma zostać zainicjowana za pomocą Centrum IoT Hub.

1. Zaloguj się do Azure Portal, wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz swoją usługę aprowizacji.

2. W menu usługi Device Provisioning wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **indywidualne rejestracje** i wybierz u góry przycisk **Dodaj rejestrację indywidualną** . 

3. W panelu **Dodawanie rejestracji** wprowadź następujące informacje:
   - Wybierz opcję **X.509** jako *Mechanizm* poświadczania tożsamości.
   - W obszarze *plik PEM lub CER certyfikatu podstawowego* wybierz *opcję Wybierz plik* , aby wybrać plik certyfikatu **Python-Device. pem** , jeśli używasz certyfikatu testowego utworzonego wcześniej.
   - Opcjonalnie można podać następujące informacje:
     - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
     - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
   - Po zakończeniu naciśnij przycisk **Zapisz** . 

     [![Dodawanie rejestracji indywidualnej dla zaświadczania X. 509 w portalu](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Po pomyślnej rejestracji urządzenie X. 509 jest wyświetlane jako **Python-Device-01** w kolumnie *Identyfikator rejestracji* na karcie *indywidualne rejestracje* . Ta wartość rejestracji pochodzi z nazwy podmiotu w certyfikacie urządzenia. 

## <a name="simulate-the-device"></a>Symulowanie urządzenia

Przykład aprowizacji języka Python, [provision_x509. PR](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) , znajduje się w `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` katalogu. Ten przykład wykorzystuje sześć zmiennych środowiskowych do uwierzytelniania i aprowizacji urządzenia IoT przy użyciu usługi DPS. Te zmienne środowiskowe są następujące:

| Nazwa zmiennej              | Opis                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Ta wartość jest globalnym punktem końcowym używanym do łączenia się z zasobem usługi DPS |    
| `PROVISIONING_IDSCOPE`     |  Ta wartość jest zakresem identyfikatorów dla zasobu usługi DPS |    
| `DPS_X509_REGISTRATION_ID` |  Ta wartość jest IDENTYFIKATORem urządzenia. Musi być również zgodna z nazwą podmiotu w certyfikacie urządzenia |    
| `X509_CERT_FILE`           |  Nazwa pliku certyfikatu urządzenia |    
| `X509_KEY_FILE`            |  Nazwa pliku klucza prywatnego dla certyfikatu urządzenia |
| `PASS_PHRASE`              |  Hasło użyte do zaszyfrowania pliku certyfikatu i klucza prywatnego ( `1234` ). |    

1. W menu usługi Device Provisioning wybierz pozycję **Przegląd**. Zanotuj _zakres identyfikatorów_ i _globalny punkt końcowy urządzenia_.

    ![Informacje o usłudze](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. W wierszu polecenia usługi git bash Użyj następujących poleceń, aby dodać zmienne środowiskowe dla globalnego punktu końcowego urządzenia i zakresu identyfikatorów.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

3. Identyfikator rejestracji urządzenia IoT musi odpowiadać nazwie podmiotu w certyfikacie urządzenia. Jeśli Wygenerowano certyfikat testu z podpisem własnym, `Python-device-01` to nazwa podmiotu i Identyfikator rejestracji dla urządzenia. 

    Jeśli masz już certyfikat urządzenia, możesz użyć `certutil` programu do zweryfikowania nazwy pospolitej podmiotu używanej przez urządzenie, jak pokazano poniżej dla certyfikatu testu z podpisem własnym:

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

    W wierszu polecenia narzędzia Git bash Ustaw zmienną środowiskową dla identyfikatora rejestracji w następujący sposób:

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

4. W wierszu polecenia usługi git bash Ustaw zmienne środowiskowe dla pliku certyfikatu, pliku klucza prywatnego i frazę.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

5. Przejrzyj kod dla [provision_x509. PR](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) , jeśli nie używasz języka **Python w wersji 3,7** lub nowszej, wprowadź [tutaj zmianę kodu](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) , aby zamienić `asyncio.run(main())` i zapisać zmiany. 

6. Uruchom przykład. Przykład zostanie nawiązane połączenie, zainicjuje urządzenie w centrum i wyśle kilka komunikatów testowych do centrum.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

7. W portalu przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie otwórz blok **urządzenia IoT** znajdujący się w sekcji **eksplorators** w menu po lewej stronie. Po pomyślnej aprowizacji symulowanego urządzenia X.509 w centrum identyfikator urządzenia jest wyświetlany w bloku **Device Explorer** z pozycją *STATUS* (stan) ustawioną na wartość **enabled** (włączone). Może być konieczne naciśnięcie przycisku **Odśwież** w górnej części, jeśli blok został już otwarty przed uruchomieniem przykładowej aplikacji urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
2. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz blok **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
3. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz blok **urządzenia IoT** dla centrum, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono symulowane urządzenie X. 509 na maszynie deweloperskiej i udostępniono je Centrum IoT Hub przy użyciu IoT Hub Device Provisioning Service platformy Azure w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie X. 509 programowo, przejdź do przewodnika Szybki Start dotyczącego rejestrowania na urządzeniach X. 509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — rejestrowanie urządzeń X. 509 w usłudze Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)
