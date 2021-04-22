---
title: Transfer danych do usługi Azure Blobs za pomocą usługi Azure Import/Export | Microsoft Docs
description: Dowiedz się, jak tworzyć zadania importu i eksportu w Azure Portal do transferu danych do i z usługi Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 39eb6c164751ebdfa293798850a8d663fe988b82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875687"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Importowanie danych do usługi Azure Import/Export przy użyciu Azure Blob Storage

Ten artykuł zawiera instrukcje krok po kroku dotyczące używania usługi Azure Import/Export do bezpiecznego importowania dużych ilości danych do usługi Azure Blob Storage. Aby zaimportować dane do usługi Azure Blobs, usługa wymaga wysłania zaszyfrowanych dysków zawierających dane do centrum danych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania importu w celu transferu danych do usługi Azure Blob Storage uważnie przejrzyj i ukończ następującą listę wymagań wstępnych dotyczących tej usługi.
Musisz:

* Mieć aktywną subskrypcję platformy Azure, która może być używana dla usługi Import/Export.
* Mieć co najmniej jedno konto usługi Azure Storage z kontenerem magazynu. Zapoznaj się z listą [obsługiwanych kont magazynu i typów magazynów dla usługi Import/Export.](storage-import-export-requirements.md)
  * Aby uzyskać informacje na temat tworzenia nowego konta magazynu, zobacz [How to Create a Storage Account (Jak utworzyć konto magazynu).](../storage/common/storage-account-create.md)
  * Aby uzyskać informacje na temat kontenera magazynu, przejdź do [tematu Create a storage container (Tworzenie kontenera magazynu).](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
* Mieć odpowiednią liczbę dysków [obsługiwanych typów.](storage-import-export-requirements.md#supported-disks)
* Mieć system Windows z [obsługiwaną wersją systemu operacyjnego.](storage-import-export-requirements.md#supported-operating-systems)
* Włącz funkcję BitLocker w systemie Windows. Zobacz [Jak włączyć funkcję BitLocker.](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)
* [Pobierz najnowszą wersję WAImportExport 1](https://www.microsoft.com/download/details.aspx?id=42659) w systemie Windows. Najnowsza wersja narzędzia zawiera aktualizacje zabezpieczeń umożliwiające zewnętrzną ochronę klucza funkcji BitLocker i zaktualizowaną funkcję trybu odblokowywania.

  * Rozpakować do folderu domyślnego `waimportexportv1` . Na przykład `C:\WaImportExportV1`.
* Mieć konto FedEx/DHL. Jeśli chcesz użyć operatora innego niż FedEx/DHL, skontaktuj się z zespołem firmy Azure Data Box Operations pod adres `adbops@microsoft.com` .
  * Konto musi być prawidłowe, powinno mieć saldo i musi mieć możliwości wysyłki powrotu.
  * Wygeneruj numer śledzenia dla zadania eksportu.
  * Każde zadanie powinno mieć oddzielny numer śledzenia. Nie ma możliwości obsługi wielu zadań o tym samym numerze śledzenia.
  * Jeśli nie masz konta operatora, przejdź do:
    * [Utwórz konto FedEx](https://www.fedex.com/en-us/create-account.html)lub
    * [Utwórz konto firmy DHL.](http://www.dhl-usa.com/en/express/shipping/open_account.html)

## <a name="step-1-prepare-the-drives"></a>Krok 1. Przygotowanie dysków

Ten krok generuje plik dziennika. Plik dziennika przechowuje podstawowe informacje, takie jak numer seryjny dysku, klucz szyfrowania i szczegóły konta magazynu.

Wykonaj poniższe kroki, aby przygotować dyski.

1. Podłącz dyski do systemu Windows za pośrednictwem łączników SATA.
2. Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie używaj punktów instalacji.
3. Włącz szyfrowanie funkcją BitLocker na woluminie NTFS. Jeśli używasz systemu Windows Server, skorzystaj z instrukcji podanych w te [tematze How to enable BitLocker on Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)(Jak włączyć funkcję BitLocker w systemie Windows Server 2012 R2).
4. Kopiowanie danych na zaszyfrowany wolumin. Użyj przeciągania i upuszczania, narzędzia Robocopy lub dowolnego takiego narzędzia do kopiowania. Plik dziennika *(jrn)* jest tworzony w tym samym folderze, w którym jest uruchamiane narzędzie.

   Jeśli dysk jest zablokowany i musisz odblokować dysk, kroki odblokowywania mogą się różnić w zależności od przypadku użycia.

   * Jeśli dodano dane do wstępnie zaszyfrowanego dysku (narzędzie WAImportExport nie zostało użyte do szyfrowania), użyj klucza funkcji BitLocker (hasła liczbowego, które określisz) w oknie podręcznym, aby odblokować dysk.

   * Jeśli dodano dane do dysku, który został zaszyfrowany przez narzędzie WAImportExport, użyj następującego polecenia, aby odblokować dysk:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Otwórz program PowerShell lub okno wiersza polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog na rozpakowany folder, uruchom następujące polecenie:

    `cd C:\WaImportExportV1`
6. Aby uzyskać klucz funkcji BitLocker dysku, uruchom następujące polecenie:

    `manage-bde -protectors -get <DriveLetter>:`
7. Aby przygotować dysk, uruchom następujące polecenie. **W zależności od rozmiaru danych przygotowanie dysku może potrwać od kilku godzin do kilku dni.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Plik dziennika jest tworzony w tym samym folderze, w którym uruchomiono narzędzie. Tworzone są również dwa inne pliki — plik *XML* (folder, w którym uruchamiasz narzędzie) i plik *drive-manifest.xml* (folder, w którym znajdują się dane).

    Używane parametry zostały opisane w poniższej tabeli:

    |Opcja  |Opis  |
    |---------|---------|
    |/j:     |Nazwa pliku dziennika z rozszerzeniem jrn. Plik dziennika jest generowany dla każdego dysku. Zaleca się użycie numeru seryjnego dysku jako nazwy pliku dziennika.         |
    |/ Identyfikator:     |Identyfikator sesji. Użyj unikatowego numeru sesji dla każdego wystąpienia polecenia.      |
    |/t:     |Litera dysku do wysłania. Na przykład dysk `D` .         |
    |/bk:     |Klucz funkcji BitLocker dla dysku. Jego hasło liczbowe z danych wyjściowych `manage-bde -protectors -get D:`      |
    |/srcdir:     |Litera dysku, który ma zostać dostarczony, a następnie `:\` . Na przykład `D:\`.         |
    |/dstdir:     |Nazwa kontenera docelowego w usłudze Azure Storage.         |
    |/blobtype:     |Ta opcja określa typ obiektów blob, do których chcesz zaimportować dane. W przypadku blokowych obiektów blob typ obiektu blob to , a `BlockBlob` w przypadku stronicowych obiektów blob jest to `PageBlob` .         |
    |/skipwrite:     | Określa, że nie trzeba kopiować nowych danych, a istniejące dane na dysku mają być przygotowane.          |
    |/enablecontentmd5:     |Opcja po włączeniu zapewnia, że rozwiązanie MD5 jest obliczane i ustawiane jako `Content-md5` właściwość dla każdego obiektu blob. Użyj tej opcji tylko wtedy, gdy chcesz użyć `Content-md5` pola po przesłaniu danych na platformę Azure. <br> Ta opcja nie ma wpływu na sprawdzanie integralności danych (domyślnie występuje). To ustawienie zwiększa czas przekazywania danych do chmury.          |
8. Powtórz poprzedni krok dla każdego dysku, który ma zostać dostarczony. Plik dziennika o podanej nazwie jest tworzony dla każdego uruchomienia wiersza polecenia.

    > [!IMPORTANT]
    > * Razem z plikiem dziennika plik jest również tworzony w tym `<Journal file name>_DriveInfo_<Drive serial ID>.xml` samym folderze, w którym znajduje się narzędzie. Plik XML jest używany w miejsce pliku dziennika podczas tworzenia zadania, jeśli plik dziennika jest zbyt duży.
   > * Maksymalny rozmiar pliku dziennika, na który zezwala portal, to 2 MB. Jeśli plik dziennika przekroczy ten limit, zwracany jest błąd.

## <a name="step-2-create-an-import-job"></a>Krok 2. Tworzenie zadania importu

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby utworzyć zadanie importu w Azure Portal.

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

   1. Przekaż pliki dziennika utworzone w poprzednim kroku [1. Przygotowanie dysków.](#step-1-prepare-the-drives) Jeśli `waimportexport.exe version1` został użyty, przekaż jeden plik dla każdego przygotowanego dysku. Jeśli rozmiar pliku dziennika przekracza 2 MB, można użyć również `<Journal file name>_DriveInfo_<Drive serial ID>.xml` utworzonego pliku dziennika.
   1. Wybierz docelowy region świadczenia usługi Azure dla zamówienia.
   1. Wybierz konto magazynu do zaimportowania.
      
      Lokalizacja listy rozwijanej jest wypełniana automatycznie na podstawie regionu wybranego konta magazynu.
   1. Jeśli nie chcesz zapisywać owego dziennika, wyczyść opcję Zapisz pełny dziennik w kontenerze obiektów **blob "waimportexport".**

   ![Tworzenie zadania importu — Krok 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Wybierz **pozycję Dalej: wysyłanie >,** aby kontynuować.

6. W **przypadku wysyłki:**

   1. Wybierz operatora z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się Azure Data Box operations pod adres , aby uzyskać informacje dotyczące przewoźnika, `adbops@microsoft.com`  z których planujesz korzystać.
   1. Wprowadź prawidłowy numer konta przewoźnika utworzonego za pomocą tego operatora. Firma Microsoft używa tego konta do wysłania dysków z powrotem do Ciebie po zakończeniu zadania importowania. Jeśli nie masz numeru konta, utwórz konto [operatora FedEx](https://www.fedex.com/us/oadr/) lub [DHL.](https://www.dhl.com/)
   1.  Podaj pełną i prawidłową nazwę kontaktu, numer telefonu, adres e-mail, adres ulicy, miasto, plik zip, województwo i kraj/region.

       > [!TIP]
       > Zamiast określać adres e-mail dla pojedynczego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet wtedy, gdy administrator odchodzi.

   ![Tworzenie zadania importu — Krok 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Wybierz **pozycję Przejrzyj i utwórz,** aby kontynuować.

7. W podsumowaniu zamówienia:

   1. Zapoznaj się **z warunkami,** a następnie wybierz pozycję "Potwierdzam, że wszystkie podane informacje są poprawne i zgadzam się na warunki i postanowienia". Następnie jest wykonywana walidacja.
   1. Przejrzyj informacje o zadaniu podane w podsumowaniu. Zanotuj nazwę zadania i adres wysyłkowy centrum danych platformy Azure, aby wysłać dyski z powrotem na platformę Azure. Te informacje są używane później na etykiecie wysyłkowej.
   1. Wybierz przycisk **Utwórz**.

     ![Tworzenie zadania importu — Krok 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć zadanie importu w interfejsie wiersza polecenia platformy Azure, należy wykonać poniższe kroki.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Tworzenie zadania

1. Użyj polecenia [az extension add,](/cli/azure/extension#az_extension_add) aby dodać [rozszerzenie az import-export:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Możesz użyć istniejącej grupy zasobów lub utworzyć ją. Aby utworzyć grupę zasobów, uruchom polecenie [az group create](/cli/azure/group#az_group_create):

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Możesz użyć istniejącego konta magazynu lub utworzyć konto. Aby utworzyć konto magazynu, uruchom [polecenie az storage account create:](/cli/azure/storage/account#az_storage_account_create)

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
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

Aby utworzyć zadanie importu w programie , należy wykonać Azure PowerShell.

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

## <a name="step-3-optional-configure-customer-managed-key"></a>Krok 3 (opcjonalnie): Konfigurowanie klucza zarządzanego przez klienta

Pomiń ten krok i przejdź do następnego kroku, jeśli chcesz użyć klucza zarządzanego firmy Microsoft do ochrony kluczy funkcji BitLocker dla dysków. Aby skonfigurować własny klucz w celu ochrony klucza funkcji BitLocker, postępuj zgodnie z instrukcjami w tesłudze Azure Key Vault configure [customer-managed keys with Azure Key Vault for Azure Import/Export](storage-import-export-encryption-key-portal.md)(Konfigurowanie kluczy zarządzanych przez klienta przy użyciu usługi Azure Import/Export) w Azure Portal .

## <a name="step-4-ship-the-drives"></a>Krok 4. Wysyłka dysków

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Krok 5. Aktualizowanie zadania przy użyciu informacji śledzenia

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Krok 6. Weryfikowanie przekazania danych na platformę Azure

Śledzenie zadania do ukończenia. Po zakończeniu zadania sprawdź, czy twoje dane zostały przekazane na platformę Azure. Usuń dane lokalne dopiero po sprawdzeniu, czy przekazywanie powiodło się.

## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przegląd wymagań dotyczących importowania/eksportowania](storage-import-export-requirements.md)
