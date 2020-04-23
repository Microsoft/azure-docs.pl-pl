---
title: Wybieranie obrazów maszyn wirtualnych systemu Windows na platformie Azure
description: Użyj programu Azure PowerShell, aby określić wydawcę, ofertę, jednostkę SKU i wersję dla obrazów maszyn wirtualnych w portalu Marketplace.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 46a2badbbe957f6a8a6af7f5a40633ea24cadcd4
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083369"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Znajdowanie obrazów maszyn wirtualnych z systemem Windows w witrynie Azure Marketplace przy użyciu programu Azure PowerShell

W tym artykule opisano sposób znajdowania obrazów maszyn wirtualnych w portalu Azure Marketplace za pomocą programu Azure PowerShell. Następnie można określić obraz portalu Marketplace podczas tworzenia maszyny Wirtualnej programowo za pomocą programu PowerShell, szablonów Menedżera zasobów lub innych narzędzi.

Można również przeglądać dostępne obrazy i oferty za pomocą sklepu [Azure Marketplace,](https://azuremarketplace.microsoft.com/) [witryny azure portal](https://portal.azure.com)lub interfejsu [wiersza polecenia platformy Azure.](../linux/cli-ps-findimage.md) 

 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela często używanych obrazów systemu Windows

W tej tabeli przedstawiono podzbiór dostępnych skusów dla wskazanych wydawców i ofert.

| Wydawca | Oferta | SKU |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019-Centrum danych |
| MicrosoftWindowsServer |WindowsServer |2019-Centrum danych-Core |
| MicrosoftWindowsServer |WindowsServer |2019-Centrum danych z kontenerami |
| MicrosoftWindowsServer |WindowsServer |Centrum danych 2016 |
| MicrosoftWindowsServer |WindowsServer |2016-Centrum danych-Serwer-Rdzeń |
| MicrosoftWindowsServer |WindowsServer |2016-Centrum danych z kontenerami |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftSharePoint |MicrosoftSharePointServer |sp2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| Serwer MicrosoftRServer |Serwer RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Poruszanie się po obrazach

Jednym ze sposobów znalezienia obrazu w lokalizacji jest uruchomienie poleceń cmdlet [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer)i [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) w kolejności:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Następnie dla wybranej jednostki SKU uruchom [program Get-AzVMImage,](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) aby wyświetlić listę wersji do wdrożenia.

1. Lista wydawców:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Wpisz wybraną nazwę wydawcy i wylicz oferty:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Wypełnij wybraną nazwę oferty i wymień jednostki SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Wpisz wybraną nazwę jednostki SKU i pobierz wersję obrazu:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Z danych wyjściowych `Get-AzVMImage` polecenia można wybrać obraz wersji, aby wdrożyć nową maszynę wirtualną.

W poniższym przykładzie przedstawiono pełną sekwencję poleceń i ich dane wyjściowe:

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

Dla wydawcy *programu MicrosoftWindowsServer:*

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

W przypadku oferty *WindowsServer:*

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

Następnie dla jednostki SKU *2019-Datacenter:*

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Teraz możesz połączyć wybranego wydawcę, ofertę, jednostkę SKU i wersję w urn (wartości oddzielone :). Przekaż tę urn `--image` z parametrem podczas tworzenia maszyny Wirtualnej z poleceniem cmdlet [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Opcjonalnie można zastąpić numer wersji w URN "najnowsze", aby uzyskać najnowszą wersję obrazu.

Jeśli wdrożysz maszynę wirtualną z szablonem Menedżera zasobów, parametry obrazu `imageReference` zostanie ustawione indywidualnie we właściwościach. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Wyświetlanie właściwości planu

Aby wyświetlić informacje o planie zakupu `Get-AzVMImage` obrazu, uruchom polecenie cmdlet. Jeśli `PurchasePlan` właściwość w danych `null`wyjściowych nie jest , obraz ma warunki, które należy zaakceptować przed wdrożeniem programowym.  

Na przykład obraz *centrum danych systemu Windows Server 2016* nie `PurchasePlan` ma `null`dodatkowych warunków, więc informacje są następujące:

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

W poniższym przykładzie pokazano podobne polecenie dla obrazu *maszyna wirtualna do nauki o danych — Windows 2016,* które `PurchasePlan` ma następujące właściwości: `name`, `product`, i `publisher`. Niektóre obrazy mają `promotion code` również właściwość. Aby wdrożyć ten obraz, zobacz następujące sekcje, aby zaakceptować warunki i włączyć wdrażanie programowe.

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

Aby wyświetlić postanowienia licencyjne, użyj polecenia cmdlet [Get-AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) i przekaż parametry planu zakupu. Dane wyjściowe zawierają łącze do warunków obrazu w portalu Marketplace i pokazują, czy wcześniej zaakceptowano warunki. Pamiętaj, aby używać wszystkich małych liter w wartościach parametrów.

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

Użyj polecenia cmdlet [Set-AzMarketplaceterms,](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) aby zaakceptować lub odrzucić warunki. Aby obraz był akceptowany tylko raz na subskrypcję. Pamiętaj, aby używać wszystkich małych liter w wartościach parametrów. 

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

### <a name="deploy-using-purchase-plan-parameters"></a>Wdrażanie przy użyciu parametrów planu zakupu

Po zaakceptowaniu warunków dla obrazu, można wdrożyć maszynę wirtualną w tej subskrypcji. Jak pokazano w poniższym urywek, użyj polecenia cmdlet [Set-AzVMPlan,](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) aby ustawić informacje planu portalu Marketplace dla obiektu maszyny Wirtualnej. Aby uzyskać pełny skrypt do tworzenia ustawień sieciowych dla maszyny Wirtualnej i ukończenia wdrożenia, zobacz [przykłady skryptów programu PowerShell](powershell-samples.md).

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Następnie przekażesz konfigurację maszyny Wirtualnej wraz `New-AzVM` z obiektami konfiguracji sieci do polecenia cmdlet.

## <a name="next-steps"></a>Następne kroki

Aby szybko utworzyć maszynę `New-AzVM` wirtualną za pomocą polecenia cmdlet przy użyciu podstawowych informacji o obrazie, zobacz [Tworzenie maszyny wirtualnej systemu Windows za pomocą programu PowerShell](quick-create-powershell.md).


Zobacz przykład skryptu programu PowerShell, aby [utworzyć w pełni skonfigurowaną maszynę wirtualną](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


