---
title: Wersja zapoznawcza — Tworzenie wersji obrazu zaszyfrowanej przy użyciu własnych kluczy
description: Utwórz wersję obrazu w galerii obrazów udostępnionych przy użyciu kluczy szyfrowania zarządzanych przez klienta.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 0f5eb6a2964cdb679c2a83bd9d9b6296e3e962ed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499292"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Wersja zapoznawcza: Używanie kluczy zarządzanych przez klienta do szyfrowania obrazów

Obrazy w galerii obrazów udostępnionych są przechowywane jako migawki, dzięki czemu są one automatycznie szyfrowane za pomocą szyfrowania po stronie serwera. Szyfrowanie po stronie serwera używa 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych. Szyfrowanie po stronie serwera jest również zgodne ze standardem FIPS 140-2. Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z dyskami zarządzanymi platformy Azure, zobacz [interfejs API kryptografii: Kolejna generacja](/windows/desktop/seccng/cng-portal).

Możesz polegać na kluczach zarządzanych przez platformę do szyfrowania obrazów lub korzystać z własnych kluczy. Można również użyć obu jednocześnie do szyfrowania dwukrotnego. Jeśli zdecydujesz się na zarządzanie szyfrowaniem przy użyciu własnych kluczy, możesz określić *klucz zarządzany przez klienta* , który będzie używany do szyfrowania i odszyfrowywania wszystkich dysków w obrazie. 

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta używa Azure Key Vault. Możesz zaimportować [klucze RSA](../key-vault/keys/hsm-protected-keys.md) do magazynu kluczy lub wygenerować nowe klucze rsa w Azure Key Vault.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule wymagane jest już ustawienie szyfrowania dysku w każdym regionie, w którym chcesz replikować obraz:

