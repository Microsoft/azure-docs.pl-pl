---
title: Znajdowanie i używanie informacji planu zakupu w witrynie Marketplace przy użyciu interfejsu wiersza polecenia
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure, aby znaleźć parametry urny i plan zakupu obrazu, takie jak Wydawca, oferta, jednostka SKU i wersja, w przypadku obrazów maszyn wirtualnych w portalu Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 70cb4cc54c6f9a376d3bd38dc8bb6cd3a059a20c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022851"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Znajdowanie informacji o obrazach portalu Azure Marketplace przy użyciu interfejsu wiersza polecenia platformy Azure

W tym temacie opisano sposób używania interfejsu wiersza polecenia platformy Azure do znajdowania obrazów maszyn wirtualnych w portalu Azure Marketplace. Te informacje służą do określania obrazu z witryny Marketplace podczas programistycznego tworzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia, szablonów Menedżer zasobów lub innych narzędzi.

Możesz także przeglądać dostępne obrazy i oferty przy użyciu [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/) lub  [Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologia

Obraz portalu Marketplace na platformie Azure ma następujące atrybuty:

* **Wydawca**: organizacja, która utworzyła obraz. Przykłady: Canonical, MicrosoftWindowsServer
* **Oferta**: Nazwa grupy powiązanych obrazów utworzonych przez wydawcę. Przykłady: UbuntuServer, WindowsServer
* **Jednostka SKU**: wystąpienie oferty, takie jak główna wersja dystrybucji. Przykłady: 18,04-LTS, 2019-Datacenter
* **Wersja**: numer wersji jednostki SKU obrazu. 

Te wartości mogą być przesyłane pojedynczo lub jako *Nazwa urn* obrazu, łącząc wartości rozdzielone średnikami (:). Na przykład: *Publisher*:*Offer*:*SKU*:*Version*. Możesz zastąpić numer wersji w nazwie URN za pomocą, `latest` Aby użyć najnowszej wersji obrazu. 

Jeśli Wydawca obrazu udostępni dodatkowe warunki licencji i zakupu, należy zaakceptować te informacje przed użyciem obrazu.  Aby uzyskać więcej informacji, zobacz [Sprawdzanie informacji o planie zakupu](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Wyświetlanie listy popularnych obrazów

Uruchom polecenie [AZ VM Image list](/cli/azure/vm/image) , bez `--all` opcji, aby wyświetlić listę popularnych obrazów maszyn wirtualnych w portalu Azure Marketplace. Na przykład uruchom następujące polecenie, aby wyświetlić buforowaną listę popularnych obrazów w formacie tabeli:

```azurecli
az vm image list --output table
```

Dane wyjściowe zawierają nazwę URN obrazu. Możesz również użyć *UrnAlias* , który jest skróconą wersją utworzoną dla popularnych obrazów, takich jak *UbuntuLTS*.

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

Aby znaleźć konkretny obraz maszyny wirtualnej w portalu Marketplace, użyj `az vm image list` polecenia z `--all` opcją. Ta wersja polecenia zajmuje trochę czasu i może zwracać długość danych wyjściowych, więc zazwyczaj filtruje listę według `--publisher` lub inny parametr. 

Na przykład następujące polecenie wyświetla wszystkie oferty Debian (należy pamiętać, że bez `--all` przełącznika, przeszukuje tylko lokalną pamięć podręczną popularnych obrazów):

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


## <a name="look-at-all-available-images"></a>Sprawdź wszystkie dostępne obrazy
 
Innym sposobem znalezienia obrazu w lokalizacji jest uruchomienie polecenia [AZ VM Image list-Wydawcas](/cli/azure/vm/image), [AZ VM Image list-offers](/cli/azure/vm/image)i [AZ VM Image list-reskus](/cli/azure/vm/image) w sekwencji. Za pomocą tych poleceń można określić następujące wartości:

1. Wyświetl listę wydawców obrazów dla lokalizacji. W tym przykładzie przeglądamy region *zachodnie stany USA* .
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Dla danego wydawcy wyświetl listę ofert. W tym przykładzie dodamy *kanoniczny* jako wydawcę.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Dla danej oferty wyświetl listę wersji SKU. W tym przykładzie dodamy *UbuntuServer* jako ofertę.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Dla danego wydawcy, oferty i jednostki SKU Pokaż wszystkie wersje obrazu. W tym przykładzie dodamy *18,04-LTS* jako jednostkę SKU.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Przekaż tę wartość kolumny URN z `--image` parametrem podczas tworzenia maszyny wirtualnej za pomocą polecenia [AZ VM Create](/cli/azure/vm) . Numer wersji w nazwie URN można także zamienić na "Najnowsza", aby po prostu użyć najnowszej wersji obrazu. 

W przypadku wdrożenia maszyny wirtualnej z szablonem Menedżer zasobów parametry obrazu są ustawiane indywidualnie we `imageReference` właściwościach. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Sprawdź informacje o planie zakupu

Niektóre obrazy maszyn wirtualnych w portalu Azure Marketplace mają dodatkowe warunki licencji i zakupu, które należy zaakceptować, zanim będzie można programowo wdrożyć je.  

Aby wdrożyć maszynę wirtualną na podstawie takiego obrazu, należy zaakceptować warunki obrazu przy pierwszym użyciu go raz na subskrypcję. Należy również określić parametry *planu zakupu* , aby wdrożyć maszynę wirtualną na podstawie tego obrazu

Aby wyświetlić informacje o planie zakupu obrazu, uruchom polecenie [AZ VM Image show](/cli/azure/image) z identyfikatorem urn obrazu. Jeśli `plan` Właściwość w danych wyjściowych nie jest `null` , obraz zawiera warunki, które należy zaakceptować przed wdrożeniem programistycznym.

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

Uruchamianie podobnego polecenia dla obrazu RabbitMQ Certified by Bitnami wyświetla następujące `plan` Właściwości: `name` , `product` , i `publisher` . (Niektóre obrazy mają również `promotion code` Właściwość). 

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

Aby wyświetlić i zaakceptować postanowienia licencyjne, użyj polecenia [AZ VM Image Accept-terms](/cli/azure/vm/image/terms) . Po zaakceptowaniu postanowień można włączyć wdrożenie programistyczne w ramach subskrypcji. Musisz tylko raz zaakceptować warunki dla każdej subskrypcji obrazu. Na przykład:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
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

Aby zaakceptować warunki, wpisz:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Wdróż nową maszynę wirtualną przy użyciu parametrów obrazu

Informacje o obrazie można wdrożyć za pomocą `az vm create` polecenia. 

Aby wdrożyć obraz, który nie zawiera informacji o planie, takich jak obraz najnowszej Ubuntu Server 18,04 z kanonicznej, należy przekazać nazwę URN dla `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


W przypadku obrazu z parametrami planu zakupów, takich jak obraz RabbitMQ certyfikowany przez Bitnami, należy przekazać nazwę URN dla `--image` i podać parametry planu zakupu:

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

Jeśli zostanie wyświetlony komunikat dotyczący zaakceptowania warunków obrazu, sekcja Przegląd [Zaakceptuj warunki](#accept-the-terms). Upewnij się, że dane wyjściowe `az vm image accept-terms` zwraca wartość `"accepted": true,` wskazującą, że zaakceptowano warunki obrazu.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Korzystanie z istniejącego wirtualnego dysku twardego z informacjami o planie zakupu

Jeśli masz istniejący wirtualny dysk twardy z maszyny wirtualnej, która została utworzona przy użyciu płatnego obrazu portalu Azure Marketplace, może być konieczne podanie informacji o planie zakupu podczas tworzenia nowej maszyny wirtualnej na podstawie tego wirtualnego dysku twardego. 

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


## <a name="next-steps"></a>Następne kroki
Aby szybko utworzyć maszynę wirtualną przy użyciu informacji o obrazie, zobacz [Tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](tutorial-manage-vm.md).
