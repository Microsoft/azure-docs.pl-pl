---
title: Wykonywanie przywracania do punktu w czasie na danych blokowych obiektów blob
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą przywracania do punktu w czasie przywrócić zestaw blokowych obiektów blob do poprzedniego stanu w danym punkcie w czasie.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e8c926c2fbc5b19f67fb78d321ee3293c73be939
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869351"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Wykonywanie przywracania do punktu w czasie na danych blokowych obiektów blob

Przywracanie do punktu w czasie umożliwia przywrócenie co najmniej jednego zestawu blokowych obiektów blob do poprzedniego stanu. W tym artykule opisano sposób włączania przywracania do punktu w czasie dla konta magazynu i wykonywania operacji przywracania.

Aby dowiedzieć się więcej na temat przywracania do punktu w czasie, zobacz Przywracanie do punktu [w czasie dla blokowych obiektów blob.](point-in-time-restore-overview.md)

> [!CAUTION]
> Przywracanie do punktu w czasie obsługuje operacje przywracania tylko dla blokowych obiektów blob. Nie można przywrócić operacji na kontenerach. Jeśli usuniesz kontener z konta magazynu [](/rest/api/storageservices/delete-container) przez wywołanie operacji Usuń kontener, nie będzie można go przywrócić przy użyciu operacji przywracania. Zamiast usuwać cały kontener, usuń poszczególne obiekty blob, jeśli chcesz je później przywrócić. Ponadto firma Microsoft zaleca włączenie usuwania nie softu dla kontenerów i obiektów blob w celu ochrony przed przypadkowym usunięciem. Aby uzyskać więcej informacji, zobacz [Usuwanie nie softowe dla kontenerów (wersja zapoznawcza)](soft-delete-container-overview.md) i Usuwanie nie soft [dla obiektów blob.](soft-delete-blob-overview.md)

## <a name="enable-and-configure-point-in-time-restore"></a>Włączanie i konfigurowanie przywracania do punktu w czasie

Przed włączeniem i skonfigurowaniem przywracania do punktu w czasie włącz jego wymagania wstępne dotyczące konta magazynu: usuwanie nie softowe, zestawienia zmian i przechowywanie wersji obiektów blob. Aby uzyskać więcej informacji na temat włączania każdej z tych funkcji, zobacz następujące artykuły:

- [Włączanie usuwania nietrwałego dla obiektów blob](./soft-delete-blob-enable.md)
- [Włączanie i wyłączanie zestawienia zmian](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)

> [!IMPORTANT]
> Włączenie usuwania nie soft, zestawienia zmian i wersji obiektów blob może spowodować naliczanie dodatkowych opłat. Aby uzyskać więcej informacji, zobacz Usuwanie nie softowe dla [obiektów blob,](soft-delete-blob-overview.md)Obsługa zestawienia zmian [w](storage-blob-change-feed.md)Azure Blob Storage i Obsługa wersji [obiektów blob.](versioning-overview.md)

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby skonfigurować przywracanie do punktu w czasie za pomocą Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W **obszarze Ustawienia** wybierz pozycję Ochrona **danych.**
1. Wybierz **pozycję Włącz przywracanie do punktu w** czasie. Po wybraniu tej opcji jest również włączane usuwanie nie softowe dla obiektów blob, wersjonowanie i zestawienia zmian.
1. Ustaw maksymalny punkt przywracania dla przywracania do punktu w czasie w dniach. Ta liczba musi być co najmniej o jeden dzień mniejsza niż okres przechowywania określony dla usuwania nie softowego obiektu blob.
1. Zapisz zmiany.

Na poniższej ilustracji przedstawiono konto magazynu skonfigurowane do przywracania do punktu w czasie z punktem przywracania z siedmiu dni temu oraz okres przechowywania dla usuwania nie softu obiektu blob o wartości 14 dni.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania przywracania do punktu w czasie w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby skonfigurować przywracanie do punktu w czasie za pomocą programu PowerShell, najpierw zainstaluj moduł [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) w wersji 2.6.0 lub nowszej. Następnie wywołaj [polecenie Enable-AzStorageBlobRestorePolicy,](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) aby włączyć przywracanie do punktu w czasie dla konta magazynu.

Poniższy przykład umożliwia usuwanie nie softowe i ustawia okres przechowywania usuwania nieużywanego, włącza kanał informacyjny zmian i przechowywanie wersji, a następnie włącza przywracanie do punktu w czasie. Podczas uruchamiania przykładu pamiętaj, aby zastąpić wartości w nawiasach kątowych własnymi wartościami:

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

