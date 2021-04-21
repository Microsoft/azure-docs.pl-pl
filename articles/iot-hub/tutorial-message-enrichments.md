---
title: Samouczek — korzystanie Azure IoT Hub wzbogaceń komunikatów
description: Samouczek przedstawiający sposób używania wzbogaceń komunikatów Azure IoT Hub komunikatów
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 0d6c90120d050b6896161f50332faf447c3ed67b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788875"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Samouczek: korzystanie Azure IoT Hub wzbogaceń komunikatów

*Wzbogacenia* komunikatów opisują możliwość  Azure IoT Hub oznaczania komunikatów dodatkowymi informacjami przed ich przesłaniem do wyznaczonego punktu końcowego. Jednym z powodów, dla których warto używać wzbogacania komunikatów, jest dołączenie danych, które mogą zostać użyte do uproszczenia przetwarzania podrzędnego. Na przykład wzbogacenie komunikatów telemetrycznych urządzenia o tag bliźniaczej reprezentacji urządzenia może zmniejszyć obciążenie klientów w celu wywołania interfejsu API bliźniaczej reprezentacji urządzenia dla tych informacji. Aby uzyskać więcej informacji, zobacz [Overview of message enrichments (Omówienie wzbogacania komunikatów).](iot-hub-message-enrichments-overview.md)

Ten samouczek zawiera dwa sposoby tworzenia i konfigurowania zasobów potrzebnych do testowania wzbogacania komunikatów dla centrum IoT. Zasoby obejmują jedno konto magazynu z dwoma kontenerami magazynu. Jeden kontener przechowuje wzbogacone komunikaty, a drugi przechowuje oryginalne komunikaty. Uwzględniono również centrum IoT, które odbiera komunikaty i przekieruje je do odpowiedniego kontenera magazynu w zależności od tego, czy są one wzbogacone, czy nie.

