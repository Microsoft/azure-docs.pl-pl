---
title: 'Szybki start — rejestrowanie urządzenia X.509 w usłudze Azure Device Provisioning przy użyciu języka C #'
description: W tym przewodniku Szybki start używane są rejestracje grupowe. W tym przewodniku Szybki start zarejestrujesz urządzenia X.509 w usłudze Azure IoT Hub Device Provisioning Service (DPS) przy użyciu języka C#.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b6b9331007ec633fe8b8368bd493ce18b4ddde24
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862097"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Przewodnik Szybki start: rejestrowanie urządzeń X.509 w usłudze Device Provisioning Service przy użyciu języka C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ten przewodnik szybki Start przedstawia sposób użycia języka C# w celu programowego utworzenia [grupy rejestracji](concepts-service.md#enrollment-group), która będzie używać certyfikatów X.509 pośredniego lub głównego urzędu certyfikacji. Grupa rejestracji jest tworzona przy użyciu zestawu [SDK Microsoft Azure IoT dla](https://github.com/Azure/azure-iot-sdk-csharp) platformy .NET i przykładowej aplikacji .NET Core w języku C#. Grupa rejestracji steruje dostępem do usługi aprowizacji dla urządzeń, które mają wspólny certyfikat podpisywania w swoim łańcuchu certyfikatów. Aby dowiedzieć się więcej, zobacz [Sterowanie dostępem urządzenia do usługi aprowizacji za pomocą certyfikatów X.509](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Aby uzyskać więcej informacji na temat używania infrastruktury kluczy publicznych opartej na certyfikatach X.509 z usługą Azure IoT Hub i Device Provisioning, zobacz [Omówienie zabezpieczeń certyfikatu X.509 urzędu certyfikacji](../iot-hub/iot-hub-x509ca-overview.md). 

Ten przewodnik Szybki start oczekuje, że utworzono już wystąpienie usług IoT Hub i Device Provisioning Service. Jeśli te zasoby nie zostały jeszcze utworzone, przed kontynuowaniem pracy z tym artykułem wykonaj IoT Hub Device Provisioning Service za pomocą Azure Portal [szybki](./quick-setup-auto-provision.md) start.

