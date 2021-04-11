---
title: Zainstaluj wolumin Azure Files do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin Azure Files, aby zachować stan utrwalania z Azure Container Instances
ms.topic: article
ms.date: 03/24/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 09a4d9922a4f9ba4296fc194d72c621fecb8342d
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968904"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances

Domyślnie usługa Azure Container Instances jest bezstanowa. Jeśli kontener zostanie ponownie uruchomiony, ulegnie awarii lub zostanie zatrzymany, jego stan zostanie utracony. Aby w takim przypadku go zachować, należy zainstalować wolumin z magazynu zewnętrznego. Jak pokazano w tym artykule, Azure Container Instances może zainstalować udział plików platformy Azure utworzony przy użyciu [Azure Files](../storage/files/storage-files-introduction.md). Azure Files oferuje w pełni zarządzane udziały plików hostowane w usłudze Azure Storage, które są dostępne za pośrednictwem protokołu Server Message Block (SMB) w warstwie Standardowa. Korzystanie z udziału plików platformy Azure z Azure Container Instances zapewnia funkcje udostępniania plików podobne do korzystania z udziału plików platformy Azure z maszynami wirtualnymi platformy Azure.

## <a name="limitations"></a>Ograniczenia

* Można instalować tylko udziały Azure Files w kontenerach systemu Linux. Zapoznaj się z informacjami o różnicach w obsłudze funkcji dla grup kontenerów systemu Linux i [Windows.](container-instances-overview.md#linux-and-windows-containers)
* Instalacja woluminu udziału plików platformy Azure wymaga, aby kontener systemu Linux był uruchomiony jako *główny* .
* Instalacje woluminów udziałów plików platformy Azure są ograniczone do obsługi protokołu CIFS.

> [!NOTE]
> Instalowanie udziału Azure Files w wystąpieniu kontenera jest podobne do instalacji programu Docker [bind](https://docs.docker.com/storage/bind-mounts/). W przypadku zainstalowania udziału w katalogu kontenera, w którym znajdują się pliki lub katalogi, instalacja zasłania pliki lub katalogi, co sprawia, że są niedostępne podczas uruchamiania kontenera.
>

> [!IMPORTANT]
> W przypadku wdrażania grup kontenerów w usłudze Azure Virtual Network należy dodać [punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) do konta usługi Azure Storage.

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Przed rozpoczęciem korzystania z udziału plików platformy Azure za pomocą usługi Azure Container Instances należy go utworzyć. Uruchom następujący skrypt, aby utworzyć konto magazynu, które będzie hostować udział plików, i sam udział. Nazwa konta magazynu musi być globalnie unikatowa, więc skrypt dodaje losową wartość do podstawowego ciągu.

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

* **Nazwa konta magazynu** — Jeśli użyto poprzedniego skryptu, nazwa konta magazynu była przechowywana w `$ACI_PERS_STORAGE_ACCOUNT_NAME` zmiennej. Aby wyświetlić nazwę konta, wpisz:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Nazwa udziału** — ta wartość jest już znana (zdefiniowana `acishare` w powyższym skrypcie)

* **Klucz konta magazynu** — tę wartość można znaleźć za pomocą następującego polecenia:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Wdrażanie kontenera i woluminu instalacji — interfejs wiersza polecenia

Aby zainstalować udział plików platformy Azure jako wolumin w kontenerze przy użyciu interfejsu wiersza polecenia platformy Azure, określ udział i punkt instalacji woluminu podczas tworzenia kontenera za pomocą [AZ Container Create][az-container-create]. Jeśli wykonano poprzednie kroki, można zainstalować utworzony wcześniej udział przy użyciu następującego polecenia, aby utworzyć kontener:

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

`--dns-name-label`Wartość musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Zaktualizuj wartość w poprzednim poleceniu, jeśli podczas wykonywania polecenia zostanie wyświetlony komunikat o błędzie **etykiety nazwy DNS** .

## <a name="manage-files-in-mounted-volume"></a>Zarządzanie plikami w zainstalowanym woluminie

Po rozpoczęciu kontenera możesz użyć prostej aplikacji internetowej wdrożonej za pośrednictwem obrazu Microsoft [ACI-hellofiles][aci-hellofiles] , aby utworzyć małe pliki tekstowe w udziale plików platformy Azure w podanej ścieżce instalacji. Uzyskaj w pełni kwalifikowaną nazwę domeny (FQDN) aplikacji sieci Web za pomocą polecenia [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Po zapisaniu tekstu przy użyciu aplikacji można użyć [Azure Portal][portal] lub narzędzia, takiego jak [Eksplorator usługi Microsoft Azure Storage][storage-explorer] , aby pobrać i zbadać plik lub pliki zapisane w udziale plików.

## <a name="deploy-container-and-mount-volume---yaml"></a>Wdróż kontener i wolumin instalacji — YAML

Możesz również wdrożyć grupę kontenerów i zainstalować wolumin w kontenerze za pomocą interfejsu wiersza polecenia platformy Azure i [szablonu YAML](container-instances-multi-container-yaml.md). Wdrażanie przy użyciu szablonu YAML jest preferowaną metodą wdrażania grup kontenerów składających się z wielu kontenerów.

Poniższy szablon YAML definiuje grupę kontenerów z jednym kontenerem utworzonym przy użyciu `aci-hellofiles` obrazu. Kontener instaluje *acishare* udział plików platformy Azure utworzony wcześniej jako wolumin. Gdzie to wskazane, wprowadź nazwę i klucz magazynu dla konta magazynu, które obsługuje udział plików. 

Podobnie jak w przykładzie interfejsu wiersza polecenia, `dnsNameLabel` wartość musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. W razie konieczności zaktualizuj wartość w pliku YAML.

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

Aby wdrożyć z szablonem YAML, Zapisz poprzedni YAML do pliku o nazwie `deploy-aci.yaml` , a następnie wykonaj polecenie [AZ Container Create][az-container-create] z `--file` parametrem:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Wdróż kontener i wolumin instalacji — Menedżer zasobów

Oprócz wdrożenia interfejsu wiersza polecenia i YAML można wdrożyć grupę kontenerów i zainstalować wolumin w kontenerze przy użyciu [szablonu Menedżer zasobów](/azure/templates/microsoft.containerinstance/containergroups)platformy Azure.

Najpierw Wypełnij `volumes` tablicę w sekcji Grupa kontenerów `properties` szablonu. 

Następnie dla każdego kontenera, w którym chcesz zainstalować wolumin, Wypełnij `volumeMounts` tablicę w `properties` sekcji definicji kontenera.

Poniższy szablon Menedżer zasobów definiuje grupę kontenerów z jednym kontenerem utworzonym przy użyciu `aci-hellofiles` obrazu. Kontener instaluje *acishare* udział plików platformy Azure utworzony wcześniej jako wolumin. Gdzie to wskazane, wprowadź nazwę i klucz magazynu dla konta magazynu, które obsługuje udział plików. 

Tak jak w poprzednich przykładach, `dnsNameLabel` wartość musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. W razie konieczności zaktualizuj wartość w szablonie.

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

Aby wdrożyć z szablonem Menedżer zasobów, Zapisz poprzedni kod JSON w pliku o nazwie `deploy-aci.json` , a następnie uruchom polecenie [AZ Deployment Group Create][az-deployment-group-create] z `--template-file` parametrem:

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Instalowanie wielu woluminów

Aby zainstalować wiele woluminów w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), pliku YAML lub innej metody programowej. Aby użyć szablonu lub pliku YAML, podaj szczegóły udziału i zdefiniuj woluminy, wypełniając `volumes` tablicę w `properties` sekcji pliku. 

Jeśli na przykład utworzono dwa Azure Files udziały o nazwie *share1* i *Share2* na koncie magazynu *mojekontomagazynu*, `volumes` Tablica w Menedżer zasobów szablon będzie wyglądać podobnie do następującej:

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

Następnie dla każdego kontenera w grupie kontenerów, w której chcesz zainstalować woluminy, Wypełnij `volumeMounts` tablicę w `properties` sekcji definicji kontenera. Na przykład spowoduje to zainstalowanie dwóch woluminów, *myvolume1* i *myvolume2*, wcześniej zdefiniowanych:

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
* [Zainstaluj wolumin gitRepo w Azure Container Instances](container-instances-volume-gitrepo.md)
* [Zainstaluj wolumin tajny w Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
