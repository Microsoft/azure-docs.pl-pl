---
title: Tworzenie IoT Hub platformy Azure przy użyciu szablonu (.NET) | Microsoft Docs
description: Jak używać szablonu Azure Resource Manager do tworzenia IoT Hub za pomocą programu C#.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: db4b676e65d36a9476fd72b66cc8ccfa38af4d85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144488"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Tworzenie Centrum IoT Hub przy użyciu szablonu Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Za pomocą Azure Resource Manager można programowo tworzyć centra Azure IoT Hub i zarządzać nimi. W tym samouczku pokazano, jak za pomocą szablonu Azure Resource Manager utworzyć Centrum IoT przy użyciu programu w języku C#.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi:  [Azure Resource Manager i klasyczne](../azure-resource-manager/management/deployment-models.md).  W tym artykule opisano użycie Azure Resource Manager model wdrażania.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Programu Visual Studio.
* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Konto usługi Azure Storage][lnk-storage-account] , na którym można przechowywać pliki szablonów Azure Resource Manager.
* [Azure PowerShell 1,0][lnk-powershell-install] lub nowszy.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Przygotowywanie projektu programu Visual Studio

1. W programie Visual Studio Utwórz projekt Visual C# Windows Classic Desktop za pomocą szablonu projektu **aplikacja konsoli (.NET Framework)** . Nazwij projekt **CreateIoTHub**.

2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij polecenie **Zarządzaj pakietami NuGet**.

3. W Menedżerze pakietów NuGet zaznacz pole wyboru **Uwzględnij wersję wstępną**, a na stronie **Przeglądaj** Wyszukaj pozycję **Microsoft. Azure. Management. ResourceManager**. Wybierz pakiet, kliknij przycisk **Instaluj**, w obszarze **Przegląd zmian** kliknij przycisk **OK**, a następnie kliknij przycisk **Akceptuję** , aby zaakceptować licencje.

4. W Menedżerze pakietów NuGet Wyszukaj ciąg **Microsoft. IdentityModel. clients. ActiveDirectory**.  Kliknij przycisk **Instaluj**, w obszarze **Przegląd zmian** kliknij przycisk **OK**, a następnie kliknij przycisk **Akceptuję** , aby zaakceptować licencję.

5. W programie program. cs Zastąp istniejące instrukcje **using** następującym kodem:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. W programie program. cs Dodaj następujące zmienne statyczne, zastępując wartości symboli zastępczych. W tym samouczku zawarto uwagi na temat identyfikatora **aplikacji**, identyfikatora **subskrypcji**, **TenantId** i **hasła** . **Nazwa konta usługi Azure Storage** to nazwa konta usługi Azure Storage, na którym są przechowywane pliki szablonów Azure Resource Manager. **Nazwa grupy zasobów** to nazwa grupy zasobów, która jest używana podczas tworzenia Centrum IoT Hub. Nazwa może być istniejącą lub nową grupą zasobów. **Nazwa wdrożenia** to nazwa wdrożenia, taka jak **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Prześlij szablon w celu utworzenia Centrum IoT Hub

Użyj szablonu i pliku parametrów JSON, aby utworzyć Centrum IoT w grupie zasobów. Możesz również użyć szablonu Azure Resource Manager, aby wprowadzić zmiany w istniejącym Centrum IoT.

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **nowy element**. Dodaj plik JSON o nazwie **template.js** do projektu.

2. Aby dodać standardowe Centrum IoT do regionu **Wschodnie stany USA** , Zastąp zawartość **template.js** przy użyciu następującej definicji zasobu. Aby zapoznać się z bieżącą listą regionów, które obsługują IoT Hub zobacz [Stan platformy Azure][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **nowy element**. Dodaj plik JSON o nazwie **parameters.js** do projektu.

4. Zastąp zawartość **parameters.js** przy użyciu następujących informacji o parametrach, które określają nazwę nowego centrum IoT, takiego jak **{Initials} mynewiothub**. Nazwa Centrum IoT musi być globalnie unikatowa, dlatego powinna zawierać nazwę lub inicjały:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. W **Eksplorator serwera** Połącz się z subskrypcją platformy Azure, a na koncie usługi Azure Storage Utwórz kontener o nazwie **templates**. W panelu **Właściwości ustaw właściwość** **publiczny dostęp do odczytu** dla kontenera **templates** na **obiekt BLOB**.

6. W **Eksplorator serwera** kliknij prawym przyciskiem myszy kontener **szablonów** , a następnie kliknij polecenie **Wyświetl kontener obiektów BLOB**. Kliknij przycisk **Przekaż obiekt BLOB** , wybierz dwa pliki, **parameters.json** i **templates.json**, a następnie kliknij przycisk **Otwórz** , aby przekazać pliki JSON do kontenera **szablonów** . Adresy URL obiektów BLOB zawierających dane JSON to:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Dodaj następującą metodę do programu program. cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Dodaj następujący kod do metody **CreateIoTHub** w celu przesłania plików szablonów i parametrów do Azure Resource Manager:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Dodaj następujący kod do metody **CreateIoTHub** , która wyświetla stan i klucze dla nowego centrum IoT:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Ukończ i uruchom aplikację

Teraz możesz ukończyć aplikację, wywołując metodę **CreateIoTHub** przed kompilacją i uruchomieniem.

1. Dodaj następujący kod na końcu metody **Main** :

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Kliknij pozycję **kompilacja** , a następnie **Skompiluj rozwiązanie**. Popraw wszelkie błędy.

3. Kliknij pozycję **Debuguj** , a następnie **Rozpocznij debugowanie** , aby uruchomić aplikację. Uruchomienie wdrożenia może potrwać kilka minut.

4. Aby sprawdzić, czy aplikacja dodała nowe centrum IoT Hub, odwiedź [Azure Portal][lnk-azure-portal] i Wyświetl listę zasobów. Alternatywnie możesz użyć polecenia cmdlet programu PowerShell **Get-AzResource** .

> [!NOTE]
> Ta przykładowa aplikacja dodaje standardowe IoT Hub S1, dla którego są naliczane opłaty. Możesz usunąć Centrum IoT za pośrednictwem [Azure Portal][lnk-azure-portal] lub przy użyciu polecenia cmdlet **Remove-AzResource** środowiska PowerShell po zakończeniu.

## <a name="next-steps"></a>Następne kroki
Po wdrożeniu Centrum IoT Hub przy użyciu szablonu Azure Resource Manager z programem w języku C# warto dowiedzieć się więcej:

* Przeczytaj o możliwościach [interfejsu API REST dostawcy zasobów IoT Hub][lnk-rest-api].
* Przeczytaj [Azure Resource Manager przegląd][lnk-azure-rm-overview] , aby dowiedzieć się więcej o możliwościach Azure Resource Manager.
* Aby poznać składnię i właściwości JSON do użycia w szablonach, zobacz [typy zasobów Microsoft. Devices](/azure/templates/microsoft.devices/iothub-allversions).

Aby dowiedzieć się więcej na temat opracowywania IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK języka C][lnk-c-sdk]
* [Zestawy SDK usługi Azure IoT][lnk-sdks]

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md