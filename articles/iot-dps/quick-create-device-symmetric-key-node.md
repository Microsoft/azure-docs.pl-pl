---
title: Szybki Start — używanie klucza symetrycznego do aprowizacji urządzenia w usłudze Azure IoT Hub przy użyciu Node.js
description: W tym przewodniku szybki start użyjesz zestawu SDK usługi Azure IoT dla Node.js z usługą Device Provisioning Service (DPS), aby udostępnić symetryczne urządzenie klucza do centrum IoT.
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 78005ba46952bcf05b19c7627feecb1ec30ac651
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92429360"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-nodejs"></a>Szybki Start: Inicjowanie obsługi klucza symetrycznego za pomocą Node.js

W tym przewodniku szybki start dowiesz się, jak zainicjować obsługę maszyny deweloperskiej systemu Windows jako urządzenia w usłudze IoT Hub przy użyciu Node.js. To urządzenie użyje klucza symetrycznego i rejestracji indywidualnej w celu uwierzytelnienia w wystąpieniu usługi Device Provisioning Service (DPS) w celu przypisania do centrum IoT Hub. Przykładowy kod z [zestawu Azure IoT SDK dla Node.js](https://github.com/Azure/azure-iot-sdk-node.git) zostanie użyty do udostępnienia urządzenia. 

Chociaż w tym artykule przedstawiono Inicjowanie obsługi przy użyciu rejestracji indywidualnej, można również użyć grup rejestracji. W przypadku korzystania z grup rejestracji istnieją pewne różnice. Na przykład należy użyć pochodnego klucza urządzenia z unikatowym IDENTYFIKATORem rejestracji dla urządzenia. [Udostępnianie urządzeń z kluczami symetrycznymi](how-to-legacy-device-symm-key.md) zapewnia przykład grupy rejestracji. Aby uzyskać więcej informacji na temat grup rejestracji, zobacz [rejestracje grup na potrzeby zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md#group-enrollments).

Jeśli nie znasz procesu inicjowania obsługi administracyjnej, zapoznaj się z omówieniem [aprowizacji](about-iot-dps.md#provisioning-process) . 

Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). Ten przewodnik Szybki start wymaga utworzonego już wystąpienia usługi Device Provisioning Service.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby zapoznać się z przykładem dla systemu Linux, zobacz temat [Obsługa wielodostępności](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się z pojęciami [aprowizacji](about-iot-dps.md#provisioning-process) .
- Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org).
- Usługi [git](https://git-scm.com/download/).


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz wystąpienie usługi Device Provisioning Service (DPS).

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 

3. W panelu **Dodaj rejestrację** Wprowadź poniższe informacje, a następnie naciśnij przycisk **Save (Zapisz** ).

   - **Mechanizm:** wybierz **Klucz symetryczny** jako *Mechanizm* poświadczania tożsamości.

   - **Automatycznie Generuj klucze**: zaznacz to pole.

   - **Identyfikator rejestracji**: wprowadź identyfikator rejestracji, aby zidentyfikować rejestrację. Użyj tylko małych znaków alfanumerycznych i kresek (-). Na przykład **Symm-Key-NodeJS-Device-01**.

   - **Identyfikator urządzenia usługi IoT Hub:** wprowadź identyfikator urządzenia. Na przykład **NodeJS-Device-01**.

     ![Dodawanie indywidualnej rejestracji dla zaświadczenia klucza symetrycznego w portalu](./media/quick-create-device-symmetric-key-node/create-individual-enrollment-node.png)

4. Po zapisaniu rejestracji **klucz podstawowy** i **klucz pomocniczy** zostaną wygenerowane i dodane do wpisu rejestracji. Rejestracja urządzenia klucza symetrycznego jest wyświetlana jako **Symm-Key-NodeJS-Device-01** w kolumnie *Identyfikator rejestracji* na karcie *indywidualne rejestracje* . 

5. Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego**. Ta wartość klucza i **Identyfikator rejestracji** będą używane później, gdy dodasz zmienne środowiskowe do użycia z przykładowym kodem aprowizacji urządzenia.



## <a name="prepare-the-nodejs-environment"></a>Przygotuj środowisko Node.js 

1. Otwórz CMD Git lub środowisko wiersza polecenia git bash. Sklonuj [zestaw SDK usługi Azure IoT dla Node.js](https://github.com/Azure/azure-iot-sdk-node.git) repozytorium GitHub przy użyciu następującego polecenia:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```


<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Przygotuj kod aprowizacji urządzenia

W tej sekcji dodasz następujące cztery zmienne środowiskowe, które będą używane jako parametry dla przykładowego kodu aprowizacji urządzenia, aby udostępnić urządzenie klucza symetrycznego. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

Kod aprowizacji będzie kontaktować się z wystąpieniem DPS na podstawie tych zmiennych w celu uwierzytelnienia urządzenia. Urządzenie zostanie następnie przypisane do centrum IoT, które jest już połączone z wystąpieniem DPS w oparciu o konfigurację rejestracji indywidualnej. Po zainicjowaniu obsługi przykładowego kodu wyśle kilka testów telemetrycznych do centrum IoT Hub.

1. W [Azure Portal](https://portal.azure.com)w menu usługi Device Provisioning wybierz pozycję **Przegląd** i skopiuj swój _punkt końcowy usługi_ i _zakres identyfikatorów_. Te wartości będą używane dla `PROVISIONING_HOST` `PROVISIONING_IDSCOPE` zmiennych środowiskowych i.

    ![Informacje o usłudze](./media/quick-create-device-symmetric-key-node/extract-dps-endpoints.png)

2. Otwórz wiersz polecenia służący do wykonywania poleceń Node.js i przejdź do katalogu " *Inicjowanie obsługi/urządzenia/przykłady* ".

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

3. W folderze *Inicjowanie obsługi/urządzenia/przykłady* Otwórz *register_symkey.js* i Przejrzyj kod. 

    Zwróć uwagę, że przykładowy kod ustawia niestandardowy ładunek...

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    Ten kod nie jest wymagany w tym przewodniku Szybki Start. Ten kod stanowi przykład ustawienia niestandardowego ładunku, jeśli chcesz użyć niestandardowej funkcji alokacji do przypisania urządzenia do IoT Hub. Aby uzyskać więcej informacji, zobacz [Samouczek: używanie niestandardowych zasad alokacji](tutorial-custom-allocation-policies.md).

    `provisioningClient.register()`Metoda próbuje zarejestrować urządzenie.

    Kod przykładowy nie będzie potrzebny do zarejestrowania urządzenia.

4. W wierszu polecenia Dodaj zmienne środowiskowe dla hosta aprowizacji, zakresu identyfikatora, identyfikatora rejestracji i podstawowego klucza symetrycznego skopiowanego z rejestracji indywidualnej w poprzedniej sekcji.  

    Poniższe polecenia są przykładami, aby wyświetlić składnię polecenia. Upewnij się, że używasz prawidłowych wartości.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-nodejs-device-01
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```


4. Kompiluj i uruchamiaj przykładowy kod przy użyciu następujących poleceń.

    ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

5. Oczekiwane dane wyjściowe powinny wyglądać podobnie do poniższego, który pokazuje połączone centrum IoT, do którego urządzenie zostało przypisane na podstawie ustawień rejestracji indywidualnej. Ciąg "Hello world" jest wysyłany do centrum jako wiadomość testowa:

    ```output
    D:\Docs\test\azure-iot-sdk-node\provisioning\device\samples>node register_symkey.js
    registration succeeded
    assigned hub=docs-test-iot-hub.azure-devices.net
    deviceId=nodejs-device-01
    payload=undefined
    Client connected
    send status: MessageEnqueued    
    ```
    
6. W Azure Portal przejdź do centrum IoT połączonego z usługą aprowizacji, a następnie otwórz blok **urządzenia IoT** . Po pomyślnym zainicjowaniu obsługi urządzenia klucza symetrycznego w centrum zostanie wyświetlony identyfikator urządzenia z **włączonym** *stanem* . Może być konieczne naciśnięcie przycisku **Odśwież** w górnej części, jeśli blok został już otwarty przed uruchomieniem przykładowego kodu urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-device-symmetric-key-node/hub-registration-node.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz pozycję **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz **urządzenia IoT** dla swojego centrum, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zostało zainicjowane urządzenie klucza symetrycznego opartego na systemie Windows w usłudze IoT Hub przy użyciu IoT Hub Device Provisioning Service. Aby dowiedzieć się, jak zainicjować obsługę administracyjną urządzeń z certyfikatem X. 509 przy użyciu Node.js, przejdź do poniższego przewodnika Szybki Start dla urządzeń X. 509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — Udostępnianie urządzeń X. 509 za pomocą usługi DPS i Node.js](quick-create-simulated-device-x509-node.md)
