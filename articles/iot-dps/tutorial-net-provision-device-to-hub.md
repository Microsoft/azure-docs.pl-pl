---
title: Samouczek — udostępnianie urządzenia przy użyciu usługi Azure IoT Hub Device Provisioning Service (.NET)
description: W tym samouczku pokazano, jak można zainicjować obsługę administracyjną urządzenia w jednym centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning Service (DPS) przy użyciu platformy .NET.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f9a14ee6ee3e10b36d64ec11fc23807efe2bfaf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966568"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Samouczek: rejestrowanie urządzenia w usłudze IoT Hub przy użyciu klienta usługi Azure IoT Hub Provisioning Service (.NET)

W poprzednim samouczku omówiono sposób konfigurowania urządzenia do nawiązywania połączenia z usługą Device Provisioning. W tym samouczku dowiesz się, jak używać tej usługi do aprowizacji urządzenia w jednym centrum IoT za pomocą zarówno **_rejestracji indywidualnej_**, jak i **_grup rejestracji_**. Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie urządzenia
> * Uruchamianie urządzenia
> * Sprawdzanie, czy urządzenie zostało zarejestrowane

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że urządzenie oraz jego *sprzętowy moduł zabezpieczeń* zostały skonfigurowane zgodnie z zaleceniami z samouczka [Konfigurowanie urządzenia do aprowizacji przy użyciu usługi Azure IoT Hub Device Provisioning](./tutorial-set-up-device.md).

* Visual Studio

