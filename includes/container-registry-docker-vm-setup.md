---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: d699e8985a3a23b3aab87601d5298d9c8f7e34e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245037"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

Do celów testowych Użyj maszyny wirtualnej Ubuntu z obsługą platformy Docker, aby uzyskać dostęp do usługi Azure Container Registry. Aby użyć uwierzytelniania Azure Active Directory do rejestru, należy również zainstalować [interfejs wiersza polecenia platformy Azure][azure-cli] na maszynie wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, Pomiń ten krok tworzenia.

Możesz użyć tej samej grupy zasobów dla maszyny wirtualnej i rejestru kontenerów. Ta konfiguracja upraszcza czyszczenie na końcu, ale nie jest wymagane. Jeśli zdecydujesz się utworzyć oddzielną grupę zasobów dla maszyny wirtualnej i sieci wirtualnej, uruchom polecenie [AZ Group Create][az-group-create]. W poniższym przykładzie przyjęto założenie, że dla nazwy grupy zasobów i lokalizacji rejestru określono zmienne środowiskowe:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Teraz Wdróż domyślną Ubuntu maszynę wirtualną platformy Azure za pomocą [AZ VM Create][az-vm-create]. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu wykonywania polecenia Zanotuj wyświetlane wartości w wierszu polecenia `publicIpAddress` platformy Azure. Użyj tego adresu, aby nawiązać połączenia SSH z maszyną wirtualną.

### <a name="install-docker-on-the-vm"></a>Zainstaluj platformę Docker na maszynie wirtualnej

Po uruchomieniu maszyny wirtualnej należy nawiązać połączenie SSH z maszyną wirtualną. Zastąp *publicIpAddress* publicznym adresem IP maszyny wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenia, aby zainstalować platformę Docker na maszynie wirtualnej Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Po zakończeniu instalacji uruchom następujące polecenie, aby sprawdzić, czy program Docker działa prawidłowo na maszynie wirtualnej:

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

Wykonaj kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure z programem apt](/cli/azure/install-azure-cli-apt) , aby zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej Ubuntu. Na przykład:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Wyjdź z połączenia SSH.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group