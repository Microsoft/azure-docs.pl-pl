---
title: Samouczek — używanie wzbogacania komunikatów usługi Azure IoT Hub
description: Samouczek przedstawiający sposób używania wzbogacenia wiadomości dla komunikatów usługi Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 78aee7829e58feede3360f30f10260903713c52f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770078"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Samouczek: korzystanie z wzbogacania komunikatów usługi Azure IoT Hub

*Wzbogacenia wiadomości* opisano możliwość usługi Azure IoT Hub do *stemplowania* wiadomości z dodatkowych informacji przed wiadomości są wysyłane do wyznaczonego punktu końcowego. Jednym z powodów, aby użyć wzbogacenia wiadomości jest uwzględnienie danych, które mogą służyć do uproszczenia przetwarzania podrzędnego. Na przykład wzbogacanie komunikatów telemetrycznych urządzenia o podwójny tag urządzenia może zmniejszyć obciążenie klientów, aby interfejs API bliźniaczej reprezentacji urządzenia powodem wywołać te informacje. Aby uzyskać więcej informacji, zobacz [Omówienie wzbogacania wiadomości](iot-hub-message-enrichments-overview.md).

W tym samouczku zostaną wyświetleni dwa sposoby tworzenia i konfigurowania zasobów, które są potrzebne do testowania wzbogacenia komunikatów dla centrum IoT hub. Zasoby obejmują jedno konto magazynu z dwoma kontenerami magazynu. Jeden kontener przechowuje wzbogacone wiadomości, a inny kontener przechowuje oryginalne wiadomości. W zestawie znajduje się również centrum IoT hub do odbierania wiadomości i kierowania ich do odpowiedniego kontenera magazynu na podstawie tego, czy są one wzbogacone, czy nie.

