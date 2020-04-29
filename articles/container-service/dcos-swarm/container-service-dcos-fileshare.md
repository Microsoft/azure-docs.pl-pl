---
title: (PRZESTARZAŁE) Udział plików dla klastra DC/OS na platformie Azure
description: Tworzenie udziału plików i instalowanie go w klastrze DC/OS w usłudze Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: e6651fc5988a1e1830807219cda02ab057db9a4f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "60480388"
---
# <a name="deprecated-create-and-mount-a-file-share-to-a-dcos-cluster"></a>(PRZESTARZAŁE) Tworzenie udziału plików i instalowanie go w klastrze DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym samouczku opisano, jak można utworzyć udział plików na platformie Azure i zainstalować go na każdym agencie i wzorcu klastra DC/OS. Skonfigurowanie udziału plików ułatwia udostępnianie plików w klastrze, na przykład takich jak pliki związane z konfiguracją, dostępem lub dzienniki. W ramach tego samouczka wykonywane są następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Tworzenie udziału plików
> * Instalowanie udziału w klastrze DC/OS

Do wykonania kroków opisanych w tym samouczku potrzebujesz klastra DC/OS usługi ACS. W razie potrzeby [ten przykładowy skrypt](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pomoże Ci go utworzyć.

Dla tego samouczka wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Tworzenie udziału plików na platformie Microsoft Azure

Przed rozpoczęciem korzystania z udziału plików platformy Azure z klastrem DC/OS usługi ACS konieczne jest utworzenie konta magazynu i udziału pliku. Uruchom następujący skrypt, aby utworzyć magazyn i udział plików. Zaktualizuj parametry wartościami z Twojego środowiska.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Instalowanie udziału w klastrze

Następnie udział plików musi zostać zainstalowany na każdej maszynie wirtualnej w Twoim klastrze. To zadanie jest wykonywane przy użyciu narzędzia/protokołu cifs. Operację instalacji można ukończyć ręcznie w każdym węźle klastra lub przez uruchomienie skryptu dla każdego węzła w klastrze.

W tym przykładzie uruchamiane są dwa skrypty, jeden w celu zainstalowania udziału plików platformy Azure, a drugi, aby uruchomić ten skrypt w każdym węźle klastra DC/OS.

Na początku wymagane są nazwa i klucz dostępu konta magazynu Azure. Aby uzyskać te informacje, uruchom następujące polecenia. Zanotuj każdą z nich. Te wartości są używane w kolejnym kroku.

Nazwa konta magazynu:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Klucz dostępu konta magazynu:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Następnie uzyskaj nazwę FQDN wzorca DC/OS i zapisz ją w zmiennej.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Skopiuj klucz prywatny do węzła głównego. Ten klucz jest potrzebny do utworzenia połączenia ssh z wszystkimi węzłami w klastrze. Zaktualizuj nazwę użytkownika, jeśli podczas tworzenia klastra użyto wartości innej niż domyślna. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Utwórz połączenie SSH przy użyciu wzorca (lub pierwszego wzorca) klastra opartego na rozwiązaniu DC/OS. Zaktualizuj nazwę użytkownika, jeśli podczas tworzenia klastra użyto wartości innej niż domyślna.

```azurecli-interactive
ssh azureuser@$FQDN
```

Utwórz plik o nazwie **cifsMount.sh** i skopiuj do niego poniższą zawartość. 

Ten skrypt jest używany do zainstalowania udziału plików platformy Azure. Zaktualizuj zmienne `STORAGE_ACCT_NAME` i `ACCESS_KEY`, korzystając z zebranych wcześniej informacji.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Utwórz drugi plik o nazwie **getNodesRunScript.sh** i skopiuj poniższą zawartość do pliku. 

Ten skrypt umożliwia odnalezienie wszystkich węzłów klastra, a następnie uruchamia skrypt **cifsMount.sh**, aby zainstalować udział plików na każdym z nich.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Uruchom skrypt, aby zainstalować udział plików platformy Azure we wszystkich węzłach klastra.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Udział plików jest teraz dostępny pod adresem `/mnt/share/dcosshare` w każdym węźle klastra.

## <a name="next-steps"></a>Następne kroki

W tym samouczku platformy Azure udział plików został udostępniony w klastrze DC/OS za pomocą kroków:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Tworzenie udziału plików
> * Instalowanie udziału w klastrze DC/OS

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat integracji usługi Azure Container Registry z rozwiązaniem DC/OS na platformie Azure.  

> [!div class="nextstepaction"]
> [Równoważenie obciążenia aplikacji](container-service-dcos-acr.md)
