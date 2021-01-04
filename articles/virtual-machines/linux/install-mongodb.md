---
title: Instalowanie MongoDB na maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Informacje na temat instalowania i konfigurowania MongoDB na maszynie wirtualnej z systemem Linux iusing interfejsu wiersza polecenia platformy Azure
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3bc8ed2745e06096e05f17319a8f7896f87f80f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97702042"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Jak zainstalować i skonfigurować MongoDB na maszynie wirtualnej z systemem Linux

[MongoDB](https://www.mongodb.org) to popularna baza danych NoSQL typu open source o wysokiej wydajności. W tym artykule opisano sposób instalowania i konfigurowania MongoDB na maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure. Przykłady przedstawiają następujące informacje:

* [Ręcznie zainstaluj i skonfiguruj podstawowe wystąpienie MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Tworzenie podstawowego wystąpienia usługi MongoDB przy użyciu szablonu Menedżer zasobów](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Tworzenie złożonego klastra MongoDB podzielonej na fragmenty z zestawami replik przy użyciu szablonu Menedżer zasobów](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ręczne instalowanie i Konfigurowanie MongoDB na maszynie wirtualnej
MongoDB [zawierają instrukcje dotyczące instalacji](https://docs.mongodb.com/manual/administration/install-on-linux/) dla systemu Linux dystrybucje, w tym Red Hat/CentOS, SUSE, Ubuntu i debian. Poniższy przykład tworzy maszynę wirtualną *CentOS* . Aby można było utworzyć to środowisko, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z użytkownikiem o nazwie *azureuser* przy użyciu uwierzytelniania klucza publicznego SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH z maszyną wirtualną przy użyciu własnej nazwy użytkownika i `publicIpAddress` wymienionej w danych wyjściowych z poprzedniego kroku:

```bash
ssh azureuser@<publicIpAddress>
```

Aby dodać źródła instalacji dla MongoDB, Utwórz plik repozytorium **yum** w następujący sposób:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Otwórz plik repozytorium MongoDB do edycji, taki jak z `vi` lub `nano` . Dodaj następujące wiersze:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Zainstaluj MongoDB przy użyciu **yum** w następujący sposób:

```bash
sudo yum install -y mongodb-org
```

Domyślnie SELinux jest wymuszane na obrazach CentOS, które uniemożliwiają dostęp do MongoDB. Zainstaluj narzędzia do zarządzania zasadami i skonfiguruj SELinux tak, aby MongoDB działał na domyślnym porcie TCP 27017 w następujący sposób:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Uruchom usługę MongoDB w następujący sposób:

```bash
sudo service mongod start
```

Sprawdź instalację MongoDB, łącząc się przy użyciu `mongo` klienta lokalnego:

```bash
mongo
```

Teraz Przetestuj wystąpienie MongoDB, dodając pewne dane, a następnie wyszukując:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

W razie potrzeby skonfiguruj MongoDB do automatycznego uruchamiania podczas ponownego uruchamiania systemu:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Tworzenie podstawowego wystąpienia MongoDB na CentOS przy użyciu szablonu
Można utworzyć podstawowe wystąpienie MongoDB na jednej maszynie wirtualnej CentOS przy użyciu następującego szablonu szybkiego startu platformy Azure z witryny GitHub. Ten szablon używa niestandardowego rozszerzenia skryptu dla systemu Linux, aby dodać repozytorium **yum** do nowo utworzonej maszyny wirtualnej CentOS, a następnie zainstalować MongoDB.

* [Podstawowe wystąpienie MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Aby można było utworzyć to środowisko, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index). Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie wdróż szablon MongoDB za pomocą pozycji [AZ Deployment Group Create](/cli/azure/deployment/group). Po wyświetleniu monitu wprowadź własne unikatowe wartości dla *newStorageAccountName*, *dnsNameForPublicIP* i nazwę użytkownika i hasło administratora:

```azurecli
az deployment group create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Zaloguj się do maszyny wirtualnej przy użyciu publicznego adresu DNS maszyny wirtualnej. Publiczny adres DNS można wyświetlić za pomocą [AZ VM show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH z maszyną wirtualną przy użyciu własnej nazwy użytkownika i publicznego adresu DNS:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Sprawdź instalację MongoDB, łącząc się przy użyciu lokalnego `mongo` klienta w następujący sposób:

```bash
mongo
```

Teraz Przetestuj wystąpienie przez dodanie danych i wyszukiwanie w następujący sposób:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Tworzenie złożonego klastra MongoDB podzielonej na fragmenty na CentOS przy użyciu szablonu
Możesz utworzyć złożony klaster MongoDB podzielonej na fragmenty przy użyciu następującego szablonu szybkiego startu platformy Azure z witryny GitHub. Ten szablon jest zgodny z [najlepszymi rozwiązaniami klastra MongoDB podzielonej na fragmenty](https://docs.mongodb.com/manual/core/sharded-cluster-components/) w celu zapewnienia nadmiarowości i wysokiej dostępności. Szablon tworzy dwie fragmentów z trzema węzłami w każdym zestawie replik. Utworzono również jedną replikę serwera konfiguracji z trzema węzłami i dwa serwery routerów **mongos** , aby zapewnić spójność aplikacji z poziomu fragmentów.

* [MongoDB fragmentowania klaster na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Wdrożenie tego złożonego klastra MongoDB podzielonej na fragmenty wymaga więcej niż 20 rdzeni, co jest zazwyczaj domyślną liczbą rdzeni na region dla subskrypcji. Otwórz żądanie pomocy technicznej platformy Azure, aby zwiększyć swoją liczbę rdzeni.

Aby można było utworzyć to środowisko, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index). Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie wdróż szablon MongoDB za pomocą pozycji [AZ Deployment Group Create](/cli/azure/deployment/group). Zdefiniuj własne nazwy zasobów i rozmiary, jeśli są one używane, jak w przypadku *mongoAdminUsername*, *sizeOfDataDiskInGB* i *configNodeVmSize*:

```azurecli
az deployment group create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Wdrożenie i skonfigurowanie wszystkich wystąpień maszyn wirtualnych może potrwać ponad godzinę. `--no-wait`Flaga jest używana na końcu poprzedniego polecenia, aby zwrócić kontrolę do wiersza polecenia po zaakceptowaniu wdrożenia szablonu przez platformę Azure. Stan wdrożenia można wyświetlić za pomocą polecenie [AZ Deployment Group Show](/cli/azure/deployment/group). Poniższy przykład ilustruje Wyświetlanie stanu wdrożenia *myMongoDBCluster* w *grupie zasobów zasobu* :

```azurecli
az deployment group show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Następne kroki
W tych przykładach połączysz się z wystąpieniem usługi MongoDB lokalnie z poziomu maszyny wirtualnej. Jeśli chcesz nawiązać połączenie z wystąpieniem usługi MongoDB z innej maszyny wirtualnej lub sieci, upewnij się, że [są tworzone odpowiednie reguły sieciowej grupy zabezpieczeń](nsg-quickstart.md).

W tych przykładach wdrożono podstawowe środowisko MongoDB na potrzeby programowania. Zastosuj wymagane opcje konfiguracji zabezpieczeń dla danego środowiska. Aby uzyskać więcej informacji, zobacz [dokumenty dotyczące zabezpieczeń MongoDB](https://docs.mongodb.com/manual/security/).

Aby uzyskać więcej informacji o tworzeniu przy użyciu szablonów, zobacz [omówienie Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Szablony Azure Resource Manager używają niestandardowego rozszerzenia skryptu do pobierania i uruchamiania skryptów na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [Korzystanie z rozszerzenia niestandardowego skryptu platformy Azure z Linux Virtual Machines](../extensions/custom-script-linux.md).
