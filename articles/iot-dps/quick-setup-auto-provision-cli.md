---
title: Szybki Start — Konfigurowanie IoT Hub Device Provisioning Service platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Szybki Start — Konfigurowanie usługi Azure IoT Hub Device Provisioning Service (DPS) przy użyciu interfejsu wiersza polecenia platformy Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 611068fa020321be88be6e1d6da663266029c658
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660189"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Szybki Start: Konfigurowanie IoT Hub Device Provisioning Service przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki Start zawiera szczegółowe informacje dotyczące tworzenia Centrum IoT Hub i IoT Hub Device Provisioning Service oraz łączenia tych dwóch usług przy użyciu interfejsu wiersza polecenia platformy Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Centrum IoT i usługa aprowizacji utworzone w tym przewodniku Szybki Start będą publicznie wykrywalne jako punkty końcowe DNS. Pamiętaj, że należy unikać wszelkich poufnych informacji, jeśli zdecydujesz się zmienić nazwy używane dla tych zasobów.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *my-sample-resource-group* w lokalizacji *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Ten przykład tworzy grupę zasobów w lokalizacji Zachodnie stany USA. Listę dostępnych lokalizacji można wyświetlić, uruchamiając polecenie `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Utwórz centrum IoT za pomocą polecenia [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

Poniższy przykład tworzy centrum IoT o nazwie *my-sample-hub* w lokalizacji *westus*. Nazwa Centrum IoT musi być globalnie unikatowa na platformie Azure, dlatego możesz chcieć dodać unikatowy prefiks lub sufiks do nazwy przykładu lub wybrać nową nazwę. Upewnij się, że nazwa jest zgodna z właściwymi konwencjami nazewnictwa Centrum IoT: długość powinna wynosić 3-50 znaków i może zawierać tylko znaki alfanumeryczne wielkie lub małe litery ("-"). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Tworzenie usługi Device Provisioning

Utwórz usługę Device Provisioning za pomocą polecenia [AZ IoT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) . 

W poniższym przykładzie jest tworzona usługa aprowizacji o nazwie *My-Sample-DPS* w lokalizacji *zachodniej* . Należy również wybrać globalnie unikatową nazwę dla własnej usługi aprowizacji. Upewnij się, że są zgodne z właściwymi konwencjami nazewnictwa dla IoT Hub Device Provisioning Service: długość powinna wynosić 3-64 znaków i może zawierać tylko wielkie lub małe litery lub łączniki ("-").

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ten przykład tworzy usługę aprowizowania w lokalizacji Zachodnie stany USA. Można wyświetlić listę dostępnych lokalizacji, uruchamiając polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przechodząc do strony [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukując usługę „Device Provisioning”. W poleceniach lokalizacje można określić w jednym formacie Word lub wielowyrazowym; na przykład: Zachodnie, zachodnie stany USA, ZACHODNIe stany USA itd. W wartości nie jest rozróżniana wielkość liter. Jeśli do określenia lokalizacji używasz formatu wielu słów, ujmij wartość w cudzysłowy, na przykład `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

Parametry połączenia centrum IoT są potrzebne do połączenia go z usługą aprowizacji urządzenia. Użyj polecenia [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string), aby pobrać parametry połączenia, i użyj jego danych wyjściowych, aby ustawić zmienną, która będzie używana podczas łączenia tych dwóch zasobów. 

Poniższy przykład ustawia zmienną *hubConnectionString* na wartość parametrów połączenia dla klucza podstawowego zasad *iothubowner* centrum ( `--policy-name` parametr może służyć do określania innych zasad). Wykorzystaj *centrum my-Sample-Hub* dla unikatowej nazwy usługi IoT Hub, która została wybrana wcześniej. Polecenie używa opcji [query](/cli/azure/query-azure-cli) i [output](/cli/azure/format-output-azure-cli#tsv-output-format) interfejsu wiersza polecenia platformy Azure, aby wyodrębnić parametry połączenia z danych wyjściowych polecenia.

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
> Jeśli używasz lokalnej powłoki Windows/CMD lub hosta programu PowerShell, zmodyfikuj polecenia, aby użyć poprawnej składni dla tego środowiska.
>
> Jeśli używasz Azure Cloud Shell, sprawdź, czy na liście rozwijanej środowisko po lewej stronie okna powłoki widnieją **bash**.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Łączenie centrum IoT z usługą aprowizowania

Połącz centrum IoT i usługę aprowizowania za pomocą polecenia [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

Poniższy przykład łączy centrum IoT o nazwie *My-Sample-Hub* w lokalizacji *zachodniej* i usługi Device Provisioning o nazwie *My-Sample-DPS*. Wykorzystaj te nazwy dla unikatowych nazw usług IoT Hub i Device Provisioning. Polecenie używa parametrów połączenia dla Centrum IoT Hub, które były przechowywane w zmiennej *hubConnectionString* w poprzednim kroku.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Wykonanie polecenia może potrwać kilka minut.

## <a name="verify-the-provisioning-service"></a>Sprawdzanie usługi aprowizowania

Pobierz szczegóły dotyczące usługi aprowizowania za pomocą polecenia [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

Poniższy przykład pobiera szczegółowe dane usługi aprowizowania o nazwie *my-sample-dps*. Należy zapoznać się z tą nazwą dla własnej nazwy usługi Device Provisioning.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
Połączone centrum IoT jest wyświetlane w kolekcji *properties.iotHubs*.

![Weryfikowanie usługi aprowizacji](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuacji, możesz użyć następujących poleceń, aby usunąć usługę aprowizowania, centrum IoT lub grupę zasobów i wszystkie jej zasoby. Zastąp nazwy zamieszczonych poniżej zasobów nazwami własnych zasobów.

Aby usunąć usługę aprowizowania, uruchom polecenie [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Aby usunąć centrum IoT, uruchom polecenie [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom polecenie [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono Centrum IoT Hub i wystąpienie usługi Device Provisioning Service i połączono te dwa zasoby. Aby dowiedzieć się, jak używać tego Instalatora do aprowizacji symulowanego urządzenia, przejdź do przewodnika Szybki Start dotyczącego tworzenia symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start dotyczący tworzenia symulowanego urządzenia](./quick-create-simulated-device.md)