Chociaż kroki opisane w tym artykule działają zarówno na komputerach z systemem Windows, jak i Linux, w tym artykule jest używany komputer dewelopera z systemem Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj program [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).
* Zainstaluj [program Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Przygotowywanie certyfikatów testowych

Na potrzeby tego przewodnika Szybki start musisz mieć plik pem lub cer, który zawiera publiczną część certyfikatu X.509 pośredniego lub głównego urzędu certyfikacji. Ten certyfikat musi zostać przekazany do usługi aprowizacji i zweryfikowany przez usługę.

Zestaw SDK języka C usługi [Azure IoT](https://github.com/Azure/azure-iot-sdk-c) zawiera narzędzia testowe, które ułatwiają tworzenie łańcucha certyfikatów X.509, przekazywanie certyfikatu głównego lub pośredniego z tego łańcucha oraz korzystanie z dowodu posiadania w usłudze w celu zweryfikowania certyfikatu.

> [!CAUTION]
> Certyfikatów utworzonych za pomocą narzędzi zestawu SDK należy używać tylko do testowania testowania programowego.
> Nie używaj tych certyfikatów w środowisku produkcyjnym.
> Zawierają one zakodowane hasła, takie jak *1234,* które wygasają po 30 dniach.
> Informacje na temat uzyskiwania certyfikatów odpowiednich do użycia w produkcji znajdują się w artykule [Jak uzyskać certyfikat X.509 urzędu certyfikacji](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) w dokumentacji usługi Azure IoT Hub.
>

Aby użyć tych narzędzi testowych do generowania certyfikatów, wykonaj następujące czynności:

1. Znajdź nazwę tagu [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK języka C usługi Azure IoT.

2. Otwórz wiersz polecenia lub powłokę Git Bash i przejdź do folderu roboczego na swojej maszynie. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium GitHub [zestawu SDK języka C usługi Azure IoT.](https://github.com/Azure/azure-iot-sdk-c) Użyj tagu znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

   Narzędzia testowe znajdują się w folderze *azure-iot-sdk-c/tools/CACertificates* sklonowanego repozytorium.

3. Postępuj zgodnie z instrukcjami opisanymi w artykule [Zarządzanie certyfikatami testowymi urzędu certyfikacji na potrzeby przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Oprócz narzędzi w zestawie SDK języka [](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) C, przykład weryfikacji certyfikatu grupy w zestawie *SDK usługi Microsoft Azure IoT* dla platformy .NET pokazuje, jak wykonać dowód posiadania w języku C# przy użyciu istniejącego pośredniego lub głównego certyfikatu X.509 urzędu certyfikacji.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Uzyskiwanie parametrów połączenia usługi aprowizacji

Na potrzeby przykładu w tym przewodniku Szybki start będą potrzebne parametry połączenia usługi aprowizacji.

1. Zaloguj się do Azure Portal, wybierz **pozycję Wszystkie zasoby,** a następnie swoją usługę Device Provisioning Service.

1. Wybierz **pozycję Zasady dostępu współużytkowego,** a następnie wybierz zasady dostępu, których chcesz użyć, aby otworzyć ich właściwości. W **zasadach dostępu** skopiuj i zapisz ciąg połączenia klucza podstawowego.

    ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Tworzenie przykładowej grupy rejestracji 

W tej sekcji pokazano, jak utworzyć aplikację konsolową .NET Core, która dodaje grupę rejestracji do usługi aprowizowania. Wprowadzając pewne modyfikacje, za pomocą tych kroków można również utworzyć aplikację konsolową [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) służącą do dodawania grupy rejestracji. Aby dowiedzieć się więcej na temat programowania w systemie IoT Core, zobacz [dokumentację dla deweloperów systemu Windows IoT Core](/windows/iot-core/).

1. Otwórz Visual Studio i wybierz **pozycję Utwórz nowy projekt.** W **oknie Create a new project (Tworzenie** nowego projektu) wybierz szablon **projektu Console App (.NET Core)** for C# (Aplikacja konsoli (.NET Core) dla języka C#, a następnie wybierz pozycję Next **(Dalej).**

1. Nadaj *projektowi nazwę CreateEnrollmentGroup,* a następnie naciśnij przycisk **Utwórz**.

    ![Konfigurowanie projektu Visual C# Windows Classic Desktop](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Gdy rozwiązanie zostanie otwarte w Visual Studio, w **okienku Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **CreateEnrollmentGroup,** a następnie wybierz pozycję Zarządzaj pakietami **NuGet.**

1. W **witrynie Menedżer pakietów NuGet** wybierz pozycję **Przeglądaj,** wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Provisioning.Service,** a następnie naciśnij **przycisk Zainstaluj.**

    ![Okno Menedżera pakietów NuGet](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Ten krok powoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet zestawu SDK klienta usługi [Azure IoT Provisioning Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) oraz jego zależności.

1. Dodaj następujące `using` instrukcje po innych `using` instrukcjach w górnej części : `Program.cs`

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Dodaj następujące pola do `Program` klasy i dokonaj wymienionych zmian.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Zastąp wartość `ProvisioningServiceConnectionString` symbolu zastępczego ciągami połączenia usługi aprowizowania, dla której chcesz utworzyć rejestrację.

   * Zastąp wartość `X509RootCertPath` symbolu zastępczego ścieżką do pliku pem lub cer. Ten plik reprezentuje publiczną część pośredniego lub głównego certyfikatu X.509 urzędu certyfikacji, który został wcześniej przekazany i zweryfikowany za pomocą usługi aprowowania.

   * Opcjonalnie możesz zmienić `EnrollmentGroupId` wartość. Ciąg może zawierać tylko małe litery i łączniki.

   > [!IMPORTANT]
   > W kodzie produkcyjnym pamiętaj o następujących kwestiach dotyczących zabezpieczeń:
   >
   > * Trwałe kodowanie parametrów połączenia administratora usługi aprowizacji jest niezgodne z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń. Zamiast tego parametry połączenia powinny być przechowywane w sposób bezpieczny, np. w zabezpieczonym pliku konfiguracji lub rejestrze.
   > * Pamiętaj, aby przekazać tylko publiczną część certyfikatu podpisywania. Nigdy nie przekazuj plików pfx (PKCS12) ani plików pem zawierających klucze prywatne do usługi aprowizacji.

1. Dodaj następującą metodę do `Program` klasy . Ten kod tworzy wpis grupy rejestracji, a następnie wywołuje metodę w celu dodania grupy rejestracji `CreateOrUpdateEnrollmentGroupAsync` `ProvisioningServiceClient` do usługi aprowizowania.

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

1. Na koniec zastąp `Main` metodę następującymi liniami:

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
  
Uruchom przykład w programie Visual Studio, aby utworzyć grupę rejestracji. Zostanie wyświetlone okno wiersza polecenia z komunikatami potwierdzającymi. Po pomyślnym utworzeniu w oknie wiersza polecenia zostaną wyświetlone właściwości nowej grupy rejestracji.

Możesz sprawdzić, czy grupa rejestracji została utworzona. Przejdź do podsumowania usługi Device Provisioning i wybierz pozycję **Zarządzaj rejestracjami,** a następnie wybierz **pozycję Grupy rejestracji.** Powinien zostać wyświetlony nowy wpis rejestracji odpowiadający identyfikatorowi rejestracji użytemu w przykładzie.

![Właściwości rejestracji w portalu](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Wybierz wpis, aby zweryfikować odcisk palca certyfikatu i inne właściwości wpisu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz eksplorować przykładową usługę w języku C#, nie czyść zasobów utworzonych w tym przewodniku Szybki start. W przeciwnym razie użyj poniższych kroków, aby usunąć wszystkie zasoby utworzone w tym przewodniku Szybki start.

1. Zamknij okno danych wyjściowych przykładu w języku C# na komputerze.

1. Przejdź do usługi Device Provisioning w usłudze Azure Portal, wybierz pozycję Zarządzaj **rejestracjami,** a następnie wybierz pozycję Grupy **rejestracji.** Wybierz identyfikator *rejestracji dla* wpisu rejestracji utworzonego w tym przewodniku Szybki start i naciśnij pozycję **Usuń.**

1. W usłudze Device Provisioning w usłudze Azure Portal wybierz pozycję **Certyfikaty,** wybierz certyfikat  przekazany w tym przewodniku Szybki start i naciśnij przycisk Usuń w górnej części **menu Szczegóły certyfikatu.**  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono grupę rejestracji dla pośredniego lub głównego certyfikatu X.509 urzędu certyfikacji przy użyciu usługi Azure IoT Hub Device Provisioning Service. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal.

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)