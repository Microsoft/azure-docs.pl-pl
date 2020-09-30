---
title: 'Szybki Start — rejestrowanie urządzenia X. 509 w usłudze Azure Device Provisioning przy użyciu języka C #'
description: W tym przewodniku Szybki start używane są rejestracje grupowe. W tym przewodniku szybki start Zarejestruj urządzenia X. 509 w usłudze Azure IoT Hub Device Provisioning Service (DPS) przy użyciu języka C#.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 618b2c6acb8792382adb4c067aa8f33003227091
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532588"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Przewodnik Szybki start: rejestrowanie urządzeń X.509 w usłudze Device Provisioning Service przy użyciu języka C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ten przewodnik szybki Start przedstawia sposób użycia języka C# w celu programowego utworzenia [grupy rejestracji](concepts-service.md#enrollment-group), która będzie używać certyfikatów X.509 pośredniego lub głównego urzędu certyfikacji. Grupa rejestracji jest tworzona przy użyciu [zestawu Microsoft Azure IoT SDK dla platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp) i przykładowej aplikacji .NET Core w języku C#. Grupa rejestracji steruje dostępem do usługi aprowizacji dla urządzeń, które mają wspólny certyfikat podpisywania w swoim łańcuchu certyfikatów. Aby dowiedzieć się więcej, zobacz [Sterowanie dostępem urządzenia do usługi aprowizacji za pomocą certyfikatów X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Aby uzyskać więcej informacji na temat używania infrastruktury kluczy publicznych opartej na certyfikatach X.509 z usługą Azure IoT Hub i Device Provisioning, zobacz [Omówienie zabezpieczeń certyfikatu X.509 urzędu certyfikacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Ten przewodnik Szybki Start oczekuje, że utworzono już wystąpienie usługi IoT Hub i Device Provisioning. Jeśli te zasoby nie zostały jeszcze utworzone, przed kontynuowaniem pracy z tym artykułem Ukończ [konfigurację IoT Hub Device Provisioning Service za pomocą](./quick-setup-auto-provision.md) przewodnika Azure Portal.