* Pierwszą metodą jest użycie interfejsu wiersza polecenia platformy Azure do tworzenia zasobów i konfigurowania routingu wiadomości. Następnie można zdefiniować wzbogacenia ręcznie przy użyciu [witryny Azure portal](https://portal.azure.com).

* Drugą metodą jest użycie szablonu usługi Azure Resource Manager do tworzenia zasobów *i* konfiguracji routingu wiadomości i wzbogacania wiadomości.

Po zakończeniu konfiguracji routingu wiadomości i wzbogacenia wiadomości, należy użyć aplikacji do wysyłania wiadomości do centrum IoT hub. Następnie koncentrator kieruje je do obu kontenerów magazynu. Tylko wiadomości wysyłane do punktu końcowego dla **kontenera magazynu wzbogaconego** są wzbogacone.

Oto zadania wykonywane w celu wykonania tego samouczka:

**Korzystanie z wzbogacenia komunikatów usługi IoT Hub**
> [!div class="checklist"]
> * Pierwsza metoda: Tworzenie zasobów i konfigurowanie routingu wiadomości przy użyciu interfejsu wiersza polecenia platformy Azure. Skonfiguruj wzbogacenia wiadomości ręcznie przy użyciu [portalu Azure](https://portal.azure.com).
> * Druga metoda: Tworzenie zasobów i konfigurowanie routingu wiadomości i wzbogacania wiadomości przy użyciu szablonu Menedżera zasobów. 
> * Uruchom aplikację, która symuluje urządzenie IoT wysyłanie wiadomości do centrum.
> * Wyświetl wyniki i sprawdź, czy wzbogacenia wiadomości działają zgodnie z oczekiwaniami.

## <a name="prerequisites"></a>Wymagania wstępne

* Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
* Zainstaluj [program Visual Studio](https://www.visualstudio.com/).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym samouczku używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Pobieranie repozytorium próbek języka IoT C#

Pobierz [próbki języka IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) z gitHub i rozpakuj je. To repozytorium zawiera kilka aplikacji, skryptów i szablonów Menedżera zasobów. Te, które mają być używane w tym samouczku są następujące:

* W przypadku metody ręcznej istnieje skrypt interfejsu wiersza polecenia, który jest używany do tworzenia zasobów. Ten skrypt jest w /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Ten skrypt tworzy zasoby i konfiguruje routing wiadomości. Po uruchomieniu tego skryptu należy ręcznie utworzyć wzbogacenia wiadomości przy użyciu [portalu Azure.](https://portal.azure.com)
* Dla metody zautomatyzowanej istnieje szablon usługi Azure Resource Manager. Szablon znajduje się w pliku /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Ten szablon tworzy zasoby, konfiguruje routing wiadomości, a następnie konfiguruje wzbogacenia wiadomości.
* Trzecia aplikacja, której używasz jest aplikacja Symulacja urządzenia, której używasz do wysyłania wiadomości do centrum IoT hub i testowania wzbogacenia wiadomości.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Ręczne konfigurowanie i konfigurowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Oprócz tworzenia niezbędnych zasobów skrypt interfejsu wiersza polecenia platformy Azure konfiguruje również dwie trasy do punktów końcowych, które są oddzielnymi kontenerami magazynu. Aby uzyskać więcej informacji na temat konfigurowania routingu wiadomości, zobacz [samouczek Routing](tutorial-routing.md). Po skonfigurowaniu zasobów użyj [witryny Azure Portal,](https://portal.azure.com) aby skonfigurować wzbogacanie wiadomości dla każdego punktu końcowego. Następnie przejdź do etapu testowania.

> [!NOTE]
> Wszystkie komunikaty są kierowane do obu punktów końcowych, ale tylko wiadomości przechodzące do punktu końcowego ze skonfigurowanym wzbogaceniem wiadomości zostaną wzbogacone.
>

Można użyć następującego skryptu lub otworzyć skrypt w folderze /resources pobranego repozytorium. Skrypt wykonuje następujące kroki:

* Utwórz centrum IoT.
* Tworzenie konta magazynu
* Utwórz dwa kontenery na koncie magazynu. Jeden kontener jest dla wzbogaconych wiadomości, a inny kontener jest dla wiadomości, które nie są wzbogacone.
* Konfigurowanie routingu dla dwóch różnych kont magazynu:
    * Utwórz punkt końcowy dla każdego kontenera konta magazynu.
    * Utwórz trasę do każdego z punktów końcowych kontenera konta magazynu.

Istnieje kilka nazw zasobów, które muszą być unikatowe globalnie, takich jak nazwa centrum IoT i nazwa konta magazynu. Aby ułatwić uruchamianie skryptu, te nazwy zasobów są dołączane z losową wartością alfanumeryczną o nazwie *randomValue*. Losowa wartość jest generowana raz w górnej części skryptu. Jest dołączany do nazw zasobów zgodnie z potrzebami w całym skrypcie. Jeśli nie chcesz, aby wartość była losowa, możesz ustawić ją na pusty ciąg lub na określoną wartość.

Jeśli jeszcze tego nie zrobiono, otwórz okno usługi Azure [Cloud Shell](https://shell.azure.com) i upewnij się, że jest ono ustawione na Bash. Otwórz skrypt w rozpakowanym repozytorium, wybierz klawisze Ctrl+A, aby zaznaczyć wszystkie, a następnie wybierz pozycję Ctrl+C, aby go skopiować. Alternatywnie można skopiować następujący skrypt interfejsu wiersza polecenia lub otworzyć go bezpośrednio w usłudze Cloud Shell. Wklej skrypt w oknie Powłoki chmury, klikając prawym przyciskiem myszy wiersz polecenia i wybierając polecenie **Wklej**. Skrypt uruchamia jedną instrukcję naraz. Po zakończeniu pracy skryptu wybierz pozycję **Enter,** aby upewnić się, że jest on uruchamiany jako ostatnie polecenie. Poniższy blok kodu pokazuje skrypt, który jest używany, z komentarzami, które wyjaśniają, co robi.

Oto zasoby utworzone przez skrypt. *Wzbogacony* oznacza, że zasób jest dla wiadomości z wzbogacenia. *Oryginalny* oznacza, że zasób jest dla wiadomości, które nie są wzbogacone.

| Nazwa | Wartość |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nazwa kontenera | Oryginał  |
| nazwa kontenera | Wzbogacony  |
| Nazwa urządzenia IoT | Contoso-Test-Urządzenie |
| Nazwa Centrum IoT | ContosoTestHubMsgEn |
| Nazwa konta magazynu | contosostorage (contosostorage) |
| nazwa punktu końcowego 1 | ContosoStorageEndpointOriginal |
| nazwa punktu końcowego 2 | ContosoStorageEndpointWzgowym wytłoczeniem|
| Nazwa trasy 1 | ContosoStorageRouteoryginalny |
| Nazwa trasy 2 | ContosoStorageRouteWzszało |

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

W tym momencie zasoby są skonfigurowane i routing wiadomości jest skonfigurowany. Można wyświetlić konfigurację routingu wiadomości w portalu i skonfigurować wzbogacenia wiadomości dla wiadomości przechodzących do **kontenera magazynu wzbogaconego.**

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Ręcznie skonfiguruj wzbogacanie wiadomości przy użyciu witryny Azure Portal

1. Przejdź do centrum IoT, wybierając pozycję **Grupy zasobów**. Następnie wybierz grupę zasobów skonfigurowaną dla tego samouczka (**ContosoResourcesMsgEn**). Znajdź centrum IoT na liście i wybierz go. Wybierz **pozycję Routing wiadomości** dla centrum IoT hub.

   ![Wybieranie routingu wiadomości](./media/tutorial-message-enrichments/select-iot-hub.png)

   Okienko routingu wiadomości ma trzy karty oznaczone **jako Trasy,** **Niestandardowe punkty końcowe**i **Wzbogać wiadomości**. Przejrzyj dwie pierwsze karty, aby wyświetlić konfigurację skonfigurowane przez skrypt. Użyj trzeciej karty, aby dodać wzbogacenia wiadomości. Wzbogaćmy wiadomości przechodzące do punktu końcowego kontenera magazynu o nazwie **wzbogacony**. Wypełnij nazwę i wartość, a następnie wybierz punkt końcowy **ContosoStorageEndpointWrzyszone** z listy rozwijanej. Oto przykład konfigurowania wzbogacenia, które dodaje nazwę centrum IoT do wiadomości:

   ![Dodaj pierwsze wzbogacenie](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Dodaj te wartości do listy contosoStorageEndpointWulowany punkt końcowy.

   | Klucz | Wartość | Punkt końcowy (lista rozwijana) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointWrzyszy |
   | Lokalizacja urządzenia | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointWrzyszy |
   |Idklienta | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointWrzyszy |

   > [!NOTE]
   > Jeśli urządzenie nie ma bliźniaczej reprezentacji, wartość, którą w tym miejscu umieścisz, zostanie ostemplowana jako ciąg dla wartości w wzbogacenia wiadomości. Aby wyświetlić informacje o bliźniaczej reprezentacji urządzenia, przejdź do centrum w portalu i wybierz **pozycję Urządzenia IoT**. Wybierz urządzenie, a następnie wybierz **pozycję Bliźniacza bliźniacza urządzenia** u góry strony.
   >
   > Można edytować informacje bliźniaczej reprezentacji, aby dodać znaczniki, takie jak lokalizacja, i ustawić je na określoną wartość. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](iot-hub-devguide-device-twins.md).

3. Po zakończeniu okienko powinno wyglądać podobnie do tego obrazu:

   ![Tabela ze wszystkimi dodanymi wzbogaceniami](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Wybierz **zastosuj,** aby zapisać zmiany. Przejdź do sekcji [Wzbogacenia wiadomości testowych.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Tworzenie i konfigurowanie przy użyciu szablonu Menedżera zasobów
Za pomocą szablonu Menedżera zasobów można tworzyć i konfigurować zasoby, routing wiadomości i wzbogacanie wiadomości.

1. Zaloguj się do Portalu Azure. Wybierz **+ Utwórz zasób,** aby wywołać pole wyszukiwania. Wprowadź *wdrożenie szablonu*i wyszukaj go. W okienku wyników wybierz pozycję **Wdrażanie szablonu (wdrażanie przy użyciu szablonu niestandardowego).**

   ![Wdrażanie szablonu w witrynie Azure portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Wybierz **pozycję Utwórz** w okienku **wdrażania szablonu.**

1. W okienku **wdrażania niestandardowego** wybierz pozycję **Skompiluj własny szablon w edytorze**.

1. W okienku **Edytowanie szablonu** wybierz pozycję **Załaduj plik**. Pojawi się Eksplorator Windows. Zlokalizuj plik **template_messageenrichments.json** w rozpakowanym pliku repozytorium w **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Wybieranie szablonu z komputera lokalnego](./media/tutorial-message-enrichments/template-select.png)

1. Wybierz **otwórz,** aby załadować plik szablonu z komputera lokalnego. Ładuje się i pojawia się w okienku edycji.

   Ten szablon jest skonfigurowany do używania globalnie unikatowej nazwy centrum IoT i nazwy konta magazynu przez dodanie losowej wartości na końcu nazw domyślnych, dzięki czemu można użyć szablonu bez wprowadzania żadnych zmian.

   Oto zasoby utworzone przez załadowanie szablonu. **Wzbogacony** oznacza, że zasób jest dla wiadomości z wzbogacenia. **Oryginalny** oznacza, że zasób jest dla wiadomości, które nie są wzbogacone. Są to te same wartości używane w skrypcie interfejsu wiersza polecenia platformy Azure.

   | Nazwa | Wartość |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nazwa kontenera | Oryginał  |
   | nazwa kontenera | Wzbogacony  |
   | Nazwa urządzenia IoT | Contoso-Test-Urządzenie |
   | Nazwa Centrum IoT | ContosoTestHubMsgEn |
   | Nazwa konta magazynu | contosostorage (contosostorage) |
   | nazwa punktu końcowego 1 | ContosoStorageEndpointOriginal |
   | nazwa punktu końcowego 2 | ContosoStorageEndpointWzgowym wytłoczeniem|
   | Nazwa trasy 1 | ContosoStorageRouteoryginalny |
   | Nazwa trasy 2 | ContosoStorageRouteWzszało |

1. Wybierz pozycję **Zapisz**. Zostanie **wyświetlona okienko wdrożenia niestandardowego** i wszystkie parametry używane przez szablon. Jedynym polem, które należy ustawić, jest **grupa zasobów**. Utwórz nowy lub wybierz go z listy rozwijanej.

   Oto górna połowa okienka **wdrażania niestandardowego.** Możesz zobaczyć, gdzie należy wypełnić grupę zasobów.

   ![Górna połowa okienka wdrażania niestandardowego](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Oto dolna połowa okienka **wdrażania niestandardowego.** Możesz zobaczyć pozostałe parametry i warunki. 

   ![Dolna połowa okienka wdrażania niestandardowego](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Zaznacz to pole wyboru, aby zaakceptować warunki. Następnie wybierz **pozycję Zakup,** aby kontynuować wdrażanie szablonu.

1. Poczekaj, aż szablon zostanie w pełni wdrożony. Wybierz ikonę dzwonka w górnej części ekranu, aby sprawdzić postęp. Po zakończeniu przejdź do sekcji [Wzbogacenia komunikatu testowego.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Wzbogacenia wiadomości testowych

Aby wyświetlić wzbogacenia wiadomości, wybierz **pozycję Grupy zasobów**. Następnie wybierz grupę zasobów, której używasz w tym samouczku. Wybierz centrum IoT z listy zasobów i przejdź do **aplikacji Wiadomości**. Zostanie wyświetlona konfiguracja routingu komunikatów i skonfigurowane wzbogacenia.

Teraz, gdy wzbogacenia wiadomości są skonfigurowane dla punktu końcowego, uruchom aplikację Symulowane urządzenie, aby wysyłać wiadomości do centrum IoT hub. Koncentrator został skonfigurowany z ustawieniami, które realizują następujące zadania:

* Wiadomości kierowane do punktu końcowego magazynu ContosoStorageEndpointOriginal nie zostaną wzbogacone `original`i będą przechowywane w kontenerze magazynu .

* Wiadomości kierowane do punktu końcowego magazynu ContosoStorageEndpointWzbogatrzane `enriched`zostaną wzbogacone i przechowywane w kontenerze magazynu .

Aplikacja Symulowane urządzenie jest jedną z aplikacji w rozpakowanym pobraniu. Aplikacja wysyła wiadomości dla każdej z różnych metod routingu wiadomości w [samouczku Routing](tutorial-routing.md), który zawiera usługę Azure Storage.

Kliknij dwukrotnie plik rozwiązania **IoT_SimulatedDevice.sln,** aby otworzyć kod w programie Visual Studio, a następnie otwórz **Program.cs**. Zastąp nazwę koncentratora `{your hub name}`IoT znacznika . Formatem nazwy hosta centrum IoT jest **{nazwa centrum}.azure-devices.net**. W tym samouczku nazwa hosta centrum jest ContosoTestHubMsgEn.azure-devices.net. Następnie zastąp klucz urządzenia zapisany wcześniej podczas po uruchomieniu `{your device key}`skryptu, aby utworzyć zasoby dla znacznika .

Jeśli nie masz klucza urządzenia, możesz go pobrać z portalu. Po zalogowaniu przejdź do **grup zasobów**, wybierz grupę zasobów, a następnie wybierz centrum IoT Hub. Sprawdź w obszarze **Urządzenia IoT** dla urządzenia testowego i wybierz urządzenie. Wybierz ikonę kopiowania obok **pozycji Klucz podstawowy,** aby skopiować ją do schowka.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Uruchamianie i testowanie

Uruchom aplikację konsoli przez kilka minut. Wiadomości, które są wysyłane są wyświetlane na ekranie konsoli aplikacji.

Aplikacja wysyła nowy komunikat z urządzenia do centrum IoT Hub co sekundę. Komunikat zawiera obiekt serializowany w notacji JSON z identyfikatorem urządzenia, temperaturą, wilgotnością i poziomem komunikatu ustawianym domyślnie na `normal`. Losowo przypisuje poziom `critical` lub `storage`, co powoduje, że wiadomość ma być kierowane do konta magazynu lub do domyślnego punktu końcowego. Wiadomości wysyłane do **wzbogaconego kontenera** na koncie magazynu zostaną wzbogacone.

Po wysłaniu kilku komunikatów magazynu wyświetl dane.

1. Wybierz pozycję **Grupy zasobów**. Znajdź grupę zasobów **ContosoResourcesMsgEn**i wybierz ją.

2. Wybierz konto magazynu, które jest **contosostorage**. Następnie wybierz **Eksploratora magazynu (podgląd)** w lewym okienku.

   ![Wybierz Eksploratora magazynu](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Wybierz **kontenery obiektów BLOB,** aby wyświetlić dwa kontenery, które mogą być używane.

   ![Wyświetlanie kontenerów na koncie magazynu](./media/tutorial-message-enrichments/show-blob-containers.png)

Wiadomości w kontenerze o nazwie **wzbogacone** mają wzbogacenia wiadomości zawarte w wiadomościach. Wiadomości w kontenerze o nazwie **original** mają nieprzetworzone wiadomości bez wzbogacenia. Przejdź do szczegółów w jednym z kontenerów, aż dojdziesz do dołu i otwórz najnowszy plik wiadomości. Następnie wykonaj to samo dla innego kontenera, aby sprawdzić, czy nie ma żadnych wzbogacenia dodane do wiadomości w tym kontenerze.

Gdy spojrzysz na wiadomości, które zostały wzbogacone, powinieneś zobaczyć "mój Centrum IoT" z nazwą centrum i lokalizacją i identyfikatorem klienta, w ten sposób:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Oto wiadomość nieznieoproszony. Należy zauważyć, że "moje Centrum IoT Hub", "devicelocation" i "customerID" nie są wyświetlane w tym miejscu, ponieważ te pola są dodawane przez wzbogacenia. Ten punkt końcowy nie ma wzbogacenia.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wszystkie zasoby utworzone w tym samouczku, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku następuje również usunięcie centrum IoT, konta magazynu i samej grupy zasobów.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Oczyszczanie zasobów za pomocą interfejsu wiersza polecenia platformy Azure

Aby usunąć grupę zasobów, użyj polecenia [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Przypomnijmy, że `$resourceGroup` został ustawiony na **ContosoResourcesMsgEn** na początku tego samouczka.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i przetestowano dodawanie wzbogacenia wiadomości do komunikatów Usługi IoT Hub przy użyciu następujących kroków:

**Korzystanie z wzbogacenia komunikatów usługi IoT Hub**
> [!div class="checklist"]
> * Pierwsza metoda: Tworzenie zasobów i konfigurowanie routingu wiadomości przy użyciu interfejsu wiersza polecenia platformy Azure. Skonfiguruj wzbogacenia wiadomości ręcznie przy użyciu [portalu Azure](https://portal.azure.com).
> * Druga metoda: Tworzenie zasobów i konfigurowanie routingu wiadomości i wzbogacania wiadomości przy użyciu szablonu usługi Azure Resource Manager.
> * Uruchom aplikację, która symuluje urządzenie IoT wysyłanie wiadomości do centrum.
> * Wyświetl wyniki i sprawdź, czy wzbogacenia wiadomości działają zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat wzbogacania wiadomości, zobacz [Omówienie wzbogacania wiadomości](iot-hub-message-enrichments-overview.md).

Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz następujące artykuły:

* [Wysyłanie wiadomości z urządzenia do chmury za pomocą usługi IoT Hub do wysyłania wiadomości z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)
* [Samouczek: Routing usługi IoT Hub](tutorial-routing.md)