Aby skonfigurować przywracanie do punktu w czasie za pomocą interfejsu wiersza polecenia platformy Azure, najpierw zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Następnie wywołaj [polecenie az storage account blob-service-properties update,](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) aby włączyć przywracanie do punktu w czasie i inne wymagane ustawienia ochrony danych dla konta magazynu.

Poniższy przykład umożliwia usuwanie nieekreślalne i ustawia okres przechowywania usuwania niegotowego na 14 dni, włącza kanał informacyjny zmian i przechowywanie wersji oraz umożliwia przywracanie do punktu w czasie z okresem przywracania 7 dni. Podczas uruchamiania przykładu pamiętaj, aby zastąpić wartości w nawiasach kątowych własnymi wartościami:

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

## <a name="choose-a-restore-point"></a>Wybieranie punktu przywracania

Punkt przywracania to data i godzina przywrócenia danych. Usługa Azure Storage zawsze używa wartości daty/czasu UTC jako punktu przywracania. Jednak funkcja Azure Portal punkt przywracania w czasie lokalnym, a następnie konwertuje tę wartość daty/czasu na wartość daty/godziny UTC w celu wykonania operacji przywracania.

Podczas wykonywania operacji przywracania przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure należy określić punkt przywracania jako wartość daty/czasu UTC. Jeśli punkt przywracania jest określony z wartością czasu lokalnego zamiast wartością czasu UTC, operacja przywracania może w niektórych przypadkach nadal działać zgodnie z oczekiwaniami. Jeśli na przykład czas lokalny to UTC minus pięć godzin, określenie wartości czasu lokalnego powoduje, że punkt przywracania jest pięć godzin wcześniejsza niż wartość, która jest podana. Jeśli nie w danych w zakresie, który ma zostać przywrócony w okresie pięciu godzin, operacja przywracania będzie dawać te same wyniki, niezależnie od tego, która wartość czasu została podana. Aby uniknąć nieoczekiwanych wyników, zaleca się określenie czasu UTC punktu przywracania.

## <a name="perform-a-restore-operation"></a>Wykonywanie operacji przywracania

Możesz przywrócić wszystkie kontenery na koncie magazynu lub przywrócić zakres obiektów blob w co najmniej jednym kontenerze. Zakres obiektów blob jest definiowany leksykograficznie, co oznacza kolejność słowników. Na operację przywracania jest obsługiwanych maksymalnie dziesięć zakresów leksykograficznych. Początek zakresu jest inkluzywny, a koniec zakresu jest wyłączny.

Wzorzec kontenera określony dla zakresu rozpoczęcia i zakresu końcowego musi zawierać co najmniej trzy znaki. Ukośnik (/) używany do oddzielania nazwy kontenera od nazwy obiektu blob nie jest wliczony do tego minimum.

Symbole wieloznaczne nie są obsługiwane w zakresie leksykograficznym. Wszystkie symbole wieloznaczne są traktowane jako znaki standardowe.

Obiekty blob w kontenerach i można przywrócić, jawnie określając je w `$root` `$web` zakresie przekazywanym do operacji przywracania. Kontenery `$root` i `$web` są przywracane tylko wtedy, gdy są jawnie określone. Nie można przywrócić innych kontenerów systemowych.

Przywracane są tylko blokowe obiekty blob. Stronicowe obiekty blob i uzupełnialne obiekty blob nie są uwzględniane w operacji przywracania. Aby uzyskać więcej informacji na temat ograniczeń związanych z uzupełnialne obiekty blob, zobacz [Przywracanie do punktu w czasie dla blokowych obiektów blob.](point-in-time-restore-overview.md)

> [!IMPORTANT]
> Podczas wykonywania operacji przywracania usługa Azure Storage blokuje operacje na danych obiektów blob w zakresach przywracanych na czas trwania operacji. Operacje odczytu, zapisu i usuwania są blokowane w lokalizacji podstawowej. Z tego powodu operacje takie jak wyświetlanie listy kontenerów w Azure Portal mogą nie działać zgodnie z oczekiwaniami, gdy operacja przywracania jest w toku.
>
> Operacje odczytu z lokalizacji pomocniczej mogą być kontynuowane podczas operacji przywracania, jeśli konto magazynu jest replikowane geograficznie.
>
> Czas, który zajmuje przywrócenie zestawu danych, zależy od liczby operacji zapisu i usuwania wykonanych w okresie przywracania. Na przykład konto z milionem obiektów z 3000 dodanymi obiektami dziennie i 1000 usuniętych obiektów dziennie będzie wymagało około dwóch godzin, aby przywrócić do punktu w ciągu ostatnich 30 dni. W przypadku konta z tą szybkością zmian nie zaleca się przechowywania i przywracania danych przez ponad 90 dni w przeszłości.

