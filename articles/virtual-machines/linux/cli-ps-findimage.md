---
title: Wybieranie obrazów maszyn wirtualnych z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure w celu określenia wydawcy, oferty, jednostki SKU i wersji obrazów maszyn wirtualnych z witryny Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 01/25/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: efa0b91c9c0e43104f36017c3b1a1f3167190d63
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562813"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Znajdowanie obrazów maszyn wirtualnych z systemem Linux w witrynie Azure Marketplace przy użyciu interfejsu wiersza polecenia platformy Azure

W tym temacie opisano sposób używania interfejsu wiersza polecenia platformy Azure do znajdowania obrazów maszyn wirtualnych w portalu Azure Marketplace. Te informacje służą do określania obrazu z witryny Marketplace podczas programistycznego tworzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia, szablonów Menedżer zasobów lub innych narzędzi.

Przeglądaj dostępne obrazy i oferty przy użyciu [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/) , [Azure Portal](https://portal.azure.com)lub  [Azure PowerShell](../windows/cli-ps-findimage.md). 

Upewnij się, że zalogowano się na koncie platformy Azure ( `az login` ).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="deploy-from-a-vhd-using-purchase-plan-parameters"></a>Wdrażanie z dysku VHD przy użyciu parametrów planu zakupu

Jeśli masz istniejący wirtualny dysk twardy, który został utworzony za pomocą płatnego obrazu portalu Azure Marketplace, może być konieczne podanie informacji o planie zakupu podczas tworzenia nowej maszyny wirtualnej na podstawie tego wirtualnego dysku twardego. 

Jeśli nadal masz oryginalną maszynę wirtualną lub inną maszynę wirtualną utworzoną przy użyciu tego samego obrazu z witryny Marketplace, możesz uzyskać od niej nazwę planu, wydawcę i informacje o produkcie przy użyciu polecenia [AZ VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view). Ten przykład pobiera maszynę wirtualną o nazwie *myVM* w grupie zasobów zasobu *webresources* , a następnie wyświetla informacje o planie zakupu.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Jeśli nie pobrano informacji o planie przed usunięciem oryginalnej maszyny wirtualnej, można [wysłać żądanie pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support). Będą one potrzebować nazwy maszyny wirtualnej, identyfikatora subskrypcji i sygnatury czasowej operacji usuwania.

Po uzyskaniu informacji o planie można utworzyć nową maszynę wirtualną przy użyciu `--attach-os-disk` parametru, aby określić dysk VHD.

```azurecli-interactive
az vm create \
   --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```

## <a name="deploy-a-new-vm-using-purchase-plan-parameters"></a>Wdrażanie nowej maszyny wirtualnej przy użyciu parametrów planu zakupu

Jeśli masz już informacje o obrazie, możesz je wdrożyć przy użyciu `az vm create` polecenia. W tym przykładzie wdrażamy maszynę wirtualną przy użyciu obrazu RabbitMQ Certified by Bitnami:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

Jeśli zostanie wyświetlony komunikat o zaakceptowaniu warunków obrazu, zobacz sekcję [Akceptowanie warunków](#accept-the-terms) w dalszej części tego artykułu.

## <a name="list-popular-images"></a>Wyświetlanie listy popularnych obrazów

Uruchom polecenie [AZ VM Image list](/cli/azure/vm/image) , bez `--all` opcji, aby wyświetlić listę popularnych obrazów maszyn wirtualnych w portalu Azure Marketplace. Na przykład uruchom następujące polecenie, aby wyświetlić buforowaną listę popularnych obrazów w formacie tabeli:

```azurecli
az vm image list --output table
```

Dane wyjściowe zawierają nazwę URN obrazu (wartość w kolumnie *urn* ). Podczas tworzenia maszyny wirtualnej przy użyciu jednego z popularnych obrazów w portalu Marketplace można alternatywnie określić *UrnAlias*, skróconą postać, taką jak *UbuntuLTS*.

```output
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Znajdowanie określonych obrazów

Aby znaleźć konkretny obraz maszyny wirtualnej w portalu Marketplace, użyj `az vm image list` polecenia z `--all` opcją. Ta wersja polecenia zajmuje trochę czasu i może zwracać długość danych wyjściowych, więc zazwyczaj filtruje listę według `--publisher` lub inny parametr. 

Na przykład następujące polecenie wyświetla wszystkie oferty Debian (należy pamiętać, że bez `--all` przełącznika, przeszukuje tylko lokalną pamięć podręczną popularnych obrazów):

```azurecli
az vm image list --offer Debian --all --output table 

```

Częściowe dane wyjściowe: 

```output
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Zastosuj podobne filtry przy użyciu `--location` `--publisher` opcji, i `--sku` . Można wykonać częściowe dopasowania filtru, takie jak wyszukiwanie, `--offer Deb` Aby znaleźć wszystkie obrazy debian.

Jeśli nie określisz konkretnej lokalizacji z `--location` opcją, zwracane są wartości domyślnej lokalizacji. (Ustaw inną lokalizację domyślną, uruchamiając `az configure --defaults location=<location>` ).

Na przykład następujące polecenie wyświetla listę wszystkich Debian 8 jednostek SKU w lokalizacji Europa Zachodnia:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Częściowe dane wyjściowe:

```output
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Nawigowanie po obrazach
 
Innym sposobem znalezienia obrazu w lokalizacji jest uruchomienie polecenia [AZ VM Image list-Wydawcas](/cli/azure/vm/image), [AZ VM Image list-offers](/cli/azure/vm/image)i [AZ VM Image list-reskus](/cli/azure/vm/image) w sekwencji. Za pomocą tych poleceń można określić następujące wartości:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Następnie dla wybranej jednostki SKU można wybrać wersję do wdrożenia.

Na przykład następujące polecenie wyświetla listę wydawców obrazów w lokalizacji zachodnie stany USA:

```azurecli
az vm image list-publishers --location westus --output table
```

Częściowe dane wyjściowe:

```output
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Użyj tych informacji, aby znaleźć oferty od określonego wydawcy. Na przykład w przypadku wydawcy *kanonicznego* w lokalizacji zachodnie stany USA Znajdź oferty przez uruchomienie `azure vm image list-offers` . Przekaż lokalizację i wydawcę, tak jak w poniższym przykładzie:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Dane wyjściowe:

```output
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Zobaczysz, że w regionie zachodnie stany USA publikuje ofertę *UbuntuServer* na platformie Azure. Ale o jakie wersje SKU chodzi? Aby uzyskać te wartości, uruchom `azure vm image list-skus` i ustaw odnalezioną lokalizację, wydawcę i ofertę:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Dane wyjściowe:

```output
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Na koniec Użyj `az vm image list` polecenia, aby znaleźć określoną wersję jednostki SKU, na przykład *18,04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Częściowe dane wyjściowe:

```output
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Teraz możesz wybrać dokładnie obraz, którego chcesz użyć, zwracając uwagę na wartość URN. Przekaż tę wartość przy użyciu `--image` parametru podczas tworzenia maszyny wirtualnej za pomocą polecenia [AZ VM Create](/cli/azure/vm) . Należy pamiętać, że opcjonalnie można zamienić numer wersji w nazwie URN na "Najnowsza". Ta wersja jest zawsze najnowszą wersją obrazu. 

W przypadku wdrożenia maszyny wirtualnej z szablonem Menedżer zasobów parametry obrazu są ustawiane indywidualnie we `imageReference` właściwościach. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Wyświetl właściwości planu

Aby wyświetlić informacje o planie zakupu obrazu, uruchom polecenie [AZ VM Image show](/cli/azure/image) . Jeśli `plan` Właściwość w danych wyjściowych nie jest `null` , obraz zawiera warunki, które należy zaakceptować przed wdrożeniem programistycznym.

Na przykład obraz kanoniczny Ubuntu Server 18,04 LTS nie ma dodatkowych warunków, ponieważ `plan` informacje są `null` następujące:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Dane wyjściowe:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Uruchamianie podobnego polecenia dla obrazu RabbitMQ Certified by Bitnami wyświetla następujące `plan` Właściwości: `name` , `product` , i `publisher` . (Niektóre obrazy mają również `promotion code` Właściwość). Aby wdrożyć ten obraz, zapoznaj się z następującymi sekcjami, aby zaakceptować warunki i włączyć wdrażanie programistyczne.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Dane wyjściowe:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

## <a name="accept-the-terms"></a>Akceptowanie warunków

Aby wyświetlić i zaakceptować postanowienia licencyjne, użyj polecenia [AZ VM Image Accept-terms](/cli/azure/vm/image?) . Po zaakceptowaniu postanowień można włączyć wdrożenie programistyczne w ramach subskrypcji. Musisz tylko raz zaakceptować warunki dla każdej subskrypcji obrazu. Na przykład:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Dane wyjściowe zawierają `licenseTextLink` do postanowień licencyjnych i wskazują, że wartość `accepted` jest `true` :

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

## <a name="next-steps"></a>Następne kroki
Aby szybko utworzyć maszynę wirtualną przy użyciu informacji o obrazie, zobacz [Tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](tutorial-manage-vm.md).
