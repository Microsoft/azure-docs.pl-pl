---
title: Wykonaj przywracanie do punktu w czasie dla danych blokowych obiektów BLOB
titleSuffix: Azure Storage
description: Dowiedz się, jak użyć przywracania do punktu w czasie, aby przywrócić zestaw blokowych obiektów BLOB do ich poprzedniego stanu w danym momencie.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b62e341d35a4ff7fd5a7ddd6d9f19b138aaf0aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071651"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Wykonaj przywracanie do punktu w czasie dla danych blokowych obiektów BLOB

Możesz użyć przywracania do punktu w czasie, aby przywrócić jeden lub więcej zestawów blokowych obiektów BLOB do poprzedniego stanu. W tym artykule opisano sposób włączania przywracania do punktu w czasie dla konta magazynu oraz wykonywania operacji przywracania.

Aby dowiedzieć się więcej o przywracaniu do punktu w czasie, zobacz [przywracanie do punktu w czasie dla blokowych obiektów BLOB](point-in-time-restore-overview.md).

> [!CAUTION]
> Przywracanie do punktu w czasie obsługuje operacje przywracania tylko dla blokowych obiektów BLOB. Nie można przywrócić operacji na kontenerach. W przypadku usunięcia kontenera z konta magazynu przez wywołanie operacji [usuwania kontenera](/rest/api/storageservices/delete-container) nie można przywrócić tego kontenera przy użyciu operacji przywracania. Zamiast usuwać cały kontener, Usuń pojedyncze obiekty blob, jeśli chcesz je później przywrócić. Ponadto firma Microsoft zaleca włączenie usuwania nietrwałego dla kontenerów i obiektów BLOB w celu ochrony przed przypadkowym usunięciem. Aby uzyskać więcej informacji, zobacz [usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)](soft-delete-container-overview.md) i [usuwanie nietrwałe dla obiektów BLOB](soft-delete-blob-overview.md).

## <a name="enable-and-configure-point-in-time-restore"></a>Włącz i skonfiguruj przywracanie do punktu w czasie

Przed włączeniem i skonfigurowaniem przywracania do punktu w czasie należy włączyć jego wymagania wstępne dla konta magazynu: usuwanie nietrwałe, Źródło zmian i przechowywanie wersji obiektów BLOB. Aby uzyskać więcej informacji na temat włączania każdej z tych funkcji, zobacz następujące artykuły:

- [Włączanie usuwania nietrwałego dla obiektów blob](./soft-delete-blob-enable.md)
- [Włączanie i wyłączanie kanału informacyjnego zmiany](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)

> [!IMPORTANT]
> Włączenie usuwania nietrwałego, źródła zmian i przechowywania wersji obiektów BLOB może spowodować naliczenie dodatkowych opłat. Aby uzyskać więcej informacji, zobacz [usuwanie nietrwałe dla obiektów BLOB](soft-delete-blob-overview.md), [Obsługa kanałów zmian w systemie Azure Blob Storage](storage-blob-change-feed.md)i [przechowywanie wersji obiektów BLOB](versioning-overview.md).

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby skonfigurować przywracanie do punktu w czasie za pomocą Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W obszarze **Ustawienia** wybierz pozycję **Ochrona danych**.
1. Wybierz pozycję **Włącz przywracanie do punktu w czasie** . Po wybraniu tej opcji jest również włączona funkcja usuwania nietrwałego dla obiektów blob, wersji i źródła zmian.
1. Ustaw maksymalny punkt przywracania dla przywracania do określonego momentu (w dniach). Ta wartość musi być co najmniej jeden dzień krótszy niż okres przechowywania określony dla usuwania nietrwałego obiektu BLOB.
1. Zapisz zmiany.

Na poniższej ilustracji przedstawiono konto magazynu skonfigurowane do przywracania do punktu w czasie z punktem przywracania wynoszącym siedem dni temu i okresem przechowywania nietrwałego usunięcia z 14 dni.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania przywracania do punktu w czasie w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby skonfigurować przywracanie do punktu w czasie za pomocą programu PowerShell, najpierw zainstaluj moduł [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) w wersji 2.6.0 lub nowszej. Następnie Wywołaj polecenie [enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) , aby włączyć przywracanie do punktu w czasie dla konta magazynu.

