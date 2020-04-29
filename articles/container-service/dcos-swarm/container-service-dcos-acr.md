---
title: (PRZESTARZAŁE) Korzystanie z usługi ACR z klastrem Azure DC/OS
description: Korzystanie z usługi Azure Container Registry z klastrem DC/OS w usłudze Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 9e69b66c7cee5a6e012ad7ed2477556fa840bfb5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78402071"
---
# <a name="deprecated-use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>(PRZESTARZAŁE) Wdrażanie aplikacji przy użyciu usługi ACR z klastrem DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule przedstawiamy sposób korzystania z usługi Azure Container Registry z klastrem DC/OS. Dzięki użyciu usługi ACR można prywatnie przechowywać obrazy kontenera i zarządzać nimi. Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Wdrażanie usługi Azure Container Registry (w razie potrzeby)
> * Konfigurowanie uwierzytelniania ACR w klastrze DC/OS
> * Przekazywanie obrazu do usługi Azure Container Registry
> * Uruchamianie obrazu z usługi Azure Container Registry

Do wykonania kroków opisanych w tym samouczku potrzebujesz klastra DC/OS usługi ACS. W razie potrzeby [ten przykładowy skrypt](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pomoże Ci go utworzyć.

Dla tego samouczka wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Wdrażanie usługi Azure Container Registry

W razie potrzeby utwórz rejestr Azure Container Registry za pomocą polecenia [az acr create](/cli/azure/acr#az-acr-create). 

Poniższy przykład tworzy rejestr z losowo wygenerowaną nazwą. Rejestr jest również konfigurowany za pomocą konta administratora przy użyciu argumentu `--admin-enabled`.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

Po utworzeniu rejestru interfejs wiersza polecenia platformy Azure generuje dane wyjściowe podobne do poniższych. Zanotuj wartości `name` i `loginServer` — będą one używane w kolejnych krokach.

```output
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Pobierz poświadczenia rejestru kontenerów przy użyciu polecenia [az acr credential show](/cli/azure/acr/credential). Zastąp wartość `--name` wartością zapisaną w poprzednim kroku. Zapisz hasło — będzie ono potrzebne w kolejnym kroku.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Aby uzyskać więcej informacji na temat usługi Azure Container Registry, zobacz [Wprowadzenie do prywatnych rejestrów kontenerów platformy Docker na platformie Azure](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Zarządzanie uwierzytelnianiem usługi ACR

Konwencjonalnym sposobem ściągania i wypychania obrazu z prywatnego rejestru jest uwierzytelnienie się najpierw przy użyciu rejestru. W tym celu należy użyć polecenia `docker login` dla dowolnego klienta, który wymaga dostępu do rejestru prywatnego. Ponieważ klaster DC/OS może zawierać wiele węzłów, które mają być uwierzytelniane przy użyciu usługi ACR, warto zautomatyzować ten proces w każdym węźle. 

### <a name="create-shared-storage"></a>Tworzenie magazynu udostępnionego

W tym procesie jest używany udział plików platformy Azure, który został zainstalowany w każdym węźle klastra. Jeśli jeszcze nie skonfigurowano magazynu udostępnionego, zobacz [Setup a file share inside a DC/OS cluster (Konfigurowanie udziału plików w klastrze DC/OS)](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Konfigurowanie uwierzytelniania usługi ACR

Najpierw uzyskaj nazwę FQDN wzorca DC/OS i zapisz ją w zmiennej.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Utwórz połączenie SSH przy użyciu wzorca (lub pierwszego wzorca) klastra opartego na rozwiązaniu DC/OS. Zaktualizuj nazwę użytkownika, jeśli podczas tworzenia klastra użyto wartości innej niż domyślna.

```console
ssh azureuser@$FQDN
```

Następnie użyj poniższego polecenia, aby zalogować się do usługi Azure Container Registry. Zastąp element `--username` nazwą rejestru kontenerów, a element `--password` — jednym z podanych haseł. Zastąp ostatni argument *mycontainerregistry.azurecr.io* w tym przykładzie nazwą loginServer rejestru kontenerów. 

To polecenie przechowuje wartości uwierzytelniania lokalnie w ścieżce `~/.docker`.

```console
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Utwórz plik skompresowany, który zawiera wartości uwierzytelniania rejestru kontenerów.

```console
tar czf docker.tar.gz .docker
```

Skopiuj ten plik do magazynu udostępnionego klastra. Ten krok powoduje udostępnienie pliku we wszystkich węzłach klastra DC/OS.

```console
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Przekazywanie obrazu do usługi ACR

Teraz na maszynie deweloperskiej lub w innym systemie z zainstalowaną platformą Docker utwórz obraz i przekaż go do usługi Azure Container Registry.

Utwórz kontener na podstawie obrazu Ubuntu.

```console
docker run ubuntu --name base-image
```

Teraz przechwyć kontener do nowego obrazu. Nazwa obrazu musi zawierać `loginServer` nazwę rejestru kontenerów z formatem. `loginServer/imageName`

```console
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
```

Zaloguj się do usługi Azure Container Registry. Zastąp nazwę nazwą loginServer, argument --username nazwą rejestru kontenerów, a argument --password jednym z podanych haseł.

```console
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Na koniec przekaż obraz do rejestru usługi ACR. W tym przykładzie przekazywany jest obraz o nazwie dcos-demo.

```console
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Uruchamianie obrazu z poziomu usługi ACR

Aby użyć obrazu z rejestru usługi ACR, utwórz nazwy pliku *acrDemo.json* i skopiuj do niego poniższy tekst. Zastąp nazwę obrazu nazwą loginServer rejestru kontenerów i nazwą obrazu, na przykład `loginServer/imageName`. Zanotuj wartość właściwości `uris`. W tej właściwości jest przechowywana lokalizacja pliku uwierzytelniania rejestru kontenerów. W tym przypadku jest to udział plików platformy Azure zainstalowany w każdym węźle klastra DC/OS.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Wdróż aplikację przy użyciu interfejsu wiersza polecenia DC/OC.

```console
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano rozwiązanie DC/OS do korzystania z usługi Azure Container Registry, w tym wykonano następujące zadania:

> [!div class="checklist"]
> * Wdrażanie usługi Azure Container Registry (w razie potrzeby)
> * Konfigurowanie uwierzytelniania ACR w klastrze DC/OS
> * Przekazywanie obrazu do usługi Azure Container Registry
> * Uruchamianie obrazu z usługi Azure Container Registry
