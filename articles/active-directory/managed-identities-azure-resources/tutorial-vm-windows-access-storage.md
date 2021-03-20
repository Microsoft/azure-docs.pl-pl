---
title: Dostęp do usługi Azure Storage przy użyciu tożsamości zarządzanej przypisanej przez system Windows VM | Microsoft Docs
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Storage.
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
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1cc69b3cfdac307edf6dfe999a5d538c2cb811
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89263182"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure Storage

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system dla maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do usługi Azure Storage. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie kontenera obiektów blob na koncie magazynu
> * Udzielenie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows dostępu do konta magazynu
> * Uzyskiwanie dostępu i używanie go do wywoływania usługi Azure Storage

> [!NOTE]
> Uwierzytelnianie usługi Azure Active Directory dla usługi Azure Storage jest dostępne w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>Włącz

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Udzielanie dostępu


### <a name="create-storage-account"></a>Tworzenie konta magazynu

W tej sekcji utworzysz konto magazynu.

1. Kliknij przycisk **+ Utwórz zasób** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij pozycję **Storage**, a następnie pozycję **Konto usługi Storage — Blob, File, Table, Queue**.
3. W obszarze **Nazwa** wprowadź nazwę konta magazynu.
4. Opcje **Model wdrażania** i **Rodzaj konta** należy ustawić na **Resource Manager** i **Magazyn (ogólnego przeznaczenia, wersja 1)**.
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.
6. Kliknij pozycję **Utwórz**.

    ![Tworzenie nowego konta magazynu](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

### <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Tworzenie kontenera obiektów blob i przekazywanie pliku na konto magazynu

Pliki wymagają magazynu obiektów blob, dlatego musimy utworzyć kontener obiektów blob, w którym będziemy przechowywać plik. Następnie przekaż plik do kontenera obiektów blob na nowym koncie magazynu.

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. W obszarze **BLOB Service** kliknij pozycję **Containers (kontenery**).
3. Kliknij pozycję **+ Kontener** w górnej części strony.
4. W obszarze **Nowy kontener** wprowadź nazwę kontenera, a następnie w obszarze **Poziom dostępu publicznego** zachowaj wartość domyślną.

    ![Tworzenie kontenera magazynu](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Za pomocą wybranego edytora utwórz plik o nazwie *hello world.txt* na maszynie lokalnej. Otwórz plik i dodaj tekst (bez cudzysłowów) „Hello world! :)”, a następnie zapisz plik.
6. Przekaż plik do nowo utworzonego kontenera, klikając nazwę kontenera, a następnie pozycję **Przekaż**
7. W okienku **Przekazywanie obiektu blob** w obszarze **Pliki** kliknij ikonę folderu i przejdź do pliku **hello_world.txt** na maszynie lokalnej, wybierz plik, a następnie kliknij pozycję **Przekaż**.
    ![Przekazywanie pliku tekstowego](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

### <a name="grant-access"></a>Udzielanie dostępu

W tej sekcji pokazano, jak udzielić dostępu do maszyny wirtualnej do kontenera usługi Azure Storage. Przypisanej przez system tożsamości zarządzanej maszyny wirtualnej można użyć do pobierania danych w obiekcie blob usługi Azure Storage.

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. Kliknij link **Kontrola dostępu (IAM)** w panelu po lewej stronie.
3. Kliknij pozycję **+ Dodaj przypisanie roli** w górnej części strony, aby dodać nowe przypisanie roli dla maszyny wirtualnej.
4. W obszarze **rola**, z listy rozwijanej wybierz pozycję **czytnik danych magazynu obiektów BLOB**.
5. Z następnej listy rozwijanej w obszarze **Przypisz dostęp do** wybierz pozycję **Maszyna wirtualna**.
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**, po czym ustaw opcję **Grupa zasobów** na wartość **Wszystkie grupy zasobów**.
7. W obszarze **Wybierz** wskaż maszynę wirtualną, a następnie kliknij pozycję **Zapisz**.

    ![Przypisywanie uprawnień](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="access-data"></a>Uzyskiwanie dostępu do danych 

Usługa Azure Storage natywnie obsługuje uwierzytelnianie usługi Azure AD, więc może bezpośrednio akceptować tokeny dostępu pozyskane przy użyciu tożsamości zarządzanej. Jest to część integracji usługi Azure Storage z usługą Azure AD. Takie rozwiązanie różni się od podawania poświadczeń w parametrach połączenia.

Oto przykład kodu platformy .NET służący do otwierania połączenia z usługą Azure Storage przy użyciu tokenu dostępu, a następnie odczytywania zawartości utworzonego wcześniej pliku. Ten kod należy uruchomić na maszynie wirtualnej, aby mógł uzyskać dostęp do punktu końcowego tożsamości zarządzanej maszyny wirtualnej. Do korzystania z metody tokenu dostępu jest wymagany .NET Framework 4,6 lub nowszy. Zastąp odpowiednio wartość `<URI to blob file>`. Tę wartość można uzyskać poprzez przejście do utworzonego i przekazanego do magazynu obiektów blob pliku, a następnie skopiowanie **adresu URL** w sekcji **Właściwości** na stronie **Przegląd**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");

            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);

            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }
    }
}
```

Odpowiedź zawiera zawartość pliku:

`Hello world! :)`


## <a name="disable"></a>Wyłącz

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak umożliwić przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows uzyskiwanie dostępu do usługi Azure Storage.  Aby dowiedzieć się więcej o usłudze Azure Storage, zobacz:

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)