Poniższy przykład włącza nietrwałe usuwanie i ustawia okres przechowywania nietrwałego, umożliwia tworzenie źródła zmian i przechowywanie wersji, a następnie włącza przywracanie do punktu w czasie. Podczas uruchamiania przykładu Pamiętaj, aby zastąpić wartości w nawiasach ostrych własnymi wartościami:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

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

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować przywracanie do punktu w czasie za pomocą interfejsu wiersza polecenia platformy Azure, najpierw zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Następnie Wywołaj polecenie [AZ Storage account BLOB-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) , aby włączyć przywracanie do punktu w czasie oraz inne wymagane ustawienia ochrony danych dla konta magazynu.

Poniższy przykład włącza nietrwałe usuwanie i ustawia okres przechowywania nietrwałego do 14 dni, umożliwia tworzenie źródła zmian i przechowywanie wersji oraz włącza przywracanie do punktu w czasie z okresem przywracania wynoszącym 7 dni. Podczas uruchamiania przykładu Pamiętaj, aby zastąpić wartości w nawiasach ostrych własnymi wartościami:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Wybierz punkt przywracania

Punkt przywracania to data i godzina przywrócenia danych. Usługa Azure Storage zawsze używa wartości daty/godziny UTC jako punktu przywracania. Jednak Azure Portal umożliwia określenie punktu przywracania w czasie lokalnym, a następnie konwersję tej wartości daty/godziny na wartość daty/godziny UTC w celu wykonania operacji przywracania.

Podczas wykonywania operacji przywracania przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure należy określić punkt przywracania jako wartość daty/godziny UTC. Jeśli punkt przywracania został określony z wartością czasu lokalnego zamiast wartości czasu UTC, operacja przywracania może nadal działać zgodnie z oczekiwaniami w niektórych przypadkach. Na przykład, jeśli czas lokalny jest UTC minus pięć godzin, a następnie określenie wartości czasu lokalnego spowoduje, że punkt przywracania będzie miał 5 godzin wcześniej od podanej wartości. Jeśli nie wprowadzono żadnych zmian w danych w zakresie, który ma zostać przywrócony w tym okresie pięciu godzin, operacja przywracania będzie generować te same wyniki, niezależnie od tego, która wartość czasu została podana. Zaleca się określenie czasu UTC dla punktu przywracania, aby uniknąć nieoczekiwanych wyników.

## <a name="perform-a-restore-operation"></a>Wykonaj operację przywracania

Można przywrócić wszystkie kontenery na koncie magazynu lub można przywrócić szereg obiektów BLOB w jednym lub większej liczbie kontenerów. Zakres obiektów BLOB jest zdefiniowany lexicographically, znaczenie w kolejności słownika. Dla operacji przywracania obsługiwane są maksymalnie dziesięć zakresów lexicographical. Początek zakresu jest włącznie, a koniec zakresu jest na wyłączność.

Wzorzec kontenera określony dla zakresu początkowego i zakresu końcowego musi zawierać co najmniej trzy znaki. Ukośnik (/), który jest używany do oddzielenia nazwy kontenera od nazwy obiektu BLOB, nie jest uwzględniany w tym minimalnym.

Symbole wieloznaczne nie są obsługiwane w zakresie lexicographical. Każdy symbol wieloznaczny jest traktowany jako znaki standardowe.

Obiekty blob można przywrócić w `$root` `$web` kontenerach i, jawnie określając je w zakresie przekazanym do operacji przywracania. `$root` `$web` Kontenery i są przywracane tylko wtedy, gdy są jawnie określone. Nie można przywrócić innych kontenerów systemu.