* Pierwszą metodą jest użycie interfejsu wiersza polecenia platformy Azure do utworzenia zasobów i skonfigurowania routingu komunikatów. Następnie należy ręcznie zdefiniować wzbogacenia przy użyciu [Azure Portal](https://portal.azure.com).

* Druga metoda polega na użyciu szablonu Azure Resource Manager do tworzenia  zasobów i konfiguracji dla routingu komunikatów i wzbogacania komunikatów.

Po zakończeniu konfiguracji routingu komunikatów i wzbogacania komunikatów użyj aplikacji do wysyłania komunikatów do centrum IoT. Następnie centrum kieruje je do obu kontenerów magazynu. Wzbogacane są tylko komunikaty wysyłane do punktu końcowego dla **wzbogaconego** kontenera magazynu.

Poniżej podano zadania, które należy wykonać w celu ukończenia tego samouczka:

**Korzystanie IoT Hub wzbogaceń komunikatów**
> [!div class="checklist"]
> * Pierwsza metoda: tworzenie zasobów i konfigurowanie routingu komunikatów przy użyciu interfejsu wiersza polecenia platformy Azure. Skonfiguruj wzbogacenia komunikatów ręcznie przy użyciu Azure Portal [.](https://portal.azure.com)
> * Druga metoda: tworzenie zasobów oraz konfigurowanie routingu komunikatów i wzbogacania komunikatów przy użyciu Resource Manager szablonu. 
> * Uruchom aplikację, która symuluje urządzenie IoT wysyłające komunikaty do centrum.
> * Wyświetl wyniki i sprawdź, czy wzbogacenia komunikatów działają zgodnie z oczekiwaniami.

## <a name="prerequisites"></a>Wymagania wstępne

- Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Zainstaluj [Visual Studio](https://www.visualstudio.com/).

- Upewnij się, że port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym samouczku używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Pobieranie repozytorium przykładów IoT C#

Pobierz [przykłady IoT dla języka C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) z usługi GitHub i rozpakować je. To repozytorium zawiera kilka aplikacji, skryptów i Resource Manager szablonów. Te, które mają być używane w tym samouczku, są następujące:

* W przypadku metody ręcznej istnieje skrypt interfejsu wiersza polecenia, który służy do tworzenia zasobów. Ten skrypt znajduje się w skrypcie /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Ten skrypt tworzy zasoby i konfiguruje routing komunikatów. Po uruchomieniu tego skryptu ręcznie utwórz wzbogacanie komunikatów przy użyciu Azure Portal [.](https://portal.azure.com)
* W przypadku metody zautomatyzowanej istnieje Azure Resource Manager szablonu. Szablon znajduje się w pliku /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.jsna. Ten szablon tworzy zasoby, konfiguruje routing komunikatów, a następnie konfiguruje wzbogacenia komunikatów.
* Trzecią używaną aplikacją jest aplikacja symulacji urządzenia, która umożliwia wysyłanie komunikatów do centrum IoT i testowanie wzbogacania komunikatów.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Ręczne konfigurowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Oprócz tworzenia niezbędnych zasobów skrypt interfejsu wiersza polecenia platformy Azure konfiguruje również dwie trasy do punktów końcowych, które są oddzielnymi kontenerami magazynu. Aby uzyskać więcej informacji na temat konfigurowania routingu komunikatów, zobacz [Samouczek routingu](tutorial-routing.md). Po skonfigurowaniu zasobów użyj [](https://portal.azure.com) Azure Portal, aby skonfigurować wzbogacanie komunikatów dla każdego punktu końcowego. Następnie przejdź do kroku testowania.

> [!NOTE]
> Wszystkie komunikaty są kierowane do obu punktów końcowych, ale tylko komunikaty kierowane do punktu końcowego ze skonfigurowanymi wzbogacaniami komunikatów zostaną wzbogacone.
>

Możesz użyć następującego skryptu lub otworzyć go w folderze /resources pobranego repozytorium. Skrypt wykonuje następujące kroki:

* Utwórz centrum IoT.
* Tworzenie konta magazynu
* Utwórz dwa kontenery na koncie magazynu. Jeden kontener jest dla wzbogaconych komunikatów, a drugi dla komunikatów, które nie są wzbogacone.
* Skonfiguruj routing dla dwóch różnych kont magazynu:
    * Utwórz punkt końcowy dla każdego kontenera konta magazynu.
    * Utwórz trasę do każdego z punktów końcowych kontenera konta magazynu.

Istnieje kilka nazw zasobów, które muszą być globalnie unikatowe, takich jak nazwa centrum IoT i nazwa konta magazynu. Aby ułatwić uruchamianie skryptu, te nazwy zasobów są dołączane z losową wartością alfanumeryczną o nazwie *randomValue*. Losowa wartość jest generowana raz w górnej części skryptu. Jest on dołączany do nazw zasobów zgodnie z potrzebami w całym skrypcie. Jeśli nie chcesz, aby wartość była losowa, możesz ustawić ją na pusty ciąg lub na określoną wartość.

Jeśli jeszcze tego nie zrobiono, otwórz okno usługi Azure [Cloud Shell](https://shell.azure.com) i upewnij się, że jest ono ustawione na bash. Otwórz skrypt w rozpakowanym repozytorium, naciśnij klawisze Ctrl+A, aby zaznaczyć wszystkie te skrypty, a następnie naciśnij klawisze Ctrl+C, aby go skopiować. Alternatywnie możesz skopiować poniższy skrypt interfejsu wiersza polecenia lub otworzyć go bezpośrednio w Cloud Shell. Wklej skrypt w oknie Cloud Shell, klikając prawym przyciskiem myszy wiersz polecenia i wybierając polecenie **Wklej**. Skrypt uruchamia po jednej instrukcji na raz. Po zakończeniu działania skryptu wybierz klawisz **Enter,** aby upewnić się, że uruchamia ostatnie polecenie. Poniższy blok kodu przedstawia używany skrypt z komentarzami objaśniacymi jego działania.

Poniżej znajdują się zasoby utworzone przez skrypt. *Wzbogacone* oznacza, że zasób jest dla komunikatów z wzbogaceniami. *Oryginalny* oznacza, że zasób jest dla komunikatów, które nie są wzbogacone.

| Nazwa | Wartość |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nazwa kontenera | Oryginał  |
| nazwa kontenera | Wzbogacony  |
| Nazwa urządzenia IoT | Contoso-Test-Device |
| IoT Hub nazwy użytkownika | ContosoTestHubMsgEn |
| nazwa konta magazynu | contosostorage |
| nazwa punktu końcowego 1 | ContosoStorageEndpointOriginal |
| nazwa punktu końcowego 2 | ContosoStorageEndpointEnriched|
| route Name 1 | ContosoStorageRouteOriginal |
| route Name 2 | ContosoStorageRouteEnriched |

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

W tym momencie wszystkie zasoby są skonfigurowane, a routing komunikatów jest skonfigurowany. Konfigurację routingu komunikatów można wyświetlić w portalu i skonfigurować wzbogacanie komunikatów dla komunikatów przechodzących do **wzbogaconego kontenera** magazynu.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Ręczne konfigurowanie wzbogacania komunikatów przy użyciu Azure Portal

1. Przejdź do centrum IoT Hub, wybierając **pozycję Grupy zasobów.** Następnie wybierz grupę zasobów ustawioną dla tego samouczka **(ContosoResourcesMsgEn).** Znajdź centrum IoT na liście i wybierz je. Wybierz **pozycję Routing komunikatów** dla centrum IoT.

   ![Wybieranie routingu komunikatów](./media/tutorial-message-enrichments/select-iot-hub.png)

   Okienko routingu komunikatów ma trzy karty z **etykietami Trasy,** **Niestandardowe punkty końcowe** i **Wzbogać komunikaty.** Przejrzyj pierwsze dwie karty, aby wyświetlić konfigurację konfiguracyjną za pomocą skryptu. Użyj trzeciej karty, aby dodać wzbogacenia komunikatów. Wzbogaćmy komunikaty wysyłane do punktu końcowego dla kontenera magazynu o nazwie **wzbogacony .** Wypełnij nazwę i wartość, a następnie wybierz punkt końcowy **ContosoStorageEndpointEnriched** z listy rozwijanej. Oto przykład sposobu skonfigurowania wzbogacania, które dodaje nazwę centrum IoT do komunikatu:

   ![Dodawanie pierwszego wzbogacania](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Dodaj te wartości do listy dla punktu końcowego ContosoStorageEndpointEnriched.

   | Klucz | Wartość | Punkt końcowy (lista rozwijana) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Idklienta | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Jeśli urządzenie nie ma bliźniaczej reprezentacji, wartość wrzucana w tym miejscu będzie oznaczana jako ciąg wartości w wzbogacaniach komunikatów. Aby wyświetlić informacje o bliźniaczej reprezentacji urządzenia, przejdź do centrum w portalu i wybierz pozycję **Urządzenia IoT.** Wybierz urządzenie, a następnie wybierz pozycję **Bliźniacze reprezentacji** urządzenia w górnej części strony.
   >
   > Możesz edytować informacje o bliźniaczej reprezentacji, aby dodać tagi, takie jak lokalizacja, i ustawić je na określoną wartość. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](iot-hub-devguide-device-twins.md).

3. Po zakończeniu okienko powinno wyglądać podobnie do tego obrazu:

   ![Tabela z dodanymi wszystkimi wzbogacaniami](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Wybierz **pozycję Zastosuj,** aby zapisać zmiany. Przejdź do sekcji [Testowanie wzbogacania komunikatów.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Tworzenie i konfigurowanie przy użyciu Resource Manager szablonu
Za pomocą szablonu Resource Manager można tworzyć i konfigurować zasoby, routing komunikatów i wzbogacanie komunikatów.

1. Zaloguj się w witrynie Azure Portal. Wybierz **pozycję + Utwórz zasób,** aby utworzyć pole wyszukiwania. Wprowadź *wdrożenie szablonu* i wyszukaj je. W okienku wyników wybierz pozycję **Template deployment (wdrażanie przy użyciu szablonu niestandardowego).**

   ![Template deployment w Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Wybierz **pozycję Utwórz** w **Template deployment** aplikacji.

1. W **okienku Wdrożenie niestandardowe** wybierz pozycję **Skompilowanie własnego szablonu w edytorze**.

1. W **okienku Edytowanie szablonu** wybierz pozycję **Załaduj plik**. Eksplorator Windows zostanie wyświetlona. Znajdź plik **template_messageenrichments.jsw** pliku rozpakowanego repo w lokalizacji **/iot-hub/Tutorials/Routing/SimulatedDevice/resources.** 

   ![Wybieranie szablonu z komputera lokalnego](./media/tutorial-message-enrichments/template-select.png)

1. Wybierz **pozycję Otwórz,** aby załadować plik szablonu z komputera lokalnego. Ładuje się i pojawia się w okienku edycji.

   Ten szablon jest ustawiony do używania globalnie unikatowej nazwy centrum IoT i nazwy konta magazynu przez dodanie losowej wartości na końcu nazw domyślnych, dzięki czemu można używać szablonu bez konieczności dokonywania w nim żadnych zmian.

   Poniżej znajdują się zasoby utworzone przez załadowanie szablonu. **Wzbogacone** oznacza, że zasób jest dla komunikatów z wzbogacaniami. **Oryginalny** oznacza, że zasób jest dla komunikatów, które nie są wzbogacone. Są to te same wartości, które są używane w skrypcie interfejsu wiersza polecenia platformy Azure.

   | Nazwa | Wartość |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nazwa kontenera | Oryginał  |
   | nazwa kontenera | Wzbogacony  |
   | Nazwa urządzenia IoT | Contoso-Test-Device |
   | IoT Hub nazwy użytkownika | ContosoTestHubMsgEn |
   | nazwa konta magazynu | contosostorage |
   | nazwa punktu końcowego 1 | ContosoStorageEndpointOriginal |
   | nazwa punktu końcowego 2 | ContosoStorageEndpointEnriched|
   | route Name 1 | ContosoStorageRouteOriginal |
   | route Name 2 | ContosoStorageRouteEnriched |

1. Wybierz pozycję **Zapisz**. Zostanie **wyświetlone okienko** Wdrożenie niestandardowe z wyświetlonymi wszystkimi parametrami używanymi przez szablon. Jedynym polem, które należy ustawić, jest **grupa zasobów**. Utwórz nową lub wybierz ją z listy rozwijanej.

   Oto górna połowa okienka **Wdrożenie** niestandardowe. Zobaczysz, gdzie wypełniasz grupę zasobów.

   ![Górna połowa okienka Wdrożenie niestandardowe](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Poniżej znajduje się dolna połowa okienka **Wdrożenie** niestandardowe. Możesz zobaczyć pozostałe parametry oraz warunki i postanowienia. 

   ![Dolna połowa okienka Wdrożenie niestandardowe](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Zaznacz pole wyboru, aby zaakceptować warunki i postanowienia. Następnie wybierz **pozycję Kup,** aby kontynuować wdrażanie szablonu.

1. Poczekaj na pełne wdrożenie szablonu. Wybierz ikonę dzwonka w górnej części ekranu, aby sprawdzić postęp. Po zakończeniu przejdź do sekcji [Testowanie wzbogaceń](#test-message-enrichments) komunikatów.

## <a name="test-message-enrichments"></a>Testowanie wzbogaceń komunikatów

Aby wyświetlić wzbogacenia komunikatów, wybierz **pozycję Grupy zasobów.** Następnie wybierz grupę zasobów używaną w tym samouczku. Wybierz centrum IoT Hub z listy zasobów, a następnie przejdź do tematu **Obsługa komunikatów.** Zostaną wyświetlone konfiguracja routingu komunikatów i skonfigurowane wzbogacenia.

Teraz, gdy wzbogacenia komunikatów są skonfigurowane dla punktu końcowego, uruchom aplikację urządzenia symulowanego, aby wysyłać komunikaty do centrum IoT. Centrum zostało ustawione przy użyciu ustawień, które wykonać następujące zadania:

* Komunikaty kierowane do punktu końcowego magazynu ContosoStorageEndpointOriginal nie zostaną wzbogacone i będą przechowywane w kontenerze magazynu `original` .

* Komunikaty kierowane do punktu końcowego magazynu ContosoStorageEndpointEnriched zostaną wzbogacone i przechowywane w kontenerze magazynu `enriched` .

Aplikacja urządzenia symulowanego jest jedną z aplikacji w rozpakowanych plikach do pobrania. Aplikacja wysyła komunikaty dla każdej z różnych metod routingu komunikatów w samouczku [Routing,](tutorial-routing.md)który obejmuje usługę Azure Storage.

Kliknij dwukrotnie plik rozwiązania **IoT_SimulatedDevice.sln,** aby otworzyć kod w pliku Visual Studio, a następnie otwórz **plik Program.cs.** Zastąp nazwę centrum IoT jako znacznik `{your hub name}` . Format nazwy hosta centrum IoT to **{nazwa centrum}.azure-devices.net**. W tym samouczku nazwa hosta centrum jest ContosoTestHubMsgEn.azure-devices.net. Następnie zastąp klucz urządzenia zapisany wcześniej podczas uruchamiania skryptu, aby utworzyć zasoby dla znacznika `{your device key}` .

Jeśli nie masz klucza urządzenia, możesz go pobrać z portalu. Po zalogowaniu przejdź do opcji **Grupy zasobów,** wybierz grupę zasobów, a następnie wybierz centrum IoT. W obszarze **Urządzenia IoT** dla urządzenia testowego wybierz urządzenie. Wybierz ikonę kopiowania obok opcji **Klucz podstawowy,** aby skopiować go do schowka.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Uruchamianie i testowanie

Uruchom aplikację konsolowa przez kilka minut. Wysyłane komunikaty są wyświetlane na ekranie konsoli aplikacji.

Aplikacja wysyła nowy komunikat z urządzenia do centrum IoT Hub co sekundę. Komunikat zawiera obiekt serializowany w notacji JSON z identyfikatorem urządzenia, temperaturą, wilgotnością i poziomem komunikatu ustawianym domyślnie na `normal`. Losowo przypisuje poziom lub , co powoduje, że komunikat jest przekierowyny do konta magazynu `critical` `storage` lub do domyślnego punktu końcowego. Komunikaty wysyłane do **wzbogaconego kontenera** na koncie magazynu zostaną wzbogacone.

Po wysłaniu kilku komunikatów magazynu wyświetl dane.

1. Wybierz pozycję **Grupy zasobów**. Znajdź grupę zasobów **ContosoResourcesMsgEn** i wybierz ją.

2. Wybierz konto magazynu, czyli **contosostorage.** Następnie wybierz **Eksplorator usługi Storage (wersja zapoznawcza)** w okienku po lewej stronie.

   ![Wybierz Eksplorator usługi Storage](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Wybierz **pozycję KONTENERY OBIEKTÓW BLOB,** aby wyświetlić dwa kontenery, których można użyć.

   ![Wyświetlanie kontenerów na koncie magazynu](./media/tutorial-message-enrichments/show-blob-containers.png)

Komunikaty w kontenerze o nazwie **wzbogacone** mają wzbogacenia komunikatów zawarte w komunikatach. Komunikaty w kontenerze o nazwie **original mają** nieprzetworzone komunikaty bez wzbogaceń. Przejdź do szczegółów jednego z kontenerów, aż dojedziesz do dołu i otworzysz najnowszy plik komunikatów. Następnie wykonaj to samo dla drugiego kontenera, aby sprawdzić, czy do komunikatów w tym kontenerze nie dodano żadnych wzbogaceń.

Gdy przyjrzysz się komunikatom, które zostały wzbogacone, powinien zostać wyświetlony komunikat "my IoT Hub" z nazwą centrum i lokalizacją oraz identyfikatorem klienta w taki sposób:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Oto komunikat niezachrzały. Zwróć uwagę, że "IoT Hub", "devicelocation" i "customerID" nie są tutaj wyświetlane, ponieważ te pola są dodawane przez wzbogacenia. Ten punkt końcowy nie ma żadnych wzbogaceń.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wszystkie zasoby utworzone w tym samouczku, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku następuje również usunięcie centrum IoT, konta magazynu i samej grupy zasobów.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Oczyszczanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć grupę zasobów, użyj polecenia [az group delete](/cli/azure/group#az_group_delete). Pamiętaj, że na początku tego samouczka ustawiono wartość `$resourceGroup` **ContosoResourcesMsgEn.**

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i przetestowano dodawanie wzbogaceń komunikatów do IoT Hub komunikatów, korzystając z następujących kroków:

**Korzystanie IoT Hub wzbogaceń komunikatów**

> [!div class="checklist"]
> * Pierwsza metoda: tworzenie zasobów i konfigurowanie routingu komunikatów przy użyciu interfejsu wiersza polecenia platformy Azure. Ręczne konfigurowanie wzbogacania komunikatów przy użyciu Azure Portal [.](https://portal.azure.com)
> * Druga metoda: tworzenie zasobów oraz konfigurowanie routingu komunikatów i wzbogacania komunikatów przy użyciu Azure Resource Manager szablonu.
> * Uruchom aplikację, która symuluje urządzenie IoT wysyłające komunikaty do centrum.
> * Wyświetl wyniki i sprawdź, czy wzbogacenia komunikatów działają zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat wzbogaceń komunikatów, [zobacz Overview of message enrichments (Omówienie wzbogaceń komunikatów).](iot-hub-message-enrichments-overview.md)

Aby uzyskać więcej informacji na temat routingu komunikatów, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Używanie IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Samouczek: IoT Hub routing](tutorial-routing.md)