### <a name="restore-all-containers-in-the-account"></a>Przywracanie wszystkich kontenerów na koncie

Możesz przywrócić wszystkie kontenery na koncie magazynu, aby przywrócić je do poprzedniego stanu w danym momencie.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby przywrócić wszystkie kontenery i obiekty blob na koncie magazynu przy użyciu Azure Portal, wykonaj następujące kroki:

1. Przejdź do listy kontenerów dla konta magazynu.
1. Na pasku narzędzi wybierz pozycję **Przywróć kontenery,** a następnie **przywróć wszystkie .**
1. W **okienku Przywróć wszystkie** kontenery określ punkt przywracania, podając datę i godzina.
1. Potwierdź, że chcesz kontynuować, zaznaczając pole wyboru.
1. Wybierz pozycję Przywróć, aby rozpocząć operację przywracania. 

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Zrzut ekranu przedstawiający sposób przywracania wszystkich kontenerów do określonego punktu przywracania":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby przywrócić wszystkie kontenery i obiekty blob na koncie magazynu przy użyciu programu PowerShell, wywołaj polecenie **Restore-AzStorageBlobRange** i podaj punkt przywracania jako wartość daty/czasu UTC. Domyślnie polecenie **Restore-AzStorageBlobRange** jest uruchamiane asynchronicznie i zwraca obiekt typu **PSBlobRestoreStatus,** który umożliwia sprawdzenie stanu operacji przywracania.

Poniższy przykład asynchronicznie przywraca kontenery na koncie magazynu do stanu 12 godzin przed obecną chwilą i sprawdza niektóre właściwości operacji przywracania:

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

Aby synchronicznie uruchomić operację przywracania, uwzględnij **parametr -WaitForComplete** w poleceniu . Gdy **parametr -WaitForComplete** jest obecny, program PowerShell wyświetla komunikat, który zawiera identyfikator przywracania dla operacji, a następnie blokuje wykonywanie do momentu ukończenia operacji przywracania. Należy pamiętać, że czas wymagany przez operację przywracania zależy od ilości danych do przywrócenia, a ukończenie dużej operacji przywracania może potrwać do godziny.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby przywrócić wszystkie kontenery i obiekty blob na koncie magazynu za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) i podaj punkt przywracania jako wartość daty/czasu UTC.

