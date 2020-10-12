---
title: Tworzenie hostów systemu Linux przy użyciu maszyny platformy Docker
description: Opisuje sposób używania maszyny platformy Docker do tworzenia hostów platformy Docker na platformie Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 83799940f8c98952a435f582a9160585e9dbdfd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289789"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Jak utworzyć hosty na platformie Azure przy użyciu maszyny platformy Docker
W tym artykule szczegółowo przedstawiono sposób użycia [maszyny platformy Docker](https://docs.docker.com/machine/) do tworzenia hostów na platformie Azure. `docker-machine`Polecenie tworzy maszynę wirtualną z systemem Linux na platformie Azure, a następnie instaluje platformę Docker. Następnie można zarządzać hostami platformy Docker na platformie Azure przy użyciu tych samych lokalnych narzędzi i przepływów pracy. Aby używać maszyny Docker w systemie Windows 10, należy użyć systemu Linux bash.

## <a name="create-vms-with-docker-machine"></a>Tworzenie maszyn wirtualnych przy użyciu maszyny platformy Docker
Najpierw Uzyskaj identyfikator subskrypcji platformy Azure za pomocą [AZ Account show](/cli/azure/account) w następujący sposób:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Możesz tworzyć maszyny wirtualne platformy Docker na platformie Azure za pomocą programu `docker-machine create` , określając *platformę Azure* jako sterownik. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą platformy Docker Azure](https://docs.docker.com/machine/drivers/azure/)

Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM*na podstawie planu "standardowa D2 v2", tworzy konto użytkownika o nazwie *azureuser*, a następnie otwiera port *80* na maszynie wirtualnej hosta. Postępuj zgodnie z monitami, aby zalogować się do konta platformy Azure i przyznać uprawnienia maszynom platformy Docker do tworzenia zasobów i zarządzania nimi.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Konfigurowanie powłoki platformy Docker
Aby nawiązać połączenie z hostem platformy Docker na platformie Azure, zdefiniuj odpowiednie ustawienia połączenia. Jak wskazano na końcu danych wyjściowych, Wyświetl informacje o połączeniu dla hosta platformy Docker w następujący sposób: 

```bash
docker-machine env myvm
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

Aby zdefiniować ustawienia połączenia, można uruchomić sugerowane polecenie konfiguracyjne ( `eval $(docker-machine env myvm)` ) lub ręcznie ustawić zmienne środowiskowe. 

## <a name="run-a-container"></a>Uruchamianie kontenera
Aby wyświetlić kontener w działaniu, program umożliwia uruchomienie podstawowego serwera webNGINX. Utwórz kontener z `docker run` i Uwidocznij port 80 dla ruchu w sieci Web w następujący sposób:

```bash
docker run -d -p 80:80 --restart=always nginx
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Wyświetl uruchomione kontenery za pomocą `docker ps` . Następujące przykładowe dane wyjściowe przedstawiają kontener NGINX z systemem z udostępnionym portem 80:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Testowanie kontenera
Uzyskaj publiczny adres IP hosta platformy Docker w następujący sposób:


```bash
docker-machine ip myvm
```

Aby wyświetlić kontener w działaniu, Otwórz przeglądarkę internetową i wprowadź publiczny adres IP zanotowany w danych wyjściowych poprzedniego polecenia:

![Uruchamianie kontenera NGNIX](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z przykładami dotyczącymi używania Docker Compose, zobacz Rozpoczynanie [pracy z platformą Docker i tworzenie na platformie Azure](docker-compose-quickstart.md).