Przywracane są tylko blokowe obiekty blob. Stronicowe obiekty blob i dołączanie obiektów BLOB nie są uwzględniane w operacji przywracania. Aby uzyskać więcej informacji o ograniczeniach związanych z dołączaniem obiektów blob, zobacz [przywracanie do punktu w czasie dla blokowych obiektów BLOB](point-in-time-restore-overview.md).

> [!IMPORTANT]
> Podczas wykonywania operacji przywracania usługa Azure Storage blokuje operacje na danych w obiektach Blob w zakresach przywracanych przez czas trwania operacji. Operacje odczytu, zapisu i usuwania są blokowane w lokalizacji podstawowej. Z tego powodu operacje, takie jak kontenery list w Azure Portal, mogą nie działać zgodnie z oczekiwaniami podczas operacji przywracania.
>
> Operacje odczytu z lokalizacji pomocniczej mogą być przetwarzane w trakcie operacji przywracania, jeśli konto magazynu ma replikację geograficzną.
>
> Czas potrzebny na przywrócenie zestawu danych zależy od liczby operacji zapisu i usuwania dokonanych w okresie przywracania. Na przykład konto z 1 000 000 obiektami o 3 000 obiektach dodane dziennie, a 1 000 obiektów, które zostały usunięte dziennie, będzie wymagało około dwóch godzin, aby przywrócić je do punktu 30 dni w przeszłości. Okres przechowywania i przywrócenie ponad 90 dni w przeszłości nie będą zalecane dla konta z tą szybkością zmiany.

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

Aby przywrócić wszystkie kontenery i obiekty blob na koncie magazynu przy użyciu programu PowerShell, wywołaj polecenie **Restore-AzStorageBlobRange** i Podaj punkt przywracania jako wartość daty/godziny UTC. Domyślnie polecenie **Restore-AzStorageBlobRange** jest uruchamiane asynchronicznie i zwraca obiekt typu **PSBlobRestoreStatus** , którego można użyć do sprawdzenia stanu operacji przywracania.

