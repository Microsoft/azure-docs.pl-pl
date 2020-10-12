---
title: Jak usunąć i wyeksportować dane osobowe z Azure DevTest Labs
description: Dowiedz się, jak usuwać i eksportować dane osobowe z usługi Azure DevLast Labs, aby wspierać zobowiązania w ramach Ogólne rozporządzenie o ochronie danych (Rodo).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2c44b2f3aa6f2dfad18ed53804842a5dad8bd94a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483520"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Eksportowanie lub usuwanie danych osobowych z Azure DevTest Labs
Ten artykuł zawiera instrukcje dotyczące usuwania i eksportowania danych osobowych z usługi Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Jakie dane osobowe są zbierane przez DevTest Labs?
DevTest Labs zbiera dwa główne dane osobowe z użytkownika. Są to: adres e-mail użytkownika i identyfikator obiektu użytkownika. Te informacje mają kluczowe znaczenie dla usługi, aby zapewnić funkcje w usłudze dla administratorów laboratorium i użytkowników laboratorium.

### <a name="user-email-address"></a>Adres e-mail użytkownika
DevTest Labs używa adresu e-mail użytkownika do wysyłania powiadomień e-mail o automatycznym zamknięciu do użytkowników laboratorium. Wiadomość e-mail powiadamia użytkowników o zamknięciu maszyny. Jeśli chcesz to zrobić, użytkownicy mogą odroczyć lub pominąć wyłączenie. Adres e-mail można skonfigurować na poziomie laboratorium lub na poziomie maszyny wirtualnej.

**Ustawianie wiadomości e-mail w laboratorium:**

![Ustawianie poczty e-mail na poziomie laboratorium](./media/personal-data-delete-export/lab-user-email.png)

**Ustawianie poczty e-mail na maszynie wirtualnej:**

