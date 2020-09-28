---
title: 'Szybki Start — rejestrowanie urządzenia TPM w usłudze Azure Device Provisioning przy użyciu języka C #'
description: Szybki Start — rejestrowanie urządzenia TPM w usłudze Azure IoT Hub Device Provisioning Service (DPS) przy użyciu zestawu SDK usługi C#. W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 872d1f43f1e9dc180e41a2bf625ca18b74522b40
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409514"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Szybki Start: rejestrowanie urządzenia TPM w IoT Hub Device Provisioning Service przy użyciu zestawu SDK usługi C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

W tym artykule pokazano, jak programowo utworzyć rejestrację indywidualną dla urządzenia TPM w usłudze Azure IoT Hub Device Provisioning Service przy użyciu [zestawu SDK usługi c#](https://github.com/Azure/azure-iot-sdk-csharp) i przykładowej aplikacji .NET Core w języku c#. Opcjonalnie można zarejestrować symulowane urządzenie TPM w usłudze aprowizacji przy użyciu tego wpisu rejestracji indywidualnej. Chociaż te kroki działają na komputerach z systemem Windows i Linux, w tym artykule jest wykorzystywany komputer deweloperski systemu Windows.

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

1. Upewnij się, że na komputerze jest zainstalowany [program Visual Studio 2019](https://www.visualstudio.com/vs/) .

1. Upewnij się, że na komputerze zainstalowano [zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows) .

1. Przed kontynuowaniem wykonaj kroki opisane w temacie [konfigurowanie IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md) .

1. Obowiązkowe Jeśli chcesz zarejestrować symulowane urządzenie na końcu tego przewodnika Szybki Start, postępuj zgodnie z procedurą opisaną w temacie [Tworzenie i udostępnianie symulowanego urządzenia TPM za pomocą zestawu SDK urządzenia w języku C#](quick-create-simulated-device-tpm-csharp.md) do kroku, w którym uzyskujesz Klucz poręczenia dla urządzenia. Zapisz klucz poręczenia, Identyfikator rejestracji i, opcjonalnie, identyfikator urządzenia, ponieważ musisz użyć ich w dalszej części tego przewodnika Szybki Start.

   > [!NOTE]
   > Nie wykonuj kroków, aby utworzyć rejestrację indywidualną przy użyciu Azure Portal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Uzyskiwanie parametrów połączenia usługi aprowizacji

Na potrzeby przykładu w tym przewodniku Szybki start będą potrzebne parametry połączenia usługi aprowizacji.

1. Zaloguj się do Azure Portal, wybierz pozycję **wszystkie zasoby**, a następnie usługę Device Provisioning.

1. Wybierz pozycję **zasady dostępu współdzielonego**, a następnie wybierz zasady dostępu, których chcesz użyć, aby otworzyć jego właściwości. W obszarze **zasady dostępu**skopiuj i Zapisz parametry połączenia klucza podstawowego.

    ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Tworzenie przykładowej rejestracji indywidualnej

W tej sekcji przedstawiono sposób tworzenia aplikacji konsolowej .NET Core, która dodaje rejestrację indywidualną dla urządzenia TPM do usługi aprowizacji. Wprowadzając pewne modyfikacje, za pomocą tych kroków można również utworzyć aplikację konsolową [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) służącą do dodawania rejestracji indywidualnej. Aby dowiedzieć się więcej na temat programowania przy użyciu programu IoT Core, zobacz [dokumentację dla deweloperów w systemie Windows IoT Core](https://docs.microsoft.com/windows/iot-core/).

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**. W obszarze **Utwórz nowy projekt**wybierz szablon projektu **aplikacja konsoli (.NET Core)** dla języka C# i wybierz pozycję **dalej**.

1. Nazwij projekt *CreateTpmEnrollment*, a następnie naciśnij pozycję **Create (Utwórz**).

    ![Konfigurowanie klasycznego projektu systemu Windows w języku Visual C#](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Gdy rozwiązanie zostanie otwarte w programie Visual Studio, w okienku **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **CreateTpmEnrollment** . Wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**, Wyszukaj i wybierz pozycję **Microsoft. Azure. Devices. Provisioning. Service**, a następnie naciśnij klawisz **Install**.

   ![Okno Menedżera pakietów NuGet](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK klienta usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) i jego zależności.

1. Dodaj następujące `using` instrukcje `using` w górnej części okna `Program.cs` :
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Dodaj następujące pola do `Program` klasy, wprowadzając zmiany wymienione poniżej.

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

   * Zastąp `ProvisioningServiceConnectionString` wartość symbolu zastępczego parametrami połączenia usługi aprowizacji, dla której chcesz utworzyć rejestrację.

   * Opcjonalnie możesz zmienić identyfikator rejestracji, klucz poręczenia, identyfikator urządzenia i stan aprowizowania.

   * Jeśli korzystasz z tego przewodnika Szybki Start wraz z usługą [tworzenia i aprowizacji symulowanego urządzenia TPM za pomocą zestawu SDK urządzenia w języku C#](quick-create-simulated-device-tpm-csharp.md) , aby udostępnić symulowane urządzenie, Zastąp Klucz poręczenia i Identyfikator rejestracji wartościami zanotowanymi w tym przewodniku Szybki Start. Identyfikator urządzenia można zastąpić wartością sugerowaną w tym przewodniku Szybki Start, użyć własnej wartości lub użyć wartości domyślnej w tym przykładzie.

1. Dodaj następującą metodę do `Program` klasy.  Ten kod tworzy poszczególne wpisy rejestracji, a następnie wywołuje `CreateOrUpdateIndividualEnrollmentAsync` metodę w `ProvisioningServiceClient` celu dodania rejestracji indywidualnej do usługi aprowizacji.

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

1. Na koniec Zastąp `Main` metodę następującymi wierszami:

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

Zostanie wyświetlone okno wiersza polecenia i rozpocznie się wyświetlanie komunikatów potwierdzających. Po pomyślnym utworzeniu w oknie wiersza polecenia zostaną wyświetlone właściwości nowej rejestracji indywidualnej.

Możesz sprawdzić, czy Rejestracja indywidualna została utworzona. Przejdź do podsumowania usługi Device Provisioning, a następnie wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz pozycję **rejestracje indywidualne**. Powinien zostać wyświetlony nowy wpis rejestracji odpowiadający identyfikatorowi rejestracji użytemu w przykładzie.

![Właściwości rejestracji w portalu](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Wybierz wpis, aby zweryfikować Klucz poręczenia i inne właściwości wpisu.

Jeśli wykonano kroki opisane w temacie [Tworzenie i udostępnianie symulowanego urządzenia TPM przy użyciu zestawu SDK języka C#](quick-create-simulated-device-tpm-csharp.md) — Szybki Start, możesz wykonać pozostałe kroki tego przewodnika Szybki Start, aby zarejestrować symulowane urządzenie. Pomiń kroki tworzenia rejestracji indywidualnej przy użyciu witryny Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz Eksplorowanie przykładu usługi C#, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. W przeciwnym razie wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij Przykładowe okno dane wyjściowe w języku C# na komputerze.

1. Przejdź do usługi Device Provisioning w Azure Portal wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* wpisu rejestracji utworzonego w ramach tego przewodnika Szybki Start i naciśnij przycisk **Usuń** w górnej części okienka.

1. Jeśli wykonano kroki opisane w temacie [Tworzenie i udostępnianie symulowanego urządzenia TPM za pomocą zestawu SDK języka C#](quick-create-simulated-device-tpm-csharp.md) w celu utworzenia symulowanego urządzenia TPM, wykonaj następujące czynności:

    1. Zamknij okno symulatora modułu TPM i przykładowe okno danych wyjściowych urządzenia symulowanego.

    1. W witrynie Azure Portal przejdź do usługi IoT Hub, w której zaaprowizowano urządzenie. W menu w obszarze **Explorer**wybierz pozycję **urządzenia IoT**, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzysz indywidualny wpis rejestracji dla urządzenia TPM. Opcjonalnie utworzono symulowane urządzenie TPM na komputerze i zainicjowano je do centrum IoT Hub przy użyciu IoT Hub Device Provisioning Service platformy Azure. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal.

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
