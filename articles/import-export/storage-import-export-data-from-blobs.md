---
title: Eksportowanie danych z usługi Azure Blobs przy użyciu usługi Azure Import/Export | Microsoft Docs
description: Dowiedz się, jak tworzyć zadania eksportu w Azure Portal do transferu danych z obiektów blob platformy Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 2d4885f23e775f84a412d176568d992ebe01166b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875705"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Eksportowanie danych z usługi Azure Blob Storage za pomocą usługi Azure Import/Export

Ten artykuł zawiera szczegółowe instrukcje dotyczące używania usługi Azure Import/Export do bezpiecznego eksportowania dużych ilości danych z usługi Azure Blob Storage. Usługa wymaga wysłania pustych dysków do centrum danych platformy Azure. Usługa eksportuje dane z konta magazynu na dyski, a następnie wysyła dyski z powrotem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania eksportu w celu transferu danych poza Azure Blob Storage dokładnie przejrzyj i ukończ następującą listę wymagań wstępnych dotyczących tej usługi.
Musisz:

- Mieć aktywną subskrypcję platformy Azure, która może być używana dla usługi Import/Export.
- Mieć co najmniej jedno konto usługi Azure Storage. Zapoznaj się z listą [obsługiwanych kont magazynu i typów magazynów dla usługi Import/Export.](storage-import-export-requirements.md) Aby uzyskać informacje na temat tworzenia nowego konta magazynu, zobacz [How to Create a Storage Account (Jak utworzyć konto magazynu).](../storage/common/storage-account-create.md)
- Mieć odpowiednią liczbę dysków [obsługiwanych typów.](storage-import-export-requirements.md#supported-disks)
- Mieć konto FedEx/DHL. Jeśli chcesz użyć operatora innego niż FedEx/DHL, skontaktuj się z zespołem Azure Data Box Operations pod adres `adbops@microsoft.com` .
  - Konto musi być prawidłowe, musi mieć saldo i musi mieć możliwości wysyłki zwrotu.
  - Wygeneruj numer śledzenia dla zadania eksportu.
  - Każde zadanie powinno mieć oddzielny numer śledzenia. Nie ma możliwości obsługi wielu zadań o tym samym numerze śledzenia.
  - Jeśli nie masz konta przewoźnika, przejdź do:
    - [Utwórz konto FedEx](https://www.fedex.com/en-us/create-account.html)lub
    - [Utwórz konto DHL.](http://www.dhl-usa.com/en/express/shipping/open_account.html)

## <a name="step-1-create-an-export-job"></a>Krok 1. Tworzenie zadania eksportu

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj poniższe kroki, aby utworzyć zadanie eksportu w Azure Portal.

1. Zaloguj się do <https://portal.azure.com/> .
2. Wyszukaj zadania **importu/eksportu.**

    ![Wyszukiwanie zadań importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Wybierz pozycję **+ Nowe**.

    ![Wybierz pozycję + Nowy, aby utworzyć nowy ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Na stronie **Podstawy**:

   1. Wybierz subskrypcję.
   1. Wybierz grupę zasobów lub wybierz **pozycję Utwórz nową** i utwórz nową.
   1. Wprowadź opisową nazwę zadania importu. Użyj nazwy , aby śledzić postęp zadań.
       * Nazwa może zawierać tylko małe litery, cyfry i łączniki.
       * Nazwa musi zaczynać się literą i może nie zawierać spacji.

   1. Wybierz pozycję **Eksportuj z platformy Azure.**

    ![Podstawowe opcje dotyczące zamówienia eksportu](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Wybierz **pozycję Dalej: Szczegóły >,** aby kontynuować.

5. W **szczegółach zadania:**

   1. Wybierz region świadczenia usługi Azure, w którym obecnie są twoje dane.
   1. Wybierz konto magazynu, z którego chcesz wyeksportować dane. Użyj konta magazynu w pobliżu swojej lokalizacji.

      Lokalizacja listy rozwijanej jest wypełniana automatycznie na podstawie regionu wybranego konta magazynu.

   1. Określ dane obiektów blob do wyeksportowania z konta magazynu na pusty dysk lub dyski. Wybierz jedną z trzech poniższych metod.

      - Wybierz opcję **Eksportuj wszystkie** dane obiektów blob na koncie magazynu.

        ![Eksportuj wszystko](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Wybierz **pozycję Wybrane kontenery i obiekty blob** oraz określ kontenery i obiekty blob do wyeksportowania. Można użyć więcej niż jednej z metod wyboru. Wybranie opcji **Dodaj** powoduje otwarcie panelu po prawej stronie, w którym można dodać ciągi wyboru.

        |Opcja|Opis|
        |------|-----------|      
        |**Dodawanie kontenerów**|Wyeksportuj wszystkie obiekty blob w kontenerze.<br>Wybierz **pozycję Dodaj kontenery** i wprowadź nazwy poszczególnych kontenerów.|
        |**Dodawanie obiektów blob**|Określ pojedyncze obiekty blob do wyeksportowania.<br>Wybierz **pozycję Dodaj obiekty blob.** Następnie określ ścieżkę względną do obiektu blob, rozpoczynając od nazwy kontenera. Użyj *$root,* aby określić kontener główny.<br>Należy podać ścieżki obiektów blob w prawidłowym formacie, aby uniknąć błędów podczas przetwarzania, jak pokazano na tym zrzucie ekranu. Aby uzyskać więcej informacji, zobacz [Przykłady prawidłowych ścieżek obiektów blob](#examples-of-valid-blob-paths).|
        |**Dodawanie prefiksów**|Użyj prefiksu, aby wybrać zestaw kontenerów o podobnej nazwie lub obiektów blob o podobnej nazwie w kontenerze. Prefiks może być prefiksem nazwy kontenera, pełną nazwą kontenera lub pełną nazwą kontenera, po której następuje prefiks nazwy obiektu blob. |

        ![Eksportowanie wybranych kontenerów i obiektów blob](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Wybierz pozycję Eksportuj z pliku listy obiektów blob **(format XML)** i wybierz plik XML zawierający listę ścieżek i prefiksów dla obiektów blob do wyeksportowania z konta magazynu. Należy skonstruować plik XML i zapisać go w kontenerze dla konta magazynu. Plik nie może być pusty.

      > [!IMPORTANT]
      > Jeśli używasz pliku XML do wybierania obiektów blob do wyeksportowania, upewnij się, że plik XML zawiera prawidłowe ścieżki i/lub prefiksy. Jeśli plik jest nieprawidłowy lub żadne dane nie pasuje do określonych ścieżek, kolejność kończy się częściowymi danymi lub żadne dane nie są eksportowane.

       Aby zobaczyć, jak dodać plik XML do kontenera, zobacz [Eksportowanie kolejności przy użyciu pliku XML](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file).

      ![Eksportowanie z pliku listy obiektów blob](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Jeśli obiekt blob do wyeksportowania jest w użyciu podczas kopiowania danych, usługa Azure Import/Export pobiera migawkę obiektu blob i kopiuje migawkę.

   Wybierz **pozycję Dalej: wysyłanie >,** aby kontynuować.

6. W **przypadku wysyłki:**

    - Wybierz operatora z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się Azure Data Box operations pod adres , aby uzyskać informacje dotyczące przewoźnika, `adbops@microsoft.com`  z których planujesz korzystać.
    - Wprowadź prawidłowy numer konta przewoźnika utworzonego za pomocą tego operatora. Firma Microsoft używa tego konta do wysłania dysków z powrotem do Ciebie po zakończeniu zadania eksportu.
    - Podaj pełną i prawidłową nazwę kontaktu, numer telefonu, adres e-mail, adres ulicy, miasto, zip, stan/województwo i kraj/region.

        > [!TIP]
        > Zamiast określać adres e-mail dla jednego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet wtedy, gdy administrator odchodzi.

    Wybierz **pozycję Przejrzyj i utwórz,** aby kontynuować.

7. W **przycisku Przeglądanie + tworzenie:**

   1. Przejrzyj szczegóły zadania.
   1. Zanotuj nazwę zadania i podaj adres wysyłkowy centrum danych platformy Azure w celu wysyłki dysków na platformę Azure.

      > [!NOTE]
      > Dyski należy zawsze wysyłać do centrum danych zanotowego w Azure Portal. Jeśli dyski są wysyłane do niewłaściwego centrum danych, zadanie nie zostanie przetworzone.

   1. Zapoznaj się **z warunkami** zamówienia dotyczącymi prywatności i usuwania danych źródłowych. Jeśli akceptujesz warunki, zaznacz pole wyboru poniżej warunków. Rozpoczyna się walidacja zamówienia.

   ![Przeglądanie i tworzenie zamówienia eksportu](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć zadanie eksportu w Azure Portal, należy wykonać następujące Azure Portal.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Tworzenie zadania

1. Użyj polecenia [az extension add,](/cli/azure/extension#az_extension_add) aby [dodać rozszerzenie az import-export:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Aby uzyskać listę lokalizacji, z których można odbierać dyski, użyj [polecenia az import-export location list:](/cli/azure/import-export/location#az_import_export_location_list)

    ```azurecli
    az import-export location list
    ```

1. Uruchom następujące polecenie [az import-export create,](/cli/azure/import-export#az_import_export_create) aby utworzyć zadanie eksportu, które używa istniejącego konta magazynu:

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
    > Zamiast określać adres e-mail dla jednego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet wtedy, gdy administrator odchodzi.

   To zadanie eksportuje wszystkie obiekty blob na koncie magazynu. Możesz określić obiekt blob do wyeksportowania, zastępując tę wartość dla **--export**:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Ta wartość parametru eksportuje obiekt blob o *logo.bmp* w kontenerze głównym.

   Istnieje również możliwość wybrania wszystkich obiektów blob w kontenerze przy użyciu prefiksu. Zastąp tę wartość dla **--export**:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Aby uzyskać więcej informacji, zobacz [Przykłady prawidłowych ścieżek obiektów blob](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Jeśli obiekt blob do wyeksportowania jest w użyciu podczas kopiowania danych, usługa Azure Import/Export pobiera migawkę obiektu blob i kopiuje migawkę.

1. Użyj polecenia [az import-export list,](/cli/azure/import-export#az_import_export_list) aby wyświetlić wszystkie zadania dla grupy zasobów myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Aby zaktualizować zadanie lub anulować zadanie, uruchom [polecenie az import-export update:](/cli/azure/import-export#az_import_export_update)

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Aby utworzyć zadanie eksportu w programie Azure PowerShell, należy wykonać następujące Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Gdy moduł **Az.ImportExport** programu PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Tworzenie zadania

1. Aby uzyskać listę lokalizacji, z których można odbierać dyski, użyj polecenia cmdlet [Get-AzImportExportLocation:](/powershell/module/az.importexport/get-azimportexportlocation)

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Uruchom następujący [przykładowy plik New-AzImportExport,](/powershell/module/az.importexport/new-azimportexport) aby utworzyć zadanie eksportu, które używa istniejącego konta magazynu:

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
    > Zamiast określać adres e-mail dla pojedynczego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet wtedy, gdy administrator odchodzi.

   To zadanie eksportuje wszystkie obiekty blob na koncie magazynu. Możesz określić obiekt blob do eksportu, zastępując tę wartość dla **wartości -ExportBlobListblobPath:**

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Ta wartość parametru eksportuje obiekt blob *ologo.bmp* w kontenerze głównym.

   Istnieje również możliwość wybrania wszystkich obiektów blob w kontenerze przy użyciu prefiksu. Zastąp tę wartość dla **wartości -ExportBlobListblobPath:**

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Aby uzyskać więcej informacji, zobacz [Przykłady prawidłowych ścieżek obiektów blob](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Jeśli obiekt blob do wyeksportowania jest w użyciu podczas kopiowania danych, usługa Azure Import/Export pobiera migawkę obiektu blob i kopiuje migawkę.

1. Użyj polecenia cmdlet [Get-AzImportExport,](/powershell/module/az.importexport/get-azimportexport) aby wyświetlić wszystkie zadania dla grupy zasobów myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Aby zaktualizować zadanie lub anulować zadanie, uruchom polecenie cmdlet [Update-AzImportExport:](/powershell/module/az.importexport/update-azimportexport)

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Krok 2. Wysyłka dysków

Jeśli nie znasz potrzebnej liczby dysków, przejdź do tematu [Sprawdzanie liczby dysków.](#check-the-number-of-drives) Jeśli znasz liczbę dysków, przejdź do wysyłki dysków.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3. Aktualizowanie zadania przy użyciu informacji śledzenia

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Krok 4. Odbieranie dysków

Gdy pulpit nawigacyjny zgłasza, że zadanie zostało ukończone, dyski są wysyłane do Ciebie, a numer śledzenia przesyłki jest dostępny w portalu.

1. Po otrzymaniu dysków z wyeksportowanych danych należy uzyskać klucze funkcji BitLocker w celu odblokowania dysków. Przejdź do zadania eksportu w Azure Portal. Kliknij **kartę Import/Export (Importuj/Eksportuj).**
2. Wybierz i kliknij zadanie eksportu z listy. Przejdź do **szyfrowania** i skopiuj klucze.

   ![Wyświetlanie kluczy funkcji BitLocker dla zadania eksportu](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Użyj kluczy funkcji BitLocker, aby odblokować dyski.

Eksportowanie jest ukończone.

## <a name="step-5-unlock-the-disks"></a>Krok 5. Odblokowywanie dysków

Użyj następującego polecenia, aby odblokować dysk:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Oto przykładowe dane wejściowe.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

W tej chwili możesz usunąć zadanie lub je opuścić. Zadania są automatycznie usuwane po upływie 90 dni.

## <a name="check-the-number-of-drives"></a>Sprawdzanie liczby dysków

Ten *opcjonalny* krok pomaga określić liczbę dysków wymaganych do zadania eksportu. Wykonaj ten krok w systemie Windows z [obsługiwaną wersją systemu operacyjnego.](storage-import-export-requirements.md#supported-operating-systems)

1. [Pobierz aplikację WAImportExport w wersji 1](https://www.microsoft.com/download/details.aspx?id=42659) w systemie Windows.
2. Rozpakować plik do folderu domyślnego `waimportexportv1` . Na przykład `C:\WaImportExportV1`.
3. Otwórz program PowerShell lub okno wiersza polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog na rozpakowany folder, uruchom następujące polecenie:

   `cd C:\WaImportExportV1`

4. Aby sprawdzić liczbę dysków wymaganą dla wybranych obiektów blob, uruchom następujące polecenie:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametry zostały opisane w poniższej tabeli:

    |Parametr wiersza polecenia|Opis|
    |--------------------------|-----------------|
    |**/logdir:**|Opcjonalny. Katalog dziennika. Pełne pliki dziennika są zapisywane w tym katalogu. Jeśli nie zostanie określony, bieżący katalog jest używany jako katalog dziennika.|
    |**/sn:**|Wymagane. Nazwa konta magazynu dla zadania eksportu.|
    |**/sk:**|Wymagane tylko wtedy, gdy nie określono sygnatury dostępu współdzielonego kontenera. Klucz konta dla konta magazynu dla zadania eksportu.|
    |**/csas:**|Wymagane tylko wtedy, gdy nie określono klucza konta magazynu. Sygnatura dostępu współdzielonego kontenera do tworzenia listy obiektów blob do wyeksportowania w zadaniu eksportu.|
    |**/ExportBlobListFile:**|Wymagane. Ścieżka do pliku XML zawierającego listę ścieżek obiektów blob lub prefiksów ścieżek obiektów blob dla obiektów blob do wyeksportowania. Format pliku używany w elemencie `BlobListBlobPath` w operacji Put [Job](/rest/api/storageimportexport/jobs) interfejsu API REST usługi Import/Export.|
    |**/DriveSize:**|Wymagane. Rozmiar dysków do użycia dla zadania *eksportu,* na przykład , 500 GB, 1,5 TB.|

    Zobacz przykład [polecenia PreviewExport](#example-of-previewexport-command).

5. Sprawdź, czy możesz odczytywać/zapisywać na dyskach, które zostaną wysłane do zadania eksportu.

### <a name="example-of-previewexport-command"></a>Przykład polecenia PreviewExport

W poniższym przykładzie pokazano `PreviewExport` polecenie :

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Plik listy obiektów blob eksportu może zawierać nazwy obiektów blob i prefiksy obiektów blob, jak pokazano poniżej:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Narzędzie Azure Import/Export wyświetla listę wszystkich obiektów blob do wyeksportowania i oblicza sposób ich pakowania na dyskach o określonym rozmiarze, uwzględniając wszelkie niezbędne obciążenia, a następnie szacuje liczbę dysków potrzebnych do przechowywania obiektów blob i informacje o użyciu dysków.

Oto przykład danych wyjściowych z pominiętym dziennikiem informacyjnym:

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

## <a name="examples-of-valid-blob-paths"></a>Przykłady prawidłowych ścieżek obiektów blob

W poniższej tabeli przedstawiono przykłady prawidłowych ścieżek obiektów blob:

   | Selektor | Ścieżka obiektu blob | Opis |
   | --- | --- | --- |
   | Rozpoczyna się od |/ |Eksportuje wszystkie obiekty blob na koncie magazynu |
   | Rozpoczyna się od |/$root/ |Eksportuje wszystkie obiekty blob w kontenerze głównym |
   | Rozpoczyna się od |/book |Eksportuje wszystkie obiekty blob w dowolnym kontenerze, który zaczyna się od prefiksu **book** |
   | Rozpoczyna się od |/music/ |Eksportuje wszystkie obiekty blob w kontenerach **music** |
   | Rozpoczyna się od |/music/love |Eksportuje wszystkie obiekty blob w **kontenerach muzyki,** które zaczynają się od **prefiksu love** |
   | Równe |$root/logo.bmp |Eksportuje **logo.bmp** obiektów blob w kontenerze głównym |
   | Równe |filmy wideo/story.mp4 |Eksportuje obiekty blob **story.mp4** wideo **kontenera** |

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
- [Przegląd wymagań dotyczących importowania/eksportowania](storage-import-export-requirements.md)