> [!NOTE]
> Program Visual Studio nie jest wymagany. Wystarczy zainstalować platformę [.NET](https://www.microsoft.com/net). Deweloperzy mogą używać preferowanego edytora w systemie Windows lub Linux.  

Ten samouczek symuluje okres podczas procesu produkcji sprzętu lub bezpośrednio po nim, gdy informacje o urządzeniu są dodawane do usługi aprowizowania. Ten kod jest zwykle uruchamiany na komputerze osobistym lub urządzeniu fabrycznym, które może uruchamiać kod .NET, i nie powinien być dodawany do samych urządzeń.


## <a name="enroll-the-device"></a>Rejestrowanie urządzenia

Ten krok obejmuje dodawanie unikatowych artefaktów zabezpieczeń urządzenia do usługi Device Provisioning. Te artefakty zabezpieczeń są następujące:

- W przypadku urządzeń opartych na modułach TPM:
    - *Klucz poręczenia*, który jest unikatowy dla każdego modułu TPM lub symulacji. Aby uzyskać więcej informacji, zobacz [Understand TPM Endorsement Key](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) (Informacje o kluczu poręczenia modułu TPM).
    - *Identyfikator rejestracji* używany do jednoznacznego identyfikowania urządzenia w zakresie/przestrzeni nazw. Może, ale nie musi być taki sam jak identyfikator urządzenia. Identyfikator jest wymagany dla każdego urządzenia. W przypadku urządzeń opartych na modułach TPM identyfikator rejestracji może pochodzić od samego modułu TPM, na przykład może to być skrót SHA-256 klucza poręczenia modułu TPM.

- W przypadku urządzeń opartych na standardzie X.509:
    - [Certyfikat X.509 wystawiony dla urządzenia](/windows/win32/seccertenroll/about-x-509-public-key-certificates) w postaci pliku *pem* lub *cer*. W przypadku rejestracji indywidualnej należy użyć *certyfikatu liścia* dla systemu X.509, natomiast w przypadku grup rejestracji należy użyć *certyfikatu głównego* lub odpowiedniego *certyfikatu osoby podpisującej*.
    - *Identyfikator rejestracji* używany do jednoznacznego identyfikowania urządzenia w zakresie/przestrzeni nazw. Może, ale nie musi być taki sam jak identyfikator urządzenia. Identyfikator jest wymagany dla każdego urządzenia. W przypadku urządzeń opartych na standardzie X.509 identyfikator rejestracji jest pochodną nazwy pospolitej certyfikatu. Aby uzyskać więcej informacji na temat tych wymagań, zobacz [Pojęcia dotyczące urządzeń](./concepts-service.md).

Istnieją dwa sposoby rejestrowania urządzenia w usłudze Device Provisioning:

- **Rejestracje indywidualne** — jest to reprezentacja wpisu dla pojedynczego urządzenia, które może zostać zarejestrowane w usłudze Device Provisioning. Rejestracje indywidualne mogą używać certyfikatów X.509 lub tokenów SAS (w rzeczywistym lub wirtualnym module TPM) jako mechanizmów zaświadczania. Zalecamy używanie rejestracji indywidualnych w przypadku urządzeń, które wymagają unikatowej konfiguracji początkowej lub urządzeń, które jako mechanizmu zaświadczania mogą używać tylko tokenów SAS za pośrednictwem modułu TPM. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

- **Grupy rejestracji** — jest to reprezentacja grupy urządzeń, które współużytkują specyficzny mechanizm zaświadczania. Firma Microsoft zaleca używanie grupy rejestracji w przypadku dużej liczby urządzeń, które współużytkują pożądaną konfigurację początkową, lub urządzeń przeznaczonych dla tej samej dzierżawy. W przypadku grup rejestracji można stosować tylko certyfikat X.509, a wszystkie te grupy współdzielą certyfikat podpisywania w łańcuchu certyfikatów X.509.

### <a name="enroll-the-device-using-individual-enrollments"></a>Rejestrowanie urządzenia za pomocą rejestracji indywidualnych

1. W programie Visual Studio utwórz projekt aplikacji konsolowej Visual C# za pomocą szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **DeviceProvisioning**.
    
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **DeviceProvisioning**, a następnie kliknij polecenie **Zarządzaj pakietami NuGet...**.

1. W oknie **Menedżer pakietów NuGet** wybierz pozycję **Przeglądaj** i wyszukaj ciąg **microsoft.azure.devices.provisioning.service**. Zaznacz wpis i kliknij przycisk **Instaluj**, aby zainstalować pakiet **Microsoft.Azure.Devices.Provisioning.Service**, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT Device Provisioning](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia usługi Device Provisioning zanotowanymi w poprzedniej sekcji.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Dodaj następujący kod w celu zaimplementowania rejestracji urządzenia:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Na koniec dodaj następujący kod do metody **Main**, aby otworzyć połączenie z centrum IoT i rozpocząć rejestrację:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. W Eksplorator rozwiązań programu Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij pozycję **Ustaw projekty startowe..**.. Wybierz pozycję **pojedynczy projekt startowy**, a następnie wybierz projekt **DeviceProvisioning** z menu rozwijanego.  

1. Uruchom aplikację urządzenia platformy .NET **DeviceProvisioning**. Powinno to spowodować skonfigurowanie aprowizacji dla urządzenia: 

    ![Uruchamianie rejestracji indywidualnej](./media/tutorial-net-provision-device-to-hub/individual.png)

Po pomyślnym zarejestrowaniu urządzenia powinno ono być widoczne w portalu w następujący sposób:

   ![Pomyślna rejestracja w portalu](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Rejestrowanie urządzenia przy użyciu grup rejestracji

> [!NOTE]
> Przykład grupy rejestracji wymaga certyfikatu X.509.

1. W Eksploratorze rozwiązań programu Visual Studio otwórz projekt **DeviceProvisioning** utworzony powyżej. 

1. Dodaj następujące `using` instrukcje w górnej części pliku **program. cs** :
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego lokalizacją certyfikatu X.509.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Dodaj następujący kod do pliku **Program.cs**, aby zaimplementować rejestrację grupy:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Na koniec zastąp kod w metodzie **Main** następującym kodem, aby otworzyć połączenie z centrum IoT i rozpocząć rejestrację grupy:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Uruchom aplikację urządzenia platformy .NET **DeviceProvisioning**. Powinno to spowodować skonfigurowanie aprowizacji grupy dla urządzenia: 

    ![Uruchamianie rejestracji grupy](./media/tutorial-net-provision-device-to-hub/group.png)

    Po pomyślnym zarejestrowaniu grupy urządzeń powinna ona być widoczna w portalu w następujący sposób:

   ![Pomyślna rejestracja grupy w portalu](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Uruchamianie urządzenia

Na tym etapie następujące konfiguracje są gotowe do rejestracji urządzenia:

1. Urządzenie (lub grupa urządzeń) zostało zarejestrowane w usłudze Device Provisioning i 
2. Urządzenie jest gotowe, ma skonfigurowane zabezpieczenia i można do niego uzyskać dostęp za pośrednictwem aplikacji przy użyciu zestawu SDK klienta usługi Device Provisioning.

Uruchom urządzenie, aby umożliwić aplikacji klienckiej rozpoczęcie rejestracji w usłudze Device Provisioning.  


## <a name="verify-the-device-is-registered"></a>Sprawdzanie, czy urządzenie zostało zarejestrowane

Po uruchomieniu urządzenia powinny zostać wykonane następujące działania. Aby uzyskać więcej informacji, zobacz [przykład aprowizacji urządzenia klienta](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device). 

1. Urządzenie wysyła żądanie rejestracji do usługi Device Provisioning.
2. W przypadku urządzeń z modułem TPM usługa Device Provisioning przesyła z powrotem wezwanie do rejestracji, na które odpowiada urządzenie. 
3. Po pomyślnej rejestracji usługa Device Provisioning przesyła z powrotem do urządzenia identyfikator URI centrum IoT, identyfikator urządzenia i zaszyfrowany klucz. 
4. Następnie aplikacja kliencka usługi IoT Hub na urządzeniu łączy się z centrum. 
5. Po pomyślnym nawiązaniu połączenia z centrum urządzenie powinno pojawić się w narzędziu **Device Explorer** w centrum IoT. 

    ![Pomyślne połączenie z centrum w portalu](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie urządzenia
> * Uruchamianie urządzenia
> * Sprawdzanie, czy urządzenie zostało zarejestrowane

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować wiele urządzeń w obrębie centrów ze zrównoważonym obciążeniem. 

> [!div class="nextstepaction"]
> [Aprowizuj urządzenia w centrach IoT Hub ze zrównoważonym obciążeniem](./tutorial-provision-multiple-hubs.md)