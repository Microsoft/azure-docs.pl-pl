---
title: Znajdowanie i używanie obrazów i planów w witrynie Azure Marketplace
description: Użyj Azure PowerShell, aby znaleźć i użyć wydawcy, oferty, jednostki SKU, wersji i planu informacji dla obrazów maszyn wirtualnych w portalu Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906271"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Znajdowanie obrazów maszyn wirtualnych w portalu Azure Marketplace i korzystanie z nich Azure PowerShell     

W tym artykule opisano, jak używać Azure PowerShell do znajdowania obrazów maszyn wirtualnych w portalu Azure Marketplace. Następnie można określić obraz portalu Marketplace i informacje o planie podczas tworzenia maszyny wirtualnej.

Dostępne obrazy i oferty można także przeglądać za pomocą [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/) , [Azure Portal](https://portal.azure.com)lub [interfejsu wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md). 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>Tworzenie maszyny wirtualnej na podstawie wirtualnego dysku twardego z informacjami o planie

Jeśli masz istniejący wirtualny dysk twardy, który został utworzony przy użyciu obrazu portalu Azure Marketplace, może być konieczne podanie informacji o planie zakupu podczas tworzenia nowej maszyny wirtualnej na podstawie tego wirtualnego dysku twardego.

Jeśli nadal masz oryginalną maszynę wirtualną lub inną maszynę wirtualną utworzoną z tego samego obrazu, możesz uzyskać od niej nazwę planu, wydawcę i informacje o produkcie przy użyciu polecenia Get-AzVM. Ten przykład pobiera maszynę wirtualną o nazwie *myVM* w grupie zasobów zasobu *webresources* , a następnie wyświetla informacje o planie zakupu.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Jeśli nie pobrano informacji o planie przed usunięciem oryginalnej maszyny wirtualnej, można [wysłać żądanie pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support). Będą one potrzebować nazwy maszyny wirtualnej, identyfikatora subskrypcji i sygnatury czasowej operacji usuwania.

Aby utworzyć maszynę wirtualną przy użyciu dysku VHD, zapoznaj się z tym artykułem, aby [utworzyć maszynę wirtualną z wyspecjalizowanego wirtualnego dysku twardego](create-vm-specialized.md) i dodać ją do konfiguracji maszyny wirtualnej przy użyciu polecenia [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) podobnego do poniższego:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Tworzenie nowej maszyny wirtualnej na podstawie obrazu z portalu Marketplace

Jeśli masz już informacje o obrazie, którego chcesz użyć, możesz przekazać te informacje do polecenia cmdlet [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) w celu dodania informacji o obrazie do konfiguracji maszyny wirtualnej. Zapoznaj się z kolejnymi sekcjami dotyczącymi wyszukiwania i wyświetlania obrazów dostępnych w portalu Marketplace.

Niektóre płatne obrazy wymagają również dostarczenia informacji o planie zakupu przy użyciu polecenia [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Następnie można przekazać konfigurację maszyny wirtualnej wraz z innymi obiektami konfiguracji do `New-AzVM` polecenia cmdlet. Aby zapoznać się z szczegółowym przykładem użycia konfiguracji maszyny wirtualnej za pomocą programu PowerShell, zobacz ten [skrypt](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Jeśli zostanie wyświetlony komunikat o zaakceptowaniu warunków obrazu, zobacz sekcję [Akceptowanie warunków](#accept-the-terms) w dalszej części tego artykułu.

## <a name="list-images"></a>Wyświetlanie obrazów

Jednym ze sposobów znalezienia obrazu w lokalizacji jest uruchomienie poleceń cmdlet [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)i [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) w kolejności:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Następnie dla wybranej jednostki SKU Uruchom polecenie [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) , aby wyświetlić listę wersji do wdrożenia.

1. Wyświetl listę wydawców:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Wypełnij wybraną nazwę wydawcy i Wyświetl listę ofert:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Wypełnij wybraną nazwę oferty i Wyświetl listę jednostek SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Wypełnij wybraną nazwę jednostki SKU i Pobierz wersję obrazu:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Na podstawie danych wyjściowych `Get-AzVMImage` polecenia można wybrać obraz wersji do wdrożenia nowej maszyny wirtualnej.

Poniższy przykład przedstawia pełną sekwencję poleceń i ich dane wyjściowe:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Częściowe dane wyjściowe:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Dla wydawcy *MicrosoftWindowsServer* :

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Dane wyjściowe:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

W przypadku oferty *WindowsServer* :

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Częściowe dane wyjściowe:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Następnie dla jednostki SKU *2019-Datacenter* :

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Teraz można połączyć wybranego wydawcy, oferty, jednostki SKU i wersji na nazwę URN (wartości rozdzielone:). Przekaż tę nazwę URN z `--image` parametrem podczas tworzenia maszyny wirtualnej za pomocą polecenia cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm) . Opcjonalnie możesz zamienić numer wersji w nazwie URN na "Najnowsza", aby uzyskać najnowszą wersję obrazu.

W przypadku wdrożenia maszyny wirtualnej z szablonem Menedżer zasobów, parametry obrazu są ustawiane indywidualnie we `imageReference` właściwościach. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Wyświetl właściwości planu

Aby wyświetlić informacje o planie zakupu obrazu, uruchom `Get-AzVMImage` polecenie cmdlet. Jeśli `PurchasePlan` Właściwość w danych wyjściowych nie jest `null` , obraz zawiera warunki, które należy zaakceptować przed wdrożeniem programistycznym.  

Na przykład obraz *systemu Windows Server 2016 Datacenter* nie ma dodatkowych warunków, więc dostępne `PurchasePlan` są następujące informacje `null` :

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Dane wyjściowe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

W poniższym przykładzie pokazano podobne polecenie dla obrazu *Data Science Virtual Machine-Windows 2016* , który ma następujące `PurchasePlan` Właściwości: `name` , `product` , i `publisher` . Niektóre obrazy mają również `promotion code` Właściwość. Aby wdrożyć ten obraz, zapoznaj się z następującymi sekcjami, aby zaakceptować warunki i włączyć wdrażanie programistyczne.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Dane wyjściowe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Akceptowanie warunków

Aby wyświetlić postanowienia licencyjne, użyj polecenia cmdlet [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) i przekaż parametry planu zakupu. Dane wyjściowe zawierają link do warunków dla obrazu z witryny Marketplace i pokazują, czy zostały wcześniej zaakceptowane warunki. Upewnij się, że w wartości parametrów są używane wszystkie małe litery.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Dane wyjściowe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Użyj polecenia cmdlet [Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) , aby zaakceptować lub odrzucić warunki. Musisz tylko raz zaakceptować warunki dla każdej subskrypcji obrazu. Upewnij się, że w wartości parametrów są używane wszystkie małe litery. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Dane wyjściowe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>Następne kroki

Aby szybko utworzyć maszynę wirtualną za pomocą `New-AzVM` polecenia cmdlet przy użyciu podstawowych informacji o obrazie, zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](quick-create-powershell.md).

Aby uzyskać więcej informacji na temat tworzenia obrazów niestandardowych w galerii obrazów udostępnionych przy użyciu obrazów portalu Azure Marketplace, zobacz [Informacje o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](../marketplace-images.md).
