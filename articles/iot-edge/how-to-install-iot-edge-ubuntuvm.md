---
title: Uruchom Azure IoT Edge na Ubuntu Virtual Machines | Microsoft Docs
description: Instrukcje dotyczące konfiguracji Azure IoT Edge Ubuntu 18,04 LTS Virtual Machines
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1cd89f3f772effce4997fb69b37858ce2077c1dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201095"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Uruchom Azure IoT Edge na Ubuntu Virtual Machines

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako niewielkich jako Raspberry Pi lub jako serwer przemysłowy. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury.

Aby dowiedzieć się więcej o tym, jak działa środowisko uruchomieniowe IoT Edge i jakie składniki są uwzględnione, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule wymieniono kroki wdrażania maszyny wirtualnej Ubuntu 18,04 LTS z zainstalowanym środowiskiem uruchomieniowym Azure IoT Edge i skonfigurowanym za pomocą wstępnie podanych parametrów połączenia urządzenia. Wdrożenie jest realizowane przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) opartego na [init w chmurze](../virtual-machines/linux/using-cloud-init.md
) , który jest przechowywany w repozytorium [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) Project.

Podczas pierwszego rozruchu maszyna wirtualna z systemem Ubuntu 18,04 LTS [instaluje najnowszą wersję środowiska uruchomieniowego Azure IoT Edge za pośrednictwem usługi Cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Ustawia również dostarczone parametry połączenia przed uruchomieniem środowiska uruchomieniowego, co pozwala łatwo skonfigurować i połączyć urządzenie IoT Edge bez konieczności uruchamiania usługi SSH lub sesji pulpitu zdalnego.

>[!NOTE]
>Szablon używany w tym artykule instaluje IoT Edge wersji 1,1.

## <a name="deploy-using-deploy-to-azure-button"></a>Wdrażanie przy użyciu przycisku Wdróż na platformie Azure

[Przycisk Wdróż na platformie Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) umożliwia usprawnienie wdrażania [szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md) obsługiwanych w witrynie GitHub.  W tej sekcji przedstawiono użycie przycisku Wdróż na platformie Azure zawartego w repozytorium [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) projektu.  


1. Wdrażamy Azure IoT Edgeą maszynę wirtualną z systemem Linux przy użyciu szablonu Azure Resource Manager iotedge-VM-Deploy.  Aby rozpocząć, kliknij poniższy przycisk:

    [![Przycisk Wdrażanie na platformie Azure dla szablonu iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. W nowo uruchomionym oknie Wypełnij pola dostępne formularza:

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający szablon iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Subskrypcja**: aktywna subskrypcja platformy Azure, w której ma zostać wdrożona maszyna wirtualna.

    **Grupa zasobów**: istniejąca lub nowo utworzona grupa zasobów zawierająca maszynę wirtualną i skojarzone z nią zasoby.

    **Prefiks etykiety DNS**: wymagana wartość wyboru, która jest używana do prefiksu nazwy hosta maszyny wirtualnej.

    **Nazwa użytkownika administratora**: Nazwa użytkownika, która będzie mieć uprawnienia root dotyczące wdrożenia.

    **Parametry połączenia urządzenia**: [Parametry połączenia urządzenia](./how-to-register-device.md) , które zostały utworzone w ramach zamierzonego [IoT Hub](../iot-hub/about-iot-hub.md).

    **Rozmiar maszyny** wirtualnej: [rozmiar](../cloud-services/cloud-services-sizes-specs.md) maszyn wirtualnych do wdrożenia

    **Ubuntu wersja systemu operacyjnego**: wersja systemu operacyjnego Ubuntu, która ma zostać zainstalowana na podstawowej maszynie wirtualnej.

    **Lokalizacja**: [region geograficzny](https://azure.microsoft.com/global-infrastructure/locations/) , w którym ma zostać wdrożona maszyna wirtualna, wartość domyślna to lokalizacja wybranej grupy zasobów.

    **Typ uwierzytelniania**: Wybierz **sshPublicKey** lub **hasło** w zależności od preferencji użytkownika.

    **Hasło administratora lub klucz**: wartość klucza publicznego SSH lub wartość hasła w zależności od wybranego typu uwierzytelniania.

    Gdy wypełniono wszystkie pola, zaznacz pole wyboru u dołu strony, aby zaakceptować warunki i wybierz pozycję **Kup** , aby rozpocząć wdrażanie.

1. Sprawdź, czy wdrożenie zakończyło się pomyślnie.  Zasób maszyny wirtualnej powinien zostać wdrożony w wybranej grupie zasobów.  Zanotuj nazwę maszyny, która powinna mieć format `vm-0000000000000` . Zanotuj również powiązaną **nazwę DNS**, która powinna być w formacie `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    **Nazwa DNS** znajduje się w sekcji **Przegląd** nowo wdrożonej maszyny wirtualnej w witrynie Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający nazwę DNS maszyny wirtualnej iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Aby SSH do tej maszyny wirtualnej po zakończeniu instalacji, należy użyć skojarzonej **nazwy DNS** z poleceniem:  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Wdrażanie z poziomu interfejsu wiersza polecenia platformy Azure

1. Upewnij się, że zainstalowano rozszerzenie IoT interfejsu wiersza polecenia platformy Azure z:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Następnie, jeśli używasz interfejsu wiersza polecenia platformy Azure na pulpicie, Zacznij od zalogowania się:

   ```azurecli-interactive
   az login
   ```

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję, której chcesz użyć:
   1. Wyświetl listę swoich subskrypcji:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Skopiuj pole identyfikatora subskrypcji, którego chcesz użyć.

   1. Ustaw swoją subskrypcję roboczą z IDENTYFIKATORem, który został skopiowany:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Utwórz nową grupę zasobów (lub określ istniejącą w następnych krokach):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Utwórz nową maszynę wirtualną:

    Aby użyć typu **AuthenticationType** z `password` , zobacz Poniższy przykład:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Aby uwierzytelnić się przy użyciu klucza SSH, można to zrobić, określając element **AuthenticationType** `sshPublicKey` , a następnie podać wartość klucza SSH w parametrze **adminPasswordOrKey** .  Przykład przedstawiono poniżej.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Sprawdź, czy wdrożenie zakończyło się pomyślnie.  Zasób maszyny wirtualnej powinien zostać wdrożony w wybranej grupie zasobów.  Zanotuj nazwę maszyny, która powinna mieć format `vm-0000000000000` . Zanotuj również powiązaną **nazwę DNS**, która powinna być w formacie `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    **Nazwę DNS** można uzyskać z formatu danych wyjściowych w formacie JSON poprzedniego kroku w sekcji dane **wyjściowe** w ramach **publicznego wpisu SSH** .  Wartość tego wpisu może być używana do użycia protokołu SSH w przypadku nowo wdrożonej maszyny.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **Nazwę DNS** można również uzyskać z sekcji **Przegląd** nowo wdrożonej maszyny wirtualnej w ramach Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający nazwę DNS maszyny wirtualnej iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Aby SSH do tej maszyny wirtualnej po zakończeniu instalacji, należy użyć skojarzonej **nazwy DNS** z poleceniem:  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz zainstalowaną IoT Edge urządzenie z zainstalowanym środowiskiem uruchomieniowym, możesz [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją środowiska uruchomieniowego IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).

Jeśli chcesz otworzyć porty w celu uzyskania dostępu do maszyny wirtualnej za pośrednictwem protokołu SSH lub innych połączeń przychodzących, zapoznaj się z dokumentacją Virtual Machines platformy Azure dotyczącą [otwierania portów i punktów końcowych na maszynę wirtualną z systemem Linux](../virtual-machines/linux/nsg-quickstart.md)