Poniższy przykład asynchronicznie przywraca wszystkie kontenery na koncie magazynu do stanu 12 godzin przed określoną datą i godziną. Aby sprawdzić stan operacji przywracania, wywołaj az [storage account show](/cli/azure/storage/account#az_storage_account_show):

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Aby sprawdzić właściwości operacji przywracania, wywołaj [az storage account show](/cli/azure/storage/account#az_storage_account_show) i rozwiń właściwość **blobRestoreStatus.** W poniższym przykładzie pokazano, jak sprawdzić **właściwość status.**

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Aby **synchronicznie uruchomić polecenie az storage blob restore** i zablokować wykonywanie do momentu ukończenia operacji przywracania, pomiń parametr `--no-wait` .

---

### <a name="restore-ranges-of-block-blobs"></a>Przywracanie zakresów blokowych obiektów blob

Można przywrócić co najmniej jeden zakres leksykograficzny obiektów blob w jednym kontenerze lub w wielu kontenerach, aby przywrócić te obiekty blob do poprzedniego stanu w danym punkcie w czasie.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby przywrócić zakres obiektów blob w co najmniej jednym kontenerze przy użyciu Azure Portal, wykonaj następujące kroki:

1. Przejdź do listy kontenerów dla konta magazynu.
1. Wybierz kontener lub kontenery do przywrócenia.
1. Na pasku narzędzi wybierz pozycję **Przywróć kontenery,** a następnie **pozycję Przywróć wybrane .**
1. W **okienku Przywróć wybrane kontenery** określ punkt przywracania, podając datę i godzina.
1. Określ zakresy do przywrócenia. Użyj ukośnika (/), aby odszyfikować nazwę kontenera od prefiksu obiektu blob.
1. Domyślnie okienko **Przywróć wybrane kontenery** określa zakres, który zawiera wszystkie obiekty blob w kontenerze. Usuń ten zakres, jeśli nie chcesz przywracać całego kontenera. Domyślny zakres jest pokazany na poniższej ilustracji.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Zrzut ekranu przedstawiający domyślny zakres obiektów blob do usunięcia przed określeniem zakresu niestandardowego":::

1. Potwierdź, że chcesz kontynuować, zaznaczając pole wyboru.
1. Wybierz pozycję Przywróć, aby rozpocząć operację przywracania. 

Na poniższej ilustracji przedstawiono operację przywracania na zestawie zakresów.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Zrzut ekranu przedstawiający sposób przywracania zakresów obiektów blob w co najmniej jednym kontenerze":::

Operacja przywracania pokazana na obrazie wykonuje następujące akcje:

- Przywraca pełną zawartość kontenera *container1*.
- Przywraca obiekty blob z zakresu leksykograficznego *blob1* do *blob5* w *kontenerze container2.* Ten zakres przywraca obiekty blob o nazwach takich jak *blob1,* *blob11,* *blob100,* *blob2* i tak dalej. Ponieważ koniec zakresu jest wyłączny, przywraca obiekty blob, których nazwy zaczynają się od *blob4,* ale nie przywraca obiektów blob, których nazwy zaczynają się od *blob5.*
- Przywraca wszystkie obiekty blob w *kontenerach container3* *i container4.* Ponieważ koniec zakresu jest wyłączny, ten zakres nie przywraca *kontenera5*.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby przywrócić pojedynczy zakres obiektów blob, wywołaj polecenie **Restore-AzStorageBlobRange** i określ zakres leksykograficznych nazw kontenerów i obiektów blob dla `-BlobRestoreRange` parametru . Aby na przykład przywrócić obiekty blob w jednym kontenerze o nazwie *container1,* można określić zakres, który rozpoczyna się od *container1* i kończy się na *kontenerze container2.* Nie jest wymagane, aby kontenery o nazwach w zakresie rozpoczęcia i zakończenia istniały. Ponieważ koniec zakresu jest wyłączny, nawet jeśli konto magazynu zawiera kontener o nazwie *container2,* zostanie przywrócony tylko kontener o nazwie *container1:*

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Aby określić podzbiór obiektów blob w kontenerze do przywrócenia, użyj ukośnika (/), aby oddzielić nazwę kontenera od wzorca prefiksu obiektu blob. Na przykład następujący zakres wybiera obiekty blob w jednym kontenerze, którego nazwy zaczynają się od liter *d* do *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Następnie podaj zakres polecenia **Restore-AzStorageBlobRange.** Określ punkt przywracania, podając wartość **dateTime** czasu UTC dla `-TimeToRestore` parametru . Poniższy przykład przywraca obiekty blob w określonym zakresie do stanu 3 dni przed obecną chwilą:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Domyślnie polecenie **Restore-AzStorageBlobRange** jest uruchamiane asynchronicznie. Podczas asynchronicznego inicjowania operacji przywracania program PowerShell natychmiast wyświetla tabelę właściwości operacji:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Aby przywrócić wiele zakresów blokowych obiektów blob, określ tablicę zakresów dla `-BlobRestoreRange` parametru . W poniższym przykładzie określono dwa zakresy, aby przywrócić pełną zawartość *kontener1* i *container4* do stanu 24 godzin temu, i zapisuje wynik w zmiennej:

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

Aby uruchomić operację przywracania synchronicznie i zablokować wykonywanie do momentu jej ukończenia, uwzględnij **parametr -WaitForComplete** w poleceniu .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby przywrócić zakres obiektów blob, wywołaj [polecenie az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) i określ zakres leksykograficznych nazw kontenerów i obiektów blob dla `--blob-range` parametru . Aby określić wiele zakresów, podaj `--blob-range` parametr dla każdego odrębnego zakresu.

Aby na przykład przywrócić obiekty blob w jednym kontenerze o nazwie *container1,* można określić zakres, który rozpoczyna się od *container1* i kończy się *na container2.* Nie jest wymagane, aby kontenery o nazwach w zakresach początku i końca istniały. Ponieważ koniec zakresu jest wyłączny, nawet jeśli konto magazynu zawiera kontener o nazwie *container2,* zostanie przywrócony tylko kontener o nazwie *container1.*

Aby określić podzbiór obiektów blob w kontenerze do przywrócenia, użyj ukośnika (/), aby oddzielić nazwę kontenera od wzorca prefiksu obiektu blob. W przykładzie przedstawionym poniżej asynchronicznie przywracany jest zakres obiektów blob w kontenerze, którego nazwy zaczynają się od liter `d` za pośrednictwem . `f`

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Aby **synchronicznie uruchomić polecenie az storage blob restore** i blokować wykonywanie do momentu ukończenia operacji przywracania, pomiń parametr `--no-wait` .

---

## <a name="next-steps"></a>Następne kroki

- [Przywracanie do punktu w czasie dla blokowych obiektów blob](point-in-time-restore-overview.md)
- [Usuwanie nie softowe](./soft-delete-blob-overview.md)
- [Zestawienia zmian](storage-blob-change-feed.md)
- [Wersjonarowanie obiektów blob](versioning-overview.md)