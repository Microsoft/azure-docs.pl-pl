---
title: 'Szybki Start — Inicjowanie obsługi symulowanego urządzenia X. 509 na platformie Azure IoT Hub przy użyciu języka C #'
description: Przewodnik Szybki Start — tworzenie i Inicjowanie obsługi symulowanego urządzenia X. 509 za pomocą zestawu SDK języka C# dla usługi Azure IoT Hub Device Provisioning Service (DPS). W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 02/01/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: a6e859a39cbcf867e3c0a21bb59c6154cbd47412
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430593"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Szybki Start: Tworzenie i udostępnianie symulowanego urządzenia X. 509 za pomocą zestawu SDK języka C# dla IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Te kroki pokazują, jak symulować urządzenie X.509 na maszynie deweloperskiej z systemem operacyjnym Windows przy użyciu [przykładów usługi Azure IoT dla języka C#](https://github.com/Azure-Samples/azure-iot-samples-csharp). Ten przykład obejmuje także połączenie symulowanego urządzenia z usługą IoT Hub przy użyciu usługi Device Provisioning.

Jeśli nie znasz procesu autozastrzegania, zapoznaj się z omówieniem [aprowizacji](about-iot-dps.md#provisioning-process) . Pamiętaj również, aby wcześniej wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). 

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

1. Upewnij się, że na maszynie jest zainstalowany [zestaw SDK platformy .NET Core 3.0.0 lub nowszy](https://www.microsoft.com/net/download/windows) . Aby sprawdzić swoją wersję, możesz użyć poniższego polecenia.

    ```bash
    dotnet --info
    ```



## <a name="create-a-self-signed-x509-device-certificate"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym

W tej sekcji utworzysz certyfikat testu X. 509 z podpisem własnym za pomocą `iothubx509device1` nazwy pospolitej podmiotu. Ważne jest, aby pamiętać o następujących kwestiach:

* Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowania i nie powinny być używane w środowisku produkcyjnym.
* Domyślny termin wygaśnięcia certyfikatu z podpisem własnym to jeden rok.
* Identyfikator urządzenia dla urządzenia IoT będzie wspólną nazwą podmiotu w certyfikacie. Upewnij się, że używasz nazwy podmiotu, która jest zgodna z [wymaganiami dotyczącymi ciągu identyfikatora urządzenia](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

Przykładowy kod z [przykładu aprowizacji urządzenia klienta — zaświadczanie za pomocą certyfikatu X.509](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) zostanie użyty do utworzenia certyfikatu, który będzie używany z indywidualnym wpisem rejestracji dla urządzenia symulowanego.


1. W wierszu polecenia programu PowerShell zmień katalogi na katalog projektu dla przykładu aprowizacji urządzenia X. 509.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Przykładowy kod jest skonfigurowany do używania certyfikatów X.509 przechowywanych w chronionym hasłem sformatowanym pliku PKCS12 (certificate.pfx). Ponadto do utworzenia rejestracji indywidualnej w dalszej części tego przewodnika Szybki Start potrzebny jest plik certyfikatu klucza publicznego (Certificate. cer). Aby wygenerować certyfikat z podpisem własnym i skojarzone z nim pliki CER i PFX, uruchom następujące polecenie:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. Skrypt wyświetli monit o podanie hasła PFX. Zapamiętaj to hasło, należy później użyć go ponownie po uruchomieniu przykładu. Można uruchomić `certutil` zrzut certyfikatu i zweryfikować nazwę podmiotu.

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

 ## <a name="create-an-individual-enrollment-entry-for-the-device"></a>Tworzenie indywidualnego wpisu rejestracji dla urządzenia


1. Zaloguj się do Azure Portal, wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz swoją usługę aprowizacji.

2. W menu usługi Device Provisioning wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **indywidualne rejestracje** i wybierz u góry przycisk **Dodaj rejestrację indywidualną** . 

3. W panelu **Dodawanie rejestracji** wprowadź następujące informacje:
   - Wybierz opcję **X.509** jako *Mechanizm* poświadczania tożsamości.
   - W obszarze *plik PEM lub CER certyfikatu podstawowego* wybierz *pozycję Wybierz plik* , aby wybrać certyfikat pliku certyfikatu **. cer** utworzony w poprzednich krokach.
   - Zostaw pole **Identyfikator urządzenia** puste. Urządzenie zostanie zaaprowizowane z identyfikatorem urządzenia ustawionym na nazwę pospolitą (CN) w certyfikacie X.509 **iothubx509device1**. Ta nazwa będzie również używana na potrzeby identyfikatora rejestracji dla wpisu rejestracji indywidualnej. 
   - Opcjonalnie można podać następujące informacje:
       - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
       - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
   - Po zakończeniu naciśnij przycisk **Zapisz** . 

     [![Dodawanie rejestracji indywidualnej dla zaświadczania X. 509 w portalu](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Po pomyślnej rejestracji wpis rejestracji X.509 jest wyświetlany jako **iothubx509device1** w kolumnie *Identyfikator rejestracji* na karcie *Indywidualne rejestracje*. 



## <a name="provision-the-simulated-device"></a>Aprowizowanie urządzenia symulowanego

1. W bloku **Przegląd** dla usługi aprowizacji Zanotuj wartość **_Identyfikator zakresu_** .

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Wpisz następujące polecenie, aby skompilować i uruchomić przykład aprowizacji urządzenia X.509. Zastąp wartość `<IDScope>` zakresem identyfikatorów Twojej usługi aprowizacji. 

    Plik certyfikatu będzie domyślnie mieć wartość *./Certificate.pfx* i zostanie wyświetlony monit o podanie hasła PFX.  

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Jeśli chcesz przekazać wszystkie elementy jako parametry, możesz użyć następującego przykładowego formatu.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```


3. Urządzenie nawiąże połączenie z usługą DPS i zostanie przypisane do IoT Hub. Urządzenie wyśle Dodatkowo komunikat telemetrii do centrum.

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

4. Upewnij się, że urządzenie zostało zaaprowizowane. Po pomyślnej aprowizacji symulowanego urządzenia w centrum IoT Hub powiązanym z Twoją usługą aprowizacji identyfikator urządzenia jest wyświetlany w bloku **urządzenia IoT** centrum. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Zamknij okno symulatora modułu TPM na swojej maszynie.
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. W górnej części bloku **Przegląd** naciśnij pozycję **Usuń** w górnej części okienka.  
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. W górnej części bloku **Przegląd** naciśnij pozycję **Usuń** w górnej części okienka.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono symulowane urządzenie X. 509 na komputerze z systemem Windows i udostępniono je Centrum IoT Hub przy użyciu IoT Hub Device Provisioning Service platformy Azure w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie X. 509 programowo, przejdź do przewodnika Szybki Start dotyczącego rejestrowania na urządzeniach X. 509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — rejestrowanie urządzeń X. 509 w usłudze Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-csharp.md)