Poniższy przykład asynchronicznie przywraca kontenery na koncie magazynu do ich stanu 12 godzin przed chwilą i sprawdza niektóre właściwości operacji przywracania:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Aby uruchomić operację przywracania synchronicznie, należy uwzględnić parametr **-WaitForComplete** w poleceniu. Gdy parametr **-WaitForComplete** jest obecny, program PowerShell wyświetla komunikat zawierający identyfikator przywracania dla operacji, a następnie blokuje wykonywanie do momentu ukończenia operacji przywracania. Należy pamiętać, że długość czasu wymaganego przez operację przywracania zależy od ilości danych do przywrócenia, a duża operacja przywracania może potrwać do godziny.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby przywrócić wszystkie kontenery i obiekty blob na koncie magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage BLOB Restore](/cli/azure/storage/blob#az_storage_blob_restore) i Podaj punkt przywracania jako wartość daty/godziny UTC.

Poniższy przykład asynchronicznie przywraca wszystkie kontenery na koncie magazynu do ich stanu 12 godzin przed określoną datą i godziną. Aby sprawdzić stan operacji przywracania, wywołaj [AZ Storage account show](/cli/azure/storage/account#az_storage_account_show):

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Aby sprawdzić właściwości operacji przywracania, wywołaj polecenie [AZ Storage account show](/cli/azure/storage/account#az_storage_account_show) i rozwiń Właściwość **blobRestoreStatus** . Poniższy przykład pokazuje, jak sprawdzić Właściwość **stanu** .

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Aby uruchomić polecenie **AZ Storage BLOB Restore** synchronicznie i zablokować wykonywanie do momentu ukończenia operacji przywracania, Pomiń `--no-wait` parametr.

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
- Przywraca obiekty blob w lexicographical zakresie *blob1* przez *blob5* w *container2*. Ten zakres przywraca obiekty blob z nazwami, takimi jak *blob1*, *blob11*, *blob100*, *blob2* i tak dalej. Ponieważ koniec zakresu jest na wyłączność, przywraca obiekty blob, których nazwy zaczynają się od *blob4*, ale nie przywraca obiektów blob, których nazwy zaczynają się od *blob5*.
- Przywraca wszystkie obiekty blob w *container3* i *container4*. Ponieważ koniec zakresu jest na wyłączność, ten zakres nie przywraca *container5*.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby przywrócić pojedynczy zakres obiektów blob, wywołaj polecenie **Restore-AzStorageBlobRange** i określ zakres lexicographical kontenerów i nazw obiektów BLOB dla `-BlobRestoreRange` parametru. Na przykład aby przywrócić obiekty blob w pojedynczym kontenerze o nazwie *container1*, można określić zakres, który rozpoczyna się od *container1* i kończąc na *container2*. Nie ma wymagań dotyczących kontenerów o nazwie w zakresach początkowych i końcowych do istniejących. Ponieważ koniec zakresu ma charakter wyłączny, nawet jeśli konto magazynu zawiera kontener o nazwie *container2*, przywrócony zostanie tylko kontener o nazwie *container1* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Aby określić podzestaw obiektów BLOB w kontenerze do przywrócenia, użyj ukośnika (/), aby oddzielić nazwę kontenera od wzorca prefiksu obiektu BLOB. Na przykład, poniższy zakres wybiera obiekty blob w jednym kontenerze, których nazwy zaczynają się od litery *d* do *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Następnie podaj zakres do polecenia **Restore-AzStorageBlobRange** . Określ punkt przywracania, podając wartość **daty i godziny** UTC dla `-TimeToRestore` parametru. Poniższy przykład przywraca obiekty blob w określonym zakresie do ich stanu 3 dni przed obecną chwilą:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Domyślnie polecenie **Restore-AzStorageBlobRange** jest uruchamiane asynchronicznie. Po zainicjowaniu operacji przywracania asynchronicznej program PowerShell natychmiast Wyświetla tabelę właściwości dla operacji:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Aby przywrócić wiele zakresów blokowych obiektów blob, określ tablicę zakresów dla `-BlobRestoreRange` parametru. W poniższym przykładzie określono dwa zakresy, aby przywrócić pełną zawartość *container1* i *container4* do ich stanu 24 godz., a następnie zapisać wynik w zmiennej:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Aby uruchomić operację przywracania synchronicznie i zablokować wykonywanie do momentu ukończenia, należy uwzględnić parametr **-WaitForComplete** polecenia.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby przywrócić zakres obiektów blob, wywołaj polecenie [AZ Storage BLOB Restore](/cli/azure/storage/blob#az_storage_blob_restore) i określ zakres lexicographical nazw kontenerów i obiektów BLOB dla `--blob-range` parametru. Aby określić wiele zakresów, podaj `--blob-range` parametr dla każdego odrębnego zakresu.

Na przykład aby przywrócić obiekty blob w pojedynczym kontenerze o nazwie *container1*, można określić zakres, który rozpoczyna się od *container1* i kończąc na *container2*. Nie ma wymagań dotyczących kontenerów o nazwie w zakresach początkowych i końcowych do istniejących. Ponieważ koniec zakresu ma charakter wyłączny, nawet jeśli konto magazynu zawiera kontener o nazwie *container2*, przywrócony zostanie tylko kontener o nazwie *container1* .

Aby określić podzestaw obiektów BLOB w kontenerze do przywrócenia, użyj ukośnika (/), aby oddzielić nazwę kontenera od wzorca prefiksu obiektu BLOB. Poniższy przykład asynchronicznie przywraca zakres obiektów BLOB w kontenerze, których nazwy zaczynają się od litery `d` `f` .

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Aby uruchomić polecenie **AZ Storage BLOB Restore** synchronicznie i zablokować wykonywanie do momentu ukończenia operacji przywracania, Pomiń `--no-wait` parametr.

---

## <a name="next-steps"></a>Następne kroki

- [Przywracanie do punktu w czasie dla blokowych obiektów BLOB](point-in-time-restore-overview.md)
- [Usuwanie nietrwałe](./soft-delete-blob-overview.md)
- [Źródło zmian](storage-blob-change-feed.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)