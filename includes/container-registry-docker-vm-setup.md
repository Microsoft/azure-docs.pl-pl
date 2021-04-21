---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: 429377cd50e83195cb1c3a422416fdb35644a28e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773486"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

W celach testowych użyj maszyny wirtualnej z systemem Ubuntu z obsługą platformy Docker, aby uzyskać dostęp do rejestru kontenerów platformy Azure. Aby użyć Azure Active Directory w rejestrze, zainstaluj również interfejs wiersza [polecenia platformy Azure][azure-cli] na maszynie wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, pomiń ten krok tworzenia.

Możesz użyć tej samej grupy zasobów dla maszyny wirtualnej i rejestru kontenerów. Ta konfiguracja upraszcza oczyszczanie na końcu, ale nie jest wymagana. Jeśli zdecydujesz się utworzyć oddzielną grupę zasobów dla maszyny wirtualnej i sieci wirtualnej, uruchom [polecenie az group create.][az-group-create] W poniższym przykładzie przyjęto założenie, że ustawiono zmienne środowiskowe dla nazwy grupy zasobów i lokalizacji rejestru:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Teraz wd wdrażaj domyślną maszynę wirtualną platformy Azure z systemem Ubuntu za [pomocą narzędzia az vm create][az-vm-create]. Poniższy przykład tworzy maszynę wirtualną *o nazwie myDockerVM.*

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu działania polecenia zanotuj dane `publicIpAddress` wyświetlane przez interfejs wiersza polecenia platformy Azure. Ten adres pozwala na połączenia SSH z maszyną wirtualną.

### <a name="install-docker-on-the-vm"></a>Instalowanie platformy Docker na maszynie wirtualnej

Po uruchomieniu maszyny wirtualnej nawiązaniu z maszyną wirtualną połączenia SSH. Zastąp *publicIpAddress* publicznym adresem IP maszyny wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenia, aby zainstalować aplikację Docker na maszynie wirtualnej z systemem Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Po zakończeniu instalacji uruchom następujące polecenie, aby sprawdzić, czy na maszynie wirtualnej działa prawidłowo na tej maszynie wirtualnej:

```bash
sudo docker run -it hello-world
```

Dane wyjściowe:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalowanie interfejsu wiersza polecenia platformy Azure

Wykonaj kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure za pomocą narzędzia apt,](/cli/azure/install-azure-cli-apt) aby zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej z systemem Ubuntu. Na przykład:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Zakończ połączenie SSH.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-group-create]: /cli/azure/group