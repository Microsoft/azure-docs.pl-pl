---
title: Eksportowanie danych z obiektów blob platformy Azure przy użyciu usługi Azure Import/Export | Microsoft Docs
description: Dowiedz się, jak tworzyć zadania eksportu w Azure Portal, aby przesyłać dane z obiektów blob platformy Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/29/2020
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 39f9a5802d7f10753c8ea81bf414da195e137cc6
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234141"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Eksportowanie danych z usługi Azure Blob Storage za pomocą usługi Azure Import/Export

Ten artykuł zawiera instrukcje krok po kroku dotyczące korzystania z usługi Azure Import/Export do bezpiecznego eksportowania dużych ilości danych z magazynu obiektów blob platformy Azure. Usługa wymaga wysłania pustych dysków do centrum danych platformy Azure. Usługa eksportuje dane z konta magazynu na dyski, a następnie dostarcza dyski z powrotem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania eksportu w celu przetransferowania danych z usługi Azure Blob Storage należy uważnie przejrzeć i wykonać poniższą listę wymagań wstępnych dla tej usługi.
Należy:

- Mieć aktywną subskrypcję platformy Azure, która może być używana w usłudze Import/Export.
- Mieć co najmniej jedno konto usługi Azure Storage. Zapoznaj się z listą [obsługiwanych kont magazynu i typów magazynów dla usługi Import/Export](storage-import-export-requirements.md). Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-account-create.md).
- Ma wystarczającą liczbę dysków [obsługiwanych typów](storage-import-export-requirements.md#supported-disks).
- Mieć konto FedEx/DHL. Jeśli chcesz użyć operatora innego niż FedEx/DHL, skontaktuj się z zespołem operacyjnym Azure Data Box `adbops@microsoft.com` .
  - Konto musi być prawidłowe, powinno mieć saldo i musi mieć możliwości wysyłki zwrotnej.
  - Generuj numer śledzenia dla zadania eksportu.
  - Każde zadanie powinno mieć oddzielny numer śledzenia. Nie ma możliwości obsługi wielu zadań o tym samym numerze śledzenia.
  - Jeśli nie masz konta nośnego, przejdź do:
    - [Utwórz konto FedEx](https://www.fedex.com/en-us/create-account.html)lub
    - [Utwórz konto DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Krok 1. Tworzenie zadania eksportu

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby utworzyć zadanie eksportu w Azure Portal.

1. Zaloguj się do <https://portal.azure.com/> .
2. Przejdź do obszaru **wszystkie usługi > magazyn > zadania importowania/eksportowania** .

    ![Przejdź do zadań importu/eksportu](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Kliknij pozycję **Utwórz zadanie importu/eksportu** .

    ![Kliknij przycisk Importuj/Eksportuj zadanie](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. **Podstawowe informacje** :

    - Wybierz pozycję **Eksportuj z platformy Azure** .
    - Wprowadź opisową nazwę zadania eksportu. Użyj wybranej nazwy do śledzenia postępu zadań.
        - Nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia.
        - Nazwa musi rozpoczynać się od litery i nie może zawierać spacji.
    - Wybierz subskrypcję.
    - Wprowadź lub wybierz grupę zasobów.

        ![Podstawy](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. W **szczegółach zadania** :

    - Wybierz konto magazynu, w którym znajdują się dane, które mają zostać wyeksportowane. Użyj konta magazynu w pobliżu miejsca, w którym się znajdujesz.
    - Lokalizacja Dropoff jest automatycznie wypełniana na podstawie regionu wybranego konta magazynu.
    - Określ dane obiektów blob, które mają zostać wyeksportowane z konta magazynu do pustego dysku lub dysków.
    - Wybierz, aby **wyeksportować wszystkie** dane obiektów BLOB na koncie magazynu.

         ![Eksportuj wszystko](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Możesz określić kontenery i obiekty blob do wyeksportowania.
        - **Aby określić obiekt BLOB do wyeksportowania** : Użyj selektora **równego** . Określ ścieżkę względną do obiektu BLOB, rozpoczynając od nazwy kontenera. Użyj *$root* , aby określić kontener główny.
        - **Aby określić wszystkie obiekty blob zaczynające się od prefiksu** : Użyj elementu **Start with** Selector. Określ prefiks, zaczynając od ukośnika "/". Prefiks może być prefiksem nazwy kontenera, pełną nazwą kontenera lub pełną nazwą kontenera, po którym następuje prefiks nazwy obiektu BLOB. Musisz podać ścieżki obiektów BLOB w prawidłowym formacie, aby uniknąć błędów podczas przetwarzania, jak pokazano na poniższym zrzucie ekranu. Aby uzyskać więcej informacji, zobacz [przykłady prawidłowych ścieżek obiektów BLOB](#examples-of-valid-blob-paths).

           ![Eksportuj wybrane kontenery i obiekty blob](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Możesz wyeksportować z pliku listy obiektów BLOB.

        ![Eksportuj z pliku listy obiektów BLOB](./media/storage-import-export-data-from-blobs/export-from-blob6.png)

   > [!NOTE]
   > Jeśli obiekt BLOB do wyeksportowania jest używany podczas kopiowania danych, usługa Azure Import/Export wykonuje migawkę obiektu BLOB i kopiuje migawkę.

6. W oknie **Informacje o wysyłce zwrotu** :

    - Wybierz operatora z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się z zespołem ds. operacyjnych Azure Data Box `adbops@microsoft.com`  z informacjami dotyczącymi przewoźnika, którego zamierzasz używać.
    - Wprowadź prawidłowy numer konta nośnego, który został utworzony za pomocą tego operatora. Firma Microsoft korzysta z tego konta do dostarczania dysków z powrotem po zakończeniu zadania eksportowania.
    - Podaj pełną i poprawną nazwę kontaktu, numer telefonu, adres e-mail, ulica, miasto, kod pocztowy, Województwo i kraj/region.

        > [!TIP]
        > Zamiast określania adresu e-mail dla pojedynczego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet w przypadku opuszczenia przez administratora.

7. **Podsumowanie** :

    - Przejrzyj szczegóły zadania.
    - Zanotuj nazwę zadania i podano adres wysyłkowy centrum danych platformy Azure na potrzeby wysyłania dysków na platformę Azure.

        > [!NOTE]
        > Zawsze wysyłaj dyski do centrum danych zanotowanego w Azure Portal. Jeśli dyski są dostarczane do niewłaściwego centrum danych, zadanie nie zostanie przetworzone.

    - Kliknij przycisk **OK** , aby zakończyć tworzenie zadania eksportowania.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Wykonaj następujące kroki, aby utworzyć zadanie eksportu w Azure Portal.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Tworzenie zadania

1. Użyj polecenia [AZ Extension Add](/cli/azure/extension#az_extension_add) , aby dodać rozszerzenie [AZ Import-Export](/cli/azure/ext/import-export/import-export) :

    ```azurecli
    az extension add --name import-export
    ```

1. Aby uzyskać listę lokalizacji, z których można odbierać dyski, użyj polecenia [AZ Import-Export Location list](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Uruchom następujące polecenie [AZ Import-Export Create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) , aby utworzyć zadanie eksportu korzystające z istniejącego konta magazynu:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Zamiast określania adresu e-mail dla pojedynczego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet w przypadku opuszczenia przez administratora.

   To zadanie eksportuje wszystkie obiekty blob na koncie magazynu. Można określić obiekt BLOB do wyeksportowania, zastępując tę wartość dla parametru **--Export** :

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Ta wartość parametru eksportuje obiekt BLOB o nazwie *logo.bmp* w kontenerze głównym.

   Istnieje również możliwość wybrania wszystkich obiektów BLOB w kontenerze przy użyciu prefiksu. Zastąp tę wartość dla parametru **--Export** :

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Aby uzyskać więcej informacji, zobacz [przykłady prawidłowych ścieżek obiektów BLOB](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Jeśli obiekt BLOB do wyeksportowania jest używany podczas kopiowania danych, usługa Azure Import/Export wykonuje migawkę obiektu BLOB i kopiuje migawkę.

1. Użyj polecenia [AZ Import-Export list](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) , aby wyświetlić wszystkie zadania dla grupy zasobów myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Aby zaktualizować zadanie lub anulować zadanie, uruchom polecenie [AZ Import-Export Update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) :

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Wykonaj następujące kroki, aby utworzyć zadanie eksportu w Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Mimo że moduł **AZ. ImportExport** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet. Po ogólnym udostępnieniu tego modułu programu PowerShell będzie on częścią przyszłych wydań modułu AZ PowerShell i dostępne domyślnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Tworzenie zadania

1. Aby uzyskać listę lokalizacji, z których można odbierać dyski, użyj polecenia cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) :

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Uruchom następujący [nowy — przykład AzImportExport](/powershell/module/az.importexport/new-azimportexport) , aby utworzyć zadanie eksportu korzystające z istniejącego konta magazynu:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Zamiast określania adresu e-mail dla pojedynczego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet w przypadku opuszczenia przez administratora.

   To zadanie eksportuje wszystkie obiekty blob na koncie magazynu. Można określić obiekt BLOB do wyeksportowania, zastępując tę wartość parametru **-ExportBlobListblobPath** :

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Ta wartość parametru eksportuje obiekt BLOB o nazwie *logo.bmp* w kontenerze głównym.

   Istnieje również możliwość wybrania wszystkich obiektów BLOB w kontenerze przy użyciu prefiksu. Zastąp tę wartość parametru **-ExportBlobListblobPath** :

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Aby uzyskać więcej informacji, zobacz [przykłady prawidłowych ścieżek obiektów BLOB](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Jeśli obiekt BLOB do wyeksportowania jest używany podczas kopiowania danych, usługa Azure Import/Export wykonuje migawkę obiektu BLOB i kopiuje migawkę.

1. Użyj polecenia cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) , aby wyświetlić wszystkie zadania dla grupy zasobów myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Aby zaktualizować zadanie lub anulować zadanie, uruchom polecenie cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) :

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Krok 2. dostarczenie dysków

Jeśli nie znasz wymaganej liczby dysków, przejdź do obszaru [Sprawdzanie liczby dysków](#check-the-number-of-drives). Jeśli znasz liczbę dysków, wykonaj instrukcje dotyczące dostarczania dysków.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3. aktualizowanie zadania przy użyciu informacji śledzenia

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Krok 4. odbieranie dysków

Gdy pulpit nawigacyjny zgłosi zadanie, dyski są wysyłane do Ciebie, a numer śledzenia dla wysyłki jest dostępny w portalu.

1. Po otrzymaniu dysków z wyeksportowanymi danymi należy pobrać klucze funkcji BitLocker w celu odblokowania dysków. Przejdź do zadania eksportu w Azure Portal. Kliknij kartę **Importuj/Eksportuj** .
2. Wybierz i kliknij zadanie eksportowania z listy. Przejdź do pozycji **szyfrowanie** i Skopiuj klucze.

   ![Wyświetl klucze funkcji BitLocker dla zadania eksportu](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Użyj kluczy funkcji BitLocker do odblokowania dysków.

Eksportowanie zostało zakończone.

## <a name="step-5-unlock-the-disks"></a>Krok 5. Odblokowywanie dysków

Aby odblokować dysk, użyj następującego polecenia:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Oto przykład danych wejściowych przykładowych.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

W tej chwili można usunąć zadanie lub pozostawić je. Zadania są usuwane automatycznie po 90 dniach.

## <a name="check-the-number-of-drives"></a>Sprawdź liczbę dysków

Ten *opcjonalny* krok pozwala określić liczbę dysków wymaganych dla zadania eksportu. Wykonaj ten krok w systemie operacyjnym Windows z [obsługiwaną wersją systemu operacyjnego](storage-import-export-requirements.md#supported-operating-systems).

1. [Pobierz wersję WAImportExport 1](https://www.microsoft.com/download/details.aspx?id=42659) w systemie Windows.
2. Rozpakuj do folderu domyślnego `waimportexportv1` . Na przykład `C:\WaImportExportV1`.
3. Otwórz okno programu PowerShell lub wiersza polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog na folder niespakowany, uruchom następujące polecenie:

   `cd C:\WaImportExportV1`

4. Aby sprawdzić liczbę dysków wymaganych dla wybranych obiektów blob, uruchom następujące polecenie:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametry są opisane w poniższej tabeli:

    |Parametr wiersza polecenia|Opis|
    |--------------------------|-----------------|
    |**/logdir:**|Opcjonalny. Katalog dzienników. Pełne pliki dziennika są zapisywane w tym katalogu. Jeśli nie zostanie określony, bieżący katalog jest używany jako katalog dziennika.|
    |**sery**|Wymagane. Nazwa konta magazynu dla zadania eksportu.|
    |**SK**|Wymagane tylko wtedy, gdy nie określono sygnatury dostępu współdzielonego kontenera. Klucz konta magazynu dla zadania eksportu.|
    |**/csas:**|Wymagane tylko wtedy, gdy nie określono klucza konta magazynu. Sygnatura dostępu współdzielonego kontenera do wyświetlania obiektów BLOB do wyeksportowania w zadaniu eksportu.|
    |**/ExportBlobListFile:**|Wymagane. Ścieżka do pliku XML zawierającego listę ścieżek obiektów blob lub prefiksów ścieżek obiektów BLOB dla obiektów BLOB do wyeksportowania. Format pliku używany w `BlobListBlobPath` elemencie w operacji [Put zadania](/rest/api/storageimportexport/jobs) interfejsu API REST usługi Import/Export.|
    |**/DriveSize:**|Wymagane. Rozmiar dysków, które mają być używane dla zadania eksportu, *np.* 500 GB, 1,5 TB.|

    Zobacz [przykład polecenia PreviewExport](#example-of-previewexport-command).

5. Sprawdź, czy możesz odczytywać i zapisywać dane na dyskach, które zostaną wysłane dla zadania eksportu.

### <a name="example-of-previewexport-command"></a>Przykład polecenia PreviewExport

Poniższy przykład ilustruje `PreviewExport` polecenie:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Plik listy eksportu obiektów BLOB może zawierać nazwy obiektów blob i prefiksy obiektów blob, jak pokazano poniżej:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Narzędzie Azure Import/Export wyświetla listę wszystkich obiektów blob, które mają zostać wyeksportowane, i oblicza sposób pakowania ich na dyski o określonym rozmiarze, uwzględniając wszelkie niezbędne obciążenie, a następnie szacuje liczbę napędów potrzebnych do przechowywania obiektów blob i informacje o użyciu dysków.

Oto przykład danych wyjściowych z pominiętymi dziennikami informacyjnymi:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Przykłady prawidłowych ścieżek obiektów BLOB

W poniższej tabeli przedstawiono przykłady prawidłowych ścieżek obiektów blob:

   | Selektor | Ścieżka obiektu BLOB | Opis |
   | --- | --- | --- |
   | Rozpoczyna się od |/ |Eksportuje wszystkie obiekty blob na koncie magazynu |
   | Rozpoczyna się od |/$root/ |Eksportuje wszystkie obiekty blob w kontenerze głównym |
   | Rozpoczyna się od |/book |Eksportuje wszystkie obiekty blob w dowolnym kontenerze rozpoczynającym się od prefiksu **książki** |
   | Rozpoczyna się od |muzyk |Eksportuje wszystkie obiekty blob w kontenerze **muzyka** |
   | Rozpoczyna się od |/music/love |Eksportuje wszystkie obiekty blob w ramach **muzyki** kontenera, które zaczynają się od prefiksu **miłość** |
   | Równa się |$root/logo.bmp |Eksportuje **logo.bmp** obiektów BLOB w kontenerze głównym |
   | Równa się |wideo/story.mp4 |Eksportuje **story.mp4** obiektów BLOB w **filmach wideo** kontenera |

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
- [Przejrzyj wymagania dotyczące importu/eksportu](storage-import-export-requirements.md)
