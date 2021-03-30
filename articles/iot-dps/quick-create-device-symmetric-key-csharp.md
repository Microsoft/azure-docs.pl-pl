---
title: 'Szybki Start — używanie klucza symetrycznego do aprowizacji urządzenia na platformie Azure IoT Hub przy użyciu języka C #'
description: W tym przewodniku szybki start użyjesz zestawu SDK języka C# dla usługi Device Provisioning Service (DPS), aby udostępnić symetryczne urządzenie klucza do centrum IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: f97840a05115bf5659a6f7579b72786e890051a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92429368"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Szybki Start: Inicjowanie obsługi symetrycznego urządzenia klucza przy użyciu języka C #

W tym przewodniku szybki start dowiesz się, jak zainicjować obsługę maszyny deweloperskiej systemu Windows jako urządzenia w usłudze IoT Hub przy użyciu języka C#. To urządzenie użyje klucza symetrycznego i rejestracji indywidualnej w celu uwierzytelnienia w wystąpieniu usługi Device Provisioning Service (DPS) w celu przypisania do centrum IoT Hub. Przykładowy kod z [przykładów usługi Azure IoT dla języka C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) zostanie użyty do udostępnienia urządzenia. 

Chociaż w tym artykule przedstawiono Inicjowanie obsługi przy użyciu rejestracji indywidualnej, można również użyć grup rejestracji. W przypadku korzystania z grup rejestracji istnieją pewne różnice. Na przykład należy użyć pochodnego klucza urządzenia z unikatowym IDENTYFIKATORem rejestracji dla urządzenia. [Udostępnianie urządzeń z kluczami symetrycznymi](how-to-legacy-device-symm-key.md) zapewnia przykład grupy rejestracji. Aby uzyskać więcej informacji na temat grup rejestracji, zobacz [rejestracje grup na potrzeby zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md#group-enrollments).

Jeśli nie znasz procesu inicjowania obsługi administracyjnej, zapoznaj się z omówieniem [aprowizacji](about-iot-dps.md#provisioning-process) . 

Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). Ten przewodnik Szybki start wymaga utworzonego już wystąpienia usługi Device Provisioning Service.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby zapoznać się z przykładem dla systemu Linux, zobacz temat [Obsługa wielodostępności](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że na komputerze z systemem Windows jest zainstalowany program [.NET Core 2,1 SDK](https://www.microsoft.com/net/download/windows) lub nowszy.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz wystąpienie usługi Device Provisioning Service (DPS).

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 

3. W panelu **Dodaj rejestrację** Wprowadź poniższe informacje, a następnie naciśnij przycisk **Save (Zapisz** ).

   - **Mechanizm:** wybierz **Klucz symetryczny** jako *Mechanizm* poświadczania tożsamości.

   - **Automatycznie Generuj klucze**: zaznacz to pole.

   - **Identyfikator rejestracji**: wprowadź identyfikator rejestracji, aby zidentyfikować rejestrację. Użyj tylko małych znaków alfanumerycznych i kresek (-). Na przykład **Symm-Key-CSharp-Device-01**.

   - **Identyfikator urządzenia usługi IoT Hub:** wprowadź identyfikator urządzenia. Na przykład **CSharp-Device-01**.

     ![Dodawanie indywidualnej rejestracji dla zaświadczenia klucza symetrycznego w portalu](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. Po zapisaniu rejestracji **klucz podstawowy** i **klucz pomocniczy** zostaną wygenerowane i dodane do wpisu rejestracji. Rejestracja urządzenia klucza symetrycznego jest wyświetlana jako **Symm-Key-CSharp-Device-01** w kolumnie *Identyfikator rejestracji* na karcie *indywidualne rejestracje* . 

5. Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego** i **klucza pomocniczego**. Ta wartość klucza i **Identyfikator rejestracji** będą używane później, gdy dodasz zmienne środowiskowe do użycia z przykładowym kodem aprowizacji urządzenia.



## <a name="prepare-the-c-environment"></a>Przygotowanie środowiska C# 

1. Otwórz CMD Git lub środowisko wiersza polecenia git bash. Sklonuj [przykłady usługi Azure IoT dla repozytorium w](https://github.com/Azure-Samples/azure-iot-samples-csharp) serwisie GitHub w języku C# przy użyciu następującego polecenia:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Przygotuj kod aprowizacji urządzenia

W tej sekcji dodasz następujące cztery zmienne środowiskowe, które będą używane jako parametry dla przykładowego kodu aprowizacji urządzenia, aby udostępnić urządzenie klucza symetrycznego. 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

Kod aprowizacji będzie kontaktować się z wystąpieniem DPS na podstawie tych zmiennych w celu uwierzytelnienia urządzenia. Urządzenie zostanie następnie przypisane do centrum IoT, które jest już połączone z wystąpieniem DPS w oparciu o konfigurację rejestracji indywidualnej. Po zainicjowaniu obsługi przykładowego kodu wyśle kilka testów telemetrycznych do centrum IoT Hub.

1. W [Azure Portal](https://portal.azure.com)w menu usługi Device Provisioning wybierz pozycję **Przegląd** i skopiuj swój _punkt końcowy usługi_ i _zakres identyfikatorów_. Te wartości będą używane dla `PROVISIONING_HOST` `DPS_IDSCOPE` zmiennych środowiskowych i.

    ![Informacje o usłudze](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. Otwórz wiersz polecenia i przejdź do *SymmetricKeySample* w repozytorium sklonowanych przykładów:

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. W folderze *SymmetricKeySample* Otwórz *program. cs* w edytorze tekstów i Znajdź wiersze kodu, które ustawiają `individualEnrollmentPrimaryKey` `individualEnrollmentSecondaryKey` ciągi i. Zaktualizuj te wiersze kodu w następujący sposób, aby zmienne środowiskowe były używane zamiast twardej kodowania kluczy.
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "";
        //private const string individualEnrollmentSecondaryKey = "";

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable("PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    Ponadto Znajdź wiersz kodu, który ustawia `registrationId` ciąg i zaktualizuj go w następujący sposób, aby również użyć zmiennej środowiskowej w następujący sposób:

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    Zapisz zmiany w *programie. cs*.

3. W wierszu polecenia Dodaj zmienne środowiskowe dla zakresu identyfikatorów, identyfikatora rejestracji, podstawowego i pomocniczych kluczy symetrycznych skopiowane z rejestracji indywidualnej w poprzedniej sekcji.  

    Poniższe polecenia są przykładami, aby wyświetlić składnię polecenia. Upewnij się, że używasz prawidłowych wartości.

    ```console
    set DPS_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-csharp-device-01
    ```

    ```console
    set PRIMARY_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

    ```console
    set SECONDARY_SYMMETRIC_KEY=Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
    ```


4. Kompiluj i uruchamiaj przykładowy kod przy użyciu następującego polecenia.

    ```console
    dotnet run
    ```

5. Oczekiwane dane wyjściowe powinny wyglądać podobnie do poniższego, który pokazuje połączone centrum IoT, do którego urządzenie zostało przypisane na podstawie ustawień rejestracji indywidualnej. Przykładowy ciąg "TestMessage" jest wysyłany do centrum jako test:

    ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run
    RegistrationID = symm-key-csharp-device-01
    ProvisioningClient RegisterAsync . . . Assigned
    ProvisioningClient AssignedHub: docs-test-iot-hub.azure-devices.net; DeviceID: csharp-device-01
    Creating Symmetric Key DeviceClient authentication
    DeviceClient OpenAsync.
    DeviceClient SendEventAsync.
    DeviceClient CloseAsync.
    Enter any key to exit
    ```
    
6. W Azure Portal przejdź do centrum IoT połączonego z usługą aprowizacji, a następnie otwórz blok **urządzenia IoT** . Po pomyślnym zainicjowaniu obsługi urządzenia klucza symetrycznego w centrum zostanie wyświetlony identyfikator urządzenia z **włączonym** *stanem* . Może być konieczne naciśnięcie przycisku **Odśwież** w górnej części, jeśli blok został już otwarty przed uruchomieniem przykładowego kodu urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz pozycję **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz **urządzenia IoT** dla swojego centrum, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zostało zainicjowane urządzenie klucza symetrycznego opartego na systemie Windows w usłudze IoT Hub przy użyciu IoT Hub Device Provisioning Service. Aby dowiedzieć się, jak zainicjować obsługę administracyjną urządzeń z certyfikatem X. 509 przy użyciu języka C#, przejdź poniżej przewodnika Szybki Start dotyczącego urządzeń X. 509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — Udostępnianie urządzeń X. 509 za pomocą usługi DPS i języka C #](quick-create-simulated-device-x509-csharp.md)
