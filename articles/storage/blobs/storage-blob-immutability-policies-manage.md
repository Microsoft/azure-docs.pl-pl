---
title: Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi w usłudze Azure Storage
description: Dowiedz się, jak używać ROBAKa (jednokrotne pisanie, odczytywanie wielu) obsługi magazynu obiektów BLOB (Object) w celu przechowywania danych w niemodyfikowalnym stanie dla określonego interwału.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a09dbd7d778a4f7ea2a9aac9ca07b9e6d06bc1ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84463624"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi

Niezmienny magazyn usługi Azure Blob Storage umożliwia użytkownikom przechowywanie obiektów danych o kluczowym znaczeniu dla firmy w ROBAKu (zapis jeden raz, odczyt wielu). Ten stan sprawia, że dane nie są wymazywane i nie są modyfikowane dla interwału określonego przez użytkownika. W czasie trwania interwału przechowywania obiekty blob mogą być tworzone i odczytywane, ale nie można ich modyfikować ani usuwać. Niezmienny magazyn jest dostępny dla kont ogólnego przeznaczenia w wersji 2 i BLOB Storage we wszystkich regionach świadczenia usługi Azure.

W tym artykule przedstawiono sposób ustawiania zasad niezmienności i zarządzania nimi dla danych w usłudze BLOB Storage przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji o niezmiennym magazynie, zobacz artykuł [przechowywanie danych obiektów BLOB o kluczowym znaczeniu w niezmiennym magazynie](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Ustawianie zasad przechowywania i blokad prawnych

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Utwórz nowy kontener lub wybierz istniejący kontener, w którym będą przechowywane obiekty blob wymagające przechowywania w stanie niezmiennym. Kontener musi znajdować się w ramach konta ogólnego przeznaczenia w wersji 2 lub magazynu obiektów BLOB.

2. Wybierz pozycję **zasady dostępu** w ustawieniach kontenera. Następnie wybierz pozycję **Dodaj zasady** w obszarze **niezmienny magazyn obiektów BLOB**.

    ![Ustawienia kontenera w portalu](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Aby włączyć przechowywanie na podstawie czasu, wybierz pozycję **przechowywanie na podstawie czasu** z menu rozwijanego.

    !["Przechowywanie oparte na czasie" wybrane w obszarze "typ zasad"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Wprowadź interwał przechowywania (w dniach) (dopuszczalne wartości to od 1 do 146000 dni).

    ![Pole "Aktualizuj okres przechowywania do"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Początkowy stan zasad jest odblokowany, co pozwala na testowanie funkcji i wprowadzanie zmian zasad przed ich zablokowaniem. Zablokowanie zasad jest niezbędne do zgodności z przepisami, takimi jak sekundy 17a-4.

5. Zablokuj zasady. Kliknij prawym przyciskiem myszy wielokropek (**...**), a następnie wyświetli się następujące menu z dodatkowymi akcjami:

    !["Zasady blokowania" w menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Wybierz pozycję **zasady blokowania** i Potwierdź blokadę. Zasady są teraz zablokowane i nie można ich usunąć. dozwolone są tylko rozszerzenia interwału przechowywania. Operacje usuwania i zastąpienia obiektów BLOB są niedozwolone. 

    ![Potwierdzenie "zasad blokowania" w menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Aby włączyć blokady prawne, wybierz pozycję **Dodaj zasady**. Wybierz pozycję **Blokada** z menu rozwijanego.

    !["Wstrzymanie prawne" w menu w obszarze "typ zasad"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Utwórz blokadę prawną z co najmniej jednym tagiem.

    ![Pole "nazwa tagu" w obszarze Typ zasad](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Aby wyczyścić blokadę prawną, Usuń zastosowany tag identyfikatora blokady dozwolonej.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Ta funkcja jest uwzględniona w następujących grupach poleceń: `az storage container immutability-policy`  i `az storage container legal-hold` . Uruchom `-h` na nich, aby zobaczyć polecenia.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Moduł AZ. Storage obsługuje niezmienny magazyn.  Aby włączyć tę funkcję, wykonaj następujące kroki:

1. Upewnij się, że masz zainstalowaną najnowszą wersję programu PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force` .
2. Usuń poprzednią instalację Azure PowerShell.
3. Zainstaluj Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber` .

Poniższy przykładowy skrypt programu PowerShell jest przeznaczony do celów referencyjnych. Ten skrypt tworzy nowe konto magazynu i kontener. Następnie pokazano, jak ustawiać i czyścić informacje prawne, tworzyć i blokować zasady przechowywania oparte na czasie (znane także jako zasady niezmienności) i zwiększać Interwał przechowywania.

Najpierw utwórz konto usługi Azure Storage:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Ustaw i wyczyść blokady prawne:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Utwórz lub zaktualizuj zasady niezmienności oparte na czasie:

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Pobierz zasady niezmienności:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Zablokuj zasady niezmienności (Dodaj, `-Force` Aby odrzucić monit):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Rozwiń zasady niezmienności:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Usuń odblokowane zasady niezmienności (Dodaj `-Force` do odrzucenia monitu):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Włączanie funkcji Zezwalaj na chronione Dodawanie obiektów BLOB

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Zezwalaj na dodatkowe zapisy do dołączenia](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Ta funkcja jest uwzględniona w następujących grupach poleceń: `az storage container immutability-policy`  i `az storage container legal-hold` . Uruchom `-h` na nich, aby zobaczyć polecenia.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Następne kroki

[Przechowywanie danych obiektów blob kluczowych dla działalności za pomocą magazynu niezmiennego](storage-blob-immutable-storage.md)
