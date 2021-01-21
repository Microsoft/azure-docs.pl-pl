---
title: Samouczek — korzystanie z ulepszonych komunikatów IoT Hub platformy Azure
description: Samouczek przedstawiający sposób używania wzbogacania komunikatów dla komunikatów usługi Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: ddc212e99c0e55156f56df6bf77e122408a727f9
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624059"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Samouczek: korzystanie z wzbogacania komunikatów IoT Hub platformy Azure

*Wzbogacanie komunikatów* zawiera opis możliwości, aby usługa Azure *IoT Hub mogła* wskazywać wiadomości z dodatkowymi informacjami przed wysłaniem komunikatów do określonego punktu końcowego. Jednym z powodów użycia wzbogacania komunikatów jest dołączenie danych, które mogą być używane do uproszczenia przetwarzania podrzędnego. Na przykład wzbogacanie komunikatów telemetrycznych urządzeń za pomocą znacznika sznurka urządzenia może zmniejszyć obciążenie klientów, aby umożliwić wywoływanie interfejsu API z użyciem urządzeń z systemem dla tych informacji. Aby uzyskać więcej informacji, zobacz [Omówienie wzbogacania komunikatów](iot-hub-message-enrichments-overview.md).

W tym samouczku przedstawiono dwa sposoby tworzenia i konfigurowania zasobów, które są potrzebne do przetestowania wzbogacania komunikatów dla Centrum IoT. Zasoby obejmują jedno konto magazynu z dwoma kontenerami magazynu. Jeden kontener zawiera wzbogacone komunikaty, a inny kontener zawiera oryginalne wiadomości. Uwzględniono również centrum IoT Hub do odbierania komunikatów i kierowania ich do odpowiedniego kontenera magazynu w zależności od tego, czy są one wzbogacane, czy nie.