- Aby użyć tylko klucza zarządzanego przez klienta, zapoznaj się z artykułami dotyczącymi włączania kluczy zarządzanych przez klienta za pomocą szyfrowania po stronie serwera przy użyciu programu [Azure Portal](./disks-enable-customer-managed-keys-portal.md) lub [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Aby korzystać z kluczy zarządzanych przez platformę i klienta (na potrzeby podwójnego szyfrowania), zobacz artykuły o włączaniu podwójnego szyfrowania przy użyciu narzędzia [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) lub [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > Musisz użyć linku, [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) Aby uzyskać dostęp do Azure Portal. Podwójne szyfrowanie w spoczynku nie jest obecnie widoczne w publicznej Azure Portal, chyba że zostanie użyty ten link.

## <a name="limitations"></a>Ograniczenia

W przypadku korzystania z kluczy zarządzanych przez klienta do szyfrowania obrazów w udostępnionej galerii obrazów są stosowane następujące ograniczenia:   

- Zestawy kluczy szyfrowania muszą znajdować się w tej samej subskrypcji co obraz.

- Zestawy kluczy szyfrowania są zasobami regionalnymi, więc każdy region wymaga innego zestawu kluczy szyfrowania.

- Nie można kopiować ani udostępniać obrazów, które używają kluczy zarządzanych przez klienta. 

- Po zastosowaniu własnych kluczy do zaszyfrowania dysku lub obrazu nie można wrócić do korzystania z kluczy zarządzanych przez platformę do szyfrowania tych dysków lub obrazów.


> [!IMPORTANT]
> Szyfrowanie za poorednictwem kluczy zarządzanych przez klienta jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Aby uzyskać publiczną wersję zapoznawczą, należy najpierw zarejestrować funkcję:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Rejestracja może potrwać kilka minut. Użyj `Get-AzProviderFeature` , aby sprawdzić stan rejestracji funkcji:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Po `RegistrationState` powrocie `Registered` , możesz przejść do następnego kroku.

Sprawdź rejestrację dostawcy. Upewnij się, że zwraca `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Jeśli nie zwróci tego `Registered` , użyj następującego kodu, aby zarejestrować dostawców:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Aby określić szyfrowanie dysków dla wersji obrazu, użyj polecenie  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) z `-TargetRegion` parametrem: 

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

Można utworzyć maszynę wirtualną z galerii obrazów udostępnionych i użyć kluczy zarządzanych przez klienta do szyfrowania dysków. Składnia jest taka sama jak tworzenie [uogólnionej](vm-generalized-image-version-powershell.md) lub [wyspecjalizowanej](vm-specialized-image-version-powershell.md) maszyny wirtualnej na podstawie obrazu. Użyj zestawu parametrów rozszerzonych i Dodaj `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` do konfiguracji maszyny wirtualnej.

W przypadku dysków z danymi Dodaj `-DiskEncryptionSetId $setID` parametr w przypadku użycia polecenie [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>Interfejs wiersza polecenia 

W publicznej wersji zapoznawczej należy najpierw zarejestrować się w celu uzyskania tej funkcji. Rejestracja trwa około 30 minut.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Sprawdź stan rejestracji funkcji:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Gdy ten kod zwraca `"state": "Registered"` , możesz przejść do następnego kroku.

Sprawdź swoją rejestrację:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Jeśli nie powiedzie się, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Aby określić szyfrowanie dysku ustawione dla wersji obrazu, użyj [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) z `--target-region-encryption` parametrem. Format `--target-region-encryption` jest rozdzielaną przecinkami listą kluczy do szyfrowania systemu operacyjnego i dysków danych. Powinien wyglądać następująco: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Jeśli źródło dysku systemu operacyjnego jest dyskiem zarządzanym lub maszyną wirtualną, użyj, `--managed-image` Aby określić źródło dla wersji obrazu. W tym przykładzie źródłem jest obraz zarządzany z dyskiem systemu operacyjnego i dyskiem danych o numerze LUN 0. Dysk systemu operacyjnego zostanie zaszyfrowany za pomocą DiskEncryptionSet1, a dysk danych zostanie zaszyfrowany przy użyciu DiskEncryptionSet2.

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

Jeśli źródło dysku systemu operacyjnego jest migawką, użyj, `--os-snapshot` Aby określić dysk systemu operacyjnego. Jeśli istnieją migawki dysków danych, które muszą być również częścią wersji obrazu, należy dodać te elementy. Użyj `--data-snapshot-luns` , aby określić jednostkę LUN, a następnie użyj, `--data-snapshots` Aby określić migawki.

W tym przykładzie źródła są migawkami dysków. Istnieje dysk systemu operacyjnego i dysk danych o numerze LUN 0. Dysk systemu operacyjnego zostanie zaszyfrowany za pomocą DiskEncryptionSet1, a dysk danych zostanie zaszyfrowany przy użyciu DiskEncryptionSet2.

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

Możesz utworzyć maszynę wirtualną na podstawie udostępnionej galerii obrazów i użyć kluczy zarządzanych przez klienta do szyfrowania dysków. Składnia jest taka sama jak tworzenie [uogólnionej](vm-generalized-image-version-cli.md) lub [wyspecjalizowanej](vm-specialized-image-version-cli.md) maszyny wirtualnej na podstawie obrazu. Po prostu Dodaj `--os-disk-encryption-set` parametr z identyfikatorem zestawu szyfrowania. W przypadku dysków z danymi Dodaj rozdzieloną `--data-disk-encryption-sets` spacjami listę zestawów szyfrowania dysków dla dysków danych.


## <a name="portal"></a>Portal

Podczas tworzenia wersji obrazu w portalu można użyć karty **szyfrowanie** , aby zastosować zestawy szyfrowania magazynu.

> [!IMPORTANT]
> Aby użyć szyfrowania podwójnego, należy użyć linku, [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) Aby uzyskać dostęp do Azure Portal. Podwójne szyfrowanie w spoczynku nie jest obecnie widoczne w publicznej Azure Portal, chyba że zostanie użyty ten link.


1. Na stronie **Tworzenie wersji obrazu** wybierz kartę **szyfrowanie** .
2. W obszarze **typ szyfrowania** wybierz pozycję **szyfrowanie w systemie i klucz zarządzany przez klienta** lub **podwójne szyfrowanie za pomocą kluczy zarządzanych przez platformę i klienta**. 
3. Dla każdego dysku w obrazie wybierz zestaw szyfrowania z listy rozwijanej **zestaw szyfrowania dysku** . 

### <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Możesz utworzyć maszynę wirtualną na podstawie wersji obrazu i użyć kluczy zarządzanych przez klienta do szyfrowania dysków. Podczas tworzenia maszyny wirtualnej w portalu na karcie **dyski** wybierz opcję **szyfrowanie w trybie spoczynku z kluczami zarządzanymi przez klienta** lub **podwójne szyfrowanie za pomocą kluczy zarządzanych przez platformę i** zarządzane przez klienta dla **typu szyfrowania**. Następnie można wybrać zestaw szyfrowania z listy rozwijanej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [szyfrowaniu dysków po stronie serwera](./disk-encryption.md).

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).