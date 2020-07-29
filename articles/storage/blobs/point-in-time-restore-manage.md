---
title: Włącz przywracanie do punktu w czasie i zarządzaj nimi w przypadku blokowych obiektów BLOB (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Dowiedz się, jak przywrócić blokowe obiekty blob do stanu w ramach wcześniejszego punktu w czasie przy użyciu funkcji przywracania do punktu w czasie (wersja zapoznawcza).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6948d4d786e918e5f3e32e6bdf2f7e23940f6815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445444"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Włącz przywracanie do punktu w czasie i zarządzaj nimi w przypadku blokowych obiektów BLOB (wersja zapoznawcza)

Możesz użyć przywracania do punktu w czasie (wersja zapoznawcza), aby przywrócić blokowe obiekty blob do ich stanu w wcześniejszym momencie. W tym artykule opisano sposób włączania przywracania do punktu w czasie dla konta magazynu za pomocą programu PowerShell. Przedstawiono w nim również sposób wykonywania operacji przywracania przy użyciu programu PowerShell.

Aby uzyskać więcej informacji i dowiedzieć się, jak zarejestrować się w celu korzystania z wersji zapoznawczej, zobacz [przywracanie do punktu w czasie dla blokowych obiektów BLOB (wersja zapoznawcza)](point-in-time-restore-overview.md).

> [!CAUTION]
> Przywracanie do punktu w czasie obsługuje operacje przywracania tylko dla blokowych obiektów BLOB. Nie można przywrócić operacji na kontenerach. W przypadku usunięcia kontenera z konta magazynu przez wywołanie operacji [usuwania kontenera](/rest/api/storageservices/delete-container) w podglądzie przywracania do punktu w czasie nie można przywrócić tego kontenera przy użyciu operacji przywracania. W trakcie okresu zapoznawczego zamiast usuwania kontenera Usuń pojedyncze obiekty blob, jeśli chcesz je przywrócić.

> [!IMPORTANT]
> Wersja zapoznawcza przywracania do punktu w czasie jest przeznaczona wyłącznie do użytku w trybie nieprodukcyjnym. Umowy dotyczące poziomu usług produkcyjnych (umowy SLA) nie są obecnie dostępne.

## <a name="install-the-preview-module"></a>Instalowanie modułu podglądu

Aby skonfigurować przywracanie do punktu w czasie platformy Azure przy użyciu programu PowerShell, najpierw zainstaluj moduł AZ. Storage Preview w wersji 1.14.1-Preview lub nowszej. Zalecane jest korzystanie z najnowszej wersji zapoznawczej, ale w wersji 1.14.1 — wersja zapoznawcza i nowsza. Usuń wszystkie inne wersje modułu AZ. Storage.

Następujące polecenie instaluje AZ. Storage [2.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) module:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.0.1-preview -AllowPrerelease
```

Powyższe polecenie wymaga zainstalowania wersji 2.2.4.1 lub nowszej z PowerShellGet. Aby określić, która wersja została załadowana:
```powershell
Get-Module PowerShellGet
```
Aby uzyskać więcej informacji o instalowaniu Azure PowerShell, zobacz [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Włącz i skonfiguruj przywracanie do punktu w czasie

Przed włączeniem i skonfigurowaniem przywracania do punktu w czasie należy włączyć jego wymagania wstępne dla konta magazynu: usuwanie nietrwałe, Źródło zmian i przechowywanie wersji obiektów BLOB. Aby uzyskać więcej informacji na temat włączania każdej z tych funkcji, zobacz następujące artykuły:

- [Włącz usuwanie nietrwałe dla obiektów BLOB](soft-delete-enable.md)
- [Włączanie i wyłączanie kanału informacyjnego zmiany](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)

Aby skonfigurować przywracanie do punktu w czasie platformy Azure za pomocą programu PowerShell, wywołaj polecenie Enable-AzStorageBlobRestorePolicy. Poniższy przykład włącza nietrwałe usuwanie i ustawia okres przechowywania nietrwałego, włącza Źródło zmian, a następnie włącza przywracanie do punktu w czasie. Przed uruchomieniem tego przykładu Użyj szablonu Azure Portal lub Azure Resource Manager, aby włączyć obsługę wersji obiektów BLOB.

Podczas uruchamiania przykładu Pamiętaj, aby zastąpić wartości w nawiasach ostrych własnymi wartościami:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Wykonaj operację przywracania

Aby zainicjować operację przywracania, wywołaj polecenie **Restore-AzStorageBlobRange** , określając punkt przywracania jako wartość **daty i godziny** UTC. Można określić zakresy lexicographical obiektów BLOB do przywrócenia lub pominąć zakres, aby przywrócić wszystkie obiekty blob we wszystkich kontenerach na koncie magazynu. Dla operacji przywracania obsługiwane są maksymalnie 10 zakresów lexicographical. Do przywracania nie są uwzględniane stronicowe obiekty blob i dołączane obiekty blob. Wykonanie operacji przywracania może potrwać kilka minut.

Należy pamiętać o następujących regułach podczas określania zakresu obiektów BLOB do przywrócenia:

- Wzorzec kontenera określony dla zakresu początkowego i zakresu końcowego musi zawierać co najmniej trzy znaki. Ukośnik (/), który jest używany do oddzielenia nazwy kontenera od nazwy obiektu BLOB, nie jest uwzględniany w tym minimalnym.
- Dla każdej operacji przywracania można określić maksymalnie 10 zakresów.
- Symbole wieloznaczne nie są obsługiwane. Są one traktowane jako znaki standardowe.
- Obiekty blob można przywrócić w `$root` `$web` kontenerach i, jawnie określając je w zakresie przekazanym do operacji przywracania. `$root` `$web` Kontenery i są przywracane tylko wtedy, gdy są jawnie określone. Nie można przywrócić innych kontenerów systemu.

> [!IMPORTANT]
> Podczas wykonywania operacji przywracania usługa Azure Storage blokuje operacje na danych w obiektach Blob w zakresach przywracanych przez czas trwania operacji. Operacje odczytu, zapisu i usuwania są blokowane w lokalizacji podstawowej. Z tego powodu operacje, takie jak kontenery list w Azure Portal, mogą nie działać zgodnie z oczekiwaniami podczas operacji przywracania.
>
> Operacje odczytu z lokalizacji pomocniczej mogą być przetwarzane w trakcie operacji przywracania, jeśli konto magazynu ma replikację geograficzną.

### <a name="restore-all-containers-in-the-account"></a>Przywróć wszystkie kontenery na koncie

Aby przywrócić wszystkie kontenery i obiekty blob na koncie magazynu, wywołaj polecenie **Restore-AzStorageBlobRange** , pomijając `-BlobRestoreRange` parametr. Poniższy przykład przywraca kontenery na koncie magazynu do ich stanu 12 godzin przed chwilą:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Przywracanie pojedynczego zakresu blokowych obiektów BLOB

Aby przywrócić zakres obiektów blob, wywołaj polecenie **Restore-AzStorageBlobRange** i określ zakres lexicographical kontenerów i nazw obiektów BLOB dla `-BlobRestoreRange` parametru. Początek zakresu jest w włącznie, a koniec zakresu jest na wyłączność.

Na przykład, aby przywrócić obiekty blob w pojedynczym kontenerze o nazwie *Sample-Container*, można określić zakres, który rozpoczyna się od *przykładowego kontenera* i kończyć się *próbką-container1*. Nie ma wymagań dotyczących kontenerów o nazwie w zakresach początkowych i końcowych do istniejących. Ponieważ koniec zakresu ma charakter wyłączny, nawet jeśli konto magazynu zawiera kontener o nazwie *Sample-container1*, przywrócony zostanie tylko kontener o nazwie *Sample-Container* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Aby określić podzestaw obiektów BLOB w kontenerze do przywrócenia, użyj ukośnika (/), aby oddzielić nazwę kontenera od wzorca obiektu BLOB. Na przykład, poniższy zakres wybiera obiekty blob w jednym kontenerze, których nazwy zaczynają się od litery *d* do *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Następnie podaj zakres do polecenia **Restore-AzStorageBlobRange** . Określ punkt przywracania, podając wartość **daty i godziny** UTC dla `-TimeToRestore` parametru. Poniższy przykład przywraca obiekty blob w określonym zakresie do ich stanu 3 dni przed obecną chwilą:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Przywróć wiele zakresów blokowych obiektów BLOB

Aby przywrócić wiele zakresów blokowych obiektów blob, określ tablicę zakresów dla `-BlobRestoreRange` parametru. Dla operacji przywracania obsługiwane są maksymalnie 10 zakresów. W poniższym przykładzie określono dwa zakresy, aby przywrócić kompletną zawartość *container1* i *container4*:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

### <a name="restore-block-blobs-asynchronously"></a>Asynchroniczne przywracanie blokowych obiektów BLOB

Aby uruchomić operację przywracania asynchronicznie, Dodaj `-AsJob` parametr do wywołania **Restore-AzStorageBlobRange** i Zapisz wynik wywołania w zmiennej. Polecenie **Restore-AzStorageBlobRange** zwraca obiekt typu **AzureLongRunningJob**. Możesz sprawdzić Właściwość **State** tego obiektu, aby określić, czy operacja przywracania została ukończona. Wartość właściwości **State** może być **uruchomiona** lub **zakończona**.

Poniższy przykład pokazuje, jak wywołać operację przywracania asynchronicznie:

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

Aby poczekać na zakończenie operacji przywracania po jej uruchomieniu, wywołaj polecenie [wait-Job](/powershell/module/microsoft.powershell.core/wait-job) , jak pokazano w następującym przykładzie:

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>Znane problemy
- Przywracanie nie powiedzie się dla podzestawu przywracania, w którym znajdują się dołączane obiekty blob. Na razie nie należy wykonywać operacji przywracania, jeśli w ramach konta znajdują się w niej obiekty blob.

## <a name="next-steps"></a>Następne kroki

- [Przywracanie do punktu w czasie dla blokowych obiektów BLOB (wersja zapoznawcza)](point-in-time-restore-overview.md)
- [Usuwanie nietrwałe](soft-delete-overview.md)
- [Źródło zmian (wersja zapoznawcza)](storage-blob-change-feed.md)
- [Przechowywanie wersji obiektów BLOB (wersja zapoznawcza)](versioning-overview.md)
