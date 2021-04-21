---
title: Szybki start — konfigurowanie usługi Azure IoT Hub Device Provisioning przy użyciu interfejsu wiersza polecenia platformy Azure
description: Szybki start — konfigurowanie usługi Azure IoT Hub Device Provisioning Service (DPS) przy użyciu interfejsu wiersza polecenia platformy Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3d52a83c8c0920c4d85aa5b4b6b89fd8d36e5fea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774975"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Szybki start: konfigurowanie interfejsu wiersza IoT Hub Device Provisioning Service platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku Szybki start za pomocą interfejsu wiersza polecenia platformy Azure otworzymy centrum IoT i IoT Hub Device Provisioning Service oraz łączymy obie usługi ze sobą. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Zarówno centrum IoT Hub, jak i usługa aprowingu, które utworzysz w tym przewodniku Szybki start, będą publicznie wykrywalne jako punkty końcowe DNS. Pamiętaj, że należy unikać wszelkich poufnych informacji, jeśli zdecydujesz się zmienić nazwy używane dla tych zasobów.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *my-sample-resource-group* w lokalizacji *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Ten przykład tworzy grupę zasobów w lokalizacji Zachodnie stany USA. Listę dostępnych lokalizacji można wyświetlić, uruchamiając polecenie `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Utwórz centrum IoT za pomocą polecenia [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create).

Poniższy przykład tworzy centrum IoT o nazwie *my-sample-hub* w lokalizacji *westus*. Nazwa centrum IoT musi być globalnie unikatowa na platformie Azure, dlatego możesz dodać unikatowy prefiks lub sufiks do przykładowej nazwy lub całkowicie wybrać nową nazwę. Upewnij się, że nazwa jest zgodna z odpowiednią konwencją nazewnictwa dla centrum IoT: powinna mieć długość od 3 do 50 znaków i może zawierać tylko małe i wielkie litery oraz łączniki (-). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Tworzenie usługi Device Provisioning Service

Utwórz usługę Device Provisioning Service za pomocą [polecenia az iot dps create.](/cli/azure/iot/dps#az_iot_dps_create) 

Poniższy przykład tworzy usługę aprowizowania o *nazwie my-sample-dps* w *lokalizacji westus.* Musisz również wybrać globalnie unikatową nazwę dla własnej usługi aprowingu. Upewnij się, że jest zgodna z odpowiednią konwencją nazewnictwa dla nazwy IoT Hub Device Provisioning Service: powinna mieć długość od 3 do 64 znaków i może zawierać tylko wielkie lub małe litery alfanumeryczne lub łączniki ("-").

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ten przykład tworzy usługę aprowizowania w lokalizacji Zachodnie stany USA. Można wyświetlić listę dostępnych lokalizacji, uruchamiając polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przechodząc do strony [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukując usługę „Device Provisioning”. W poleceniach lokalizacje można określić w jednym wyrazie lub w formacie wielu słów; na przykład: westus, West US, WEST US itp. W wartości nie jest wrażliwa wielkość liter. Jeśli do określenia lokalizacji używasz formatu wielu słów, ujmij wartość w cudzysłowy, na przykład `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

Parametry połączenia centrum IoT są potrzebne do połączenia go z usługą aprowizacji urządzenia. Użyj polecenia [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string), aby pobrać parametry połączenia, i użyj jego danych wyjściowych, aby ustawić zmienną, która będzie używana podczas łączenia tych dwóch zasobów. 

W poniższym przykładzie zmienna *hubConnectionString* jest ustawiana na wartość parametrów połączenia dla klucza podstawowego zasad *iothubowner* centrum (parametr może służyć do określenia innych `--policy-name` zasad). Dla wybranej wcześniej unikatowej nazwy centrum IoT wyekstoruj *my-sample-hub.* Polecenie używa opcji [query](/cli/azure/query-azure-cli) i [output](/cli/azure/format-output-azure-cli#tsv-output-format) interfejsu wiersza polecenia platformy Azure, aby wyodrębnić parametry połączenia z danych wyjściowych polecenia.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Aby wyświetlić parametry połączenia, możesz użyć polecenia `echo`.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Te dwa polecenia są prawidłowe dla hosta z powłoką Bash.
> 
> Jeśli używasz lokalnej powłoki systemu Windows/cmd lub hosta programu PowerShell, zmodyfikuj polecenia, aby użyć poprawnej składni dla tego środowiska.
>
> Jeśli używasz programu Azure Cloud Shell, sprawdź, czy lista rozwijana środowiska po lewej stronie okna powłoki ma stan **Bash**.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Łączenie centrum IoT z usługą aprowizowania

Połącz centrum IoT i usługę aprowizowania za pomocą polecenia [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

Poniższy przykład łączy centrum IoT o nazwie *my-sample-hub* w lokalizacji *westus* i usługę Device Provisioning Service o nazwie *my-sample-dps.* Wyekstuj te nazwy unikatowych nazw centrów IoT i usługi Device Provisioning Service, które zostały wybrane wcześniej. Polecenie używa parametrów połączenia dla centrum IoT, które zostało zapisane w zmiennej *hubConnectionString* w poprzednim kroku.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Ukończenie polecenia może potrwać kilka minut.

## <a name="verify-the-provisioning-service"></a>Sprawdzanie usługi aprowizowania

Pobierz szczegóły dotyczące usługi aprowizowania za pomocą polecenia [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show).

Poniższy przykład pobiera szczegółowe dane usługi aprowizowania o nazwie *my-sample-dps*. Zamieć tę nazwę na własną nazwę usługi Device Provisioning Service.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Połączone centrum IoT jest wyświetlane w kolekcji *properties.iotHubs*.

![Weryfikowanie usługi Aprowing Service](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuacji, możesz użyć następujących poleceń, aby usunąć usługę aprowizowania, centrum IoT lub grupę zasobów i wszystkie jej zasoby. Zastąp nazwy zasobów napisanych poniżej nazwami własnych zasobów.

Aby usunąć usługę aprowizowania, uruchom polecenie [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Aby usunąć centrum IoT, uruchom polecenie [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom polecenie [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono centrum IoT i wystąpienie usługi Device Provisioning Service oraz powiązyliśmy te dwa zasoby. Aby dowiedzieć się, jak za pomocą tej konfiguracji aprowizować symulowane urządzenie, przejdź do przewodnika Szybki start, aby utworzyć urządzenie symulowane.

> [!div class="nextstepaction"]
> [Przewodnik Szybki start tworzenia symulowanego urządzenia](./quick-create-simulated-device.md)
