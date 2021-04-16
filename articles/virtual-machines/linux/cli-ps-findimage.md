---
title: Znajdowanie i używanie informacji o planie zakupu w witrynie Marketplace przy użyciu interfejsu wiersza polecenia
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure znaleźć adresy URL obrazów i parametry planu zakupu, takie jak wydawca, oferta, SKU i wersja, dla obrazów maszyn wirtualnych z witryny Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: be0535a49b47c45cad49abd1bf720b6347a660b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484204"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Znajdowanie Azure Marketplace obrazów przy użyciu interfejsu wiersza polecenia platformy Azure

W tym temacie opisano sposób używania interfejsu wiersza polecenia platformy Azure do wyszukiwania obrazów maszyn wirtualnych w Azure Marketplace. Te informacje pozwalają określić obraz z witryny Marketplace podczas programowego tworzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia, Resource Manager szablonów lub innych narzędzi.

Możesz również przeglądać dostępne obrazy i oferty przy [użyciu Azure Marketplace](https://azuremarketplace.microsoft.com/) [lub Azure PowerShell.](../windows/cli-ps-findimage.md) 

## <a name="terminology"></a>Terminologia

Obraz z witryny Marketplace na platformie Azure ma następujące atrybuty:

* **Wydawca:** organizacja, która utworzyła obraz. Przykłady: Canonical, MicrosoftWindowsServer
* **Oferta:** nazwa grupy powiązanych obrazów utworzonych przez wydawcę. Przykłady: UbuntuServer, WindowsServer
* **SKU:** wystąpienie oferty, takie jak główna wersja dystrybucji. Przykłady: 18.04-LTS, 2019-Datacenter
* **Wersja:** numer wersji sku obrazu. 

Te wartości mogą być przekazywane indywidualnie lub jako adres *URL obrazu,* łącząc wartości rozdzielone dwukropkiem (:). Na przykład: *Wydawca:**Offer*:*Sku*:*Version*. Numer wersji w adresie URL można zastąpić , aby `latest` użyć najnowszej wersji obrazu. 

Jeśli wydawca obrazu udostępnia dodatkowe postanowienia licencyjne i zakupu, musisz je zaakceptować, aby można było użyć obrazu.  Aby uzyskać więcej informacji, zobacz [Sprawdzanie informacji o planie zakupu](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Lista popularnych obrazów

Uruchom polecenie [az vm image list](/cli/azure/vm/image) bez opcji , aby wyświetlić listę popularnych obrazów maszyn wirtualnych w `--all` Azure Marketplace. Na przykład uruchom następujące polecenie, aby wyświetlić buforowane listy popularnych obrazów w formacie tabeli:

```azurecli
az vm image list --output table
```

Dane wyjściowe zawierają adres URL obrazu. Możesz również użyć *urnAlias,* czyli skróconej wersji utworzonej dla popularnych obrazów, takich *jak UbuntuLTS.*

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Znajdowanie określonych obrazów

Aby znaleźć określony obraz maszyny wirtualnej w witrynie Marketplace, użyj `az vm image list` polecenia z `--all` opcją . Ukończenie tej wersji polecenia zajmuje trochę czasu i może zwrócić długie dane wyjściowe, więc zazwyczaj filtruje się listę według `--publisher` lub innego parametru. 

Na przykład następujące polecenie wyświetla wszystkie oferty Debian (pamiętaj, że bez przełącznika przeszukuje on tylko lokalną pamięć podręczną `--all` wspólnych obrazów):

```azurecli
az vm image list --offer Debian --all --output table 
```

Częściowe dane wyjściowe: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Przyjrzyj się wszystkim dostępnym obrazom
 
Innym sposobem znalezienia obrazu w lokalizacji jest uruchomienie kolejno poleceń [az vm image list-publishers,](/cli/azure/vm/image) [az vm image list-offers](/cli/azure/vm/image)i [az vm image list-skus.](/cli/azure/vm/image) Za pomocą tych poleceń można określić następujące wartości:

1. Lista wydawców obrazów dla lokalizacji. W tym przykładzie patrzymy na region *Zachodnie stany* USA.
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Dla danego wydawcy wyświetl listę ofert. W tym przykładzie dodamy *canonical* jako wydawcę.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Dla danej oferty wyświetl listę wersji SKU. W tym przykładzie dodamy *ubuntuServer* jako ofertę.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Dla danego wydawcy, oferty i SKU wyświetl wszystkie wersje obrazu. W tym przykładzie dodamy *18.04-LTS* jako sku.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Przekaż tę wartość kolumny URN z parametrem podczas tworzenia maszyny wirtualnej za `--image` pomocą polecenia az vm [create.](/cli/azure/vm) Możesz również zastąpić numer wersji w adresie URL "latest", aby po prostu użyć najnowszej wersji obrazu. 

Jeśli wdrażasz maszynę wirtualną przy Resource Manager szablonu, parametry obrazu są ustawiane indywidualnie we `imageReference` właściwościach. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Sprawdzanie informacji o planie zakupu

Niektóre obrazy maszyn wirtualnych w Azure Marketplace mają dodatkowe postanowienia licencyjne i zakupu, które należy zaakceptować przed ich programowym wdrożeniem.  

Aby wdrożyć maszynę wirtualną z takiego obrazu, musisz zaakceptować warunki obrazu podczas pierwszego użycia, raz na subskrypcję. Należy również określić parametry planu *zakupu,* aby wdrożyć maszynę wirtualną z tego obrazu

Aby wyświetlić informacje o planie zakupu obrazu, uruchom [polecenie az vm image show](/cli/azure/image) z identyfikatorem URN obrazu. Jeśli właściwość w danych wyjściowych nie ma wartości , obraz zawiera `plan` `null` warunki, które należy zaakceptować przed wdrożeniem programowym.

Na przykład obraz Canonical Ubuntu Server 18.04 LTS nie ma dodatkowych terminów, ponieważ `plan` informacje to `null` :

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

Uruchomienie podobnego polecenia dla obrazu RabbitMQ Certified by Bitnami zawiera następujące `plan` właściwości: `name` , i `product` `publisher` . (Niektóre obrazy mają również `promotion code` właściwość ). 

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

Aby wdrożyć ten obraz, musisz zaakceptować warunki i podać parametry planu zakupu podczas wdrażania maszyny wirtualnej przy użyciu tego obrazu.

## <a name="accept-the-terms"></a>Akceptowanie warunków

Aby wyświetlić i zaakceptować postanowienia licencyjne, użyj [polecenia az vm image accept-terms.](/cli/azure/vm/image/terms) Po zaakceptowaniu warunków należy włączyć wdrożenie programowe w ramach subskrypcji. Musisz zaakceptować warunki dla obrazu tylko raz dla subskrypcji. Na przykład:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Dane wyjściowe zawierają `licenseTextLink` wartość w postanowieniach licencyjnych i wskazują, że wartość to `accepted` `true` :

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

Aby zaakceptować warunki, wpisz:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Wdrażanie nowej maszyny wirtualnej przy użyciu parametrów obrazu

Informacje o obrazie można wdrożyć przy użyciu `az vm create` polecenia . 

Aby wdrożyć obraz, który nie zawiera informacji o planie, taki jak najnowszy obraz systemu Ubuntu Server 18.04 z aplikacji Canonical, przekaż adres URL dla programu `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


W przypadku obrazu z parametrami planu zakupu, na przykład obrazem RabbitMQ Certified by Bitnami, należy przekazać adres URL dla parametru , a także podać `--image` parametry planu zakupu:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Jeśli zostanie wyświetlony komunikat o zaakceptowaniu warunków obrazu, zapoznaj się z sekcją [Zaakceptuj warunki](#accept-the-terms). Upewnij się, że dane wyjściowe metody zwraca wartość pokazującą, że warunki `az vm image accept-terms` `"accepted": true,` obrazu zostały zaakceptowane.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Używanie istniejącego wirtualnego dysku twardego z informacjami o planie zakupu

Jeśli masz istniejący wirtualny dysk twardy z maszyny wirtualnej utworzonej przy użyciu płatnego obrazu Azure Marketplace, może być konieczne dostarczenie informacji o planie zakupu podczas tworzenia nowej maszyny wirtualnej na podstawie tego dysku VHD. 

Jeśli nadal masz oryginalną maszynę wirtualną lub inną maszynę wirtualną utworzoną przy użyciu tego samego obrazu z witryny Marketplace, możesz uzyskać z niego nazwę planu, wydawcę i informacje o produkcie za pomocą [narzędzia az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view). Ten przykład pobiera maszynę wirtualną *o nazwie myVM* w grupie zasobów *myResourceGroup,* a następnie wyświetla informacje o planie zakupu.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Jeśli informacje o planie nie zostały podane przed usunięciem oryginalnej maszyny wirtualnej, możesz złożyć [wniosek o pomoc techniczną.](https://ms.portal.azure.com/#create/Microsoft.Support) Będą potrzebować nazwy maszyny wirtualnej, identyfikatora subskrypcji i sygnatury czasowej operacji usuwania.

Po utworzeniu informacji o planie możesz utworzyć nową maszynę wirtualną przy użyciu `--attach-os-disk` parametru , aby określić wirtualny dysk twardy.

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


## <a name="next-steps"></a>Następne kroki
Aby szybko utworzyć maszynę wirtualną przy użyciu informacji o obrazie, zobacz [Create and Manage Linux VMs with the Azure CLI](tutorial-manage-vm.md)(Tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure).
