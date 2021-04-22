---
title: 'Szybki start — rejestrowanie urządzenia TPM w usłudze Azure Device Provisioning przy użyciu języka C #'
description: Szybki start — rejestrowanie urządzenia TPM w usłudze Azure IoT Hub Device Provisioning Service (DPS) przy użyciu zestawu SDK usługi C#. W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 73a9b27c7b9119ee49fc451ca0a1c03d8db3db0e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868631"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Szybki start: rejestrowanie urządzenia TPM w usłudze IoT Hub Device Provisioning Service zestawu SDK usługi C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

W tym artykule pokazano, jak programowo utworzyć rejestrację indywidualną dla urządzenia TPM w usłudze Azure IoT Hub Device Provisioning przy użyciu zestawu SDK usługi [języka C#](https://github.com/Azure/azure-iot-sdk-csharp) i przykładowej aplikacji .NET Core w języku C#. Możesz opcjonalnie zarejestrować symulowane urządzenie TPM w usłudze aprowizowania przy użyciu tego wpisu rejestracji indywidualnej. Chociaż te kroki działają zarówno na komputerach z systemem Windows, jak i Linux, w tym artykule jest używany komputer dewelopera z systemem Windows.

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

1. Sprawdź, [czy Visual Studio 2019 na](https://www.visualstudio.com/vs/) komputerze.

1. Sprawdź, czy [na komputerze zestaw .NET Core SDK](https://dotnet.microsoft.com/download) zainstalowane aplikacje.

1. Przed kontynuowaniem wykonaj kroki opisane w IoT Hub Device Provisioning Service za pomocą [Azure Portal.](./quick-setup-auto-provision.md)

1. (Opcjonalnie) Jeśli chcesz zarejestrować symulowane urządzenie na końcu tego przewodnika Szybki start, postępuj zgodnie z procedurą z tematu Tworzenie i aprowizować symulowane urządzenie TPM przy użyciu zestawu SDK urządzenia w języku [C#,](quick-create-simulated-device-tpm-csharp.md) aż do kroku, w którym otrzymasz klucz poręczenia dla urządzenia. Zapisz klucz poręczenia, identyfikator rejestracji i, opcjonalnie, identyfikator urządzenia, ponieważ będą potrzebne w dalszej części tego przewodnika Szybki start.

   > [!NOTE]
   > Nie postępuj zgodnie z instrukcjami, aby utworzyć rejestrację indywidualną przy użyciu Azure Portal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Uzyskiwanie parametrów połączenia usługi aprowizacji

Na potrzeby przykładu w tym przewodniku Szybki start będą potrzebne parametry połączenia usługi aprowizacji.

1. Zaloguj się do Azure Portal, wybierz **pozycję Wszystkie zasoby,** a następnie swoją usługę Device Provisioning Service.

1. Wybierz **pozycję Zasady dostępu współużytkowego,** a następnie wybierz zasady dostępu, których chcesz użyć, aby otworzyć ich właściwości. W **zasadach dostępu** skopiuj i zapisz ciąg połączenia klucza podstawowego.

    ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Tworzenie przykładowej rejestracji indywidualnej

W tej sekcji pokazano, jak utworzyć aplikację konsolową .NET Core, która dodaje rejestrację indywidualną urządzenia TPM do usługi aprowizowania. Wprowadzając pewne modyfikacje, za pomocą tych kroków można również utworzyć aplikację konsolową [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) służącą do dodawania rejestracji indywidualnej. Aby dowiedzieć się więcej na temat tworzenia aplikacji za pomocą usługi IoT Core, zobacz dokumentację dla deweloperów [systemu Windows IoT Core.](/windows/iot-core/)

1. Otwórz Visual Studio i wybierz **pozycję Create a new project (Utwórz nowy projekt).** W **oknie Tworzenie nowego projektu** wybierz szablon projektu Aplikacja **konsolowa (.NET Core)** dla języka C# i wybierz pozycję **Dalej.**

1. Nadaj *projektowi nazwę CreateTpmEnrollment* i naciśnij przycisk **Utwórz.**

    ![Konfigurowanie projektu Visual C# Windows Classic Desktop](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Gdy rozwiązanie zostanie otwarte w Visual Studio, w **okienku Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **CreateTpmEnrollment.** Wybierz **pozycję Zarządzaj pakietami NuGet.**

1. W **witrynie Menedżer pakietów NuGet** wybierz pozycję **Przeglądaj,** wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Provisioning.Service,** a następnie naciśnij **przycisk Zainstaluj.**

   ![Okno Menedżera pakietów NuGet](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Ten krok powoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet zestawu SDK klienta usługi [Azure IoT Provisioning Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje po innych `using` instrukcjach w górnej części : `Program.cs`
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Dodaj następujące pola do `Program` klasy , aby wprowadzić zmiany wymienione poniżej.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Zastąp wartość `ProvisioningServiceConnectionString` symbolu zastępczego ciągami połączenia usługi aprowizowania, dla której chcesz utworzyć rejestrację.

   * Opcjonalnie możesz zmienić identyfikator rejestracji, klucz poręczenia, identyfikator urządzenia i stan aprowizowania.

   * Jeśli używasz tego przewodnika Szybki start wraz z przewodnikem Szybki start Tworzenie i aprowizować symulowane urządzenie TPM przy użyciu zestawu SDK urządzenia w języku [C#,](quick-create-simulated-device-tpm-csharp.md) aby aprowizować symulowane urządzenie, zastąp klucz poręczenia i identyfikator rejestracji wartościami zanotowane w tym przewodniku Szybki start. Możesz zastąpić identyfikator urządzenia wartością sugerowaną w tym przewodniku Szybki start, użyć własnej wartości lub użyć wartości domyślnej w tym przykładzie.

1. Dodaj następującą metodę do `Program` klasy .  Ten kod tworzy wpis rejestracji indywidualnej, a następnie wywołuje metodę w metodzie , aby dodać rejestrację indywidualną `CreateOrUpdateIndividualEnrollmentAsync` `ProvisioningServiceClient` do usługi aprowizowania.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Na koniec zastąp `Main` metodę następującymi wierszami:

   ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
   ```

1. Skompiluj rozwiązanie.

## <a name="run-the-individual-enrollment-sample"></a>Uruchamianie przykładowej rejestracji indywidualnej
  
Uruchom przykład w programie Visual Studio, aby utworzyć rejestrację indywidualną dla urządzenia TPM.

Zostanie wyświetlone okno wiersza polecenia z komunikatami potwierdzającymi. Po pomyślnym utworzeniu w oknie wiersza polecenia zostaną wyświetlone właściwości nowej rejestracji indywidualnej.

Możesz sprawdzić, czy rejestracja indywidualna została utworzona. Przejdź do podsumowania usługi Device Provisioning i wybierz pozycję **Zarządzaj rejestracjami,** a następnie wybierz **pozycję Rejestracje indywidualne.** Powinien zostać wyświetlony nowy wpis rejestracji odpowiadający identyfikatorowi rejestracji użytemu w przykładzie.

![Właściwości rejestracji w portalu](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Wybierz wpis, aby zweryfikować klucz poręczenia i inne właściwości wpisu.

Jeśli instrukcje z przewodnika Szybki start Tworzenie i aprowizować symulowane urządzenie TPM przy użyciu zestawu SDK urządzeń w języku [C#](quick-create-simulated-device-tpm-csharp.md) zostały opisane w przewodniku Szybki start, możesz kontynuować pracę z pozostałymi krokami w tym przewodniku Szybki start, aby zarejestrować symulowane urządzenie. Pomiń kroki tworzenia rejestracji indywidualnej przy użyciu witryny Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz eksplorowanie przykładowej usługi języka C#, nie czyść zasobów utworzonych w tym przewodniku Szybki start. W przeciwnym razie użyj poniższych kroków, aby usunąć wszystkie zasoby utworzone w tym przewodniku Szybki start.

1. Zamknij okno danych wyjściowych przykładu w języku C# na komputerze.

1. Przejdź do usługi Device Provisioning service w Azure Portal, wybierz pozycję Zarządzaj rejestracjami, a następnie wybierz **kartę Rejestracje indywidualne.** Zaznacz pole wyboru obok  identyfikatora rejestracji wpisu rejestracji utworzonego w tym  przewodniku Szybki start, a następnie naciśnij przycisk Usuń w górnej części okienka.

1. Jeśli w celu utworzenia symulowanego urządzenia TPM wykonaj kroki opisane w te tematu Create [and provision a simulated TPM device using C# device SDK](quick-create-simulated-device-tpm-csharp.md) (Tworzenie i aprowizowane symulowane urządzenie TPM przy użyciu zestawu SDK urządzenia w języku C#), wykonaj następujące czynności:

    1. Zamknij okno symulatora modułu TPM i przykładowe okno danych wyjściowych urządzenia symulowanego.

    1. W witrynie Azure Portal przejdź do usługi IoT Hub, w której zaaprowizowano urządzenie. W menu w obszarze **Eksploratory** wybierz pozycję Urządzenia **IoT,** zaznacz pole wyboru obok identyfikatora urządzenia zarejestrowanego  w tym przewodniku Szybki start, a następnie naciśnij przycisk Usuń w górnej części okienka. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start programowo utworzono wpis rejestracji indywidualnej dla urządzenia TPM. Opcjonalnie utworzono symulowane urządzenie TPM na komputerze i aprowizowanie go w centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal.

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)