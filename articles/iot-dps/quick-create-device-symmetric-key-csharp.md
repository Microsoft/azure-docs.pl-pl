---
title: 'Szybki start — używanie klucza symetrycznego do aprowizować urządzenie do Azure IoT Hub przy użyciu języka C #'
description: W tym przewodniku Szybki start użyjemy zestawu SDK języka C# dla usługi Device Provisioning Service (DPS) do aprowizowania urządzenia z kluczem symetrycznym w centrum IoT
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: a38d58eecd200ec312e7677a05531e27bf4ba6b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868703"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Szybki start: aprowizuje urządzenie z kluczem symetrycznym przy użyciu języka C #

W tym przewodniku Szybki start dowiesz się, jak aprowizować maszynę dewelopera z systemem Windows jako urządzenie w centrum IoT Przy użyciu języka C#. To urządzenie będzie używać klucza symetrycznego i indywidualnej rejestracji do uwierzytelniania za pomocą wystąpienia usługi Device Provisioning Service (DPS) w celu przypisania do centrum IoT. Przykładowy kod z [przykładów usługi Azure IoT dla języka C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) zostanie użyty do aprowizowania urządzenia. 

Chociaż w tym artykule pokazano aprowizowanie z rejestracją indywidualną, można również użyć grup rejestracji. Istnieją pewne różnice w przypadku korzystania z grup rejestracji. Na przykład należy użyć pochodnego klucza urządzenia z unikatowym identyfikatorem rejestracji dla urządzenia. [Przykładowa grupa rejestracji zawiera aprowizowanie](how-to-legacy-device-symm-key.md) urządzeń za pomocą kluczy symetrycznych. Aby uzyskać więcej informacji na temat grup rejestracji, zobacz [Rejestracje grupowe dla zaświadczenia klucza symetrycznego](concepts-symmetric-key-attestation.md#group-enrollments).

Jeśli nie masz jeszcze informacji na temat procesu automatycznego aprowizowania, zapoznaj się z [omówieniem aprowacji.](about-iot-dps.md#provisioning-process) 

Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). Ten przewodnik Szybki start wymaga utworzonego już wystąpienia usługi Device Provisioning Service.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby uzyskać przykład dla systemu Linux, zobacz [Provision for multitenancy (Aprowizacja dla wielowątkości).](how-to-provision-multitenant.md)


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że na maszynie z systemem Windows jest zainstalowany zestaw [SDK platformy .NET Core 2.1](https://dotnet.microsoft.com/download) lub nowszy.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz przycisk **Wszystkie** zasoby w menu po lewej stronie i otwórz wystąpienie usługi Device Provisioning Service (DPS).

2. Wybierz **kartę Zarządzanie rejestracjami,** a następnie wybierz przycisk **Dodaj rejestrację** indywidualną u góry strony. 

3. W **panelu Dodawanie** rejestracji wprowadź następujące informacje i naciśnij **przycisk** Zapisz.

   - **Mechanizm:** wybierz **Klucz symetryczny** jako *Mechanizm* poświadczania tożsamości.

   - **Automatycznie generuj klucze:** zaznacz to pole wyboru.

   - **Identyfikator rejestracji**: wprowadź identyfikator rejestracji, aby zidentyfikować rejestrację. Użyj tylko małych znaków alfanumerycznych i kresek (-). Na przykład **symm-key-csharp-device-01.**

   - **Identyfikator urządzenia usługi IoT Hub:** wprowadź identyfikator urządzenia. Na przykład **csharp-device-01**.

     ![Dodawanie indywidualnej rejestracji dla zaświadczenia klucza symetrycznego w portalu](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. Po zapisaniu rejestracji zostaną wygenerowane  **i** dodane do wpisu rejestracji klucz podstawowy i klucz pomocniczy. Rejestracja urządzenia klucza symetrycznego jest wyświetlana jako **symm-key-csharp-device-01** w kolumnie *Identyfikator* rejestracji na *karcie Indywidualne rejestracje.* 

5. Otwórz rejestrację i skopiuj wartość wygenerowanego klucza **podstawowego i** **klucza pomocniczego.** Tej wartości klucza i  identyfikatora rejestracji użyjemy później, gdy dodasz zmienne środowiskowe do użycia z przykładowym kodem aprowizowania urządzeń.



## <a name="prepare-the-c-environment"></a>Przygotowywanie środowiska C# 

1. Otwórz środowisko wiersza polecenia Git CMD lub Git Bash. Sklonuj [repozytorium GitHub przykładów usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp) dla języka C# za pomocą następującego polecenia:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Przygotowywanie kodu aprowizowania urządzenia

W tej sekcji dodasz następujące cztery zmienne środowiskowe, które będą używane jako parametry przykładowego kodu aprowowania urządzeń w celu aprowizowania urządzenia z kluczem symetrycznym. 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

Kod aprowizowania skontaktuje się z wystąpieniem usługi DPS na podstawie tych zmiennych w celu uwierzytelnienia urządzenia. Następnie urządzenie zostanie przypisane do centrum IoT hub już połączonego z wystąpieniem usługi DPS na podstawie konfiguracji rejestracji indywidualnej. Po aprowizowania przykładowy kod wyśle dane telemetryczne testowe do centrum IoT.

1. W menu [Azure Portal](https://portal.azure.com)device provisioning wybierz pozycję Przegląd, a następnie skopiuj punkt końcowy usługi _i_ _zakres identyfikatorów._  Użyjemy tych wartości dla zmiennych `PROVISIONING_HOST` `DPS_IDSCOPE` środowiskowych i .

    ![Informacje o usłudze](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. Otwórz wiersz polecenia i przejdź do aplikacji *SymmetricKeySample* w sklonowanym repozytorium przykładów:

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. W *folderze SymmetricKeySample* otwórz *plik Program.cs* w edytorze tekstów i znajdź wiersze kodu, które ustawiają `individualEnrollmentPrimaryKey` ciągi `individualEnrollmentSecondaryKey` i . Zaktualizuj te wiersze kodu w następujący sposób, aby zamiast kodowania kluczy były używane zmienne środowiskowe.
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "&quot;;
        //private const string individualEnrollmentSecondaryKey = &quot;&quot;;

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable(&quot;PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    Ponadto znajdź wiersz kodu, który ustawia ciąg, i zaktualizuj go w następujący sposób, aby użyć `registrationId` zmiennej środowiskowej w następujący sposób:

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    Zapisz zmiany w *programie Program.cs.*

3. W wierszu polecenia dodaj zmienne środowiskowe dla zakresu identyfikatorów, identyfikatora rejestracji, podstawowych i pomocniczych kluczy symetrycznych skopiowanych z indywidualnej rejestracji w poprzedniej sekcji.  

    Następujące polecenia są przykładami, aby wyświetlić składnię polecenia. Upewnij się, że używasz prawidłowych wartości.

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


4. Skompilowanie i uruchomienie przykładowego kodu przy użyciu następującego polecenia.

    ```console
    dotnet run
    ```

5. Oczekiwane dane wyjściowe powinny wyglądać podobnie do poniższych, które pokazują połączone centrum IoT, do którego przypisano urządzenie, na podstawie indywidualnych ustawień rejestracji. Przykładowy ciąg "TestMessage" jest wysyłany do centrum jako test:

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
    
6. W Azure Portal przejdź do centrum IoT połączonego z twoją usługą aprowiwizowania i otwórz blok **Urządzenia IoT.** Po pomyślnym aprowizowania urządzenia klucza symetrycznego w centrum identyfikator urządzenia jest wyświetlany z *włączonym* **stanem .** Może być konieczne naciśnięcie **przycisku Odśwież** u góry, jeśli blok został już otwarty przed uruchomieniem przykładowego kodu urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym przewodniku Szybki start.

1. W menu po lewej stronie w Azure Portal wybierz pozycję **Wszystkie zasoby,** a następnie wybierz swoją usługę Device Provisioning. Otwórz **okno Zarządzanie rejestracjami** dla usługi, a następnie wybierz **kartę Rejestracje** indywidualne. Zaznacz pole wyboru obok identyfikatora *REJESTRACJI* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
1. W menu po lewej stronie w Azure Portal wybierz pozycję **Wszystkie zasoby,** a następnie wybierz swoje centrum IoT. Otwórz **urządzenia IoT** dla centrum, zaznacz pole  wyboru obok identyfikatora urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk Usuń w górnej części okienka. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start aprowizowane zostało urządzenie z kluczem symetrycznym oparte na systemie Windows do centrum IoT przy użyciu IoT Hub Device Provisioning Service. Aby dowiedzieć się, jak aprowizować urządzenia z certyfikatami X.509 przy użyciu języka C#, przejdź do poniższego przewodnika Szybki start dla urządzeń X.509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki start platformy Azure — aprowizuj urządzenia X.509 przy użyciu usług DPS i C #](quick-create-simulated-device-x509-csharp.md)
