---
title: Samouczek `:` Używanie tożsamości zarządzanej do uzyskiwania dostępu do usługi Azure Storage przy użyciu poświadczeń SAS — Azure AD
description: Samouczek przedstawiający sposób użycia tożsamości zarządzanej przypisanej przez system Windows VM do uzyskiwania dostępu do usługi Azure Storage przy użyciu poświadczeń SAS zamiast klucza dostępu do konta magazynu.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa04247aca777612c05a7531dc5b36e7af40e60e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255855"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Samouczek: używanie tożsamości zarządzanej przypisanej przez system Windows VM do uzyskiwania dostępu do usługi Azure Storage za pośrednictwem poświadczeń SYGNATURy dostępu współdzielonego

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób użycia tożsamości przypisanej do systemu dla maszyny wirtualnej z systemem Windows (VM) w celu uzyskania poświadczeń sygnatury dostępu współdzielonego (SAS) magazynu. W szczególności [poświadczeń SAS usługi](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

SYGNATURa dostępu współdzielonego usługi umożliwia przyznanie ograniczonego dostępu do obiektów na koncie magazynu, przez ograniczony czas i konkretną usługę (w naszym przypadku usługa BLOB Service) bez ujawniania klucza dostęp do konta. Możesz użyć poświadczeń SAS w zwykły sposób wykorzystywany podczas wykonywania operacji magazynu, np. podczas używania zestawu SDK usługi Storage. W tym samouczku przedstawiono przekazywanie i pobieranie obiektu BLOB przy użyciu programu PowerShell usługi Azure Storage. Poznasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu
> * Udzielanie maszynie wirtualnej dostępu do sygnatury SAS konta magazynu w usłudze Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania sygnatury SAS z usługi Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli jeszcze nie masz konta magazynu, teraz je utworzysz. Możesz również pominąć ten krok i udzielić zarządzanej tożsamości przypisanej do systemu przez maszynę wirtualną do poświadczenia SYGNATURy dostępu współdzielonego istniejącego konta magazynu. 

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij opcję **Magazyn**, a następnie **Konto magazynu**. Zostanie wyświetlony nowy panel „Utwórz konto magazynu”.
3. Wprowadź nazwę konta magazynu, której będziesz używać później.  
4. Opcje **Model wdrażania** i **Rodzaj konta** należy ustawić na „Resource Manager” i „Ogólnego przeznaczenia”. 
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.
6. Kliknij przycisk **Utwórz**.

    ![Tworzenie nowego konta magazynu](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Tworzenie kontenera obiektów blob na koncie magazynu

Później przekażemy i pobierzemy plik do nowego konta magazynu. Ponieważ pliki wymagają magazynu obiektów blob, musimy utworzyć kontener obiektów blob, w którym będziemy przechowywać plik.

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. Kliknij link **Kontenery** w panelu po lewej stronie, w sekcji „Usługa obiektów blob”.
3. Kliknij pozycję **+ Kontener** w górnej części strony, aby wysunąć panel „Nowy kontener”.
4. Podaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana w dalszej części tego samouczka. 

    ![Tworzenie kontenera magazynu](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Udzielanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej dostępu do używania sygnatury SAS magazynu 

Usługa Azure Storage nie zapewnia natywnej obsługi uwierzytelniania usługi Azure AD.  Można jednak użyć tożsamości zarządzanej do pobierania sygnatury dostępu współdzielonego magazynu z Menedżer zasobów, a następnie użyć sygnatury dostępu współdzielonego, aby uzyskać dostęp do magazynu.  W tym kroku udzielasz przypisanej przez system tożsamości zarządzanej maszyny wirtualnej dostępu do sygnatury SAS konta magazynu.   

1. Przejdź z powrotem do nowo utworzonego konta magazynu.   
2. Kliknij link **Kontrola dostępu (IAM)** w panelu po lewej stronie.  
3. Kliknij przycisk **+ Dodaj przypisanie roli** w górnej części strony, aby dodać nowe przypisanie roli dla maszyny wirtualnej
4. Ustaw opcję **Rola** na „Współautor konta magazynu” w prawej części strony.  
5. Na kolejnej liście rozwijanej ustaw opcję **Przypisz dostęp do** na zasób „Maszyna wirtualna”.  
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**, po czym ustaw opcję **Grupa zasobów** na wartość „Wszystkie grupy zasobów”.  
7. Na koniec w pozycji **Wybierz** użyj listy rozwijanej, aby wybrać maszynę wirtualną z systemem Windows, i kliknij przycisk **Zapisz**. 

    ![Alternatywny tekst obrazu](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania usługi Azure Resource Manager 

W pozostałej części tego samouczka będziemy pracować z poziomu wcześniej utworzonej maszyny wirtualnej.

W tej części będzie wymagane użycie poleceń cmdlet programu PowerShell w usłudze Azure Resource Manager.  Jeśli nie masz zainstalowanego [programu, Pobierz najnowszą wersję](/powershell/azure/) przed kontynuowaniem.

1. W witrynie Azure Portal przejdź do pozycji **Maszyny wirtualne**, przejdź do maszyny wirtualnej z systemem Windows, a następnie na stronie **Przegląd** kliknij opcję **Połącz** u góry.
2. Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia maszyny wirtualnej z systemem Windows. 
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program PowerShell w sesji zdalnej. 
4. Używając polecenia Invoke-WebRequest programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanej dla zasobów platformy Azure, aby uzyskać token dostępu na potrzeby usługi Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Azure Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI.
    
    Następnie wyodrębnij element„Content”, który jest przechowywany w ciągu w formacie JavaScript Object Notation (JSON) w obiekcie $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Następnie wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Pobieranie poświadczeń SAS z usługi Azure Resource Manager w celu wykonywania wywołań do magazynu 

Teraz Użyj programu PowerShell, aby wywołać Menedżer zasobów przy użyciu tokenu dostępu pobranego w poprzedniej sekcji, aby utworzyć poświadczenia SAS magazynu. Po pobraniu poświadczeń SAS możemy wywoływać operacje magazynu.

W przypadku tego żądania użyjemy następujących parametrów żądania HTTP, aby utworzyć poświadczenia SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Te parametry są uwzględnione w treści żądania POST dla poświadczeń SAS. Aby uzyskać więcej informacji o parametrach potrzebnych do tworzenia poświadczeń SAS, zobacz [Dokumentację interfejsu REST sygnatury dostępu współdzielonego usługi listy](/rest/api/storagerp/storageaccounts/listservicesas).

Najpierw przekonwertuj parametry na format JSON, a następnie Wywołaj `listServiceSas` punkt końcowy magazynu, aby utworzyć poświadczenie sygnatury dostępu WSPÓŁdzielonego:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> W adresie URL rozróżniana jest wielkość liter, więc upewnij się, że użyto takich samych wartości jak wcześniej, podczas nazywania grupy zasobów — z wielką literą „G” w nazwie „resourceGroups”. 

Teraz możemy wyodrębnić poświadczenie sygnatury dostępu współdzielonego z odpowiedzi:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Po sprawdzeniu poświadczeń SAS zobaczysz coś poniżej:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Następnie utworzymy plik o nazwie „test.txt”. Następnie Użyj poświadczeń sygnatury dostępu współdzielonego w celu uwierzytelnienia za pomocą `New-AzStorageContent` polecenia cmdlet, Przekaż plik do naszego kontenera obiektów blob, a następnie Pobierz plik.

```bash
echo "This is a test text file." > test.txt
```

Pamiętaj, aby najpierw zainstalować polecenia cmdlet usługi Azure Storage przy użyciu polecenia `Install-Module Azure.Storage`. Następnie przekaż właśnie utworzony obiekt blob przy użyciu polecenia cmdlet `Set-AzStorageBlobContent` programu PowerShell:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Odpowiedź:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Możesz też pobrać właśnie przekazany obiekt blob przy użyciu polecenia cmdlet `Get-AzStorageBlobContent` programu PowerShell:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Odpowiedź:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia tożsamości zarządzanej przypisanej do systemu Windows maszyny wirtualnej w celu uzyskania dostępu do usługi Azure Storage przy użyciu poświadczeń SYGNATURy dostępu współdzielonego.  Aby dowiedzieć się więcej o sygnaturze dostępu współdzielonego usługi Azure Storage, zobacz:

> [!div class="nextstepaction"]
>[Używanie sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-sas-overview.md)