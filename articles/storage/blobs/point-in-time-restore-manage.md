---
title: Włącz przywracanie do punktu w czasie i zarządzaj nimi w przypadku blokowych obiektów BLOB (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Dowiedz się, jak przywrócić zestaw blokowych obiektów BLOB do poprzedniego stanu przy użyciu funkcji przywracania do określonego momentu (wersja zapoznawcza).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 140e1203a29dcebec9d6483e73e906591b2213fb
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068517"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Włącz przywracanie do punktu w czasie i zarządzaj nimi w przypadku blokowych obiektów BLOB (wersja zapoznawcza)

Możesz użyć przywracania do określonego momentu (wersja zapoznawcza), aby przywrócić zestaw blokowych obiektów BLOB do poprzedniego stanu. W tym artykule opisano sposób włączania przywracania do punktu w czasie dla konta magazynu za pomocą programu PowerShell. Przedstawiono w nim również sposób wykonywania operacji przywracania przy użyciu programu PowerShell.

Aby uzyskać więcej informacji i dowiedzieć się, jak zarejestrować się w celu korzystania z wersji zapoznawczej, zobacz [przywracanie do punktu w czasie dla blokowych obiektów BLOB (wersja zapoznawcza)](point-in-time-restore-overview.md).

> [!CAUTION]
> Przywracanie do punktu w czasie obsługuje operacje przywracania tylko dla blokowych obiektów BLOB. Nie można przywrócić operacji na kontenerach. W przypadku usunięcia kontenera z konta magazynu przez wywołanie operacji [usuwania kontenera](/rest/api/storageservices/delete-container) w podglądzie przywracania do punktu w czasie nie można przywrócić tego kontenera przy użyciu operacji przywracania. W trakcie okresu zapoznawczego zamiast usuwania kontenera Usuń pojedyncze obiekty blob, jeśli chcesz je przywrócić.

> [!IMPORTANT]
> Wersja zapoznawcza przywracania do punktu w czasie jest przeznaczona wyłącznie do użytku w trybie nieprodukcyjnym.

## <a name="enable-and-configure-point-in-time-restore"></a>Włącz i skonfiguruj przywracanie do punktu w czasie

Przed włączeniem i skonfigurowaniem przywracania do punktu w czasie należy włączyć jego wymagania wstępne dla konta magazynu: usuwanie nietrwałe, Źródło zmian i przechowywanie wersji obiektów BLOB. Aby uzyskać więcej informacji na temat włączania każdej z tych funkcji, zobacz następujące artykuły:

- [Włączanie usuwania nietrwałego dla obiektów blob](soft-delete-enable.md)
- [Włączanie i wyłączanie kanału informacyjnego zmiany](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby skonfigurować przywracanie do punktu w czasie za pomocą Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W obszarze **Ustawienia**wybierz pozycję **Ochrona danych**.
1. Wybierz pozycję **Włącz przywracanie do punktu w czasie** . Po wybraniu tej opcji jest również włączona funkcja usuwania nietrwałego dla obiektów blob, wersji i źródła zmian.
1. Ustaw maksymalny punkt przywracania dla przywracania do określonego momentu (w dniach). Ta wartość musi być co najmniej jeden dzień krótszy niż okres przechowywania określony dla usuwania nietrwałego obiektu BLOB.
1. Zapisz zmiany.

Na poniższej ilustracji przedstawiono konto magazynu skonfigurowane do przywracania do punktu w czasie z punktem przywracania wynoszącym siedem dni temu i okresem przechowywania nietrwałego usunięcia z 14 dni.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania przywracania do punktu w czasie w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby skonfigurować przywracanie do punktu w czasie za pomocą programu PowerShell, najpierw zainstaluj moduł AZ. Storage w wersji zapoznawczej 1.14.1-Preview lub nowszą wersję modułu w wersji zapoznawczej. Usuń wszystkie inne wersje modułu AZ. Storage.

Sprawdź, czy masz zainstalowaną wersję 2.2.4.1 lub nowszą PowerShellGet. Aby określić, która wersja jest aktualnie zainstalowana, uruchom następujące polecenie:

```powershell
Get-InstalledModule PowerShellGet
```

Następnie zainstaluj moduł AZ. Storage Preview. Następujące polecenie instaluje wersję [2.5.2-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) modułu AZ. Storage:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
```

Aby uzyskać więcej informacji na temat instalowania Azure PowerShell, zobacz [Instalowanie programu PowerShellGet](/powershell/scripting/gallery/installing-psget) i [Instalowanie Azure PowerShell z PowerShellGet](/powershell/azure/install-az-ps).

Aby skonfigurować przywracanie do punktu w czasie platformy Azure za pomocą programu PowerShell, wywołaj polecenie Enable-AzStorageBlobRestorePolicy. Poniższy przykład włącza nietrwałe usuwanie i ustawia okres przechowywania nietrwałego, włącza Źródło zmian, a następnie włącza przywracanie do punktu w czasie. Przed uruchomieniem tego przykładu Użyj szablonu Azure Portal lub Azure Resource Manager, aby włączyć obsługę wersji obiektów BLOB.

Podczas uruchamiania przykładu Pamiętaj, aby zastąpić wartości w nawiasach ostrych własnymi wartościami:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

---

## <a name="perform-a-restore-operation"></a>Wykonaj operację przywracania

Podczas wykonywania operacji przywracania należy określić punkt przywracania jako wartość **daty i godziny** UTC. Kontenery i obiekty blob zostaną przywrócone do ich stanu w tym dniu i czasie. Wykonanie operacji przywracania może potrwać kilka minut.

Można przywrócić wszystkie kontenery na koncie magazynu lub można przywrócić szereg obiektów BLOB w jednym lub większej liczbie kontenerów. Zakres obiektów BLOB jest zdefiniowany lexicographically, znaczenie w kolejności słownika. Dla operacji przywracania obsługiwane są maksymalnie dziesięć zakresów lexicographical. Początek zakresu jest włącznie, a koniec zakresu jest na wyłączność.

Wzorzec kontenera określony dla zakresu początkowego i zakresu końcowego musi zawierać co najmniej trzy znaki. Ukośnik (/), który jest używany do oddzielenia nazwy kontenera od nazwy obiektu BLOB, nie jest uwzględniany w tym minimalnym.

Symbole wieloznaczne nie są obsługiwane w zakresie lexicographical. Każdy symbol wieloznaczny jest traktowany jako znaki standardowe.

Obiekty blob można przywrócić w `$root` `$web` kontenerach i, jawnie określając je w zakresie przekazanym do operacji przywracania. `$root` `$web` Kontenery i są przywracane tylko wtedy, gdy są jawnie określone. Nie można przywrócić innych kontenerów systemu.

Przywracane są tylko blokowe obiekty blob. Stronicowe obiekty blob i dołączanie obiektów BLOB nie są uwzględniane w operacji przywracania. Aby uzyskać więcej informacji o ograniczeniach związanych z dołączaniem obiektów blob, zobacz [znane problemy](#known-issues).

> [!IMPORTANT]
> Podczas wykonywania operacji przywracania usługa Azure Storage blokuje operacje na danych w obiektach Blob w zakresach przywracanych przez czas trwania operacji. Operacje odczytu, zapisu i usuwania są blokowane w lokalizacji podstawowej. Z tego powodu operacje, takie jak kontenery list w Azure Portal, mogą nie działać zgodnie z oczekiwaniami podczas operacji przywracania.
>
> Operacje odczytu z lokalizacji pomocniczej mogą być przetwarzane w trakcie operacji przywracania, jeśli konto magazynu ma replikację geograficzną.

### <a name="restore-all-containers-in-the-account"></a>Przywróć wszystkie kontenery na koncie

Można przywrócić wszystkie kontenery na koncie magazynu, aby przywrócić ich poprzedni stan w danym momencie.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby przywrócić wszystkie kontenery i obiekty blob na koncie magazynu przy użyciu Azure Portal, wykonaj następujące czynności:

1. Przejdź do listy kontenerów dla konta magazynu.
1. Na pasku narzędzi wybierz pozycję **Przywróć kontenery**, a następnie pozycję **Przywróć wszystko**.
1. W okienku **Przywróć wszystkie kontenery** Określ punkt przywracania, podając datę i godzinę.
1. Potwierdź, że chcesz wykonać zaznaczenie pola wyboru.
1. Wybierz pozycję **Przywróć** , aby rozpocząć operację przywracania.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Zrzut ekranu przedstawiający sposób przywracania wszystkich kontenerów do określonego punktu przywracania":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby przywrócić wszystkie kontenery i obiekty blob na koncie magazynu przy użyciu programu PowerShell, wywołaj polecenie **Restore-AzStorageBlobRange** , pomijając `-BlobRestoreRange` parametr. Poniższy przykład przywraca kontenery na koncie magazynu do ich stanu 12 godzin przed chwilą:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

---

### <a name="restore-ranges-of-block-blobs"></a>Przywróć zakresy blokowych obiektów BLOB

Można przywrócić jeden lub więcej lexicographical zakresów obiektów BLOB w ramach jednego kontenera lub w wielu kontenerach, aby przywrócić te obiekty blob do ich poprzedniego stanu w danym momencie.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby przywrócić zakres obiektów BLOB w jednym lub większej liczbie kontenerów z Azure Portal, wykonaj następujące kroki:

1. Przejdź do listy kontenerów dla konta magazynu.
1. Wybierz kontener lub kontenery do przywrócenia.
1. Na pasku narzędzi wybierz pozycję **Przywróć kontenery**, a następnie pozycję **Przywróć zaznaczone**.
1. W okienku **Przywróć wybrane kontenery** Określ punkt przywracania, podając datę i godzinę.
1. Określ zakresy do przywrócenia. Użyj ukośnika (/), aby odróżnić nazwę kontenera z prefiksu obiektu BLOB.
1. Domyślnie okienko **Przywróć wybrane kontenery** określa zakres, który obejmuje wszystkie obiekty blob w kontenerze. Usuń ten zakres, jeśli nie chcesz przywrócić całego kontenera. Domyślny zakres jest pokazany na poniższej ilustracji.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Zrzut ekranu przedstawiający domyślny zakres obiektów BLOB do usunięcia przed określeniem zakresu niestandardowego":::

1. Potwierdź, że chcesz wykonać zaznaczenie pola wyboru.
1. Wybierz pozycję **Przywróć** , aby rozpocząć operację przywracania.

Na poniższej ilustracji przedstawiono operację przywracania na zestawie zakresów.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Zrzut ekranu przedstawiający sposób przywracania zakresów obiektów BLOB w jednym lub większej liczbie kontenerów":::

Operacja przywracania pokazana w obrazie wykonuje następujące czynności:

- Przywraca kompletną zawartość *container1*.
- Przywraca obiekty blob w lexicographical zakresie *blob1* przez *blob5* w *container2*. Ten zakres przywraca obiekty blob z nazwami, takimi jak *blob1*, *blob11*, *blob100*, *blob2*i tak dalej. Ponieważ koniec zakresu jest na wyłączność, przywraca obiekty blob, których nazwy zaczynają się od *blob4*, ale nie przywraca obiektów blob, których nazwy zaczynają się od *blob5*.
- Przywraca wszystkie obiekty blob w *container3* i *container4*. Ponieważ koniec zakresu jest na wyłączność, ten zakres nie przywraca *container5*.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby przywrócić pojedynczy zakres obiektów blob, wywołaj polecenie **Restore-AzStorageBlobRange** i określ zakres lexicographical kontenerów i nazw obiektów BLOB dla `-BlobRestoreRange` parametru. Na przykład, aby przywrócić obiekty blob w pojedynczym kontenerze o nazwie *Sample-Container*, można określić zakres, który rozpoczyna się od *przykładowego kontenera* i kończyć się *próbką-container1*. Nie ma wymagań dotyczących kontenerów o nazwie w zakresach początkowych i końcowych do istniejących. Ponieważ koniec zakresu ma charakter wyłączny, nawet jeśli konto magazynu zawiera kontener o nazwie *Sample-container1*, przywrócony zostanie tylko kontener o nazwie *Sample-Container* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container `
    -EndRange sample-container1
```

Aby określić podzestaw obiektów BLOB w kontenerze do przywrócenia, użyj ukośnika (/), aby oddzielić nazwę kontenera od wzorca prefiksu obiektu BLOB. Na przykład, poniższy zakres wybiera obiekty blob w jednym kontenerze, których nazwy zaczynają się od litery *d* do *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d `
    -EndRange sample-container/g
```

Następnie podaj zakres do polecenia **Restore-AzStorageBlobRange** . Określ punkt przywracania, podając wartość **daty i godziny** UTC dla `-TimeToRestore` parametru. Poniższy przykład przywraca obiekty blob w określonym zakresie do ich stanu 3 dni przed obecną chwilą:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Aby przywrócić wiele zakresów blokowych obiektów blob, określ tablicę zakresów dla `-BlobRestoreRange` parametru. W poniższym przykładzie określono dwa zakresy, aby przywrócić kompletną zawartość *container1* i *container4*:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

---

### <a name="restore-block-blobs-asynchronously-with-powershell"></a>Asynchroniczne przywracanie blokowych obiektów BLOB za pomocą programu PowerShell

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

W przypadku podzbioru operacji przywracania, w których znajdują się dołączane obiekty blob, operacja przywracania zakończy się niepowodzeniem. Firma Microsoft zaleca, aby nie przeprowadzać przywracania do punktu w czasie w wersji zapoznawczej, jeśli w ramach konta znajdują się w niej obiekty blob.

## <a name="next-steps"></a>Następne kroki

- [Przywracanie do punktu w czasie dla blokowych obiektów BLOB (wersja zapoznawcza)](point-in-time-restore-overview.md)
- [Usuwanie nietrwałe](soft-delete-overview.md)
- [Źródło zmian (wersja zapoznawcza)](storage-blob-change-feed.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)
