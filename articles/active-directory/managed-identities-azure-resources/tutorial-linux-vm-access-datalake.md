---
title: Samouczek `:` Używanie tożsamości zarządzanej do uzyskiwania dostępu do Azure Data Lake Store-Linux — Azure AD
description: Samouczek przedstawiający sposób uzyskiwania dostępu do usługi Azure Data Lake Store za pomocą przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Linux.
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
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d465419dfe36fd5dd67abdef22a6f54fba69a98e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89267466"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku pokazano, jak za pomocą tożsamości zarządzanej przypisanej przez system do maszyny wirtualnej z systemem Linux (VM) uzyskać dostęp do Azure Data Lake Store. Omawiane kwestie: 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej praw dostępu do usługi Azure Data Lake Store.
> * Uzyskiwanie tokenu dostępu przy użyciu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej do uzyskiwania dostępu do usługi Azure Data Lake Store.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>Udzielanie dostępu

W tej sekcji pokazano, jak przydzielić MASZYNom wirtualnym dostęp do plików i folderów w Azure Data Lake Store. W tym kroku możesz użyć istniejącego wystąpienia usługi Data Lake Store lub utworzyć nowe. Aby utworzyć wystąpienie usługi Data Lake Store przy użyciu witryny Azure Portal, wykonaj czynności opisane w [przewodniku Szybki start dotyczącym usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md). W [dokumentacji usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md) są również dostępne przewodniki Szybki start, które używają interfejsu wiersza polecenia platformy Azure oraz programu Azure PowerShell.

W usłudze Data Lake Store utwórz nowy folder, a następnie przyznaj przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux uprawnienia do odczytu, zapisu i wykonywania plików w tym folderze:

1. W witrynie Azure Portal wybierz pozycję **Data Lake Store** w okienku po lewej stronie.
2. Wybierz wystąpienie usługi Data Lake Store, którego chcesz używać.
3. Wybierz pozycję **Eksplorator danych** na pasku poleceń.
4. Zostanie zaznaczony folder główny wystąpienia usługi Data Lake Store. Wybierz pozycję **Uzyskaj dostęp** na pasku poleceń.
5. Wybierz pozycję **Dodaj**.  W polu **Wybierz** wprowadź nazwę maszyny wirtualnej — na przykład **DevTestVM**. Wybierz maszynę wirtualną spośród wyników wyszukiwania, a następnie kliknij pozycję **Wybierz**.
6. Kliknij przycisk **Wybierz uprawnienia**.  Wybierz pozycje **Odczyt** i **Wykonywanie**, dodaj do pozycji **Ten folder** i dodaj jako **Tylko uprawnienie dostępu**. Wybierz przycisk **OK**.  Dodawanie uprawnienia powinno zakończyć się pomyślnie.
7. Zamknij okienko **Dostęp**.
8. W tym samouczku utworzymy nowy folder. Wybierz pozycję **Nowy folder** na pasku poleceń i nadaj folderowi nową nazwę — na przykład **TestFolder**.  Wybierz przycisk **OK**.
9. Wybierz utworzony folder, a następnie wybierz pozycję **Uzyskaj dostęp** na pasku poleceń.
10. Podobnie jak w kroku 5, wybierz pozycję **Dodaj**. W polu **Wybierz** wprowadź nazwę maszyny wirtualnej. Wybierz maszynę wirtualną spośród wyników wyszukiwania, a następnie kliknij pozycję **Wybierz**.
11. Podobnie jak w kroku 6, wybierz pozycję **Wybierz uprawnienia**. Wybierz pozycje **Odczyt**, **Zapis** i **Wykonywanie**, dodaj do pozycji **Ten folder** i dodaj jako **Wpis uprawnień dostępu i wpis uprawnień domyślnych**. Wybierz przycisk **OK**.  Dodawanie uprawnienia powinno zakończyć się pomyślnie.

