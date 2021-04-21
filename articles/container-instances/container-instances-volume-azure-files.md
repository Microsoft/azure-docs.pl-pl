---
title: Zainstaluj Azure Files do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin Azure Files, aby utrwalić stan za pomocą Azure Container Instances
ms.topic: article
ms.date: 03/24/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c541d4faa8728d99fd07396bc056a3e69dc93fe8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763743"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances

Domyślnie usługa Azure Container Instances jest bezstanowa. Jeśli kontener zostanie uruchomiony ponownie, ulega awarii lub zatrzymuje się, cały jego stan zostanie utracony. Aby w takim przypadku go zachować, należy zainstalować wolumin z magazynu zewnętrznego. Jak pokazano w tym artykule, Azure Container Instances zainstalować udział plików platformy Azure utworzony za pomocą [Azure Files](../storage/files/storage-files-introduction.md). Azure Files oferuje w pełni zarządzane udziały plików hostowane w usłudze Azure Storage, które są dostępne za pośrednictwem standardowego w branży protokołu Bloku komunikatów serwera (SMB). Korzystanie z udziału plików platformy Azure Azure Container Instances udostępnia funkcje udostępniania plików podobne do korzystania z udziału plików platformy Azure z maszynami wirtualnymi platformy Azure.

## <a name="limitations"></a>Ograniczenia

