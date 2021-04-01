---
title: Samouczek `:` Używanie tożsamości zarządzanej do uzyskiwania dostępu do usługi Azure Storage — Linux — Azure AD
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Storage.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4c7612188043be070ead92c88838b567b22787d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98131274"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Storage 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system dla maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Storage. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie konta magazynu
> * Tworzenie kontenera obiektów blob na koncie magazynu
> * Udzielanie tożsamości zarządzanej maszyny wirtualnej z systemem Linux dostępu do kontenera usługi Azure Storage
> * Uzyskiwanie tokenu dostępu i używanie go do wywoływania usługi Azure Storage

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia w tym samouczku, masz dwie opcje:

- Użyj usługi [Azure Cloud Shell](~/articles/cloud-shell/overview.md) w witrynie Azure Portal lub za pośrednictwem przycisku **Wypróbuj** znajdującego się w prawym górnym rogu każdego bloku kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia w wersji 2.0](/cli/azure/install-azure-cli) (2.0.23 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

W tej sekcji utworzysz konto magazynu. 

1. Kliknij przycisk **+ Utwórz zasób** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij pozycję **Storage**, a następnie pozycję **Konto usługi Storage — Blob, File, Table, Queue**.
3. W obszarze **Nazwa** wprowadź nazwę konta magazynu.  
4. Opcje **Model wdrażania** i **Rodzaj konta** należy ustawić na **Resource Manager** i **Magazyn (ogólnego przeznaczenia, wersja 1)**. 
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.
6. Kliknij pozycję **Utwórz**.

    ![Tworzenie nowego konta magazynu](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Tworzenie kontenera obiektów blob i przekazywanie pliku na konto magazynu

Pliki wymagają magazynu obiektów blob, dlatego musimy utworzyć kontener obiektów blob, w którym będziemy przechowywać plik. Następnie przekaż plik do kontenera obiektów blob w ramach nowego konta magazynu.

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. W obszarze **BLOB Service** kliknij pozycję **Containers (kontenery**).
3. Kliknij pozycję **+ Kontener** w górnej części strony.
4. W obszarze **Nowy kontener** wprowadź nazwę kontenera, a następnie w obszarze **Poziom dostępu publicznego** zachowaj wartość domyślną.

    ![Tworzenie kontenera magazynu](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Za pomocą wybranego edytora utwórz plik o nazwie *hello world.txt* na maszynie lokalnej.  Otwórz plik i dodaj tekst (bez cudzysłowów) „Hello world! :)”, a następnie zapisz plik. 

6. Przekaż plik do nowo utworzonego kontenera, klikając nazwę kontenera, a następnie pozycję **Przekaż**
7. W okienku **Przekazywanie obiektu blob** w obszarze **Pliki** kliknij ikonę folderu i przejdź do pliku **hello_world.txt** na maszynie lokalnej, wybierz plik, a następnie kliknij pozycję **Przekaż**.

    ![Przekazywanie pliku tekstowego](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Udzielanie maszynie wirtualnej dostępu do kontenera usługi Azure Storage 

Tożsamość zarządzana maszyny wirtualnej służy do pobierania danych w obiekcie blob usługi Azure Storage.

>[!NOTE]
> Aby uzyskać więcej informacji na temat różnych ról, których można użyć w celu udzielenia uprawnień do przeglądu magazynu [, Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)

1. Przejdź z powrotem do nowo utworzonego konta magazynu.  
2. Kliknij link **Kontrola dostępu (IAM)** w panelu po lewej stronie.  
3. Kliknij pozycję **+ Dodaj przypisanie roli** w górnej części strony, aby dodać nowe przypisanie roli dla maszyny wirtualnej.
4. W obszarze **rola**, z listy rozwijanej wybierz pozycję **czytnik danych magazynu obiektów BLOB**. 
5. Z następnej listy rozwijanej w obszarze **Przypisz dostęp do** wybierz pozycję **Maszyna wirtualna**.  
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**, po czym ustaw opcję **Grupa zasobów** na wartość **Wszystkie grupy zasobów**.  
7. W obszarze **Wybierz** wskaż maszynę wirtualną, a następnie kliknij pozycję **Zapisz**.

    ![Przypisywanie uprawnień](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Uzyskiwanie tokenu dostępu i używanie go do wywoływania usługi Azure Storage

Usługa Azure Storage natywnie obsługuje uwierzytelnianie usługi Azure AD, więc może bezpośrednio akceptować tokeny dostępu pozyskane przy użyciu tożsamości zarządzanej. Jest to część integracji usługi Azure Storage z usługą Azure AD. Takie rozwiązanie różni się od podawania poświadczeń w parametrach połączenia.

Aby wykonać poniższe kroki, musisz pracować na utworzonej wcześniej maszynie wirtualnej, a do nawiązania połączenia z nią użyć klienta protokołu SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](/windows/wsl/about). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. W witrynie Azure Portal przejdź do pozycji **Maszyny wirtualne**, przejdź do maszyny wirtualnej z systemem Linux, a następnie na stronie **Przegląd** kliknij pozycję **Połącz**. Skopiuj ciąg, aby nawiązać połączenie z maszyną wirtualną.
2. **Połącz** się z maszyną wirtualną za pomocą wybranego klienta SSH. 
3. W oknie terminalu, używając narzędzia CURL, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanej, aby uzyskać token dostępu do usługi Azure Storage.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. Teraz możesz użyć tokenu dostępu, aby uzyskać dostęp do usługi Azure Storage, na przykład w celu odczytania zawartości przykładowego pliku, który został wcześniej przekazany do kontenera. Zastąp wartości elementów `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` i `<FILE NAME>` wartościami określonymi wcześniej, a element `<ACCESS TOKEN>` tokenem zwróconym w poprzednim kroku.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Odpowiedź zawiera zawartość pliku:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób włączania przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Storage.  Aby dowiedzieć się więcej o usłudze Azure Storage, zobacz:

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)