* Pierwsza metoda polega na użyciu interfejsu wiersza polecenia platformy Azure do tworzenia zasobów i konfigurowania routingu komunikatów. Następnie można zdefiniować wzbogacanie ręcznie przy użyciu [Azure Portal](https://portal.azure.com).

* Druga metoda polega na użyciu szablonu Azure Resource Manager, aby utworzyć zasoby *i* konfiguracje dla wzbogacenia komunikatów i komunikatów.

Po zakończeniu konfiguracji dla operacji przysposobienia routingu i komunikatów należy użyć aplikacji do wysyłania komunikatów do centrum IoT. Następnie centrum kieruje je do obu kontenerów magazynu. Tylko komunikaty wysyłane do punktu końcowego dla **wzbogaconego** kontenera magazynu są wzbogacane.

Poniżej przedstawiono zadania, które należy wykonać w celu wykonania tego samouczka:

**Użyj wzbogacania komunikatów IoT Hub**
> [!div class="checklist"]
> * Pierwsza metoda: Tworzenie zasobów i Konfigurowanie routingu komunikatów przy użyciu interfejsu wiersza polecenia platformy Azure. Ręcznie skonfiguruj wzbogacanie komunikatów przy użyciu [Azure Portal](https://portal.azure.com).
> * Druga metoda: Tworzenie zasobów i Konfigurowanie routingu komunikatów i wzbogacania komunikatów przy użyciu szablonu Menedżer zasobów. 
> * Uruchom aplikację, która symuluje wysyłanie komunikatów przez urządzenie IoT do centrum.
> * Wyświetl wyniki i sprawdź, czy wzbogacania komunikatów działają zgodnie z oczekiwaniami.

## <a name="prerequisites"></a>Wymagania wstępne

- Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Zainstaluj [program Visual Studio](https://www.visualstudio.com/).

- Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym samouczku używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Pobierz repozytorium przykładów w języku C# dla IoT

Pobierz [przykłady usługi IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) z usługi GitHub i rozpakuj je. To repozytorium zawiera kilka aplikacji, skryptów i Menedżer zasobów szablonów. Te, które mają być używane na potrzeby tego samouczka, są następujące:

* Dla metody ręcznej istnieje skrypt interfejsu wiersza polecenia, który służy do tworzenia zasobów. Ten skrypt znajduje się w/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli. Ten skrypt służy do tworzenia zasobów i konfigurowania routingu komunikatów. Po uruchomieniu tego skryptu należy ręcznie utworzyć wzbogacanie komunikatów przy użyciu [Azure Portal](https://portal.azure.com).
* W przypadku metody zautomatyzowanej istnieje szablon Azure Resource Manager. Szablon znajduje się w/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.js. Ten szablon służy do tworzenia zasobów, konfigurowania routingu wiadomości, a następnie konfigurowania wzbogacania komunikatów.
* Trzecia aplikacja jest używana przez aplikację do symulacji urządzenia, która służy do wysyłania komunikatów do centrum IoT Hub i testowania wzbogacania komunikatów.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Ręczne konfigurowanie i Konfigurowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Oprócz tworzenia niezbędnych zasobów skrypt interfejsu wiersza polecenia platformy Azure konfiguruje również dwa trasy do punktów końcowych, które są oddzielnymi kontenerami magazynu. Aby uzyskać więcej informacji na temat konfigurowania routingu wiadomości, zobacz [samouczek routingu](tutorial-routing.md). Po skonfigurowaniu zasobów Użyj [Azure Portal](https://portal.azure.com) , aby skonfigurować wzbogacania komunikatów dla każdego punktu końcowego. Następnie przejdź do kroku testowania.

> [!NOTE]
> Wszystkie komunikaty są kierowane do obu punktów końcowych, ale tylko komunikaty przechodzące do punktu końcowego ze skonfigurowanymi wzbogacami komunikatów zostaną wzbogacone.
>

Możesz użyć poniższego skryptu lub otworzyć skrypt w folderze/Resources pobranego repozytorium. Skrypt wykonuje następujące kroki:

* Utwórz centrum IoT.
* Tworzenie konta magazynu
* Utwórz dwa kontenery na koncie magazynu. Jeden kontener jest przeznaczony dla ulepszonych komunikatów, a inny kontener dotyczy komunikatów, które nie są wzbogacane.
* Skonfiguruj Routing dla dwóch różnych kont magazynu:
    * Utwórz punkt końcowy dla każdego kontenera konta magazynu.
    * Utwórz trasę do każdego z punktów końcowych kontenera kont magazynu.

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowe, takie jak nazwa Centrum IoT i nazwa konta magazynu. Aby ułatwić uruchamianie skryptu, nazwy tych zasobów są dołączane losowo wartością alfanumeryczną o nazwie *randomValue*. Wartość losowa jest generowana raz u góry skryptu. Jest on dołączany do nazw zasobów zgodnie z wymaganiami w skrypcie. Jeśli nie chcesz, aby wartość była Losowa, możesz ustawić ją na pusty ciąg lub do określonej wartości.

Jeśli jeszcze tego nie zrobiono, Otwórz [okno Cloud Shell](https://shell.azure.com) platformy Azure i upewnij się, że jest ono ustawione na bash. Otwórz skrypt w niespakowanym repozytorium, wybierz kombinację klawiszy Ctrl + A, aby zaznaczyć wszystkie, a następnie wybierz kombinację klawiszy CTRL + C, aby skopiować. Alternatywnie można skopiować następujący skrypt interfejsu wiersza polecenia lub otworzyć go bezpośrednio w Cloud Shell. Wklej skrypt w oknie Cloud Shell, klikając prawym przyciskiem myszy wiersz polecenia i wybierając pozycję **Wklej**. Skrypt uruchamia jedną instrukcję w danym momencie. Po zatrzymaniu skryptu wybierz **klawisz ENTER** , aby upewnić się, że uruchomiono ostatnie polecenie. Poniższy blok kodu pokazuje używany skrypt z komentarzami, które wyjaśniają, co robi.

Poniżej przedstawiono zasoby utworzone przez skrypt. *Ulepszony* oznacza, że zasób jest przeznaczony dla komunikatów z wzbogacaniem. *Oryginalna* oznacza, że zasób jest przeznaczony dla komunikatów, które nie są wzbogacane.

| Nazwa | Wartość |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nazwa kontenera | Oryginał  |
| nazwa kontenera | wzbogacone  |
| Nazwa urządzenia IoT | Contoso-test-Device |
| Nazwa IoT Hub | ContosoTestHubMsgEn |
| Nazwa konta magazynu | contosostorage |
| Nazwa punktu końcowego 1 | ContosoStorageEndpointOriginal |
| Nazwa punktu końcowego 2 | ContosoStorageEndpointEnriched|
| Nazwa trasy 1 | ContosoStorageRouteOriginal |
| Nazwa trasy 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

W tym momencie wszystkie zasoby są skonfigurowane i skonfigurowano Routing komunikatów. Konfigurację routingu wiadomości można wyświetlić w portalu i skonfigurować wzbogacanie komunikatów dla komunikatów przechodzących do **wzbogaconego** kontenera magazynu.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Ręczne konfigurowanie wzbogacania komunikatów przy użyciu Azure Portal

1. Przejdź do centrum IoT Hub, wybierając pozycję **grupy zasobów**. Następnie wybierz grupę zasobów skonfigurowaną dla tego samouczka (**ContosoResourcesMsgEn**). Znajdź Centrum IoT na liście i wybierz je. Wybierz pozycję **routing wiadomości** dla Centrum IoT.

   ![Wybierz routing wiadomości](./media/tutorial-message-enrichments/select-iot-hub.png)

   Okienko routing wiadomości zawiera trzy karty z etykietą **trasy**, **niestandardowe punkty końcowe** i **komunikaty wzbogacania**. Przeglądaj pierwsze dwie karty, aby zobaczyć konfigurację skonfigurowaną przez skrypt. Użyj trzeciej karty, aby dodać wzbogacania komunikatów. Wzbogacamy komunikaty przechodzące do punktu końcowego dla kontenera magazynu o nazwie **wzbogacone**. Wypełnij pola Nazwa i wartość, a następnie wybierz punkt końcowy **ContosoStorageEndpointEnriched** z listy rozwijanej. Oto przykład sposobu konfigurowania wzbogacania, które dodaje do wiadomości nazwę Centrum IoT Hub:

   ![Dodaj pierwsze wzbogacanie](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Dodaj te wartości do listy dla punktu końcowego ContosoStorageEndpointEnriched.

   | Klucz | Wartość | Punkt końcowy (lista rozwijana) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin. Tags. Location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Identyfikator | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Jeśli urządzenie nie ma sznurka, wartość umieszczona w tym miejscu będzie sygnaturą ciągu dla wartości w wzbogacaniu komunikatów. Aby wyświetlić informacje dotyczące sznurka urządzenia, przejdź do centrum w portalu i wybierz pozycję **urządzenia IoT**. Wybierz urządzenie, a następnie wybierz pozycję **sznurki urządzenia** u góry strony.
   >
   > Można edytować informacje o przędze w celu dodania tagów, takich jak lokalizacja, i ustawić ją na określoną wartość. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](iot-hub-devguide-device-twins.md).

3. Po zakończeniu okienko powinno wyglądać podobnie do tego obrazu:

   ![Tabela ze wszystkimi dodanymi wzbogacaniem](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Wybierz pozycję **Zastosuj** , aby zapisać zmiany. Przejdź do sekcji [wzbogacanie wiadomości testowych](#test-message-enrichments) .

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Tworzenie i Konfigurowanie przy użyciu szablonu Menedżer zasobów
Za pomocą szablonu Menedżer zasobów można tworzyć i konfigurować zasoby, routing komunikatów i wzbogacenia komunikatów.

1. Zaloguj się do witryny Azure Portal. Wybierz pozycję **+ Utwórz zasób** , aby wyświetlić pole wyszukiwania. Wprowadź *wdrożenie szablonu* i wyszukaj je. W okienku wyników wybierz pozycję **Template Deployment (Wdróż przy użyciu szablonu niestandardowego)**.

   ![Template deployment w Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Wybierz pozycję **Utwórz** w okienku **Template Deployment** .

1. W okienku **wdrożenie niestandardowe** wybierz opcję **Kompiluj własny szablon w edytorze**.

1. W okienku **Edytuj szablon** wybierz pozycję **Załaduj plik**. Zostanie wyświetlony Eksplorator Windows. Znajdź **template_messageenrichments.js** w pliku w pliku niespakowanego repozytorium w **/IoT-Hub/Tutorials/Routing/SimulatedDevice/Resources**. 

   ![Wybierz szablon z komputera lokalnego](./media/tutorial-message-enrichments/template-select.png)

1. Wybierz pozycję **Otwórz** , aby załadować plik szablonu z komputera lokalnego. Ładuje i pojawia się w okienku Edycja.

   Ten szablon jest skonfigurowany do używania globalnie unikatowej nazwy Centrum IoT Hub i nazwy konta magazynu przez dodanie losowo wartości do końca nazw domyślnych, aby można było użyć szablonu bez wprowadzania żadnych zmian.

   Poniżej przedstawiono zasoby utworzone przez załadowanie szablonu. **Ulepszony** oznacza, że zasób jest przeznaczony dla komunikatów z wzbogacaniem. **Oryginalna** oznacza, że zasób jest przeznaczony dla komunikatów, które nie są wzbogacane. Są to te same wartości, które są używane w skrypcie interfejsu wiersza polecenia platformy Azure.

   | Nazwa | Wartość |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nazwa kontenera | Oryginał  |
   | nazwa kontenera | wzbogacone  |
   | Nazwa urządzenia IoT | Contoso-test-Device |
   | Nazwa IoT Hub | ContosoTestHubMsgEn |
   | Nazwa konta magazynu | contosostorage |
   | Nazwa punktu końcowego 1 | ContosoStorageEndpointOriginal |
   | Nazwa punktu końcowego 2 | ContosoStorageEndpointEnriched|
   | Nazwa trasy 1 | ContosoStorageRouteOriginal |
   | Nazwa trasy 2 | ContosoStorageRouteEnriched |

1. Wybierz pozycję **Zapisz**. Zostanie wyświetlone okienko **wdrożenie niestandardowe** zawierające wszystkie parametry używane przez szablon. Jedyne pole, które należy ustawić, jest **grupą zasobów**. Utwórz nową lub wybierz jeden z listy rozwijanej.

   Poniżej znajduje się Górna połowa niestandardowego okienka **wdrożenia** . Możesz zobaczyć miejsce wypełnienia grupy zasobów.

   ![Górna połowa okienka wdrożenia niestandardowego](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Poniżej znajduje się dolna połowa **niestandardowego okienka wdrożenia** . Można zobaczyć resztę parametrów oraz warunki i postanowienia. 

   ![Dolna połowa niestandardowego okienka wdrożenia](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Zaznacz pole wyboru, aby zaakceptować warunki i postanowienia. Następnie wybierz pozycję **Kup** , aby kontynuować wdrażanie szablonu.

1. Zaczekaj, aż szablon zostanie całkowicie wdrożony. Aby sprawdzić postęp, wybierz ikonę dzwonka w górnej części ekranu. Po zakończeniu przejdź do sekcji [wzbogacanie wiadomości testowych](#test-message-enrichments) .

## <a name="test-message-enrichments"></a>Wzbogacanie komunikatów testowych

Aby wyświetlić wzbogacanie komunikatów, wybierz pozycję **grupy zasobów**. Następnie wybierz grupę zasobów używaną w tym samouczku. Wybierz z listy zasobów Centrum IoT Hub i przejdź do **obsługi komunikatów**. Zostanie wyświetlona konfiguracja routingu komunikatów i skonfigurowane wzbogacenia.

Teraz, gdy wzbogacanie komunikatów są skonfigurowane dla punktu końcowego, uruchom aplikację symulowanego urządzenia, aby wysyłać komunikaty do centrum IoT. Centrum zostało skonfigurowane przy użyciu ustawień, które spełniają następujące zadania:

* Komunikaty kierowane do punktu końcowego magazynu ContosoStorageEndpointOriginal nie zostaną wzbogacone i będą przechowywane w kontenerze magazynu `original` .

* Komunikaty kierowane do punktu końcowego magazynu ContosoStorageEndpointEnriched będą wzbogacone i przechowywane w kontenerze magazynu `enriched` .

Aplikacja symulowanego urządzenia jest jedną z aplikacji w niespakowanym pobieraniu. Aplikacja wysyła komunikaty dla każdej z różnych metod routingu komunikatów w [samouczku routingu](tutorial-routing.md), który obejmuje usługę Azure Storage.

Kliknij dwukrotnie plik rozwiązania **IoT_SimulatedDevice. sln** , aby otworzyć kod w programie Visual Studio, a następnie otwórz **program.cs**. Zastąp nazwę Centrum IoT Hub znacznikiem `{your hub name}` . Format nazwy hosta Centrum IoT to **{nazwa centrum}. Azure-Devices.NET**. W tym samouczku nazwa hosta centrum to ContosoTestHubMsgEn.azure-devices.net. Następnie zastąp klucz urządzenia zapisany wcześniej po uruchomieniu skryptu w celu utworzenia zasobów dla znacznika `{your device key}` .

Jeśli nie masz klucza urządzenia, możesz go pobrać z portalu. Po zalogowaniu przejdź do pozycji **grupy zasobów**, wybierz grupę zasobów, a następnie wybierz Centrum IoT Hub. Sprawdź w obszarze **urządzenia IoT** dla urządzenia testowego i wybierz urządzenie. Wybierz ikonę kopiowania obok **klucza podstawowego** , aby skopiować ją do Schowka.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Uruchamianie i testowanie

Uruchom aplikację konsolową przez kilka minut. Wysyłane komunikaty są wyświetlane na ekranie konsoli aplikacji.

Aplikacja wysyła nowy komunikat z urządzenia do centrum IoT Hub co sekundę. Komunikat zawiera obiekt serializowany w notacji JSON z identyfikatorem urządzenia, temperaturą, wilgotnością i poziomem komunikatu ustawianym domyślnie na `normal`. Losowo przypisuje poziom `critical` lub `storage` , który powoduje kierowanie komunikatu do konta magazynu lub domyślnego punktu końcowego. Komunikaty wysyłane do **wzbogaconego** kontenera na koncie magazynu zostaną wzbogacone.

Po wysłaniu kilku komunikatów magazynu Wyświetl dane.

1. Wybierz pozycję **Grupy zasobów**. Znajdź grupę zasobów, **ContosoResourcesMsgEn** i wybierz ją.

2. Wybierz konto magazynu, które jest **contosostorage**. Następnie wybierz pozycję **Eksplorator usługi Storage (wersja zapoznawcza)** w okienku po lewej stronie.

   ![Wybierz Eksplorator usługi Storage](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Wybierz **kontenery obiektów BLOB** , aby wyświetlić dwa kontenery, których można użyć.

   ![Zobacz kontenery na koncie magazynu](./media/tutorial-message-enrichments/show-blob-containers.png)

Komunikaty w kontenerze o nazwie **wzbogacono** mają wzbogacenia komunikatów zawarte w wiadomościach. Wiadomości w kontenerze o nazwie **Original** mają nieprzetworzone komunikaty bez wzbogacania. Przejdź do szczegółów jednego z kontenerów do momentu, aż zobaczysz u dołu i otworzysz najnowszy plik komunikatów. Następnie wykonaj te same czynności dla drugiego kontenera, aby sprawdzić, czy do komunikatów w tym kontenerze nie zostały dodane żadne wzbogacania.

Podczas przeglądania komunikatów, które zostały wzbogacone, powinna zostać wyświetlona wartość "My IoT Hub" z nazwą centrum i lokalizacją oraz IDENTYFIKATORem klienta, na przykład:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Oto niewzbogacony komunikat. Należy zauważyć, że "My IoT Hub," "devicelocation" i "customerID" nie są tutaj wyświetlane, ponieważ te pola są dodawane przez wzbogacenia. Ten punkt końcowy nie ma wzbogacania.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wszystkie zasoby utworzone w tym samouczku, Usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku następuje również usunięcie centrum IoT, konta magazynu i samej grupy zasobów.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Czyszczenie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć grupę zasobów, użyj polecenia [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true). Odwołaj się `$resourceGroup` do **ContosoResourcesMsgEn** na początku tego samouczka.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i przetestowano Dodawanie wzbogacania komunikatów do IoT Hub komunikatów, wykonując następujące czynności:

**Użyj wzbogacania komunikatów IoT Hub**

> [!div class="checklist"]
> * Pierwsza metoda: Tworzenie zasobów i Konfigurowanie routingu komunikatów przy użyciu interfejsu wiersza polecenia platformy Azure. Ręcznie skonfiguruj wzbogacanie komunikatów przy użyciu [Azure Portal](https://portal.azure.com).
> * Druga metoda: Tworzenie zasobów i Konfigurowanie routingu komunikatów i wzbogacania komunikatów przy użyciu szablonu Azure Resource Manager.
> * Uruchom aplikację, która symuluje wysyłanie komunikatów przez urządzenie IoT do centrum.
> * Wyświetl wyniki i sprawdź, czy wzbogacania komunikatów działają zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat wzbogacania komunikatów, zobacz [Omówienie wzbogacania komunikatów](iot-hub-message-enrichments-overview.md).

Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Używanie routingu komunikatów IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Samouczek: Routing IoT Hub](tutorial-routing.md)