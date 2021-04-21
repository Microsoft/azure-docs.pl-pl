---
title: Wersja zapoznawcza — tworzenie wersji obrazu zaszyfrowanej przy użyciu własnych kluczy
description: Utwórz wersję obrazu w galerii obrazów udostępnionych przy użyciu kluczy szyfrowania zarządzanych przez klienta.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 601b8236ca413dd510585bdfffddc3e892caa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759673"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Wersja zapoznawcza: szyfrowanie obrazów przy użyciu kluczy zarządzanych przez klienta

Obrazy w galerii obrazów udostępnionych są przechowywane jako migawki, dzięki czemu są automatycznie szyfrowane za pomocą szyfrowania po stronie serwera. Szyfrowanie po stronie serwera używa 256-bitowego szyfrowania [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych dostępnych szyfrów blokowych. Szyfrowanie po stronie serwera jest również zgodne ze standardem FIPS 140-2. Aby uzyskać więcej informacji na temat modułów kryptograficznych bazowych na dyskach zarządzanych platformy Azure, zobacz [Interfejs API kryptografii: następna generacja.](/windows/desktop/seccng/cng-portal)

Do szyfrowania obrazów możesz polegać na kluczach zarządzanych przez platformę lub używać własnych kluczy. Obu tych metod można również używać razem w celu podwójnego szyfrowania. Jeśli zdecydujesz się na zarządzanie szyfrowaniem przy  użyciu własnych kluczy, możesz określić klucz zarządzany przez klienta, który będzie używany do szyfrowania i odszyfrowywania wszystkich dysków w obrazach. 

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta używa Azure Key Vault. Możesz zaimportować [klucze RSA do](../key-vault/keys/hsm-protected-keys.md) magazynu kluczy lub wygenerować nowe klucze RSA w Azure Key Vault.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł wymaga, aby szyfrowanie dysków było już ustawione w każdym regionie, w którym chcesz replikować obraz:

- Aby użyć tylko klucza zarządzanego przez klienta, zobacz artykuły dotyczące włączania kluczy zarządzanych przez klienta przy użyciu szyfrowania po stronie serwera przy [użyciu](./disks-enable-customer-managed-keys-portal.md) Azure Portal programu [PowerShell.](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation)

- Aby użyć zarówno kluczy zarządzanych przez platformę, jak i zarządzanych przez klienta (w celu podwójnego szyfrowania), zobacz artykuły dotyczące włączania podwójnego szyfrowania danych spoczynku przy użyciu interfejsu [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) lub [programu PowerShell.](./windows/disks-enable-double-encryption-at-rest-powershell.md)

   > [!IMPORTANT]
   > Musisz użyć tego linku, [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) aby uzyskać dostęp do Azure Portal. Podwójne szyfrowanie danych w spoczynku nie jest obecnie widoczne w publicznym Azure Portal chyba że użyjemy tego linku.

## <a name="limitations"></a>Ograniczenia

W przypadku używania kluczy zarządzanych przez klienta do szyfrowania obrazów w galerii obrazów udostępnionych obowiązują następujące ograniczenia:   

- Zestawy kluczy szyfrowania muszą znajdować się w tej samej subskrypcji co obraz.

- Zestawy kluczy szyfrowania są zasobami regionalnymi, dlatego każdy region wymaga innego zestawu kluczy szyfrowania.

- Nie można kopiować ani udostępniać obrazów, które używają kluczy zarządzanych przez klienta. 

- Po zaszyfrowaniu dysku lub obrazu przy użyciu własnych kluczy nie można wrócić do szyfrowania tych dysków lub obrazów przy użyciu kluczy zarządzanych przez platformę.


> [!IMPORTANT]
> Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie zalecamy jej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

W przypadku publicznej wersji zapoznawczej należy najpierw zarejestrować funkcję:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Ukończenie rejestracji może potrwać kilka minut. Użyj `Get-AzProviderFeature` , aby sprawdzić stan rejestracji funkcji:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Gdy `RegistrationState` funkcja zwróci wartość , możesz przejść do `Registered` następnego kroku.

Sprawdź rejestrację dostawcy. Upewnij się, że zwraca wartość `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Jeśli nie zwróci , użyj `Registered` następującego kodu, aby zarejestrować dostawców:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Aby określić zestaw szyfrowania dysków dla wersji obrazu, użyj [parametru New-AzGalleryImageDefinition:](/powershell/module/az.compute/new-azgalleryimageversion) `-TargetRegion` 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Maszynę wirtualną można utworzyć z galerii obrazów udostępnionych i zaszyfrować dyski przy użyciu kluczy zarządzanych przez klienta. Składnia jest taka sama jak tworzenie [uogólnionych lub](vm-generalized-image-version-powershell.md) wyspecjalizowanych maszyn [wirtualnych](vm-specialized-image-version-powershell.md) na obrazie. Użyj rozszerzonego zestawu parametrów i dodaj `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` parametr do konfiguracji maszyny wirtualnej.

W przypadku dysków danych dodaj `-DiskEncryptionSetId $setID` parametr w przypadku używania narzędzia [Add-AzVMDataDisk.](/powershell/module/az.compute/add-azvmdatadisk)


## <a name="cli"></a>Interfejs wiersza polecenia 

W przypadku publicznej wersji zapoznawczej musisz najpierw zarejestrować się, aby uzyskać dostęp do tej funkcji. Rejestracja trwa około 30 minut.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Sprawdź stan rejestracji funkcji:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Gdy ten kod zwróci `"state": "Registered"` wartość , możesz przejść do następnego kroku.

Sprawdź rejestrację:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Jeśli nie zostanie ono zarejestrowane, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Aby określić zestaw szyfrowania dysków dla wersji obrazu, użyj [narzędzia az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) z `--target-region-encryption` parametrem . Format to rozdzielana przecinkami lista kluczy do szyfrowania dysków systemu operacyjnego `--target-region-encryption` i danych. Powinno to wyglądać tak: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Jeśli źródłem dysku systemu operacyjnego jest dysk zarządzany lub maszyna wirtualna, użyj funkcji , aby określić źródło `--managed-image` dla wersji obrazu. W tym przykładzie źródłem jest obraz zarządzany, który ma dysk systemu operacyjnego i dysk danych o jednostce LUN 0. Dysk systemu operacyjnego zostanie zaszyfrowany za pomocą zestawu DiskEncryptionSet1, a dysk danych zostanie zaszyfrowany za pomocą zestawu DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Jeśli źródłem dysku systemu operacyjnego jest migawka, użyj , `--os-snapshot` aby określić dysk systemu operacyjnego. Jeśli istnieją migawki dysku danych, które również powinny być częścią wersji obrazu, dodaj je. Użyj `--data-snapshot-luns` , aby określić jednostkę LUN, a następnie użyj , aby określić `--data-snapshots` migawki.

W tym przykładzie źródłami są migawki dysku. Istnieje dysk systemu operacyjnego i dysk danych o jednostce LUN 0. Dysk systemu operacyjnego zostanie zaszyfrowany za pomocą zestawu DiskEncryptionSet1, a dysk danych zostanie zaszyfrowany za pomocą zestawu DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Maszynę wirtualną można utworzyć z galerii obrazów udostępnionych i zaszyfrować dyski przy użyciu kluczy zarządzanych przez klienta. Składnia jest taka sama jak tworzenie [uogólnionych lub](vm-generalized-image-version-cli.md) wyspecjalizowanych maszyn [wirtualnych](vm-specialized-image-version-cli.md) na obrazie. Wystarczy dodać `--os-disk-encryption-set` parametr z identyfikatorem zestawu szyfrowania. W przypadku dysków danych dodaj listę zestawów szyfrowania dysków danych rozdzielonych `--data-disk-encryption-sets` spacjami.


## <a name="portal"></a>Portal

Podczas tworzenia wersji obrazu w portalu można użyć karty **Szyfrowanie,** aby zastosować zestawy szyfrowania magazynu.

> [!IMPORTANT]
> Aby użyć podwójnego szyfrowania, należy użyć linku w [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) celu uzyskania dostępu do Azure Portal. Podwójne szyfrowanie danych w spoczynku nie jest obecnie widoczne w publicznym Azure Portal chyba że użyjemy tego linku.


1. Na stronie **Tworzenie wersji obrazu** wybierz **kartę** Szyfrowanie.
2. W **obszarze Typ szyfrowania** wybierz pozycję Szyfrowanie danych **spoczynku przy** użyciu klucza zarządzanego przez klienta lub Podwójne szyfrowanie przy użyciu kluczy zarządzanych przez platformę i **zarządzanych przez klienta.** 
3. Dla każdego dysku w obrazie wybierz zestaw szyfrowania z **listy** rozwijanej Zestaw szyfrowania dysków. 

### <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Maszynę wirtualną można utworzyć na pomocą wersji obrazu i zaszyfrować dyski przy użyciu kluczy zarządzanych przez klienta. Podczas tworzenia maszyny wirtualnej w  portalu na  karcie Dyski wybierz opcję Szyfrowanie  danych spoczynku przy użyciu kluczy zarządzanych przez klienta lub Podwójne szyfrowanie za pomocą kluczy zarządzanych przez platformę i zarządzanych przez klienta dla ustawienia **Typ szyfrowania.** Następnie możesz wybrać zestaw szyfrowania z listy rozwijanej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [o szyfrowaniu dysków po stronie serwera.](./disk-encryption.md)

Aby uzyskać informacje na temat sposobu dostarczania informacji o planie zakupu, zobacz Supply Azure Marketplace purchase plan information when creating images (Dostarczanie informacji o planie zakupu podczas [tworzenia obrazów).](marketplace-images.md)