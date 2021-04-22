---
title: 'Szybki start — aprowizować symulowane urządzenie X.509 w celu Azure IoT Hub przy użyciu języka C #'
description: Szybki start — tworzenie i aprowizowanie urządzenia X.509 przy użyciu zestawu SDK języka C# dla usługi Azure IoT Hub Device Provisioning Service (DPS). W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 02/01/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 7aca75d1abed5470d51de22f9285459381f684bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868595"
---
# <a name="quickstart-create-and-provision-an-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Szybki start: tworzenie i aprowizować urządzenie X.509 przy użyciu zestawu SDK języka C# dla IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Te kroki pokazują, jak aprowizować urządzenie X.509 za pomocą kodu urządzenia z przykładów [usługi Azure IoT dla języka C#.](https://github.com/Azure-Samples/azure-iot-samples-csharp) W tym artykule zostanie uruchomiony przykładowy kod urządzenia na komputerze dewelopera, aby nawiązać połączenie z IoT Hub przy użyciu usługi Device Provisioning Service.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze informacji na temat procesu automatycznego aprowizowania, zapoznaj się z [omówieniem aprowacji.](about-iot-dps.md#provisioning-process) Pamiętaj również, aby wcześniej wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md).

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:
- [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
- [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służą do rejestrowania pojedynczych urządzeń.

W tym artykule przedstawiono rejestracje indywidualne.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego 

1. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie `git` i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

1. Otwórz wiersz polecenia lub powłokę Git Bash. Sklonuj repozytorium GitHub Azure IoT Samples for C#:
    
    ```bash
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Upewnij się, że na maszynie jest zainstalowany zestaw [SDK platformy .NET Core 3.1](https://dotnet.microsoft.com/download) lub nowszy. Aby sprawdzić swoją wersję, możesz użyć następującego polecenia.

    ```bash
    dotnet --info
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym

W tej sekcji utworzysz certyfikat testowy X.509 z podpisem własnym przy użyciu `iothubx509device1` nazwy pospolitej podmiotu. Należy pamiętać o następujących ważnych kwestiach:

* Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowania i nie powinny być używane w środowisku produkcyjnym.
* Domyślny termin wygaśnięcia certyfikatu z podpisem własnym to jeden rok.
* Identyfikator urządzenia IoT będzie nazwą pospolitą podmiotu w certyfikacie. Upewnij się, że używasz nazwy podmiotu, która jest zgodna z wymaganiami [ciągów identyfikatorów urządzeń.](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)

Przykładowy kod z przykładu [X509Sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) zostanie użyty do utworzenia certyfikatu do użycia z indywidualnym wpisem rejestracji dla urządzenia.


1. W wierszu polecenia programu PowerShell zmień katalogi na katalog projektu dla przykładu aprowowania urządzeń X.509.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Przykładowy kod jest skonfigurowany do używania certyfikatów X.509 przechowywanych w chronionym hasłem sformatowanym pliku PKCS12 (certificate.pfx). Ponadto potrzebny jest plik certyfikatu klucza publicznego (certificate.cer), aby utworzyć rejestrację indywidualną w dalszej części tego przewodnika Szybki start. Aby wygenerować certyfikat z podpisem własnym i skojarzone z nim pliki cer i pfx, uruchom następujące polecenie:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. Skrypt wyświetli monit o podanie hasła PFX. Zapamiętaj to hasło, ponieważ będzie ono konieczne później, po uruchomieniu przykładu. Możesz uruchomić program `certutil` , aby zrzucić certyfikat i zweryfikować nazwę podmiotu.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

 ## <a name="create-an-individual-enrollment-entry-for-the-device"></a>Tworzenie wpisu rejestracji indywidualnej dla urządzenia


1. Zaloguj się do Azure Portal, wybierz przycisk **Wszystkie** zasoby w menu po lewej stronie i otwórz swoją usługę aprowowania.

2. W menu Device Provisioning Service wybierz pozycję **Zarządzaj rejestracjami.** Wybierz **kartę Rejestracje** indywidualne i wybierz przycisk **Dodaj rejestrację indywidualną** u góry strony. 

3. W **panelu Dodawanie** rejestracji wprowadź następujące informacje:
   - Wybierz opcję **X.509** jako *Mechanizm* poświadczania tożsamości.
   - W obszarze *Plik PEM* lub CER certyfikatu podstawowego wybierz *pozycję* Wybierz plik, aby wybrać plik certyfikatu **certificate.cer** utworzony w poprzednich krokach.
   - Zostaw pole **Identyfikator urządzenia** puste. Urządzenie zostanie zaaprowizowane z identyfikatorem urządzenia ustawionym na nazwę pospolitą (CN) w certyfikacie X.509 **iothubx509device1**. Ta nazwa pospolita będzie również nazwą używaną dla identyfikatora rejestracji dla wpisu rejestracji indywidualnej. 
   - Opcjonalnie można podać następujące informacje:
       - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
       - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
   - Po zakończeniu naciśnij **przycisk** Zapisz. 

     [![Dodawanie indywidualnej rejestracji dla zaświadczenia X.509 w portalu](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Po pomyślnej rejestracji wpis rejestracji X.509 jest wyświetlany jako **iothubx509device1** w kolumnie *Identyfikator rejestracji* na karcie *Indywidualne rejestracje*. 



## <a name="provision-the-device"></a>Aprowizuj urządzenie

1. **Zanotuj** wartość **_Zakres_** identyfikatorów w bloku Przegląd dla usługi aprowiwizowania.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Wpisz następujące polecenie, aby skompilować i uruchomić przykład aprowizacji urządzenia X.509. Zastąp wartość `<IDScope>` zakresem identyfikatorów Twojej usługi aprowizacji. 

    Domyślnie plik certyfikatu to *./certificate.pfx* i jest wyświetlany monit o hasło pfx.  

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Jeśli chcesz przekazać wszystko jako parametr, możesz użyć następującego przykładowego formatu.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```


3. Urządzenie połączy się z dps i zostanie przypisane do IoT Hub. Urządzenie dodatkowo wyśle komunikat telemetrii do centrum.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

4. Upewnij się, że urządzenie zostało zaaprowizowane. Po pomyślnej aprowice urządzenia w centrum IoT Połączonym z twoją usługą aprowiwizowania identyfikator urządzenia zostanie wyświetlony w bloku **Urządzenia IoT** centrum. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym przewodniku Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Zamknij okno symulatora modułu TPM na swojej maszynie.
1. W menu po lewej stronie w Azure Portal wybierz pozycję **Wszystkie** zasoby, a następnie wybierz swoją usługę Device Provisioning Service. W górnej części **bloku Przegląd** naciśnij pozycję **Usuń** w górnej części okienka.  
1. W menu po lewej stronie w Azure Portal wybierz pozycję **Wszystkie zasoby,** a następnie wybierz swoje centrum IoT. W górnej części **bloku Przegląd** naciśnij pozycję **Usuń** w górnej części okienka.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start aprowizowanie urządzenia X.509 w centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning. Aby dowiedzieć się, jak zarejestrować urządzenie X.509 programowo, przejdź do przewodnika Szybki start na temat programowej rejestracji urządzeń X.509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki start platformy Azure — rejestrowanie urządzeń X.509 w Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-csharp.md)
