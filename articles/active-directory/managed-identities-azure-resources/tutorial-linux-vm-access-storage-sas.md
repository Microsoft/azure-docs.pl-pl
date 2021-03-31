---
title: 'Samouczek: uzyskiwanie dostępu do usługi Azure Storage przy użyciu poświadczeń SAS-Linux-Azure AD'
description: Samouczek, który przedstawia sposób użycia przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Linux do uzyskania dostępu do usługi Azure Storage przy użyciu poświadczeń SAS zamiast klucza dostępu do konta magazynu.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3edc63a1532bb6889fc490e400dbb57e7bce10d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360415"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-identity-to-access-azure-storage-via-a-sas-credential"></a>Samouczek: uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem poświadczeń SAS przy użyciu przypisanej przez system tożsamości maszyny wirtualnej z systemem Linux

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek przedstawia sposób używania tożsamości zarządzanej przypisanej przez system do maszyny wirtualnej z systemem Linux w celu uzyskania poświadczeń sygnatury dostępu współdzielonego magazynu. W szczególności [poświadczeń SAS usługi](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

> [!NOTE]
> Klucz sygnatury dostępu współdzielonego wygenerowany w tym samouczku nie zostanie ograniczony/powiązany z maszyną wirtualną.  

Sygnatura dostępu współdzielonego usługi zapewnia możliwość udzielenia ograniczonego dostępu do obiektów na koncie magazynu przez ograniczony czas oraz dla konkretnej usługi (w naszym przypadku usługi obiektów blob) bez ujawniania klucza dostępu do konta. Możesz użyć poświadczeń SAS w zwykły sposób wykorzystywany podczas wykonywania operacji magazynu, np. podczas używania zestawu SDK usługi Storage. W tym samouczku zademonstrujemy przekazywanie i pobieranie obiektu blob przy użyciu interfejsu wiersza polecenia usługi Azure Storage. Poznasz następujące czynności:


> [!div class="checklist"]
> * Tworzenie konta magazynu
> * Tworzenie kontenera obiektów blob na koncie magazynu
> * Udzielanie maszynie wirtualnej dostępu do sygnatury SAS konta magazynu w usłudze Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania sygnatury SAS z usługi Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli jeszcze nie masz konta magazynu, teraz je utworzysz.  Możesz też pominąć ten krok i przyznać przypisanej przez system tożsamości zarządzanej maszyny wirtualnej dostęp do kluczy na istniejącym koncie magazynu. 

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij opcję **Magazyn**, a następnie **Konto magazynu**. Zostanie wyświetlony nowy panel „Utwórz konto magazynu”.
3. Wprowadź **nazwę** konta magazynu, którego będziesz używać później.  
4. Opcje **Model wdrażania** i **Rodzaj konta** należy ustawić na „Resource Manager” i „Ogólnego przeznaczenia”. 
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.
6. Kliknij pozycję **Utwórz**.

    ![Tworzenie nowego konta magazynu](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Tworzenie kontenera obiektów blob na koncie magazynu

Później przekażemy i pobierzemy plik do nowego konta magazynu. Ponieważ pliki wymagają magazynu obiektów blob, musimy utworzyć kontener obiektów blob, w którym będziemy przechowywać plik.

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. Kliknij link **Kontenery** w panelu po lewej stronie, w sekcji „Usługa obiektów blob”.
3. Kliknij pozycję **+ Kontener** w górnej części strony, aby wysunąć panel „Nowy kontener”.
4. Podaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana w dalszej części tego samouczka. 

    ![Tworzenie kontenera magazynu](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Udzielanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej dostępu do używania sygnatury SAS magazynu

Usługa Azure Storage nie zapewnia natywnej obsługi uwierzytelniania usługi Azure AD.  Możesz jednak użyć przypisanej przez system tożsamości zarządzanej maszyny wirtualnej, aby pobrać sygnaturę SAS magazynu z usługi Resource Manager, a następnie użyć sygnatury SAS do uzyskania dostępu do magazynu.  W tym kroku udzielasz przypisanej przez system tożsamości zarządzanej maszyny wirtualnej dostępu do sygnatury SAS konta magazynu.   

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. Kliknij link **Kontrola dostępu (IAM)** w panelu po lewej stronie.  
3. Kliknij przycisk **+ Dodaj przypisanie roli** w górnej części strony, aby dodać nowe przypisanie roli dla maszyny wirtualnej
4. Ustaw opcję **Rola** na „Współautor konta magazynu” w prawej części strony. 
5. Na kolejnej liście rozwijanej ustaw opcję **Przypisz dostęp do** na zasób „Maszyna wirtualna”.  
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**, po czym ustaw opcję **Grupa zasobów** na wartość „Wszystkie grupy zasobów”.  
7. Na koniec w pozycji **Wybierz** użyj listy rozwijanej, aby wybrać maszynę wirtualną z systemem Linux, i kliknij przycisk **Zapisz**.  

    ![Alternatywny tekst obrazu](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania usługi Azure Resource Manager

W pozostałej części tego samouczka będziemy pracować z poziomu wcześniej utworzonej maszyny wirtualnej.

Aby wykonać te kroki, będziesz potrzebować klienta SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](/windows/wsl/install-win10). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W witrynie Azure Portal przejdź do pozycji **Maszyny wirtualne**, przejdź do maszyny wirtualnej z systemem Linux, a następnie na stronie **Przegląd** kliknij opcję **Połącz** u góry. Skopiuj ciąg, aby nawiązać połączenie z maszyną wirtualną. 
2. Połącz się z maszyną wirtualną przy użyciu klienta SSH.  
3. Zobaczysz monit o wprowadzenie **hasła** dodanego podczas tworzenia **maszyny wirtualnej z systemem Linux**. Logowanie powinno wtedy zostać pomyślnie wykonane.  
4. Użyj programu CURL, aby uzyskać token dostępu dla usługi Azure Resource Manager.  

    Żądanie programu CURL oraz odpowiedź dla tokenu dostępu znajduje się poniżej:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > W poprzednim żądaniu wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Azure Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI.
    > W następującej odpowiedzi element access_token został skrócony dla zwięzłości.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Pobieranie poświadczeń SAS z usługi Azure Resource Manager w celu wykonywania wywołań do magazynu

Teraz użyj programu CURL, aby wywołać usługę Resource Manager przy użyciu tokenu dostępu pozyskanego w poprzedniej sekcji, aby utworzyć poświadczenia SAS magazynu. Po uzyskaniu sygnatury SAS magazynu możemy wywołać operacje przekazywania/pobierania magazynu.

W przypadku tego żądania użyjemy następujących parametrów żądania HTTP, aby utworzyć poświadczenia SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Te parametry są uwzględnione w treści żądania POST dla poświadczeń SAS. Aby uzyskać więcej informacji o parametrach potrzebnych do tworzenia poświadczeń SAS, zobacz [Dokumentację interfejsu REST sygnatury dostępu współdzielonego usługi listy](/rest/api/storagerp/storageaccounts/listservicesas).

Użyj następującego żądania CURL, aby uzyskać poświadczenia SAS. Upewnij się, że parametry `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` i `<EXPIRATION TIME>` zostały zastąpione własnymi wartościami. Zastąp wartość `<ACCESS TOKEN>` tokenem dostępu pobranym wcześniej:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> W tekście w poprzednim adresie URL rozróżniana jest wielkość liter, więc upewnij się, że stosujesz odpowiednie małe i wielkie litery dla grup zasobów, aby odpowiednio je przedstawić. Ponadto należy pamiętać, że jest to żądanie POST, a nie żądanie GET.

Odpowiedź programu CURL zwróci poświadczenia SAS:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Utwórz przykładowy plik obiektu blob do przekazania do kontenera magazynu obiektów blob. Na maszynie wirtualnej z systemem Linux możesz to zrobić przy użyciu następującego polecenia. 

```bash
echo "This is a test file." > test.txt
```

Następnie przeprowadź uwierzytelnianie przy użyciu polecenia `az storage` interfejsu wiersza polecenia, korzystając z poświadczeń SAS, i przekaż plik do kontenera obiektów blob. W tym kroku musisz [zainstalować najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na maszynie wirtualnej, jeśli jeszcze tego nie zrobiono.

```azurecli
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Odpowiedź: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Ponadto możesz pobrać plik przy użyciu interfejsu wiersza polecenia platformy Azure oraz przeprowadzić uwierzytelnianie przy użyciu poświadczeń SAS. 

Żądanie: 

```azurecli
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Odpowiedź: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Storage przy użyciu poświadczeń SAS.  Aby dowiedzieć się więcej o sygnaturze dostępu współdzielonego usługi Azure Storage, zobacz:

> [!div class="nextstepaction"]
>[Używanie sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-sas-overview.md)
