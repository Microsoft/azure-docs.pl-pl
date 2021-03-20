---
title: Szybki Start — używanie klucza symetrycznego do aprowizacji urządzenia w usłudze Azure IoT Hub przy użyciu języka Python
description: W tym przewodniku szybki start użyjesz zestawu SDK języka Python usługi Azure IoT, aby udostępnić symetryczne urządzenie klucza do centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 8b87d9d487257504a438cf13a5b94e3ca4f9233d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92426368"
---
# <a name="quickstart-provision-a-python-device-with-symmetric-keys"></a>Szybki Start: Inicjowanie obsługi urządzenia w języku Python przy użyciu kluczy symetrycznych

W tym przewodniku szybki start dowiesz się, jak zainicjować obsługę maszyny deweloperskiej systemu Windows jako urządzenia w usłudze IoT Hub przy użyciu języka Python. To urządzenie użyje klucza symetrycznego i rejestracji indywidualnej w celu uwierzytelnienia w wystąpieniu usługi Device Provisioning Service (DPS) w celu przypisania do centrum IoT Hub. Przykładowy kod z [zestawu SDK usługi Azure IoT Python](https://github.com/Azure/azure-iot-sdk-python) zostanie użyty do udostępnienia urządzenia. 

Chociaż w tym artykule przedstawiono Inicjowanie obsługi przy użyciu rejestracji indywidualnej, można również użyć grup rejestracji. W przypadku korzystania z grup rejestracji istnieją pewne różnice. Na przykład należy użyć pochodnego klucza urządzenia z unikatowym IDENTYFIKATORem rejestracji dla urządzenia. Mimo że grupy rejestracji klucza symetrycznego nie są ograniczone do starszych urządzeń, artykuł [Aprowizowanie starszych urządzeń za pomocą zaświadczenia klucza symetrycznego](how-to-legacy-device-symm-key.md) zawiera przykład grupy rejestracji. Aby uzyskać więcej informacji, zobacz [Rejestrowanie grupy dla zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md#group-enrollments).

Jeśli nie znasz procesu inicjowania obsługi administracyjnej, zapoznaj się z omówieniem [aprowizacji](about-iot-dps.md#provisioning-process) . 

Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). Ten przewodnik Szybki start wymaga utworzonego już wystąpienia usługi Device Provisioning Service.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby uzyskać przykład dla systemu Linux, zobacz [Aprowizowanie pod kątem wielu dzierżaw](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że na komputerze z systemem Windows jest zainstalowany język [Python 3,7](https://www.python.org/downloads/) lub nowszy. Aby sprawdzić wersję środowiska Python, należy uruchomić program `python --version` .

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-python-sdk-environment"></a>Przygotowanie środowiska zestawu SDK języka Python 

1. Upewnij się, że na Twojej maszynie jest zainstalowane oprogramowanie Git i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

2. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub dla zestawu Azure IoT Python SDK:
    
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```
3. Przejdź do katalogu, w `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` którym znajduje się przykładowy plik _provision_symmetric_key. PR_.
   
   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```
4. Zainstaluj bibliotekę _Azure-IoT-Device_ , uruchamiając następujące polecenie.

    ```console
    pip install azure-iot-device
    ```


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz wystąpienie usługi Device Provisioning Service (DPS).

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 

3. W panelu **Dodaj rejestrację** Wprowadź poniższe informacje, a następnie naciśnij przycisk **Save (Zapisz** ).

   - **Mechanizm:** wybierz **Klucz symetryczny** jako *Mechanizm* poświadczania tożsamości.

   - **Automatycznie Generuj klucze**: zaznacz to pole.

   - **Identyfikator rejestracji**: wprowadź identyfikator rejestracji, aby zidentyfikować rejestrację. Użyj tylko małych znaków alfanumerycznych i kresek (-). Na przykład **Symm-Key-Python-Device-008**.

   - **Identyfikator urządzenia usługi IoT Hub:** wprowadź identyfikator urządzenia. Na przykład **Python-Device-008**.

     ![Dodawanie indywidualnej rejestracji dla zaświadczenia klucza symetrycznego w portalu](./media/quick-create-device-symm-key-python/create-individual-enrollment-python.png)

4. Po zapisaniu rejestracji **klucz podstawowy** i **klucz pomocniczy** zostaną wygenerowane i dodane do wpisu rejestracji. Rejestracja urządzenia klucza symetrycznego jest wyświetlana jako **Symm-Key-Python-Device-008** w kolumnie *Identyfikator rejestracji* na karcie *indywidualne rejestracje* . 

5. Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego**. Ta wartość klucza i **Identyfikator rejestracji** będą używane później, gdy dodasz zmienne środowiskowe do użycia z przykładowym kodem aprowizacji urządzenia.



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Przygotuj kod aprowizacji urządzenia

W tej sekcji dodasz następujące cztery zmienne środowiskowe, które będą używane jako parametry dla przykładowego kodu inicjowania obsługi urządzenia dla urządzenia klucza symetrycznego. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

Kod aprowizacji będzie kontaktować się z wystąpieniem DPS na podstawie tych zmiennych w celu uwierzytelnienia urządzenia. Urządzenie zostanie następnie przypisane do centrum IoT, które jest już połączone z wystąpieniem DPS w oparciu o konfigurację rejestracji indywidualnej. Po zainicjowaniu obsługi przykładowego kodu wyśle kilka testów telemetrycznych do centrum IoT Hub.

1. W [Azure Portal](https://portal.azure.com)w menu usługi Device Provisioning wybierz pozycję **Przegląd** i skopiuj swój _punkt końcowy usługi_ i _zakres identyfikatorów_. Te wartości będą używane dla `PROVISIONING_HOST` `PROVISIONING_IDSCOPE` zmiennych środowiskowych i.

    ![Informacje o usłudze](./media/quick-create-device-symm-key-python/extract-dps-endpoints.png)

2. W wierszu polecenia języka Python Dodaj zmienne środowiskowe przy użyciu skopiowanych wartości. 

    Poniższe polecenia są przykładami, aby wyświetlić składnię polecenia. Upewnij się, że używasz prawidłowych wartości.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

3. W wierszu polecenia języka Python Dodaj zmienne środowiskowe dla identyfikatora rejestracji i klucza symetrycznego skopiowane z rejestracji indywidualnej w poprzedniej sekcji. 

    Poniższe polecenia są przykładami, aby wyświetlić składnię polecenia. Upewnij się, że używasz prawidłowych wartości.

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-python-device-008
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

4. Uruchom przykładowy kod w języku Python w _provision_symmetric_key. PR_.

    ```console
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    ```

5. Oczekiwane dane wyjściowe powinny wyglądać podobnie do poniższego, który pokazuje połączone centrum IoT, do którego urządzenie zostało przypisane na podstawie ustawień rejestracji indywidualnej. Niektóre przykładowe komunikaty telemetryczne dotyczące szybkości wiatru są również wysyłane do centrum jako test:

    ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```
    
6. W Azure Portal przejdź do centrum IoT połączonego z usługą aprowizacji, a następnie otwórz blok **urządzenia IoT** . Po pomyślnym zainicjowaniu obsługi urządzenia klucza symetrycznego w centrum zostanie wyświetlony identyfikator urządzenia z **włączonym** *stanem* . Może być konieczne naciśnięcie przycisku **Odśwież** w górnej części, jeśli blok został już otwarty przed uruchomieniem przykładowego kodu urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-device-symm-key-python/hub-registration-python.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz pozycję **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz **urządzenia IoT** dla swojego centrum, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zostało zainicjowane urządzenie klucza symetrycznego opartego na systemie Windows w usłudze IoT Hub przy użyciu IoT Hub Device Provisioning Service. Aby dowiedzieć się, jak zainicjować obsługę administracyjną urządzeń z certyfikatem X. 509 przy użyciu języka Python, przejdź poniżej przewodnika Szybki Start dotyczącego urządzeń X. 509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — Udostępnianie urządzeń X. 509 za pomocą usługi DPS i języka Python](quick-create-simulated-device-x509-python.md)