* Udziały można zainstalować tylko Azure Files kontenerach systemu Linux. Aby uzyskać więcej informacji na temat różnic w obsługi funkcji dla grup kontenerów systemów Linux i Windows, zapoznaj się z [omówieniem .](container-instances-overview.md#linux-and-windows-containers)
* Aby zainstalować wolumin udziału plików platformy Azure, kontener systemu Linux musi działać jako *główny .*
* Instalacji woluminu udziału plików platformy Azure są ograniczone do obsługi cifs.

> [!NOTE]
> Instalowanie udziału Azure Files w wystąpieniu kontenera jest podobne do instalacji powiązania [platformy](https://docs.docker.com/storage/bind-mounts/)Docker . Jeśli zainstalujemy udział w katalogu kontenera, w którym istnieją pliki lub katalogi, ta instalacji zasłania pliki lub katalogi, przez co są one niedostępne podczas pracy kontenera.
>

> [!IMPORTANT]
> Jeśli wdrażasz grupy kontenerów w usłudze Azure Virtual Network, musisz dodać punkt końcowy usługi [do](../virtual-network/virtual-network-service-endpoints-overview.md) konta usługi Azure Storage.

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Przed rozpoczęciem korzystania z udziału plików platformy Azure za pomocą usługi Azure Container Instances należy go utworzyć. Uruchom następujący skrypt, aby utworzyć konto magazynu do hostowanie udziału plików i samego udziału. Nazwa konta magazynu musi być globalnie unikatowa, więc skrypt dodaje losową wartość do podstawowego ciągu.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Uzyskiwanie poświadczeń magazynu

Aby zainstalować udział plików platformy Azure jako wolumin w usłudze Azure Container Instances, potrzebujesz trzech wartości: nazwy konta magazynu, nazwy udziału i klucza dostępu do magazynu.

* **Nazwa konta magazynu** — jeśli został użyty poprzedni skrypt, nazwa konta magazynu została zapisana w `$ACI_PERS_STORAGE_ACCOUNT_NAME` zmiennej . Aby wyświetlić nazwę konta, wpisz:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Nazwa udziału** — ta wartość jest już znana (zdefiniowana w `acishare` poprzednim skrypcie)

* **Klucz konta magazynu** — tę wartość można znaleźć przy użyciu następującego polecenia:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Wdrażanie kontenera i wolumin instalacji — interfejs wiersza polecenia

Aby zainstalować udział plików platformy Azure jako wolumin w kontenerze przy użyciu interfejsu wiersza polecenia platformy Azure, określ udział i punkt instalacji woluminu podczas tworzenia kontenera za pomocą polecenia [az container create.][az-container-create] Jeśli powyższe kroki zostały opisane, możesz zainstalować utworzony wcześniej udział, używając następującego polecenia, aby utworzyć kontener:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Wartość `--dns-name-label` musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Zaktualizuj wartość w poprzednim poleceniu,  jeśli podczas wykonywania polecenia zostanie wyświetlony komunikat o błędzie etykiety nazwy DNS.

## <a name="manage-files-in-mounted-volume"></a>Zarządzanie plikami w zainstalowanym woluminie

Po utworzeniu kontenera możesz użyć prostej aplikacji internetowej wdrożonej za pośrednictwem obrazu Microsoft [aci-hellofiles,][aci-hellofiles] aby utworzyć małe pliki tekstowe w udziałach plików platformy Azure we wskazanej ścieżce instalacji. Uzyskaj w pełni kwalifikowaną nazwę domeny (FQDN) aplikacji internetowej za pomocą [polecenia az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Po zapisaniu tekstu przy użyciu [][portal] aplikacji możesz użyć narzędzia Azure Portal, takiego jak [Eksplorator usługi Microsoft Azure Storage,][storage-explorer] aby pobrać i sprawdzić plik lub pliki zapisane w udziałach plików.

## <a name="deploy-container-and-mount-volume---yaml"></a>Wdrażanie kontenera i woluminu instalacji — YAML

Możesz również wdrożyć grupę kontenerów i zainstalować wolumin w kontenerze za pomocą interfejsu wiersza polecenia platformy Azure i [szablonu YAML.](container-instances-multi-container-yaml.md) Wdrażanie za pomocą szablonu YAML jest preferowaną metodą podczas wdrażania grup kontenerów składających się z wielu kontenerów.

Poniższy szablon YAML definiuje grupę kontenerów z jednym kontenerem utworzonym za pomocą `aci-hellofiles` obrazu. Kontener zainstaluje udział plików platformy Azure *utworzony wcześniej* jako wolumin. Tam, gdzie jest to wskazane, wprowadź nazwę i klucz magazynu dla konta magazynu, które hostuje udział plików. 

Tak jak w przykładzie interfejsu `dnsNameLabel` wiersza polecenia, wartość musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. W razie potrzeby zaktualizuj wartość w pliku YAML.

```yaml
apiVersion: '2019-12-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Aby wdrożyć za pomocą szablonu YAML, zapisz powyższy plik YAML w pliku o nazwie , a następnie wykonaj polecenie `deploy-aci.yaml` [az container create][az-container-create] z `--file` parametrem :

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Wdrażanie kontenera i woluminu instalacji — Resource Manager

Oprócz wdrożenia interfejsu wiersza polecenia i yaml można wdrożyć grupę [kontenerów](/azure/templates/microsoft.containerinstance/containergroups)i zainstalować wolumin w kontenerze przy użyciu szablonu usługi Azure Resource Manager .

Najpierw wypełnij `volumes` tablicę w sekcji grupy `properties` kontenerów szablonu. 

Następnie dla każdego kontenera, w którym chcesz zainstalować wolumin, wypełnij tablicę `volumeMounts` w `properties` sekcji definicji kontenera.

Poniższy szablon Resource Manager grupy kontenerów z jednym kontenerem utworzonym za pomocą `aci-hellofiles` obrazu. Kontener zainstaluje utworzony wcześniej  udział plików platformy Azure jako wolumin. Tam, gdzie jest to wskazane, wprowadź nazwę i klucz magazynu dla konta magazynu, które hostuje udział plików. 

Tak jak w poprzednich przykładach, wartość musi być unikatowa w regionie `dnsNameLabel` świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. W razie potrzeby zaktualizuj wartość w szablonie.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Aby wdrożyć za pomocą Resource Manager szablonu, zapisz powyższy kod JSON w pliku o nazwie , a następnie wykonaj polecenie `deploy-aci.json` az deployment group [create][az-deployment-group-create] z `--template-file` parametrem :

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Zainstaluj wiele woluminów

Aby zainstalować wiele woluminów w wystąpieniu kontenera, należy wdrożyć przy użyciu szablonu [Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), pliku YAML lub innej metody programowej. Aby użyć szablonu lub pliku YAML, podaj szczegóły udziału i zdefiniuj woluminy, wypełniając tablicę `volumes` `properties` w sekcji pliku. 

Jeśli na przykład utworzono dwa udziały Azure Files o nazwach *share1* i *share2* na koncie magazynu *myStorageAccount,* tablica w szablonie Resource Manager będzie wyglądać podobnie do `volumes` następującej:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Następnie dla każdego kontenera w grupie kontenerów, w której chcesz zainstalować woluminy, wypełnij tablicę w `volumeMounts` `properties` sekcji definicji kontenera. Na przykład to zainstaluje dwa woluminy, *myvolume1* *i myvolume2,* zdefiniowane wcześniej:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zainstalować inne typy woluminów w Azure Container Instances:

* [Zainstaluj wolumin emptyDir w Azure Container Instances](container-instances-volume-emptydir.md)
* [Zainstaluj wolumin gitRepo w usłudze Azure Container Instances](container-instances-volume-gitrepo.md)
* [Zainstaluj wolumin tajny w Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
