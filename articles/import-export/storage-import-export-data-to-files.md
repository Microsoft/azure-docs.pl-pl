---
title: Używanie usługi Azure Import/Export do transferu danych do Azure Files | Microsoft Docs
description: Dowiedz się, jak tworzyć zadania importu w Azure Portal, aby przesyłać dane do Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 9c13ffc597349cdd2b304889d142ca7c2f89c713
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861539"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Use Azure Import/Export service to import data to Azure Files (Używanie usługi Azure Import/Export do importowania danych do usługi Azure Files)

Ten artykuł zawiera instrukcje krok po kroku dotyczące używania usługi Azure Import/Export do bezpiecznego importowania dużych ilości danych do Azure Files. Aby zaimportować dane, usługa wymaga wysłania obsługiwanych dysków zawierających dane do centrum danych platformy Azure.

Usługa Import/Export obsługuje tylko importowanie Azure Files do usługi Azure Storage. Eksportowanie Azure Files nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania importu w celu transferu danych do Azure Files dokładnie przejrzyj i ukończ następującą listę wymagań wstępnych. Musisz:

- Mieć aktywną subskrypcję platformy Azure do użycia z usługą Import/Export.
- Mieć co najmniej jedno konto usługi Azure Storage. Zapoznaj się z listą [obsługiwanych kont magazynu i typów magazynów dla usługi Import/Export.](storage-import-export-requirements.md) Aby uzyskać informacje na temat tworzenia nowego konta magazynu, zobacz [How to Create a Storage Account (Jak utworzyć konto magazynu).](../storage/common/storage-account-create.md)
- Mieć odpowiednią liczbę dysków [obsługiwanych typów.](storage-import-export-requirements.md#supported-disks)
- Mieć system Windows z [obsługiwaną wersją systemu operacyjnego.](storage-import-export-requirements.md#supported-operating-systems)
- [Pobierz program WAImportExport w wersji 2](https://aka.ms/waiev2) w systemie Windows. Rozpakować do folderu domyślnego `waimportexport` . Na przykład `C:\WaImportExport`.
- Mieć konto FedEx/DHL. Jeśli chcesz użyć operatora innego niż FedEx/DHL, skontaktuj się z zespołem Azure Data Box Operations pod adres `adbops@microsoft.com` .
    - Konto musi być prawidłowe, musi mieć saldo i musi mieć możliwości wysyłki zwrotu.
    - Wygeneruj numer śledzenia dla zadania eksportu.
    - Każde zadanie powinno mieć oddzielny numer śledzenia. Nie ma możliwości obsługi wielu zadań o tym samym numerze śledzenia.
    - Jeśli nie masz konta operatora, przejdź do:
        - [Utwórz konto FedEx](https://www.fedex.com/en-us/create-account.html)lub
        - [Utwórz konto firmy DHL.](http://www.dhl-usa.com/en/express/shipping/open_account.html)


## <a name="step-1-prepare-the-drives"></a>Krok 1. Przygotowanie dysków

Ten krok generuje plik dziennika. Plik dziennika przechowuje podstawowe informacje, takie jak numer seryjny dysku, klucz szyfrowania i szczegóły konta magazynu.

Wykonaj poniższe kroki, aby przygotować dyski.

1. Podłącz nasze dyski do systemu Windows za pośrednictwem łączników SATA.
2. Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie używaj punktów instalacji.
3. *Zmodyfikujdataset.csv* w folderze głównym, w którym znajduje się narzędzie. W zależności od tego, czy chcesz zaimportować plik, folder, czy oba te elementy, dodaj wpisy wdataset.csv *podobne* do poniższych przykładów.

   - **Aby zaimportować plik:** w poniższym przykładzie dane do skopiowania są na dysku F:. Plik *MyFile1.txt* skopiowany do katalogu głównego *myazurefileshare1.* Jeśli udział *MyAzureFileshare1* nie istnieje, zostanie utworzony na koncie usługi Azure Storage. Struktura folderów jest zachowywana.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Aby zaimportować folder:** wszystkie pliki i foldery w *folderze MyFolder2* są rekursywnie kopiowane do udostępniania plików. Struktura folderów jest zachowywana.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     W tym samym pliku odpowiadającym importowanych folderom lub plikom można utworzyć wiele wpisów.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Dowiedz się więcej [na temat przygotowywania pliku CSV zestawu danych.](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)


4. *Zmodyfikujdriveset.csv* w folderze głównym, w którym znajduje się narzędzie. Dodaj wpisy w *driveset.csv* podobne do następujących przykładów. Plik zestawu dysków zawiera listę dysków i odpowiadające litery dysku, dzięki czemu narzędzie może prawidłowo wybrać listę dysków do przygotowania.

    W tym przykładzie przyjęto założenie, że są dołączone dwa dyski i podstawowe woluminy NTFS G:\ i H:\ zostaną utworzone. H:\nie jest zaszyfrowany, gdy G: jest już zaszyfrowany. Narzędzie formatuje i szyfruje dysk hostujący H:\ tylko (a nie G: \) .

   - **W przypadku dysku, który nie jest zaszyfrowany:** określ *opcję Szyfruj,* aby włączyć szyfrowanie funkcją BitLocker na dysku.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Dla dysku, który jest już zaszyfrowany:** określ *wartość AlreadyEncrypted* i podaj klucz funkcji BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     W tym samym pliku odpowiadającym wielu dyskom można utworzyć wiele wpisów. Dowiedz się więcej [na temat przygotowywania pliku CSV zestawu dysków.](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)

5. Użyj opcji `PrepImport` kopiowania i przygotowywania danych na dysk. W przypadku pierwszej sesji kopiowania w celu skopiowania katalogów i/lub plików z nową sesją kopiowania uruchom następujące polecenie:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Poniżej przedstawiono przykład importowania.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Plik dziennika o nazwie podanej za pomocą `/j:` parametru jest tworzony dla każdego uruchomienia wiersza polecenia. Każdy dysk, który przygotujemy, ma plik dziennika, który należy przekazać podczas tworzenia zadania importu. Dyski bez plików dziennika nie są przetwarzane.

    > [!IMPORTANT]
    > - Nie należy modyfikować danych na dyskach lub pliku dziennika po zakończeniu przygotowywania dysku.

Aby uzyskać dodatkowe przykłady, przejdź do [przykładów dla plików dziennika](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Krok 2. Tworzenie zadania importu

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj poniższe kroki, aby utworzyć zadanie importu w Azure Portal.
1. Zaloguj się do usługi https://portal.azure.com/ .
2. Wyszukaj zadania **importu/eksportu.**

    ![Wyszukiwanie zadań importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Wybierz pozycję **+ Nowe**.

    ![Wybierz pozycję Nowy, aby utworzyć nową ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Na stronie **Podstawy**:

   1. Wybierz subskrypcję.
   1. Wybierz grupę zasobów lub wybierz **pozycję Utwórz nową** i utwórz nową.
   1. Wprowadź opisową nazwę zadania importu. Użyj nazwy , aby śledzić postęp zadań.
       * Nazwa może zawierać tylko małe litery, cyfry i łączniki.
       * Nazwa musi zaczynać się od litery i może nie zawierać spacji.
   1. Wybierz **pozycję Importuj na platformę Azure.**

    ![Tworzenie zadania importu — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

   Wybierz **pozycję Dalej: Szczegóły >,** aby kontynuować.

5. W **szczegółach zadania:**

   1. Przekaż pliki dziennika utworzone w poprzednim kroku [1. Przygotowanie dysków.](#step-1-prepare-the-drives)
   1. Wybierz docelowy region świadczenia usługi Azure dla zamówienia.
   1. Wybierz konto magazynu do zaimportowania.

      Lokalizacja listy rozwijanej jest wypełniana automatycznie na podstawie regionu wybranego konta magazynu.

   1. Jeśli nie chcesz zapisywać owego dziennika, wyczyść opcję Zapisz pełny dziennik w kontenerze obiektów **blob "waimportexport".**


   ![Tworzenie zadania importu — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   Wybierz **pozycję Dalej: wysyłanie >,** aby kontynuować.

4. W **przypadku wysyłki:**

    1. Wybierz operatora z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się Azure Data Box zespołu operacji firmy , `adbops@microsoft.com`  aby uzyskać informacje o przewoźnika, z których planujesz korzystać.
    1. Wprowadź prawidłowy numer konta przewoźnika utworzonego z tym operatorem. Firma Microsoft używa tego konta do wysłania dysków z powrotem do Ciebie po zakończeniu zadania importowania.
    1. Podaj pełną i prawidłową nazwę kontaktu, numer telefonu, adres e-mail, adres ulicy, miasto, plik zip, stan/województwo i kraj/region.

        > [!TIP]
        > Zamiast określać adres e-mail dla jednego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet wtedy, gdy administrator odchodzi.

    ![Tworzenie zadania importu — Krok 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Wybierz **pozycję Przejrzyj i utwórz,** aby kontynuować.

5. W podsumowaniu zamówienia:

   1. Zapoznaj się **z warunkami,** a następnie wybierz pozycję "Potwierdzam, że wszystkie podane informacje są poprawne i zgadzam się na warunki i postanowienia". Następnie jest wykonywana walidacja.
   1. Przejrzyj informacje o zadaniu podane w podsumowaniu. Zanotuj nazwę zadania i adres wysyłkowy centrum danych platformy Azure, aby wysłać dyski z powrotem na platformę Azure. Te informacje są używane później na etykiecie wysyłkowej.
   1. Wybierz przycisk **Utwórz**.

        ![Tworzenie zadania importu — Krok 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć zadanie importu w interfejsie wiersza polecenia platformy Azure, należy wykonać poniższe kroki.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Tworzenie zadania

1. Użyj polecenia [az extension add,](/cli/azure/extension#az_extension_add) aby [dodać rozszerzenie az import-export:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Możesz użyć istniejącej grupy zasobów lub utworzyć ją. Aby utworzyć grupę zasobów, uruchom polecenie [az group create](/cli/azure/group#az_group_create):

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Możesz użyć istniejącego konta magazynu lub utworzyć je. Aby utworzyć konto magazynu, uruchom [polecenie az storage account create:](/cli/azure/storage/account#az_storage_account_create)

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Aby uzyskać listę lokalizacji, do których można wysłać dyski, użyj [polecenia az import-export location list:](/cli/azure/import-export/location#az_import_export_location_list)

    ```azurecli
    az import-export location list
    ```

1. Użyj polecenia [az import-export location show,](/cli/azure/import-export/location#az_import_export_location_show) aby uzyskać lokalizacje dla swojego regionu:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Uruchom następujące polecenie [az import-export create,](/cli/azure/import-export#az_import_export_create) aby utworzyć zadanie importu:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Zamiast określać adres e-mail dla jednego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet wtedy, gdy administrator odchodzi.


1. Użyj polecenia [az import-export list,](/cli/azure/import-export#az_import_export_list) aby wyświetlić wszystkie zadania dla grupy zasobów myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Aby zaktualizować zadanie lub anulować zadanie, uruchom [polecenie az import-export update:](/cli/azure/import-export#az_import_export_update)

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Aby utworzyć zadanie importu w programie Azure PowerShell, należy wykonać następujące Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Gdy moduł **Az.ImportExport** programu PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Tworzenie zadania

1. Możesz użyć istniejącej grupy zasobów lub utworzyć ją. Aby utworzyć grupę zasobów, uruchom polecenie cmdlet [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Możesz użyć istniejącego konta magazynu lub utworzyć je. Aby utworzyć konto magazynu, uruchom polecenie cmdlet [New-AzStorageAccount:](/powershell/module/az.storage/new-azstorageaccount)

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Aby uzyskać listę lokalizacji, do których można wysyłać dyski, użyj polecenia cmdlet [Get-AzImportExportLocation:](/powershell/module/az.importexport/get-azimportexportlocation)

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Użyj polecenia `Get-AzImportExportLocation` cmdlet z `Name` parametrem , aby uzyskać lokalizacje dla regionu:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Uruchom następujący [przykładowy kod New-AzImportExport,](/powershell/module/az.importexport/new-azimportexport) aby utworzyć zadanie importu:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
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
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > Zamiast określać adres e-mail dla jednego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet wtedy, gdy administrator odchodzi.

1. Użyj polecenia [cmdlet Get-AzImportExport,](/powershell/module/az.importexport/get-azimportexport) aby wyświetlić wszystkie zadania dla grupy zasobów myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Aby zaktualizować zadanie lub anulować zadanie, uruchom polecenie cmdlet [Update-AzImportExport:](/powershell/module/az.importexport/update-azimportexport)

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Krok 3. Wysyłka dysków do centrum danych platformy Azure

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4. Aktualizowanie zadania przy użyciu informacji śledzenia

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5. Weryfikowanie przekazania danych na platformę Azure

Śledzenie zadania do ukończenia. Po zakończeniu zadania sprawdź, czy twoje dane zostały przekazane na platformę Azure. Usuń dane lokalne dopiero po sprawdzeniu, czy przekazywanie powiodło się.

## <a name="samples-for-journal-files"></a>Przykłady dla plików dziennika

Aby **dodać więcej dysków,** utwórz nowy plik zestawu dysków i uruchom poniższe polecenie.

W przypadku kolejnych sesji kopiowania na dyski inne niż określone w pliku *CSV InitialDriveset* określ nowy plik *csv* zestawu dysków i podaj go jako wartość parametru `AdditionalDriveSet` . Użyj tej **samej nazwy pliku dziennika** i podaj nowy identyfikator **sesji**. Format pliku CSV AdditionalDriveset jest taki sam jak format InitialDriveSet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Poniżej przedstawiono przykład importowania.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Aby dodać dodatkowe dane do tego samego zestawu dysków, użyj prepImport polecenia dla kolejnych sesji kopiowania, aby skopiować dodatkowe pliki/katalog.

W przypadku kolejnych sesji kopiowania na te same dyski  twarde określone w pliku *InitialDriveset.csv* określ tę samą nazwę pliku dziennika i podaj **nowy identyfikator sesji;** nie ma potrzeby podania klucza konta magazynu.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Poniżej przedstawiono przykład importowania.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przegląd wymagań dotyczących importowania/eksportowania](storage-import-export-requirements.md)