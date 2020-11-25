---
title: Plik dyrektywy include
description: " — plik"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 6c7536e38a0d2cf7d4e906947aff645c74e459c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026819"
---
## <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnego użytku w ogólny sposób. Aby to osiągnąć, musi być uogólniony wirtualny dysk twardy systemu operacyjnego, operacja, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny wirtualnej.

### <a name="for-windows"></a>W przypadku systemu Windows

Dyski systemu operacyjnego Windows są uogólnione za pomocą narzędzia [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Jeśli później zaktualizujesz lub ponownie skonfigurujesz system operacyjny, należy ponownie uruchomić program Sysprep.

> [!WARNING]
> Po uruchomieniu programu Sysprep należy wyłączyć maszynę wirtualną, dopóki nie zostanie wdrożona, ponieważ aktualizacje mogą być uruchamiane automatycznie. To zamknięcie spowoduje uniknięcie kolejnych aktualizacji w celu wprowadzenia zmian specyficznych dla wystąpienia do systemu operacyjnego lub zainstalowanych usług. Aby uzyskać więcej informacji na temat uruchamiania programu Sysprep, zobacz [procedura uogólniania dysku VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>W przypadku systemu Linux

Poniższy proces uogólni maszynę wirtualną z systemem Linux i ponownie wdraża ją jako oddzielną maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../virtual-machines/linux/capture-image.md). Możesz zatrzymać, gdy dojdziesz do sekcji o nazwie "Tworzenie maszyny wirtualnej z przechwyconego obrazu".

1. Usuń agenta platformy Azure dla systemu Linux.
    1. Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
    2. W oknie SSH wprowadź następujące polecenie: `sudo waagent –deprovision+user` .
    3. Wpisz Y, aby kontynuować (parametr-Force można dodać do poprzedniego polecenia, aby uniknąć kroku potwierdzenia).
    4. Po zakończeniu wykonywania polecenia wpisz **Exit** , aby zamknąć klienta SSH.
2. Zatrzymaj maszynę wirtualną.
    1. W Azure Portal wybierz grupę zasobów (RG), a następnie usuń przydział maszyny wirtualnej.
    2. Maszyna wirtualna jest teraz uogólniona i można utworzyć nową maszynę wirtualną przy użyciu tego dysku maszyny wirtualnej.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Utwórz migawkę dysku maszyny wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).
2. Zaczynając od lewego górnego rogu, wybierz pozycję **Utwórz zasób**, a następnie wyszukaj i wybierz pozycję **migawka**.
3. W bloku migawka wybierz pozycję  **Utwórz**.
4. Wprowadź **nazwę** migawki.
5. Wybierz istniejącą grupę zasobów lub wprowadź nazwę nowej.
6. W polu **dysk źródłowy** wybierz dysk zarządzany do utworzenia migawki.
7. Wybierz **Typ konta** , który ma być używany do przechowywania migawki. Użyj **HDD w warstwie Standardowa** , chyba że będzie to konieczne w przypadku dysku SSD o wysokiej wydajności.
8. Wybierz pozycję **Utwórz**.

#### <a name="extract-the-vhd"></a>Wyodrębnij wirtualny dysk twardy

Użyj poniższego skryptu, aby wyeksportować migawkę do dysku VHD na koncie magazynu.

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu wygenerowania identyfikatora URI sygnatury dostępu współdzielonego dla migawki i skopiowania bazowego wirtualnego dysku twardego do konta magazynu przy użyciu identyfikatora URI SAS. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
| --- | --- |
| az disk grant-access | Generuje sygnaturę dostępu współdzielonego tylko do odczytu, która umożliwia skopiowanie odpowiedniego pliku wirtualnego dysku twardego na konto magazynu lub pobranie go do środowiska lokalnego
| az storage blob copy start | Umożliwia asynchroniczne Kopiowanie obiektu BLOB z jednego konta magazynu do innego. Użyj `az storage blob show` , aby sprawdzić stan nowego obiektu BLOB. |
|