Chociaż kroki opisane w tym artykule działają na komputerach z systemem Windows i Linux, w tym artykule jest wykorzystywany komputer deweloperski systemu Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj program [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Zainstaluj [zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows).
* Zainstaluj narzędzie [git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Przygotowywanie certyfikatów testowych

Na potrzeby tego przewodnika Szybki start musisz mieć plik pem lub cer, który zawiera publiczną część certyfikatu X.509 pośredniego lub głównego urzędu certyfikacji. Ten certyfikat musi zostać przekazany do usługi aprowizacji i zweryfikowany przez usługę.

[Zestaw SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) zawiera narzędzia testowe, które mogą ułatwić tworzenie łańcucha certyfikatów X. 509, przekazywanie certyfikatu głównego lub pośredniego z tego łańcucha oraz wykonywanie dowodu posiadania w usłudze w celu zweryfikowania certyfikatu.

> [!CAUTION]
> Używaj certyfikatów utworzonych przy użyciu narzędzi zestawu SDK wyłącznie do testowania deweloperskiego.
> Nie należy używać tych certyfikatów w środowisku produkcyjnym.
> Zawierają one stałe hasła, na przykład *1234*, które wygasają po upływie 30 dni.
> Informacje na temat uzyskiwania certyfikatów odpowiednich do użycia w produkcji znajdują się w artykule [Jak uzyskać certyfikat X.509 urzędu certyfikacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) w dokumentacji usługi Azure IoT Hub.
>

Aby użyć tego narzędzia testowego do wygenerowania certyfikatów, wykonaj następujące czynności:

1. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK języka C usługi Azure IoT.

2. Otwórz wiersz polecenia lub powłokę Git Bash i przejdź do folderu roboczego na swojej maszynie. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium [usługi Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Użyj znacznika znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

   Narzędzia testowe znajdują się w folderze *azure-iot-sdk-c/tools/CACertificates* sklonowanego repozytorium.

3. Postępuj zgodnie z instrukcjami opisanymi w artykule [Zarządzanie certyfikatami testowymi urzędu certyfikacji na potrzeby przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Oprócz narzędzi w zestawie SDK języka C, [przykład weryfikacji certyfikatu grupy](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) w *zestawie Microsoft Azure IoT SDK dla platformy .NET* pokazuje, jak przeprowadzić dowód posiadania w języku C# z istniejącym certyfikatem pośredniego lub głównego urzędu certyfikacji X. 509.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Uzyskiwanie parametrów połączenia usługi aprowizacji

Na potrzeby przykładu w tym przewodniku Szybki start będą potrzebne parametry połączenia usługi aprowizacji.

1. Zaloguj się do Azure Portal, wybierz pozycję **wszystkie zasoby**, a następnie usługę Device Provisioning.

1. Wybierz pozycję **zasady dostępu współdzielonego**, a następnie wybierz zasady dostępu, których chcesz użyć, aby otworzyć jego właściwości. W obszarze **zasady dostępu**skopiuj i Zapisz parametry połączenia klucza podstawowego.

    ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Tworzenie przykładowej grupy rejestracji 

W tej sekcji przedstawiono sposób tworzenia aplikacji konsolowej .NET Core, która dodaje grupę rejestracji do usługi aprowizacji. Wprowadzając pewne modyfikacje, za pomocą tych kroków można również utworzyć aplikację konsolową [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) służącą do dodawania grupy rejestracji. Aby dowiedzieć się więcej na temat programowania w systemie IoT Core, zobacz [dokumentację dla deweloperów systemu Windows IoT Core](https://docs.microsoft.com/windows/iot-core/).

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**. W obszarze **Utwórz nowy projekt**wybierz pozycję **aplikacja konsoli (.NET Core)** dla szablonu projektu C#, a następnie wybierz pozycję **dalej**.

1. Nazwij projekt moja *rejestracja*, a następnie naciśnij pozycję **Utwórz**.

    ![Konfigurowanie klasycznego projektu systemu Windows w języku Visual C#](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Gdy w programie Visual Studio zostanie otwarte rozwiązanie, w okienku **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy **projekt a** następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**, Wyszukaj i wybierz pozycję **Microsoft. Azure. Devices. Provisioning. Service**, a następnie naciśnij klawisz **Install**.

    ![Okno Menedżera pakietów NuGet](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Ten krok spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK klienta usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) i jego zależności.

1. Dodaj następujące `using` instrukcje `using` w górnej części okna `Program.cs` :

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Dodaj następujące pola do `Program` klasy i wprowadź zmiany na liście.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Zastąp `ProvisioningServiceConnectionString` wartość symbolu zastępczego parametrami połączenia usługi aprowizacji, dla której chcesz utworzyć rejestrację.

   * Zastąp `X509RootCertPath` wartość symbolu zastępczego ścieżką do pliku PEM lub CER. Ten plik reprezentuje publiczną część pośredniego lub głównego certyfikatu X. 509 urzędu certyfikacji, który został wcześniej przekazany i zweryfikowany przy użyciu usługi aprowizacji.

   * Opcjonalnie możesz zmienić `EnrollmentGroupId` wartość. Ciąg może zawierać tylko małe litery i łączniki.

   > [!IMPORTANT]
   > W kodzie produkcyjnym pamiętaj o następujących kwestiach dotyczących zabezpieczeń:
   >
   > * Trwałe kodowanie parametrów połączenia administratora usługi aprowizacji jest niezgodne z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń. Zamiast tego parametry połączenia powinny być przechowywane w sposób bezpieczny, np. w zabezpieczonym pliku konfiguracji lub rejestrze.
   > * Pamiętaj, aby przekazać tylko publiczną część certyfikatu podpisywania. Nigdy nie przekazuj plików pfx (PKCS12) ani plików pem zawierających klucze prywatne do usługi aprowizacji.

1. Dodaj następującą metodę do `Program` klasy. Ten kod tworzy wpis grupy rejestracji, a następnie wywołuje `CreateOrUpdateEnrollmentGroupAsync` metodę w `ProvisioningServiceClient` celu dodania grupy rejestracji do usługi aprowizacji.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
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

## <a name="run-the-enrollment-group-sample"></a>Uruchamianie przykładowej grupy rejestracji
  
Uruchom przykład w programie Visual Studio, aby utworzyć grupę rejestracji. Zostanie wyświetlone okno wiersza polecenia i rozpocznie się wyświetlanie komunikatów potwierdzających. Po pomyślnym utworzeniu okna wiersza polecenia zostaną wyświetlone właściwości nowej grupy rejestracji.

Można sprawdzić, czy grupa rejestracji została utworzona. Przejdź do podsumowania usługi Device Provisioning, a następnie wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz pozycję **grupy rejestracji**. Powinien zostać wyświetlony nowy wpis rejestracji odpowiadający identyfikatorowi rejestracji użytemu w przykładzie.

![Właściwości rejestracji w portalu](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Wybierz wpis, aby zweryfikować odcisk palca certyfikatu i inne właściwości wpisu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz Eksplorowanie przykładu usługi C#, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. W przeciwnym razie wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij Przykładowe okno dane wyjściowe w języku C# na komputerze.

1. Przejdź do usługi Device Provisioning w Azure Portal wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz pozycję **grupy rejestracji**. Wybierz *Identyfikator rejestracji* wpisu rejestracji utworzonego w ramach tego przewodnika Szybki Start i naciśnij klawisz **delete**.

1. Z poziomu usługi Device Provisioning w Azure Portal wybierz pozycję **Certyfikaty**, wybierz certyfikat, który został przekazany do tego przewodnika Szybki Start, a następnie naciśnij klawisz **delete** w górnej części strony **Szczegóły certyfikatu**.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono grupę rejestracji dla certyfikatu pośredniego lub głównego urzędu certyfikacji X. 509 przy użyciu IoT Hub Device Provisioning Service platformy Azure. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal.

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