Tożsamości zarządzane dla zasobów platformy Azure mogą teraz wykonywać wszystkie operacje na plikach w utworzonym folderze. Aby uzyskać więcej informacji na temat zarządzania dostępem do usługi Data Lake Store, zobacz [Kontrola dostępu w usłudze Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu 

W tej sekcji pokazano, jak uzyskać token dostępu i wywołać system plików Data Lake Store. Usługa Azure Data Lake Store natywnie obsługuje uwierzytelnianie usługi Azure AD, więc może bezpośrednio akceptować tokeny dostępu pozyskane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Aby uwierzytelniać w systemie plików Data Lake Store, wysyłasz token dostępu wystawiony przez usługę Azure AD do punktu końcowego systemu plików usługi Data Lake Store. Token dostępu znajduje się w nagłówku autoryzacji w formacie "Bearer \<ACCESS_TOKEN_VALUE\> ".  Aby dowiedzieć się więcej na temat obsługi usługi Data Lake Store na potrzeby uwierzytelniania usługi Azure AD, zobacz [Authentication with Data Lake Store using Azure Active Directory (Uwierzytelnianie w usłudze Data Lake Store za pomocą usługi Azure Active Directory)](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).

W tym samouczku uwierzytelniasz się w interfejsie API REST dla systemu plików Data Lake Store przy użyciu programu cURL w celu wysłania żądań REST.

> [!NOTE]
> Zestawy SDK klientów dla systemu plików usługi Data Lake Store jeszcze nie obsługują tożsamości zarządzanych dla zasobów platformy Azure.

Aby wykonać te kroki, potrzebujesz klienta SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](/windows/wsl/about). Jeśli potrzebujesz pomocy przy konfigurowaniu kluczy klienta SSH, zobacz [jak używać kluczy SSH w systemie Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [jak utworzyć i używać pary kluczy publicznych i prywatnych SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu przejdź do maszyny wirtualnej z systemem Linux. W obszarze **Omówienie** wybierz pozycję **Połącz**.  
2. Połącz się z maszyną wirtualną przy użyciu wybranego klienta protokołu SSH. 
3. W oknie terminalu, używając programu cURL, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanych dla platformy Azure, aby uzyskać token dostępu do systemu plików Data Lake Store. Identyfikator zasobu dla Data Lake Store ma wartość `https://datalake.azure.net/` .  Ważne jest, aby na końcu identyfikatora zasobu uwzględnić kreskę ukośną.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Odpowiedź oznaczająca powodzenie zwraca token dostępu służący do uwierzytelniania w usłudze Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Za pomocą narzędzia cURL wyślij żądanie do punktu końcowego REST systemu plików usługi Data Lake Store, aby wyświetlić listę folderów w folderze głównym. Jest to prosty sposób sprawdzenia, czy wszystko zostało poprawnie skonfigurowane. Skopiuj wartość tokenu dostępu z poprzedniego kroku. Ważne jest, że ciąg „Bearer” w nagłówku autoryzacji rozpoczynał się wielką literą „B”. Nazwę wystąpienia usługi Data Lake Store można znaleźć w sekcji **Omówienie** okienka **Data Lake Store** w witrynie Azure Portal.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Odpowiedź oznaczająca powodzenie wygląda następująco:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Teraz możesz spróbować przekazać plik do wystąpienia usługi Data Lake Store. Najpierw utwórz plik do przekazania.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Za pomocą narzędzia cURL wyślij żądanie do punktu końcowego REST systemu plików usługi Data Lake Store, aby przekazać plik do utworzonego wcześniej folderu. Przekazywanie obejmuje przekierowanie, a narzędzie cURL automatycznie przeprowadza przekierowanie. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Odpowiedź oznaczająca powodzenie wygląda następująco:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Za pomocą innych interfejsów API w systemie plików Data Lake Store można dołączać do plików, plików do pobrania i innych elementów.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób używania przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Data Lake Store. Aby dowiedzieć się więcej o usłudze Azure Data Lake Store, zobacz:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)