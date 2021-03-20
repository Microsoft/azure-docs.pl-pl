---
title: Tworzenie usługi Azure IoT Hub przy użyciu interfejsu API REST dostawcy zasobów | Microsoft Docs
description: Dowiedz się, jak używać interfejsu API REST języka C# dla dostawcy zasobów, aby programowo tworzyć IoT Hub i zarządzać nimi.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f393701b97be76acab49a627a195b337018fa29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144424"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Tworzenie Centrum IoT Hub przy użyciu interfejsu API REST dostawcy zasobów (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Za pomocą [interfejsu API REST dostawcy zasobów IoT Hub](/rest/api/iothub/iothubresource) można programowo tworzyć centra Azure IoT Hub i zarządzać nimi. W tym samouczku pokazano, jak za pomocą interfejsu API REST dostawcy zasobów IoT Hub utworzyć Centrum IoT za pomocą programu w języku C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Programu Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Azure PowerShell 1,0](/powershell/azure/install-Az-ps) lub nowszy.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Przygotowywanie projektu programu Visual Studio

1. W programie Visual Studio Utwórz projekt Visual C# Windows Classic Desktop za pomocą szablonu projektu **aplikacja konsoli (.NET Framework)** . Nazwij projekt **CreateIoTHubREST**.

2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij polecenie **Zarządzaj pakietami NuGet**.

3. W Menedżerze pakietów NuGet zaznacz pole wyboru **Uwzględnij wersję wstępną**, a na stronie **Przeglądaj** Wyszukaj pozycję **Microsoft. Azure. Management. ResourceManager**. Wybierz pakiet, kliknij przycisk **Instaluj**, w obszarze **Przegląd zmian** kliknij przycisk **OK**, a następnie kliknij przycisk **Akceptuję** , aby zaakceptować licencje.

4. W Menedżerze pakietów NuGet Wyszukaj ciąg **Microsoft. IdentityModel. clients. ActiveDirectory**.  Kliknij przycisk **Instaluj**, w obszarze **Przegląd zmian** kliknij przycisk **OK**, a następnie kliknij przycisk **Akceptuję** , aby zaakceptować licencję.

5. W programie program. cs Zastąp istniejące instrukcje **using** następującym kodem:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. W programie program. cs Dodaj następujące zmienne statyczne, zastępując wartości symboli zastępczych. W tym samouczku zawarto uwagi na temat identyfikatora **aplikacji**, identyfikatora **subskrypcji**, **TenantId** i **hasła** . **Nazwa grupy zasobów** to nazwa grupy zasobów, która jest używana podczas tworzenia Centrum IoT Hub. Można użyć istniejącej lub nowej grupy zasobów. **Nazwa IoT Hub** to nazwa tworzonego IoT Hub, na przykład **MyIoTHub**. Nazwa Centrum IoT musi być globalnie unikatowa. **Nazwa wdrożenia** to nazwa wdrożenia, taka jak **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Tworzenie Centrum IoT Hub przy użyciu interfejsu API REST dostawcy zasobów

Użyj [interfejsu API REST dostawcy zasobów IoT Hub](/rest/api/iothub/iothubresource) , aby utworzyć Centrum IoT w grupie zasobów. Możesz również użyć interfejsu API REST dostawcy zasobów, aby wprowadzić zmiany w istniejącym Centrum IoT.

1. Dodaj następującą metodę do programu program. cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Dodaj następujący kod do metody **CreateIoTHub** . Ten kod tworzy obiekt **HttpClient** z tokenem uwierzytelniania w nagłówkach:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Dodaj następujący kod do metody **CreateIoTHub** . Ten kod opisuje Centrum IoT Hub w celu utworzenia i wygenerowania reprezentacji JSON. Aby uzyskać aktualną listę lokalizacji, które obsługują IoT Hub zobacz [Stan platformy Azure](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Dodaj następujący kod do metody **CreateIoTHub** . Ten kod przesyła żądanie REST do platformy Azure. Następnie kod sprawdza odpowiedź i Pobiera adres URL, którego można użyć do monitorowania stanu zadania wdrażania:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Dodaj następujący kod na końcu metody **CreateIoTHub** . Ten kod używa adresu **asyncStatusUri** pobranego w poprzednim kroku, aby poczekać na zakończenie wdrożenia:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Dodaj następujący kod na końcu metody **CreateIoTHub** . Ten kod pobiera klucze utworzonego Centrum IoT i drukuje je do konsoli programu:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Ukończ i uruchom aplikację

Teraz możesz ukończyć aplikację, wywołując metodę **CreateIoTHub** przed kompilacją i uruchomieniem.

1. Dodaj następujący kod na końcu metody **Main** :

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Kliknij pozycję **kompilacja** , a następnie **Skompiluj rozwiązanie**. Popraw wszelkie błędy.

3. Kliknij pozycję **Debuguj** , a następnie **Rozpocznij debugowanie** , aby uruchomić aplikację. Uruchomienie wdrożenia może potrwać kilka minut.

4. Aby sprawdzić, czy aplikacja dodała nowe centrum IoT Hub, odwiedź [Azure Portal](https://portal.azure.com/) i Wyświetl listę zasobów. Alternatywnie możesz użyć polecenia cmdlet programu PowerShell **Get-AzResource** .

> [!NOTE]
> Ta przykładowa aplikacja dodaje standardowe IoT Hub S1, dla którego są naliczane opłaty. Po zakończeniu możesz usunąć Centrum IoT za pośrednictwem [Azure Portal](https://portal.azure.com/) lub przy użyciu polecenia cmdlet **Remove-AzResource** środowiska PowerShell po zakończeniu.

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu usługi IoT Hub przy użyciu interfejsu API REST dostawcy zasobów warto dowiedzieć się więcej:

* Przeczytaj o możliwościach [interfejsu API REST dostawcy zasobów IoT Hub](/rest/api/iothub/iothubresource).

* Przeczytaj [Azure Resource Manager przegląd](../azure-resource-manager/management/overview.md) , aby dowiedzieć się więcej o możliwościach Azure Resource Manager.

Aby dowiedzieć się więcej na temat opracowywania IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)