![Ustawianie adresu e-mail na poziomie maszyny wirtualnej](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Identyfikator obiektu użytkownika
DevTest Labs używa identyfikatora obiektu użytkownika do wyświetlania trendów kosztów miesięcznych i kosztów według informacji o zasobach do administratorów laboratorium. Umożliwia im śledzenie kosztów i zarządzanie progami dla laboratorium. 

**Szacowany trend kosztów dla bieżącego miesiąca kalendarzowego:** 
 ![ Szacowany trend kosztów dla bieżącego miesiąca kalendarzowego](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Szacowany koszt dla miesiąca według zasobu:** 
 ![ Szacowany koszt od początku miesiąca według zasobu](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Dlaczego dane osobowe są potrzebne?
Usługa DevTest Labs używa danych osobowych do celów operacyjnych. Te dane mają kluczowe znaczenie dla usługi do dostarczania najważniejszych funkcji. Jeśli ustawisz zasady przechowywania na adres e-mail użytkownika, użytkownicy laboratorium nie otrzymają do czasu powiadomienia e-mail o godzinie automatycznego zamknięcia po usunięciu adresu e-mail z systemu. Analogicznie, administrator laboratorium nie może wyświetlić trendów kosztów miesięcznych i kosztów według zasobów dla maszyn w ich laboratoriach, jeśli identyfikatory obiektów użytkownika są usuwane na podstawie zasad przechowywania. W związku z tym te dane muszą być przechowywane przez cały czas, gdy zasób użytkownika jest aktywny w laboratorium.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Jak mogę zapomnieć moje dane osobiste przez system?
Jeśli chcesz, aby dane osobowe zostały usunięte przez użytkownika laboratoryjnego, możesz to zrobić przez usunięcie odpowiedniego zasobu z laboratorium. Usługa DevTest Labs filtruje usunięte dane osobowe przez 30 dni po ich usunięciu przez użytkownika.

Na przykład po usunięciu maszyny wirtualnej lub usunięciu adresu e-mail usługa DevTest Labs filtruje te dane 30 dni po usunięciu zasobu. Zasady przechowywania 30 dni po usunięciu należy upewnić się, że firma Microsoft udostępnia dokładne, miesięczne koszty dla administratora laboratorium.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Jak mogę zażądać eksportu danych osobowych?
Dane dotyczące użycia osobistego i laboratorium można eksportować za pomocą Azure Portal lub programu PowerShell. Dane są eksportowane jako dwa różne pliki CSV:

- **disks.csv** — zawiera informacje o dyskach używanych przez różne maszyny wirtualne
- **virtualmachines.csv** — zawiera informacje o maszynach wirtualnych w laboratorium.

### <a name="azure-portal"></a>Azure Portal
Jako użytkownik laboratorium możesz zażądać eksportu danych osobowych, które są przechowywane w usłudze DevTest Labs. Aby zażądać eksportu, przejdź do opcji **dane osobowe** na stronie **Przegląd** w laboratorium. Wybierz przycisk **Zażądaj eksportu** umożliwia rozpoczęcie tworzenia pliku programu Excel do pobrania na koncie magazynu administratora laboratorium. Następnie można skontaktować się z administratorem laboratorium, aby wyświetlić te dane.

1. W menu po lewej stronie wybierz pozycję **dane osobowe** . 

    ![Strona danych osobowych](./media/personal-data-delete-export/personal-data-page.png)
2. Wybierz **grupę zasobów** , która zawiera laboratorium.

    ![Wybieranie grupy zasobów](./media/personal-data-delete-export/select-resource-group.png)
3. Wybierz **konto magazynu** w grupie zasobów.
4. Na stronie **konto magazynu** wybierz pozycję **obiekty blob**.

    ![Wybierz kafelek obiektów BLOB](./media/personal-data-delete-export/select-blobs-tile.png)
5. Wybierz kontener o nazwie **labresourceusage** na liście kontenerów.

    ![Wybieranie kontenera obiektów BLOB](./media/personal-data-delete-export/select-blob-container.png)
6. Wybierz **folder** o nazwie po Twoim laboratorium. W tym folderze znajdują się pliki **CSV** dla **dysków** i **maszyn wirtualnych** w laboratorium. Możesz pobrać te pliki CSV, odfiltrować zawartość dla użytkownika laboratorium żądającego dostępu i udostępnić je z nimi.

    ![Pobierz plik CSV](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Najważniejsze składniki w powyższym przykładzie są następujące:

- Invoke-AzureRmResourceAction polecenie.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dwa parametry akcji
    - **blobStorageAbsoluteSasUri** — identyfikator URI konta magazynu z tokenem sygnatury dostępu współdzielonego (SAS). W skrypcie programu PowerShell ta wartość może zostać przeniesiona zamiast klucza magazynu.
    - **usageStartDate** — Data rozpoczęcia ściągania danych wraz z datą końcową bieżącej daty wykonania akcji. Stopień szczegółowości jest na poziomie dnia, więc nawet w przypadku dodania informacji o czasie zostanie on zignorowany.

### <a name="exported-data---a-closer-look"></a>Wyeksportowane dane — bliższe spojrzenie
Teraz przyjrzyjmy się bliżej wyeksportowanych danych. Jak wspomniano wcześniej, po pomyślnym wyeksportowaniu danych będą dostępne dwa pliki CSV. 

**virtualmachines.csv** zawiera następujące kolumny danych:

| Nazwa kolumny | Opis |
| ----------- | ----------- | 
| SubscriptionId | Identyfikator subskrypcji, w której istnieje laboratorium. |
| LabUId | Unikatowy identyfikator identyfikatora GUID dla laboratorium. |
| LabName | Nazwa laboratorium. |
| LabResourceId | W pełni kwalifikowany identyfikator zasobu laboratorium. |
| ResourceGroupName | Nazwa grupy zasobów zawierającej maszynę wirtualną | 
| ResourceId | W pełni kwalifikowany identyfikator zasobu dla maszyny wirtualnej. |
| ResourceUId | Identyfikator GUID dla maszyny wirtualnej |
| Nazwa | Nazwa maszyny wirtualnej. |
| CreatedTime | Data i godzina utworzenia maszyny wirtualnej. |
| DeletedDate | Data i godzina usunięcia maszyny wirtualnej. Jeśli jest pusty, usuwanie nie nastąpiło jeszcze. |
| ResourceOwner | Właściciel maszyny wirtualnej. Jeśli wartość jest pusta, jest to albo maszyna wirtualna z obsługą lub utworzona przez jednostkę usługi. |
| PricingTier | Warstwa cenowa maszyny wirtualnej |
| ResourceStatus | Stan dostępności maszyny wirtualnej. Aktywny, jeśli nadal istnieje lub nieaktywny, jeśli maszyna wirtualna została usunięta. |
| ComputeResourceId | W pełni kwalifikowany identyfikator zasobu obliczeniowego maszyny wirtualnej. |
| Losową | Ustaw wartość true, jeśli maszyna wirtualna jest maszyną wirtualną | 
| EnvironmentId | Identyfikator zasobu środowiska, w którym utworzono maszynę wirtualną. Jest ona pusta, jeśli maszyna wirtualna nie została utworzona jako część zasobu środowiska. |
| ExpirationDate | Data wygaśnięcia dla maszyny wirtualnej. Ta wartość jest pusta, jeśli nie została ustawiona data wygaśnięcia.
| GalleryImageReferenceVersion |  Wersja obrazu podstawowego maszyny wirtualnej. |
| GalleryImageReferenceOffer | Oferta obrazu podstawowego maszyny wirtualnej. |
| GalleryImageReferencePublisher | Wydawca obrazu podstawowego maszyny wirtualnej. |
| GalleryImageReferenceSku | Jednostka SKU obrazu podstawowego maszyny wirtualnej |
| GalleryImageReferenceOsType | Typ systemu operacyjnego obrazu podstawowego maszyny wirtualnej |
| CustomImageId | W pełni kwalifikowany Identyfikator niestandardowego obrazu podstawowego maszyny wirtualnej. |

Następujące kolumny danych zawarte w **disks.csv** są wymienione poniżej:

| Nazwa kolumny | Opis | 
| ----------- | ----------- | 
| SubscriptionId | Identyfikator subskrypcji zawierającej laboratorium |
| LabUId | Identyfikator GUID laboratorium |
| LabName | Nazwa laboratorium | 
| LabResourceId | W pełni kwalifikowany identyfikator zasobu dla laboratorium | 
| ResourceGroupName | Nazwa grupy zasobów zawierającej laboratorium | 
| ResourceId | W pełni kwalifikowany identyfikator zasobu dla maszyny wirtualnej. |
| ResourceUId | Identyfikator GUID dla maszyny wirtualnej |
 |Nazwa | Nazwa dołączonego dysku |
| CreatedTime |Data i godzina utworzenia dysku z danymi. |
| DeletedDate | Data i godzina usunięcia dysku z danymi. |
| ResourceStatus | Stan zasobu. Aktywny, jeśli istnieje zasób. Nieaktywny, po usunięciu. |
| DiskBlobName | Nazwa obiektu BLOB dla dysku danych. |
| DiskSizeGB | Rozmiar dysku z danymi. |
| DiskType | Typ dysku z danymi. 0 w przypadku wersji Standard, 1 dla warstwy Premium. |
| LeasedByVmId | Identyfikator zasobu maszyny wirtualnej, do której został dołączony dysk z danymi. |


> [!NOTE]
> Jeśli zamierzasz korzystać z wielu laboratoriów i chcesz uzyskać ogólne informacje, dwie kolumny klucza to **LabUID** i **ResourceUId**, które są unikatowymi identyfikatorami w ramach subskrypcji.

Eksportowane dane można manipulować i wizualizować przy użyciu narzędzi, takich jak SQL Server, Power BI itd. Ta funkcja jest szczególnie przydatna, gdy chcesz zgłosić użycie laboratorium do zespołu zarządzania, który może nie używać tej samej subskrypcji platformy Azure.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Ustawianie zasad dla laboratorium](devtest-lab-set-lab-policy.md)
- [Często zadawane pytania](devtest-lab-faq.md)
